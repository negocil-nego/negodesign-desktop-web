<script lang="ts">
  /**
   * Reflect Background — fundo WebGL com padrão de reflexos animado,
   * que reage ao ponteiro. Preenche o elemento pai (100% x 100%) por padrão.
   */
  import { onMount, type Snippet } from "svelte";

  interface Props {
    tint?: string;
    backgroundColor?: string;
    speed?: number;
    scale?: number;
    contrast?: number;
    iterations?: number;
    opacity?: number;
    blur?: number;
    animation?: boolean;
    pointerStrength?: number;
    width?: number;
    height?: number;
    style?: string;
    children?: Snippet;
  }

  let {
    tint = "#005980",
    backgroundColor = "#000000",
    speed = 100,
    scale = 1,
    contrast = 8,
    iterations = 5,
    opacity = 100,
    blur = 4,
    animation = true,
    pointerStrength = 40,
    width = undefined,
    height = undefined,
    style = "",
    children,
  }: Props = $props();

  const VERTEX_SRC = `
attribute vec4 a_position;
void main() { gl_Position = a_position; }
`;

  const FRAGMENT_SRC = `
precision highp float;

uniform vec2 iResolution;
uniform float iTime;
uniform float u_speed;
uniform vec3 u_tint;
uniform float u_scale;
uniform float u_contrast;
uniform float u_iterations;
uniform vec2 u_pointer;
uniform float u_pointerStrength;

#define TAU 6.28318530718
#define MAX_ITER 8

void mainImage(out vec4 fragColor, in vec2 fragCoord) {
    float time = iTime * u_speed + 23.0;
    vec2 uv = fragCoord.xy / iResolution.xy;

    vec2 p = mod(uv * TAU * u_scale, TAU) - 250.0;

    vec2 pointerDelta = uv - u_pointer;
    pointerDelta.x *= iResolution.x / max(iResolution.y, 1.0);
    float pointerDist = length(pointerDelta);
    p += normalize(pointerDelta + 1e-4) * u_pointerStrength * exp(-pointerDist * 4.0) * TAU;

    vec2 i = vec2(p);
    float c = 1.0;
    float inten = 0.005;
    float used = 0.0;

    for (int n = 0; n < MAX_ITER; n++) {
        if (float(n) >= u_iterations) break;
        float t = time * (1.0 - (3.5 / float(n + 1)));
        i = p + vec2(
            cos(t - i.x) + sin(t + i.y),
            sin(t - i.y) + cos(t + i.x)
        );
        // Keep denominators off zero so p/(sin/inten) can't blow past a
        // low-precision float's max (NaN -> black on GPUs that ignore highp).
        float sx = sin(i.x + t) / inten;
        float sy = cos(i.y + t) / inten;
        sx = (sx >= 0.0 ? 1.0 : -1.0) * max(abs(sx), 0.05);
        sy = (sy >= 0.0 ? 1.0 : -1.0) * max(abs(sy), 0.05);
        c += 1.0 / length(vec2(p.x / sx, p.y / sy));
        used += 1.0;
    }

    c /= max(used, 1.0);
    c = 1.17 - pow(c, 1.4);

    float lum = pow(abs(c), u_contrast);
    vec3 colour = clamp(u_tint * lum * 2.0, 0.0, 1.0);

    fragColor = vec4(colour, 1.0);
}

void main() { mainImage(gl_FragColor, gl_FragCoord.xy); }
`;

  /** Aceita "#rgb", "#rrggbb", "#rrggbbaa" ou "rgb()/rgba()". */
  function parseColor(input: string): [number, number, number] {
    const fallback: [number, number, number] = [0, 0.35, 0.5];
    if (!input) return fallback;
    const str = input.trim();
    if (str[0] === "#") {
      let hex = str.slice(1);
      if (hex.length === 3 || hex.length === 4) {
        hex = hex[0] + hex[0] + hex[1] + hex[1] + hex[2] + hex[2];
      }
      if (hex.length >= 6) {
        const r = parseInt(hex.slice(0, 2), 16);
        const g = parseInt(hex.slice(2, 4), 16);
        const b = parseInt(hex.slice(4, 6), 16);
        if (!isNaN(r) && !isNaN(g) && !isNaN(b))
          return [r / 255, g / 255, b / 255];
      }
      return fallback;
    }
    const m = str.match(/[\d.]+/g);
    if (m && m.length >= 3) {
      return [
        Math.min(255, parseFloat(m[0])) / 255,
        Math.min(255, parseFloat(m[1])) / 255,
        Math.min(255, parseFloat(m[2])) / 255,
      ];
    }
    return fallback;
  }

  function compileShader(
    gl: WebGLRenderingContext,
    type: number,
    src: string,
  ): WebGLShader | null {
    const shader = gl.createShader(type);
    if (!shader) return null;
    gl.shaderSource(shader, src);
    gl.compileShader(shader);
    if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
      console.error("Shader compile error:", gl.getShaderInfoLog(shader));
      gl.deleteShader(shader);
      return null;
    }
    return shader;
  }

  let canvas: HTMLCanvasElement;

  // Valores derivados das props (lidos a cada frame pelo loop de desenho)
  const tintRgb = $derived(parseColor(tint));
  const speedValue = $derived(
    ((Number.isFinite(speed) ? speed : 100) / 100) * 0.5,
  );
  const scaleValue = $derived(Number.isFinite(scale) ? scale : 1);
  const contrastValue = $derived(Number.isFinite(contrast) ? contrast : 8);
  const iterationsValue = $derived(
    Number.isFinite(iterations) ? Math.round(iterations) : 5,
  );
  const animationOn = $derived(animation !== false);
  const pointerStrengthValue = $derived(
    Number.isFinite(pointerStrength) ? pointerStrength / 100 : 0.4,
  );
  const safeBlur = $derived(Number.isFinite(blur) ? blur : 0);
  const safeOpacity = $derived(Number.isFinite(opacity) ? opacity / 100 : 1);
  const fixedW = $derived(Number.isFinite(width) ? (width as number) : 0);
  const fixedH = $derived(Number.isFinite(height) ? (height as number) : 0);

  // Estado do ponteiro (não precisa ser reativo)
  const pointer = { x: 0.5, y: 0.5 };
  let pointerActive = 0;
  let pointerActiveTarget = 0;

  onMount(() => {
    const gl = canvas.getContext("webgl");
    if (!gl) {
      console.error("WebGL not supported");
      return;
    }

    const vs = compileShader(gl, gl.VERTEX_SHADER, VERTEX_SRC);
    const fs = compileShader(gl, gl.FRAGMENT_SHADER, FRAGMENT_SRC);
    if (!vs || !fs) return;

    const program = gl.createProgram();
    if (!program) return;
    gl.attachShader(program, vs);
    gl.attachShader(program, fs);
    gl.linkProgram(program);
    if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
      console.error("Program link error:", gl.getProgramInfoLog(program));
      return;
    }
    gl.useProgram(program);

    const buffer = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
    gl.bufferData(
      gl.ARRAY_BUFFER,
      new Float32Array([-1, -1, 1, -1, -1, 1, -1, 1, 1, -1, 1, 1]),
      gl.STATIC_DRAW,
    );
    const posLoc = gl.getAttribLocation(program, "a_position");
    gl.enableVertexAttribArray(posLoc);
    gl.vertexAttribPointer(posLoc, 2, gl.FLOAT, false, 0, 0);

    const uResolution = gl.getUniformLocation(program, "iResolution");
    const uTime = gl.getUniformLocation(program, "iTime");
    const uSpeed = gl.getUniformLocation(program, "u_speed");
    const uTint = gl.getUniformLocation(program, "u_tint");
    const uScale = gl.getUniformLocation(program, "u_scale");
    const uContrast = gl.getUniformLocation(program, "u_contrast");
    const uIterations = gl.getUniformLocation(program, "u_iterations");
    const uPointer = gl.getUniformLocation(program, "u_pointer");
    const uPointerStrength = gl.getUniformLocation(
      program,
      "u_pointerStrength",
    );

    const start = performance.now();
    let rafId = 0;
    let frozenT = 0;

    const draw = () => {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      const cw = fixedW || canvas.clientWidth || 300;
      const ch = fixedH || canvas.clientHeight || 300;
      const bw = Math.max(1, Math.round(cw * dpr));
      const bh = Math.max(1, Math.round(ch * dpr));
      if (canvas.width !== bw || canvas.height !== bh) {
        canvas.width = bw;
        canvas.height = bh;
        gl.viewport(0, 0, bw, bh);
      }

      const t = animationOn ? (performance.now() - start) / 1000 : frozenT;
      frozenT = t;
      const [r, g, b] = tintRgb;

      gl.uniform2f(uResolution, bw, bh);
      gl.uniform1f(uTime, t);
      gl.uniform1f(uSpeed, speedValue);
      gl.uniform3f(uTint, r, g, b);
      gl.uniform1f(uScale, scaleValue);
      gl.uniform1f(uContrast, contrastValue);
      gl.uniform1f(uIterations, iterationsValue);

      pointerActive += (pointerActiveTarget - pointerActive) * 0.08;
      gl.uniform2f(uPointer, pointer.x, pointer.y);
      gl.uniform1f(uPointerStrength, pointerStrengthValue * pointerActive);

      gl.drawArrays(gl.TRIANGLES, 0, 6);
    };

    const loop = () => {
      draw();
      rafId = requestAnimationFrame(loop);
    };

    const ro = new ResizeObserver(() => draw());
    ro.observe(canvas);

    draw();
    loop();

    return () => {
      cancelAnimationFrame(rafId);
      ro.disconnect();
    };
  });

  function handlePointerMove(e: PointerEvent) {
    if (!canvas) return;
    const rect = canvas.getBoundingClientRect();
    if (rect.width === 0 || rect.height === 0) return;
    pointer.x = (e.clientX - rect.left) / rect.width;
    pointer.y = 1 - (e.clientY - rect.top) / rect.height;
    pointerActiveTarget = 1;
  }

  function handlePointerLeave() {
    pointerActiveTarget = 0;
  }
</script>

<div
  class="reflect-root"
  role="presentation"
  style="width:{fixedW > 0 ? `${fixedW}px` : '100%'};height:{fixedH > 0
    ? `${fixedH}px`
    : '100%'};background:{backgroundColor || '#000'};{style}"
  onpointermove={handlePointerMove}
  onpointerleave={handlePointerLeave}
>
  <canvas bind:this={canvas} style="opacity:{safeOpacity};"></canvas>
  {#if safeBlur > 0}
    <div
      class="blur"
      style="backdrop-filter:blur({safeBlur}px);-webkit-backdrop-filter:blur({safeBlur}px);"
    ></div>
  {/if}
  <div class="reflect-content">
    {@render children?.()}
  </div>
</div>

<style>
  .reflect-root {
    position: relative;
    overflow: hidden;
  }
  canvas {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    display: block;
    pointer-events: none;
  }
  .blur {
    position: absolute;
    inset: 0;
    pointer-events: none;
  }
  .reflect-content {
    position: relative;
    z-index: 1;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
  }
</style>

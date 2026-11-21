<script lang="ts">
  /**
   * Tiles loader — grelha de blocos que "tingem" um a um conforme a página carrega.
   * A percentagem fica por cima em mix-blend-mode: difference.
   * Revelação: os blocos encolhem em onda diagonal a partir do canto inferior esquerdo.
   * Uso: <Tiles minTime={4} onComplete={() => ...} />
   * Fica fixo sobre a página; toca uma vez, espera o load real, revela e faz fade-out.
   */
  import { onMount } from "svelte";

  export let minTime = 14;
  export let background = "#EEEAE3";
  export let accent = "#151412";
  export let fontFamily = '"Instrument Serif", "Times New Roman", Times, serif';
  export let fontWeight: number | string = 400;
  export let fontStyle = "normal";
  export let letterSpacing = "-0.02em";
  export let lineHeight: number | string = 1;
  export let onComplete: (() => void) | undefined = undefined;

  const REVEAL_S = 1.6;
  const FADE_S = 0.35;
  const SHORT_SIDE = 7; // blocos ao longo do lado curto
  const INK_WINDOW = 0.04; // fatia do load que cada bloco leva a tingir

  // ── utilidades ──
  function rand(seed: number): () => number {
    let a = seed >>> 0;
    return () => {
      a = (a + 0x6d2b79f5) >>> 0;
      let t = a;
      t = Math.imul(t ^ (t >>> 15), t | 1);
      t ^= t + Math.imul(t ^ (t >>> 7), t | 61);
      return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
    };
  }

  /** Load simulado: pedaços chegam em tempos irregulares; exatamente 1 em loadS. */
  function loadCurve(seed: number, loadS: number): (t: number) => number {
    const r = rand(seed);
    const k = Math.min(1, loadS / 4.4);
    const chunks: { size: number; start: number; len: number }[] = [];
    let total = 0;
    for (let i = 0; i < 7; i++) {
      const size = 0.4 + r() * 1.2;
      chunks.push({
        size,
        start: r() * 0.78 * loadS,
        len: (0.25 + r() * 0.9) * k,
      });
      total += size;
    }
    chunks.push({ size: total * 0.12, start: loadS - 0.45 * k, len: 0.45 * k });
    total *= 1.12;
    return (t) => {
      if (t >= loadS) return 1;
      let p = 0;
      for (const c of chunks) {
        const x = Math.min(
          1,
          Math.max(0, (t - c.start) / Math.max(1e-3, c.len)),
        );
        p += c.size * x * x * (3 - 2 * x);
      }
      return Math.min(0.999, p / total);
    };
  }

  /** Load real do documento, 0..1. */
  function realLoad(): number {
    if (document.readyState === "complete") return 1;
    const imgs = document.images;
    let done = 0;
    for (let i = 0; i < imgs.length; i++) if (imgs[i].complete) done++;
    const frac = imgs.length ? done / imgs.length : 1;
    const fonts = document.fonts && document.fonts.status === "loaded" ? 1 : 0;
    return Math.min(0.95, 0.15 + 0.6 * frac + 0.2 * fonts);
  }

  const clamp = (x: number) => (x < 0 ? 0 : x > 1 ? 1 : x);
  const seg = (x: number, a: number, b: number) =>
    clamp((x - a) / Math.max(1e-6, b - a));
  const outCubic = (x: number) => 1 - Math.pow(1 - x, 3);
  const inOutCubic = (x: number) =>
    x < 0.5 ? 4 * x * x * x : 1 - Math.pow(-2 * x + 2, 3) / 2;

  // ── estado ──
  let root: HTMLDivElement;
  let cols = 11;
  let rows = 7;
  let tileEls: (HTMLDivElement | null)[] = [];
  let paperEls: (HTMLDivElement | null)[] = [];
  let countEl: HTMLDivElement;
  let numEl: HTMLSpanElement;

  $: count = cols * rows;
  /** Ponto do load em que cada bloco tinge: embaralhamento fixo por tamanho de grelha. */
  $: order = (() => {
    const r = rand(count * 7919 + 13);
    return Array.from({ length: count }, () => r() * (1 - INK_WINDOW));
  })();

  function frame(t: number, progress: number, reveal: number) {
    for (let i = 0; i < count; i++) {
      const paper = paperEls[i];
      if (paper)
        paper.style.opacity = String(
          1 - seg(progress, order[i], order[i] + INK_WINDOW),
        );
      const tile = tileEls[i];
      if (!tile) continue;
      const c = i % cols;
      const r = Math.floor(i / cols);
      const d =
        0.5 * (cols > 1 ? c / (cols - 1) : 0) +
        0.5 * (rows > 1 ? (rows - 1 - r) / (rows - 1) : 0);
      const e = inOutCubic(seg(reveal, d * 0.55, d * 0.55 + 0.45));
      tile.style.transform = `scale(${1 - e})`;
    }
    if (numEl) {
      const digits = String(Math.round(progress * 100));
      if (numEl.textContent !== digits) numEl.textContent = digits;
    }
    if (countEl) {
      countEl.style.opacity = String(
        outCubic(seg(t, 0.1, 0.8)) * (1 - seg(reveal, 0, 0.15)),
      );
    }
  }

  onMount(() => {
    const reduced =
      typeof window.matchMedia === "function" &&
      window.matchMedia("(prefers-reduced-motion: reduce)").matches;
    const revealS = reduced ? 0.4 : REVEAL_S;
    const loadS = reduced ? 0.6 : Math.max(0.5, minTime);
    const curve = loadCurve(7, loadS);

    // Grelha: 7 blocos no lado curto, quase quadrados em qualquer proporção
    const fit = () => {
      const w = root.offsetWidth;
      const h = root.offsetHeight;
      if (!w || !h) return;
      const cell = Math.min(w, h) / SHORT_SIDE;
      const nc = Math.max(1, Math.round(w / cell));
      const nr = Math.max(1, Math.round(h / cell));
      if (nc !== cols) cols = nc;
      if (nr !== rows) rows = nr;
    };
    fit();
    const ro = new ResizeObserver(fit);
    ro.observe(root);

    let t = 0;
    let progress = 0;
    let revealAt = -1;
    let last = -1;
    let raf = 0;
    let timeout = 0;

    frame(0, 0, 0);

    const tick = (now: number) => {
      const dt = last < 0 ? 0 : Math.min(0.05, (now - last) / 1000);
      last = now;
      t += dt;

      if (revealAt < 0) {
        // O load real só segura a curva simulada, nunca a adianta
        const target = Math.min(curve(t), realLoad());
        progress = target >= 1 ? 1 : Math.min(target, progress + dt * 1.5);
        if (progress >= 1 && t >= loadS) revealAt = t;
      }

      const reveal = revealAt < 0 ? 0 : clamp((t - revealAt) / revealS);
      frame(t, progress, reveal);

      if (revealAt >= 0 && t - revealAt >= revealS) {
        root.style.transition = `opacity ${FADE_S}s ease`;
        root.style.opacity = "0";
        root.style.pointerEvents = "none";
        timeout = window.setTimeout(() => {
          root.style.visibility = "hidden";
        }, FADE_S * 1000);
        onComplete?.();
        return;
      }
      raf = requestAnimationFrame(tick);
    };
    raf = requestAnimationFrame(tick);

    return () => {
      cancelAnimationFrame(raf);
      clearTimeout(timeout);
      ro.disconnect();
    };
  });
</script>

<div bind:this={root} class="tiles-root">
  {#each Array(count) as _, i (`${cols}x${rows}-${i}`)}
    <div
      bind:this={tileEls[i]}
      class="tile"
      style="left:{((i % cols) * 100) / cols}%;top:{(Math.floor(i / cols) *
        100) /
        rows}%;width:calc({100 / cols}% + 1px);height:calc({100 /
        rows}% + 1px);background:{accent};"
    >
      <!-- sobreposição de 1px evita frestas entre blocos fechados -->
      <div
        bind:this={paperEls[i]}
        class="paper"
        style="background:{background};"
      ></div>
    </div>
  {/each}

  <!-- mix-blend-mode: difference contra os blocos (a raiz isola), lê-se em ambos os estados -->
  <div
    bind:this={countEl}
    class="count"
    aria-hidden="true"
    style="font-family:{fontFamily};font-weight:{fontWeight};font-style:{fontStyle};letter-spacing:{letterSpacing};line-height:{lineHeight};"
  >
    <span class="inline">
      <span bind:this={numEl}></span>
      <span class="pct">%</span>
    </span>
  </div>
</div>

<style>
  .tiles-root {
    position: fixed;
    inset: 0;
    z-index: 9999;
    overflow: hidden;
    container-type: size;
    isolation: isolate;
  }
  .tile {
    position: absolute;
  }
  .paper {
    position: absolute;
    inset: 0;
  }
  .count {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    mix-blend-mode: difference;
    color: #fff;
    pointer-events: none;
    white-space: nowrap;
    font-size: min(30cqh, 40cqw);
    font-variant-numeric: tabular-nums;
    opacity: 0;
  }
  .inline {
    display: inline-flex;
    align-items: flex-start;
  }
  .pct {
    font-size: 0.3em;
    font-style: italic;
    margin-top: 0.9em;
    margin-left: 0.08em;
  }
</style>

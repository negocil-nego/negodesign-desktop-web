<script lang="ts">
  import { onMount } from "svelte";

  type PlatformId = "windows" | "macos" | "linux";

  interface PlatformInfo {
    id: PlatformId;
    name: string;
    meta: string;
  }

  interface NavigatorUAData {
    platform?: string;
  }

  const platforms: Record<PlatformId, PlatformInfo> = {
    windows: { id: "windows", name: "Windows", meta: ".msi / .exe" },
    macos: { id: "macos", name: "macOS", meta: "Apple & Intel" },
    linux: { id: "linux", name: "Linux", meta: ".AppImage / .deb" },
  };

  function detectOS(): PlatformId | null {
    if (typeof navigator === "undefined") return null;

    const uaData = (
      navigator as Navigator & { userAgentData?: NavigatorUAData }
    ).userAgentData;
    const raw =
      `${uaData?.platform ?? ""} ${navigator.platform ?? ""} ${navigator.userAgent ?? ""}`.toLowerCase();

    if (
      raw.includes("android") ||
      raw.includes("iphone") ||
      raw.includes("ipad") ||
      raw.includes("ipod")
    ) {
      return null;
    }
    if (raw.includes("mac") || raw.includes("darwin")) return "macos";
    if (raw.includes("win")) return "windows";
    if (raw.includes("linux") || raw.includes("x11")) return "linux";
    return null;
  }

  let detectedOs = $state<PlatformId | null>(null);
  const activePlatform = $derived(platforms[detectedOs ?? "windows"]);

  onMount(() => {
    detectedOs = detectOS();
  });
</script>

<section
  class="min-h-screen w-full mx-auto px-margin md:px-margin-desktop pt-24 pb-12 md:pb-16 flex flex-col justify-end"
  id="top"
>
  <div class="flex flex-col sm:flex-row sm:items-end justify-between gap-6">
    <!-- Texto simples à esquerda (footer do background) -->
    <div>
      <p
        class="text-2xl sm:text-4xl md:text-5xl font-semibold text-white/60 tracking-tight leading-tight"
      >
        Desenvolva mais rápido com
      </p>
      <h1
        class="text-3xl sm:text-5xl md:text-6xl font-extrabold text-white tracking-tight leading-tight"
      >
        NegoDesign
      </h1>
    </div>

    <!-- Botão de download à direita (sistema detectado) -->
    <a
      href="#desktop-preview-section"
      title={`Baixar NegoDesign para ${activePlatform.name} (${activePlatform.meta})`}
      class="inline-flex items-center justify-center gap-3 px-7 py-4 rounded-lg bg-secondary hover:bg-secondary-fixed text-on-secondary-fixed font-semibold text-base transition-all shadow-[0_8px_32px_rgba(76,215,246,0.28)] shrink-0"
    >
      {#if activePlatform.id === "windows"}
        <svg
          class="w-5 h-5 shrink-0"
          viewBox="0 0 24 24"
          fill="currentColor"
          aria-hidden="true"
        >
          <path
            d="M3 5.5 10.5 4.4v7.1H3V5.5Zm8.5-1.3L21 2.8v8.7h-9.5V4.2ZM3 12.5h7.5v7.1L3 18.5v-6Zm8.5 0H21v8.7l-9.5-1.4v-7.3Z"
          />
        </svg>
      {:else if activePlatform.id === "macos"}
        <svg
          class="w-5 h-5 shrink-0"
          viewBox="0 0 24 24"
          fill="currentColor"
          aria-hidden="true"
        >
          <path
            d="M16.365 1.43c0 1.14-.493 2.27-1.177 3.08-.744.9-1.99 1.57-2.987 1.57-.12 0-.23-.02-.3-.03-.01-.06-.04-.22-.04-.39 0-1.15.572-2.27 1.206-2.98.804-.94 2.142-1.64 3.248-1.68.03.13.05.28.05.43Zm4.565 15.71c-.03.07-.463 1.58-1.518 3.12-.945 1.34-1.94 2.71-3.43 2.71-1.517 0-1.9-.88-3.63-.88-1.698 0-2.302.91-3.67.91-1.377 0-2.332-1.26-3.428-2.8-1.287-1.82-2.323-4.63-2.323-7.28 0-4.28 2.797-6.55 5.552-6.55 1.448 0 2.675.95 3.6.95.865 0 2.222-1.01 3.902-1.01.613 0 2.886.06 4.374 2.19-.13.09-2.383 1.37-2.383 4.19 0 3.26 2.854 4.42 2.955 4.45Z"
          />
        </svg>
      {:else}
        <svg
          class="w-5 h-5 shrink-0"
          viewBox="0 0 24 24"
          fill="currentColor"
          aria-hidden="true"
        >
          <path
            d="M12 2a4 4 0 0 0-4 4v2.1c0 .5-.2 1-.5 1.4L5.6 12c-.9 1.2-1.1 2.8-.6 4.2l.4 1.1c.3.8 1.1 1.4 2 1.4h.6c.4.8 1.2 1.3 2.1 1.3h3.8c.9 0 1.7-.5 2.1-1.3h.6c.9 0 1.7-.6 2-1.4l.4-1.1c.5-1.4.3-3-.6-4.2l-1.9-2.5c-.3-.4-.5-.9-.5-1.4V6a4 4 0 0 0-4-4Zm-1.5 4.5a.8.8 0 1 1 0 1.6.8.8 0 0 1 0-1.6Zm3 0a.8.8 0 1 1 0 1.6.8.8 0 0 1 0-1.6ZM12 9.5c.8 0 1.5.4 1.5.9s-.7.9-1.5.9-1.5-.4-1.5-.9.7-.9 1.5-.9Z"
          />
        </svg>
      {/if}

      <span>
        {#if detectedOs}
          Download para {activePlatform.name}
        {:else}
          Download NegoDesign
        {/if}
      </span>
    </a>
  </div>
</section>

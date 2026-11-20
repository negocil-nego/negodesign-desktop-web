<script lang="ts">
  const items = [
    {
      name: "Windows",
      meta: ".msi / .exe",
      icon: "https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/windows-11.png",
    },
    {
      name: "Linux",
      meta: ".AppImage / .deb",
      icon: "https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/linux.png",
    },
    {
      name: "macOS",
      meta: "Apple & Intel",
      icon: "https://dashboardicons.com/api/icons/external/simpleicons/macos/dark.png",
    },
  ];
</script>

<section
  class="mx-auto px-margin md:px-margin-desktop py-space-3xl grid grid-cols-1 md:grid-cols-2 gap-10 items-center"
  id="top"
>
  <!-- Main Headline & Subtitle -->
  <div>
    <div class="font-display-hero text-display-hero text-on-surface mb-4">
      Programa para te ajudar <span class="text-primary"
        >as no desenvolvimento</span
      >
    </div>
    <p class="text-body-lg text-on-surface-variant">
      Acelere a criação das suas aplicações gerando interfaces em Tailwind CSS e
      integrando contratos OpenAPI para Svelte, React, Vue e Flutter.
    </p>
  </div>

  <!-- Platform Downloads -->
  <div>
    <div class="text-label-caps uppercase text-on-surface-variant mb-3">
      Baixa para sua plataforma
    </div>
    <div class="grid grid-cols-2 gap-3">
      {#each items as item (item.name)}
        <a
          href="#desktop-preview-section"
          class="bg-surface-container hover:bg-surface-container-high border border-outline-variant p-4 rounded-xl flex items-center gap-3 transition-colors"
        >
          <img src={item.icon} alt={item.name} class="w-8 h-8 object-contain" />
          <div>
            <div class="text-title-md text-on-surface">{item.name}</div>
            <div class="text-code-sm text-outline">{item.meta}</div>
          </div>
        </a>
      {/each}
    </div>
  </div>
</section>

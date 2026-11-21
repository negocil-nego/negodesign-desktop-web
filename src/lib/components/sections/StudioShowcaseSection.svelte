<script lang="ts">
  import DownloadButton from "../buttons/DownloadButton.svelte";
  import DesktopStudioSimulator from "./DesktopStudioSimulator.svelte";

  const actionLabels: Record<string, string> = {
    new: "Novo Projecto",
    templates: "Templates",
    gallery: "Galeria",
    "new-frontend": "Novo Frontend",
    openapi: "Gerar via OpenAPI",
    "open-folder": "Abrir Pasta",
    "toggle-theme": "Tema Studio",
    docs: "Documentação",
    help: "Ajuda",
    github: "GitHub",
  };

  let simulatorStatus = $state("Pronto");
  let showOnStart = $state(true);

  function handleSimulatorSelect({ id }: { id: string }) {
    simulatorStatus = actionLabels[id] ?? id;
  }

  function handleToggleStartup(checked: boolean) {
    simulatorStatus = checked ? "Início rápido ativo" : "Início rápido oculto";
  }
</script>

<section
  class="relative w-full min-h-screen flex-1 flex flex-col items-center pt-24 sm:pt-28 pb-0 px-3 sm:px-6 lg:px-8 overflow-hidden"
  id="top"
>
  <!-- Halo radial ambiente atrás do cabeçalho e topo da janela -->
  <div
    aria-hidden="true"
    class="pointer-events-none absolute top-24 left-1/2 -translate-x-1/2 w-225 max-w-[110vw] h-130 rounded-full bg-radial from-[#4c6af5]/30 via-[#4c6af5]/10 to-transparent blur-2xl"
  ></div>

  <!-- Cabeçalho centralizado -->
  <div class="relative z-10 w-full max-w-6xl mx-auto text-center shrink-0">
    <h1
      class="text-3xl sm:text-5xl lg:text-6xl font-bold tracking-tight leading-[1.15] bg-linear-to-b from-white via-white to-white/75 bg-clip-text text-transparent"
    >
      <span class="block md:whitespace-nowrap">Dê à sua grande ideia o</span>
      <span class="block md:whitespace-nowrap">design que ela merece</span>
    </h1>

    <p
      class="mt-5 max-w-2xl mx-auto text-sm sm:text-base text-white/65 leading-relaxed"
    >
      Gere interfaces em Tailwind CSS e integre contratos OpenAPI para Svelte,
      React, Vue e Flutter com consistência nativa.
    </p>

    <!-- Botões de ação -->
    <div class="mt-7 flex flex-wrap items-center justify-center gap-3">
      <DownloadButton
        variant="light"
        href="#studio-simulator"
        prefixLabel="Baixar para"
        fallbackLabel="Começar agora"
      />

      <a
        href="https://github.com"
        target="_blank"
        rel="noreferrer"
        class="inline-flex items-center justify-center gap-2 px-5 py-2.5 rounded-lg bg-white/10 hover:bg-white/15 border border-white/15 text-white font-medium text-sm backdrop-blur-md transition-colors"
      >
        <svg
          class="size-4 shrink-0"
          viewBox="0 0 24 24"
          fill="currentColor"
          aria-hidden="true"
        >
          <path
            fill-rule="evenodd"
            clip-rule="evenodd"
            d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0 1 12 6.844a9.59 9.59 0 0 1 2.504.337c1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.02 10.02 0 0 0 22 12.017C22 6.484 17.522 2 12 2Z"
          />
        </svg>
        <span>GitHub</span>
      </a>
    </div>
  </div>

  <!-- Janela do DesktopStudioSimulator em destaque -->
  <div
    id="studio-simulator"
    class="relative z-10 mt-8 sm:mt-10 w-full flex-1 flex flex-col min-h-0"
  >
    <div
      class="relative w-full flex-1 flex flex-col min-h-0 rounded-t-xl border border-b-0 border-white/15 bg-[#131316] shadow-[0_-8px_70px_rgba(76,106,245,0.22),0_28px_80px_rgba(0,0,0,0.85)] overflow-hidden"
    >
      <DesktopStudioSimulator
        status={simulatorStatus}
        bind:showOnStart
        onSelect={handleSimulatorSelect}
        onToggleStartup={handleToggleStartup}
      />
    </div>
  </div>
</section>

<script lang="ts">
  import DownloadButton from "../buttons/DownloadButton.svelte";

  const navLinks = [
    { label: "Visão Geral", path: "visao-geral" },
    { label: "Documentação", path: "documentacao" },
    { label: "Blog", path: "blog" },
    { label: "Changelog", path: "changelog" },
  ];
</script>

<header
  class="fixed top-0 left-0 w-full z-50 shadow-[0_1px_8px_rgba(0,0,0,0.3)]"
>
  <div
    class="w-full mx-auto px-margin md:px-margin-tablet lg:px-margin-desktop flex items-center justify-between gap-space-lg"
  >
    <div
      class="flex items-center justify-between gap-space-lg bg-white rounded-full p-3"
    >
      <a
        class="flex items-center gap-space-sm"
        data-path="visao-geral"
        href="#top"
      >
        <img
          alt="NegoDesign Logo"
          class="h-8 w-auto object-contain"
          src="/desktop/icon.png"
        />
        <span
          class="font-headline-sm text-headline-sm tracking-tight font-bold hidden sm:inline-block text-black"
        >
          NegoDesign
        </span>
      </a>
      <nav
        class="hidden xl:flex items-center gap-space-xs"
        data-active-classes="bg-surface-container-high text-primary font-title-md text-title-md rounded-lg"
      >
        {#each navLinks as link (link.path)}
          <a
            class="font-body-md text-black hover:bg-gray-400 transition-colors px-space-sm py-space-xs rounded-lg"
            data-path={link.path}
            href="#{link.path}"
          >
            {link.label}
          </a>
        {/each}
      </nav>
    </div>
    <div>
      <DownloadButton />
    </div>
  </div>
</header>

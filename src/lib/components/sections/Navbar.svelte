<script lang="ts">
  const navLinks = [
    { label: "Visão Geral", path: "visao-geral" },
    { label: "Documentação", path: "documentacao" },
  ];
</script>

<header class="top-0 left-0 w-full z-50 shadow-[0_1px_8px_rgba(0,0,0,0.3)]">
  <div
    class="h-16 w-full mx-auto px-margin md:px-margin-tablet lg:px-margin-desktop flex items-center justify-between gap-space-lg"
  >
    <div class="flex items-center justify-between gap-space-lg">
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
          class="font-headline-sm text-headline-sm text-on-surface tracking-tight font-bold hidden sm:inline-block"
          >NegoDesign</span
        >
      </a>
    </div>
    <nav
      class="hidden xl:flex items-center gap-space-xs"
      data-active-classes="bg-surface-container-high text-primary font-title-md text-title-md rounded-lg"
    >
      {#each navLinks as link (link.path)}
        <a
          class="font-body-md text-body-md text-on-surface-variant hover:text-on-surface hover:bg-surface-container transition-colors px-space-sm py-space-xs rounded-lg"
          data-path={link.path}
          href="#{link.path}"
        >
          {link.label}
        </a>
      {/each}
    </nav>
  </div>
</header>

<script lang="ts">
  import {
    Header,
    HeroSection,
    DesktopStudioSimulator,
    CorePillarsSection,
    FinalCtaBanner,
    Footer,
  } from "#lib";
</script>

<svelte:head>
  <title
    >NegoDesign Desktop - O Ecossistema UI Multiplataforma e Conversão OpenAPI</title
  >
</svelte:head>

<Header />

<main class="w-full pt-16 bg-surface min-h-[calc(100vh-16rem)]">
  <div class="flex flex-col w-full md:max-w-4/5 mx-auto">
    <div class="relative w-full overflow-hidden">
      <HeroSection />
      <DesktopStudioSimulator />
      <CorePillarsSection />
      <FinalCtaBanner />
    </div>
  </div>
</main>

<Footer />

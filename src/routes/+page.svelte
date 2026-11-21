<script lang="ts">
  import {
    Loader,
    Background,
    Navbar,
    HeroSection,
    DesktopStudioSimulator,
    Footer,
  } from "#lib";

  let loaded = $state(false);
</script>

<svelte:head>
  <title>NegoDesign</title>
</svelte:head>

<Loader onComplete={() => (loaded = true)} />

<div class={!loaded ? "h-screen overflow-hidden" : ""} inert={!loaded}>
  <Navbar />
  <Background>
    <HeroSection />
  </Background>
  <DesktopStudioSimulator />
  <Footer />
</div>

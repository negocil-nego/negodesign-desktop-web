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

<Loader accent="#4c6af5" onComplete={() => (loaded = true)} />

<div class={!loaded ? "h-screen overflow-hidden" : ""} inert={!loaded}>
  <Navbar />
  <Background squareColor="#4c6af5">
    <HeroSection />
  </Background>
  <DesktopStudioSimulator />
  <Footer />
</div>

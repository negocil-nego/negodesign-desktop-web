<script lang="ts">
  import {
    Loader,
    Background,
    Navbar,
    StudioShowcaseSection,
    Footer,
  } from "#lib";

  let loaded = $state(false);
</script>

<svelte:head>
  <title>NegoDesign</title>
</svelte:head>

<!-- <Loader accent="#4c6af5" onComplete={() => (loaded = true)} /> -->

<!-- <div class={!loaded ? "h-screen overflow-hidden" : ""} inert={!loaded}> -->
<div>
  <Navbar />
  <Background squareColor="#4c6af5">
    <StudioShowcaseSection />
  </Background>
  <Footer />
</div>

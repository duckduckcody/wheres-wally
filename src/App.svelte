<script lang="ts">
  import { onMount } from "svelte";
  import Building from "./icons/building.svelte";
  import Pinch from "./icons/pinch.svelte";
  import Reset from "./icons/reset.svelte";
  import Panzoom from "@panzoom/panzoom";

  var wally = undefined;

  onMount(async () => {
    const elem = document.getElementById("wheres-wally");
    wally = Panzoom(elem, {
      maxScale: 10,
      minScale: 1,
      contain: "outside",
      touchAction: "",
      panOnlyWhenZoomed: true,
    });

    const parent = elem.parentElement;
    parent.addEventListener("wheel", wally.zoomWithWheel);
  });

  const handleZoomIn = () => wally.zoomIn();
  const handleZoomOut = () => wally.zoomOut();
  const handleZoomReset = () => wally.reset();
</script>

<div class="container" id="wheres-wally">
  <h1>
    WHERE'S<br />
    LOVERIDGE
  </h1>

  <Building class="building" />

  <p>
    This is a where’s wally inspired illustration. The characters in here are
    everyone from Hatchd and Adapptor plus some of our close clients.
  </p>

  <div class="pinchTipContainer">
    <Pinch />
    <p>Pinch to zoom in and out</p>
  </div>
</div>

<div class="controls-container">
  <button class="button icon-button" on:click={handleZoomIn}>+</button>
  <button class="button icon-button" on:click={handleZoomOut}>-</button>
  <button class="button reset-button" on:click={handleZoomReset}
    ><Reset /> RESET</button
  >
</div>

<style>
  :global(html) {
    box-sizing: border-box;
  }

  *,
  *:before,
  *:after {
    box-sizing: inherit;
  }

  h1,
  p,
  :global(body) {
    margin: 0;
    padding: 0;
  }

  .container {
    display: grid;
    align-content: center;
    justify-items: center;
    gap: 54px;
    position: relative;
    height: 100vh;
    width: 100vw;
    grid-template-rows: auto 1fr auto;
    padding: 24px;
    cursor: grab;
  }

  h1 {
    font-family: "Montserrat", sans-serif;
    font-weight: bold;
    text-align: center;
    letter-spacing: 0.8em;
    font-size: 48px;
    line-height: 200%;
    color: #000000;
  }

  p {
    font-family: "Montserrat", sans-serif;
    font-weight: normal;
    text-align: center;
    font-size: 18px;
    line-height: 150%;
    letter-spacing: 1.9px;
    color: #000000;
    max-width: 466px;
  }

  .pinchTipContainer {
    display: none;
  }

  @media only screen and (max-width: 811px) {
    .container {
      padding: 24px 0 0;
      gap: 16px;
      align-content: start;
      grid-template-rows: auto auto auto auto;
    }

    h1 {
      font-size: 24px;
      letter-spacing: 0.4em;
      line-height: 200%;
    }

    p {
      font-size: 12px;
      line-height: 150%;
      padding: 16px;
    }

    .pinchTipContainer {
      display: flex;
      flex-flow: column nowrap;
      align-items: center;
      gap: 8px;
    }
  }

  :global(.building) {
    height: 100%;
    width: auto;
    max-width: 100%;
    background: linear-gradient(0deg, #cae2ff 50%, rgba(204, 227, 255, 0) 100%);
  }

  @media only screen and (max-width: 811px) {
    :global(.building) {
      height: auto;
      width: 100%;
      max-width: 100%;
    }
  }

  .controls-container {
    position: absolute;
    right: 28px;
    bottom: 32px;
    display: flex;
    flex-flow: column nowrap;
    align-items: end;
  }

  .button {
    width: 114px;
    height: 40px;
    border: 1px solid black;
    border-radius: 2.5px;
    background: #f4f4f4;
    filter: drop-shadow(0px 6.31783px 12.6357px rgba(170, 213, 255, 0.5));
    margin: 0;
    margin-top: 24px;
    cursor: pointer;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    justify-content: space-around;

    font-family: "Montserrat", sans-serif;
    font-weight: bold;
    font-size: 11px;
    line-height: 150%;
    letter-spacing: 1.9px;
    color: #312f32;
  }

  .icon-button {
    width: 40px;
    margin-top: 18px;
  }

  .reset-button {
    padding: 0 12px;
  }

  @media only screen and (max-width: 811px) {
    .icon-button {
      display: none;
    }

    .controls-container {
      bottom: 24px;
      right: calc(50% - (114px / 2));
    }
  }
</style>

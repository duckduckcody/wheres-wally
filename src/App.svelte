<script lang="ts">
  import { onMount } from 'svelte';
  import Building from './icons/building.svelte';
  import Pinch from './icons/pinch.svelte';
  import Reset from './icons/reset.svelte';
  import svgPanZoom from 'svg-pan-zoom';
  import Panzoom from '@panzoom/panzoom'
  import Hammer from 'hammerjs';

  var wally = undefined;
  var eventsHandler;

  eventsHandler = {
    haltEventListeners: [
      'touchstart',
      'touchend',
      'touchmove',
      'touchleave',
      'touchcancel',
    ],
    init: function (options) {
      var instance = options.instance,
        initialScale = 1,
        pannedX = 0,
        pannedY = 0;

      // Init Hammer
      // Listen only for pointer and touch events
      this.hammer = new Hammer(options.svgElement, {
        inputClass: Hammer.PointerEventInput
          ? Hammer.PointerEventInput
          : Hammer.TouchInput,
      });

      // Handle pan
      this.hammer.on('panstart panmove', function (ev) {
        // On pan start reset panned variables
        if (ev.type === 'panstart') {
          pannedX = 0;
          pannedY = 0;
        }

        // Pan only the difference
        instance.panBy({ x: ev.deltaX - pannedX, y: ev.deltaY - pannedY });
        pannedX = ev.deltaX;
        pannedY = ev.deltaY;
      });

      // Enable pinch
      this.hammer.get('pinch').set({ enable: true });

      // Handle pinch
      this.hammer.on('pinchstart pinchmove', function (ev) {
        // On pinch start remember initial zoom
        if (ev.type === 'pinchstart') {
          initialScale = instance.getZoom();
          instance.zoomAtPoint(initialScale * ev.scale, {
            x: ev.center.x,
            y: ev.center.y,
          });
        }

        instance.zoomAtPoint(initialScale * ev.scale, {
          x: ev.center.x,
          y: ev.center.y,
        });
      });

      // Prevent moving the page on some devices when panning over SVG
      options.svgElement.addEventListener('touchmove', function (e) {
        e.preventDefault();
      });
    },

    destroy: function () {
      this.hammer.destroy();
    },
  };

  onMount(async () => {
    // wally = svgPanZoom('#wheres-wally', {
    //   zoomEnabled: true,
    //   controlIconsEnabled: false,
    //   fit: true,
    //   center: true,
    //   contain: true,
    //   zoomScaleSensitivity: 0.3,
    //   maxZoom: 20,
    //   minZoom: 1,
    //   customEventsHandler: eventsHandler,
    //   refreshRate: 144,
    // });

    const elem = document.getElementById('wheres-wally');
    wally = Panzoom(elem, {
      maxScale: 5
    });
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

  <div class='pinchTipContainer'>
    <Pinch />
    <p>Pinch to zoom in and out</p>
  </div>
</div>

<div class="controls-container">
  <button class="button icon-button" on:click={handleZoomIn}>+</button>
  <button class="button icon-button" on:click={handleZoomOut}>-</button>
  <button class="button reset-button" on:click={handleZoomReset}><Reset /> RESET</button>
</div>

<style>
  html {
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
  }

  h1 {
    font-family: 'Montserrat', sans-serif;
    font-weight: bold;
    text-align: center;
    letter-spacing: 0.8em;
    font-size: 48px;
    line-height: 200%;
    color: #000000;
  }

  p {
    font-family: 'Montserrat', sans-serif;
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
      align-content: start;
      grid-template-rows: auto auto auto auto;
    }

    h1 {
      font-size: 32px;
      line-height: 200%;
    }

    p {
      font-size: 12px;
      line-height: 150%;
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
    cursor: grab;
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

    font-family: 'Montserrat', sans-serif;
    font-weight: bold;
    font-size: 11px;
    line-height: 150%;
    letter-spacing: 1.9px;
    color: #312F32;
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
      bottom: 48px;
      right: calc(50% - (114px / 2));
    }
  }
</style>

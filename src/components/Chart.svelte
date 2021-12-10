<script>
  import {
    easeCubicInOut,
    forceSimulation,
    forceX,
    forceY,
    forceCollide,
    select,
    selectAll
  } from "d3";
  import { base } from "$app/paths";
  import { browser } from "$app/env";
  import { onMount, tick } from "svelte";
  import viewport from "$stores/viewport.js";

  export let data;

  const path = `${base}/assets/images`;

  let chartEl;
  let width;
  let height;
  let nodes;
  let players;

  $: radius = Math.floor(Math.min(width, height) * 0.08);
  $: centerX = width / 2;
  $: centerY = height / 2;
  $: $viewport.width, runSim();

  const getImage = (d) => {
    return `url("${path}/${d.name.replace(" ", "_")}.jpg")`;
  };

  const ticked = () => {
    players
      .style("width", `${radius * 2}px`)
      .style("height", `${radius * 2}px`)
      .style("top", (d) => `${d.y - radius}px`)
      .style("left", (d) => `${d.x - radius}px`);
  };

  const sim = forceSimulation().on("tick", ticked).stop();

  const runSim = () => {
    if (!browser || !data.length) return;
    data.forEach((d) => {
      if (!d.x) {
        d.x = centerX;
        d.y = centerY;
      }
    });

    players = select(chartEl)
      .selectAll(".player")
      .data(data, (d) => d.name)
      .join(
        (enter) => {
          const p = enter.append("div").attr("class", "player new");
          p.style("opacity", 0);
          p.style("transform", "scale(0)");
          p.style("background-image", getImage);
          p.append("div").attr("class", "layer");
          p.append("p").text((d) => d.name);
          p.transition()
            .duration(500)
            .ease(easeCubicInOut)
            .delay(1000)
            .style("opacity", 1)
            .style("transform", "scale(1)");
          return p;
        },
        (update) => {
          update.classed("new", false);
          return update;
        },
        (exit) =>
          exit
            .transition()
            .duration(1000)
            .ease(easeCubicInOut)
            .style("opacity", 0)
            .style("transform", "scale(0)")
            .remove()
      );

    sim
      .nodes(data)
      .velocityDecay(0.95)
      // .alphaDecay(0.001)
      .force("x", forceX().x(centerX).strength(0.75))
      .force("y", forceY().y(centerY).strength(1))
      .force(
        "collide",
        forceCollide()
          .radius(radius * 1.1)
          .iterations(2)
      )
      .alpha(1)
      .restart();
  };

  $: if (browser) runSim(data);

  onMount(async () => {
    await tick();
    runSim();
  });
</script>

{#if browser}
  <div
    class="chart"
    bind:this={chartEl}
    bind:clientWidth={width}
    bind:clientHeight={height}
    style="height: {width}px;"
  />
{/if}

<style>
  .chart {
    width: 90%;
    margin: 0 auto;
    height: 100vh;
    max-height: 640px;
    position: relative;
  }

  :global(.player) {
    position: absolute;
    background: var(--color-yellow);
    border-radius: 50%;
    transform-origin: 50% 50%;
    cursor: pointer;
    border: 2px solid var(--color-blue);
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center center;
    overflow: hidden;
  }

  :global(.player .layer) {
    background: var(--color-yellow);
    position: absolute;
    width: 100%;
    height: 100%;
    opacity: 0.25;
    transition: all 250ms ease-in-out;
  }

  :global(.player:hover) {
    z-index: 1;
    transform: scale(2);
  }

  :global(.player:hover .layer) {
    opacity: 0.75;
  }

  :global(.player:hover p) {
    opacity: 1;
  }

  :global(.player p) {
    color: var(--color-blue);
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    line-height: 1;
    margin: 0;
    text-align: center;
    font-family: var(--display);
    letter-spacing: 0.05em;
    font-size: 1.25vw;
    opacity: 0;
    transition: all 250ms ease-in-out;
  }

  :global(.player.new) {
    box-shadow: 0 0 0.5vw 0.5vw var(--color-yellow);
  }

  @media only screen and (min-width: 1024px) {
    .chart {
      max-height: 100%;
    }
  }
</style>

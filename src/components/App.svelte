<script>
  import ButtonSet from "$components/helpers/ButtonSet.svelte";
  import Scrolly from "$components/helpers/Scrolly.svelte";
  import Intro from "$components/Intro.svelte";
  import Chart from "$components/Chart.svelte";
  import players from "$data/players.csv";

  const data = players.map((d) => ({
    ...d,
    d1: d.d1 === "true",
    d2: d.d2 === "true",
    d3: d.d3 === "true"
  }));

  let value = 0;
  $: current = `d${value + 1}`;

  $: filtered = data.filter((d) => d[current]);

  const movies = [
    {
      title: "D1: The Mighty Ducks",
      description:
        "Just a couple of kids from Minnesota who literally couldn't skate but ended up winning the league."
    },
    {
      title: "D2: The Mighty Ducks",
      description:
        "Just a couple of kids from Minnesota, most of who are good enough to represent team USA now?"
    },
    {
      title: "D3: The Mighty Ducks",
      description:
        "Just a couple of kids from Minnesota, plus the best kids in the country, who together won gold at the Junior Goodwill Games, and now struggle to beat a slightly older prep school team. But they lost Jesse."
    }
  ];
</script>

<!-- <audio preload autoplay loop src="assets/audio/theme.mp3" /> -->

<section>
  <article>
    <Intro />
    <Scrolly bind:value>
      {#each movies as { title, description }, i}
        <div class:active={value === i}>
          <h2>{title}</h2>
          <p>{description}</p>
        </div>
      {/each}
    </Scrolly>
    <div>By Russell.</div>
  </article>
  <figure>
    <Chart data={filtered} />
  </figure>
</section>

<style>
  section {
    display: flex;
  }

  article {
    width: 30em;
  }

  div {
    height: 75vh;
    opacity: 0.5;
    font-size: clamp(18px, 3vw, 24px);
    margin: 0 auto;
    padding: 1em;
  }

  h2 {
    font-size: 2em;
  }

  .active {
    opacity: 1;
  }

  figure {
    position: sticky;
    width: 100%;
    flex: 1;
    height: 100vh;
    top: 0;
  }
</style>

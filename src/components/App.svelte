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
  let toggleValue = "d1";

  $: current = toggleValue;
  $: if (!isNaN(value)) current = `d${value + 1}`;

  $: filtered = data.filter((d) => d[current]);

  const movies = [
    {
      title: "D1: The Mighty Ducks",
      description:
        "Just a couple of kids from Minnesota, who literally couldn't skate but ended up winning the league."
    },
    {
      title: "D2: The Mighty Ducks",
      description:
        "Just a couple of kids from Minnesota, most of who are good enough to represent team USA now?"
    },
    {
      title: "D3: The Mighty Ducks",
      description:
        "Just a couple of kids from Minnesota, plus the best kids in the country, who together won gold at the Junior Goodwill Games, and now struggle to beat a slightly older prep school team."
    }
  ];
</script>

<!-- <audio preload autoplay loop src="assets/audio/theme.mp3" /> -->

<section>
  <article>
    <Intro />

    <div class="toggle">
      <ButtonSet
        bind:value={toggleValue}
        options={[{ value: "d1" }, { value: "d2" }, { value: "d3" }]}
      />
    </div>

    <div class="scrolly">
      <Scrolly bind:value>
        {#each movies as { title, description }, i}
          <div class="text" class:active={value === i}>
            <h2>{title}</h2>
            <p>{description}</p>
          </div>
        {/each}
      </Scrolly>
    </div>
  </article>
  <figure>
    <Chart data={filtered} />
  </figure>
</section>

<style>
  section {
    /* display: flex; */
  }

  article {
    width: 90%;
    max-width: 640px;
    padding: 0 2em;
    margin: 0 auto;
  }

  .text {
    height: 75vh;
    opacity: 0.5;
    margin: 0 auto;
  }

  h2 {
    font-size: 2em;
  }

  .active {
    opacity: 1;
  }

  .toggle {
    text-align: center;
    font-family: var(--display);
    margin: 1em auto;
  }

  .scrolly {
    display: none;
  }

  @media only screen and (min-width: 1024px) {
    section {
      display: flex;
    }

    article {
      width: 30em;
    }

    figure {
      position: sticky;
      width: 100%;
      flex: 1;
      height: 100vh;
      top: 0;
    }

    .toggle {
      display: none;
    }

    .scrolly {
      display: block;
    }
  }
</style>

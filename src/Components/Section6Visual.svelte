<script>
  import Scrolly from "./Scrolly.svelte";
  import { fade } from "svelte/transition";
  import { scaleLinear } from "d3-scale";

  // which step the reader is on (set by the scroll component)
  let value;

  const PURPLE = "#7c5aed";

  // size of the map, in SVG units
  const W = 400;
  const H = 310;
  const M = 36;
  const x = scaleLinear().domain([0, 7]).range([M, W - 12]);
  const y = scaleLinear().domain([0, 7]).range([H - M, 12]);

  const points = [
    {
      text: "Dogs are allowed in the office.",
      short: "dogs",
      x: 1,
      y: 5,
      color: "#d1495b",
      dx: -14,
      dy: -12,
      anchor: "end",
    },
    {
      text: "Pets are welcome at work.",
      short: "pets",
      x: 2,
      y: 4,
      color: "#edae49",
      dx: 15,
      dy: 5,
      anchor: "start",
    },
    {
      text: "You can work remotely on Fridays.",
      short: "remote work",
      x: 6,
      y: 1,
      color: "#00798c",
      dx: 0,
      dy: -16,
      anchor: "middle",
    },
  ];

  const query = { text: "Can I bring my dog to work?", x: 1.4, y: 4.6 };

  const steps = [
    `<p>Take three sentences. Computers can't read meaning the way we do, but they're very good with numbers.</p>`,
    `<p>An embedding model turns each sentence into a list of numbers. Real ones have hundreds. Here we use just two, so we can draw them.</p>`,
    `<p>Treat the numbers as coordinates, and each sentence becomes a dot on a map. Similar meanings land close together. Different meanings land far apart.</p>`,
    `<p>A new question gets turned into numbers too. It lands right beside the two sentences about animals at work, so that's where the search looks.</p>`,
  ];
</script>

<section>
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each steps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="charts-container">
      <div class="panel">
        <div class="cards">
          {#each points as p}
            <div class="card" style="border-color: {p.color}">
              <span class="swatch" style="background: {p.color}"></span>
              <span class="card-text">{p.text}</span>
              {#if value >= 1}
                <span class="nums" in:fade={{ duration: 400 }}>
                  ({p.x}, {p.y})
                </span>
              {/if}
            </div>
          {/each}
          {#if value >= 3}
            <div class="card query" in:fade={{ duration: 400 }}>
              <span class="swatch" style="background: {PURPLE}"></span>
              <span class="card-text">{query.text}</span>
              <span class="nums">({query.x}, {query.y})</span>
            </div>
          {/if}
        </div>

        <svg viewBox="0 0 {W} {H}" class="map">
          <line
            x1={M}
            y1={H - M}
            x2={W - 12}
            y2={H - M}
            stroke="#999"
            stroke-width="2"
          />
          <line x1={M} y1={H - M} x2={M} y2="12" stroke="#999" stroke-width="2" />
          <text x={(W + M) / 2} y={H - 8} text-anchor="middle" class="axis">
            number 1
          </text>
          <text
            x="12"
            y={H / 2}
            transform="rotate(-90 12 {H / 2})"
            text-anchor="middle"
            class="axis"
          >
            number 2
          </text>

          {#if value >= 2}
            {#each points as p}
              <g in:fade={{ duration: 500 }}>
                <circle
                  cx={x(p.x)}
                  cy={y(p.y)}
                  r="9"
                  fill={p.color}
                  stroke="black"
                  stroke-width="2.5"
                />
                <text
                  x={x(p.x) + p.dx}
                  y={y(p.y) + p.dy}
                  text-anchor={p.anchor}
                  class="dot-label"
                >
                  {p.short}
                </text>
              </g>
            {/each}
          {/if}

          {#if value >= 3}
            <g in:fade={{ duration: 500 }}>
              <line
                x1={x(query.x)}
                y1={y(query.y)}
                x2={x(points[0].x)}
                y2={y(points[0].y)}
                stroke={PURPLE}
                stroke-width="2"
                stroke-dasharray="4 4"
              />
              <line
                x1={x(query.x)}
                y1={y(query.y)}
                x2={x(points[1].x)}
                y2={y(points[1].y)}
                stroke={PURPLE}
                stroke-width="2"
                stroke-dasharray="4 4"
              />
              <circle
                cx={x(query.x)}
                cy={y(query.y)}
                r="9"
                fill="white"
                stroke={PURPLE}
                stroke-width="4"
              />
              <text
                x={x(query.x) + 16}
                y={y(query.y) - 8}
                text-anchor="start"
                class="dot-label query-label"
              >
                your question
              </text>
            </g>
          {/if}
        </svg>
      </div>
    </div>
  </div>
</section>

<style>
  .spacer {
    height: 40vh;
  }

  .charts-container {
    position: sticky;
    top: 10%;
    width: 50%;
    height: 85vh;
    border: 3px solid black;
  }

  .panel {
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 1rem;
    padding: 1rem 1.5rem;
    text-align: left;
  }

  .cards {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .card {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    border: 3px solid black;
    background: white;
    padding: 0.4rem 0.7rem;
    font-size: 15px;
  }

  .card.query {
    border-color: #7c5aed;
    background: #e8e3ff;
  }

  .swatch {
    width: 14px;
    height: 14px;
    border: 2px solid black;
    flex-shrink: 0;
  }

  .card-text {
    flex: 1;
  }

  .nums {
    font-weight: bold;
    font-family: monospace;
  }

  .map {
    width: 100%;
    height: auto;
  }

  .axis {
    font-size: 12px;
    fill: #777;
  }

  .dot-label {
    font-size: 14px;
    font-weight: bold;
  }

  .query-label {
    fill: #7c5aed;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    display: flex;
  }

  .step {
    height: 110vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 18px;
    background: var(--bg);
    color: #ccc;
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    text-align: left;
    width: 75%;
    margin: auto;
    max-width: 500px;
    font-family: var(--font-main);
    line-height: 1.3;
    border: 5px solid var(--default);
  }

  .step.active .step-content {
    background: #f1f3f3ee;
    color: var(--squid-ink);
  }

  .steps-container {
    height: 100%;
    flex: 1 1 40%;
    z-index: 10;
  }

  @media screen and (max-width: 950px) {
    .section-container {
      flex-direction: column-reverse;
    }

    .steps-container {
      pointer-events: none;
    }

    .charts-container {
      top: 7.5%;
      width: 95%;
      margin: auto;
    }

    .step {
      height: 130vh;
    }

    .step-content {
      width: 95%;
      max-width: 768px;
      font-size: 17px;
      line-height: 1.6;
    }

    .spacer {
      height: 100vh;
    }
  }
</style>
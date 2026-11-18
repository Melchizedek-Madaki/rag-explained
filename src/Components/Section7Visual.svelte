<script>
  import Scrolly from "./Scrolly.svelte";
  import { scaleLinear } from "d3-scale";

  // which step the reader is on (set by the scroll component)
  let value;

  // size of the map, in SVG units
  const W = 400;
  const H = 230;
  const M = 36;
  const x = scaleLinear().domain([0, 7]).range([M, W - 12]);
  const y = scaleLinear().domain([0, 6]).range([H - M, 12]);

  const points = [
    { short: "dogs", x: 1, y: 5, color: "#d1495b", dx: -10, dy: -8, anchor: "end" },
    { short: "pets", x: 2, y: 4, color: "#edae49", dx: 12, dy: 2, anchor: "start" },
    { short: "remote", x: 6, y: 1, color: "#00798c", dx: 0, dy: -14, anchor: "middle" },
  ];

  const pairs = [
    {
      label: "Dogs vs pets",
      calc: "(1 × 2) + (5 × 4) = 22",
      score: 0.96,
      calcStep: 1,
      scoreStep: 2,
    },
    {
      label: "Dogs vs remote",
      calc: "(1 × 6) + (5 × 1) = 11",
      score: 0.35,
      calcStep: 3,
      scoreStep: 3,
    },
    {
      label: "Pets vs remote",
      calc: "(2 × 6) + (4 × 1) = 16",
      score: 0.59,
      calcStep: 3,
      scoreStep: 3,
    },
  ];

  // while one pair is being worked out, the third arrow fades back
  $: lit = value === 1 || value === 2 ? [0, 1] : [0, 1, 2];

  // the triangle at the tip of an arrow
  function head(px, py) {
    const ox = x(0);
    const oy = y(0);
    const tx = x(px);
    const ty = y(py);
    const a = Math.atan2(ty - oy, tx - ox);
    const s = 12;
    const p1 = [tx - s * Math.cos(a - 0.45), ty - s * Math.sin(a - 0.45)];
    const p2 = [tx - s * Math.cos(a + 0.45), ty - s * Math.sin(a + 0.45)];
    return `${tx},${ty} ${p1[0]},${p1[1]} ${p2[0]},${p2[1]}`;
  }

  const steps = [
    `<p>Each sentence is now an arrow starting from the same point and pointing to its numbers. <b>Dogs</b> and <b>pets</b> point almost the same way. <b>Remote work</b> points somewhere else.</p>`,
    `<p>To compare <b>dogs</b> and <b>pets</b>, multiply the numbers in matching positions, then add the results: (1 × 2) + (5 × 4) = 22.</p>`,
    `<p>Bigger numbers give bigger scores just for being big, so we adjust for size. The result is a score from 0 to 1: <b>0.96</b>, very similar.</p>`,
    `<p>The other two pairs get the same treatment. Dogs and pets score highest, just like on the map. Remote work is much less similar to either.</p>`,
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

          {#each points as p, i}
            <g class="arrow" style="opacity: {lit.includes(i) ? 1 : 0.2}">
              <line
                x1={x(0)}
                y1={y(0)}
                x2={x(p.x)}
                y2={y(p.y)}
                stroke={p.color}
                stroke-width="4"
                stroke-linecap="round"
              />
              <polygon points={head(p.x, p.y)} fill={p.color} />
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
        </svg>

        <div class="pairs">
          {#each pairs as pr}
            <div class="pair">
              <div class="pair-head">
                <b>{pr.label}</b>
                <span class="calc" class:show={value >= pr.calcStep}>
                  {pr.calc}
                </span>
              </div>
              <div class="bar-row">
                <div class="track">
                  <div
                    class="fill"
                    style="width: {value >= pr.scoreStep ? pr.score * 100 : 0}%"
                  ></div>
                </div>
                <span class="score" class:show={value >= pr.scoreStep}>
                  {pr.score.toFixed(2)}
                </span>
              </div>
            </div>
          {/each}
        </div>
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

  .map {
    width: 100%;
    height: auto;
  }

  .arrow {
    transition: opacity 500ms ease;
  }

  .dot-label {
    font-size: 13px;
    font-weight: bold;
  }

  .pairs {
    display: flex;
    flex-direction: column;
    gap: 0.8rem;
  }

  .pair-head {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    font-size: 15px;
    margin-bottom: 0.25rem;
  }

  .calc {
    font-family: monospace;
    font-size: 14px;
    opacity: 0;
    transition: opacity 500ms ease;
  }

  .calc.show {
    opacity: 1;
  }

  .bar-row {
    display: flex;
    align-items: center;
    gap: 0.6rem;
  }

  .track {
    flex: 1;
    height: 14px;
    border: 2px solid black;
    background: white;
  }

  .fill {
    height: 100%;
    background: var(--primary);
    transition: width 700ms ease;
  }

  .score {
    font-weight: bold;
    font-family: monospace;
    min-width: 2.5rem;
    opacity: 0;
    transition: opacity 500ms ease;
  }

  .score.show {
    opacity: 1;
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
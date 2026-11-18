<script>
  import Scrolly from "./Scrolly.svelte";

  // which step the reader is on (set by the scroll component)
  let value;

  const PURPLE = "#7c5aed";

  // ---- the data: 80 dots in 4 neighborhoods (the same every time) ----
  function mulberry32(a) {
    return function () {
      a |= 0;
      a = (a + 0x6d2b79f5) | 0;
      let t = Math.imul(a ^ (a >>> 15), 1 | a);
      t = (t + Math.imul(t ^ (t >>> 7), 61 | t)) ^ t;
      return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
    };
  }
  const rand = mulberry32(7);

  const clusters = [
    { name: "animals", cx: 100, cy: 85, color: "#d1495b" },
    { name: "health", cx: 300, cy: 85, color: "#00798c" },
    { name: "remote work", cx: 100, cy: 215, color: "#edae49" },
    { name: "travel", cx: 300, cy: 215, color: "#6a994e" },
  ];

  const dots = [];
  clusters.forEach((c, ci) => {
    for (let i = 0; i < 20; i++) {
      const a = rand() * 2 * Math.PI;
      const r = Math.sqrt(rand()) * 40;
      dots.push({
        id: dots.length,
        c: ci,
        x: c.cx + r * Math.cos(a),
        y: c.cy + r * Math.sin(a),
      });
    }
  });

  const query = { x: 122, y: 100 };
  const dist = (a, b) => Math.hypot(a.x - b.x, a.y - b.y);

  // ---- IVF: search only the two nearest neighborhoods ----
  const litClusters = clusters
    .map((c, i) => ({ i, d: dist({ x: c.cx, y: c.cy }, query) }))
    .sort((a, b) => a.d - b.d)
    .slice(0, 2)
    .map((o) => o.i);
  const ivfCount = dots.filter((d) => litClusters.includes(d.c)).length;

  // ---- HNSW: streets (nearest neighbors) + highways (between hubs) ----
  const hubs = clusters.map((c, ci) => {
    let best = null;
    let bd = 1e9;
    dots
      .filter((d) => d.c === ci)
      .forEach((d) => {
        const k = dist(d, { x: c.cx, y: c.cy });
        if (k < bd) {
          bd = k;
          best = d.id;
        }
      });
    return best;
  });

  const adj = dots.map(() => new Set());
  const streets = [];
  dots.forEach((d) => {
    dots
      .filter((o) => o.id !== d.id)
      .map((o) => ({ id: o.id, k: dist(d, o) }))
      .sort((a, b) => a.k - b.k)
      .slice(0, 3)
      .forEach((n) => {
        if (!adj[d.id].has(n.id)) {
          adj[d.id].add(n.id);
          adj[n.id].add(d.id);
          streets.push([d.id, n.id]);
        }
      });
  });

  const highways = [];
  for (let i = 0; i < hubs.length; i++) {
    for (let j = i + 1; j < hubs.length; j++) {
      adj[hubs[i]].add(hubs[j]);
      adj[hubs[j]].add(hubs[i]);
      highways.push([hubs[i], hubs[j]]);
    }
  }

  // the search starts far from the question and always moves to the
  // neighbor that is closest to it, until no neighbor is closer
  const entry = hubs[3];
  let cur = entry;
  const visitedSet = new Set([cur]);
  const path = [cur];
  for (;;) {
    let best = cur;
    let bd = dist(dots[cur], query);
    adj[cur].forEach((n) => {
      visitedSet.add(n);
      const k = dist(dots[n], query);
      if (k < bd) {
        bd = k;
        best = n;
      }
    });
    if (best === cur) break;
    cur = best;
    path.push(cur);
  }
  const pathSet = new Set(path);
  const pathEdges = path.slice(0, -1).map((id, i) => [id, path[i + 1]]);
  const found = path[path.length - 1];

  // ---- LSH: three lines, each dot gets a 3-digit code ----
  const lines = [
    { x: 200, y: 140, a: 5 },
    { x: 200, y: 150, a: 95 },
    { x: 150, y: 100, a: 130 },
  ].map((l) => {
    const rad = (l.a * Math.PI) / 180;
    const nx = Math.cos(rad);
    const ny = Math.sin(rad);
    return {
      x1: l.x + 600 * -ny,
      y1: l.y + 600 * nx,
      x2: l.x - 600 * -ny,
      y2: l.y - 600 * nx,
      x: l.x,
      y: l.y,
      nx,
      ny,
    };
  });
  const code = (p) =>
    lines.map((l) => ((p.x - l.x) * l.nx + (p.y - l.y) * l.ny > 0 ? 1 : 0));
  const qcode = code(query).join("-");
  const bucketSet = new Set(
    dots.filter((d) => code(d).join("-") === qcode).map((d) => d.id)
  );

  // ---- what to draw at each step ----
  const names = ["Checking every dot", "IVF", "HNSW", "LSH"];
  $: step = value > 0 ? value : 0;
  $: checked =
    step === 1
      ? ivfCount
      : step === 2
      ? visitedSet.size
      : step === 3
      ? bucketSet.size
      : dots.length;

  function fillFor(d, v) {
    if (v === 1) return clusters[d.c].color;
    if (v === 2) return pathSet.has(d.id) ? PURPLE : "#bbb";
    if (v === 3) return bucketSet.has(d.id) ? PURPLE : "#bbb";
    return "#bbb";
  }
  function opacityFor(d, v) {
    if (v === 1) return litClusters.includes(d.c) ? 1 : 0.2;
    if (v === 3) return bucketSet.has(d.id) ? 1 : 0.5;
    return 1;
  }
  function strokeFor(d, v) {
    return v === 2 && visitedSet.has(d.id) ? PURPLE : "#555";
  }
  function widthFor(d, v) {
    return v === 2 && visitedSet.has(d.id) ? 2.5 : 1;
  }

  const steps = [
    `<p>A real company has thousands of pages, and big systems have millions. Here each dot is one chunk, and the purple ring is your question. Comparing the question with every dot means checking all of them.</p>`,
    `<p><b>IVF</b> sorts the dots into neighborhoods ahead of time. The question is compared with the neighborhood centers first, and only the two nearest neighborhoods are searched. Half of the dots are skipped.</p>`,
    `<p><b>HNSW</b> links every dot to its closest neighbors (streets), plus a few long links between neighborhoods (highways). The search starts far away, takes a highway toward the question, then follows streets until no neighbor is closer.</p>`,
    `<p><b>LSH</b> draws random lines across the map. Each dot gets a code from the side of each line it falls on, and the question gets a code too. Only dots with the same code are checked. Two dots from the same neighborhood landed on the other side of a line and were skipped, which is why the search is approximate.</p>`,
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
        <svg viewBox="0 0 400 300" class="map">
          <!-- lines drawn under the dots -->
          {#if step === 0}
            {#each dots as d}
              <line
                x1={query.x}
                y1={query.y}
                x2={d.x}
                y2={d.y}
                stroke={PURPLE}
                stroke-opacity="0.18"
              />
            {/each}
          {/if}

          {#if step === 1}
            {#each dots as d}
              {#if litClusters.includes(d.c)}
                <line
                  x1={query.x}
                  y1={query.y}
                  x2={d.x}
                  y2={d.y}
                  stroke={PURPLE}
                  stroke-opacity="0.25"
                />
              {/if}
            {/each}
          {/if}

          {#if step === 2}
            {#each streets as e}
              <line
                x1={dots[e[0]].x}
                y1={dots[e[0]].y}
                x2={dots[e[1]].x}
                y2={dots[e[1]].y}
                stroke="#ccc"
                stroke-width="1"
              />
            {/each}
            {#each highways as e}
              <line
                x1={dots[e[0]].x}
                y1={dots[e[0]].y}
                x2={dots[e[1]].x}
                y2={dots[e[1]].y}
                stroke="#888"
                stroke-width="2"
                stroke-dasharray="6 4"
              />
            {/each}
            {#each pathEdges as e}
              <line
                x1={dots[e[0]].x}
                y1={dots[e[0]].y}
                x2={dots[e[1]].x}
                y2={dots[e[1]].y}
                stroke={PURPLE}
                stroke-width="4"
              />
            {/each}
          {/if}

          {#if step === 3}
            {#each lines as l}
              <line
                x1={l.x1}
                y1={l.y1}
                x2={l.x2}
                y2={l.y2}
                stroke="#555"
                stroke-width="2"
                stroke-dasharray="6 4"
              />
            {/each}
          {/if}

          <!-- the dots -->
          {#each dots as d}
            <circle
              class="dot"
              cx={d.x}
              cy={d.y}
              r={step === 2 && hubs.includes(d.id) ? 6 : 4}
              style="fill: {fillFor(d, step)}; opacity: {opacityFor(d, step)}; stroke: {strokeFor(d, step)}; stroke-width: {widthFor(d, step)}px"
            />
          {/each}

          <!-- IVF: neighborhood centers -->
          {#if step === 1}
            {#each clusters as c}
              <line
                x1={c.cx - 6}
                y1={c.cy}
                x2={c.cx + 6}
                y2={c.cy}
                stroke="black"
                stroke-width="2.5"
              />
              <line
                x1={c.cx}
                y1={c.cy - 6}
                x2={c.cx}
                y2={c.cy + 6}
                stroke="black"
                stroke-width="2.5"
              />
              <text x={c.cx} y={c.cy + 54} text-anchor="middle" class="label">
                {c.name}
              </text>
            {/each}
          {/if}

          <!-- HNSW: where the search starts and ends -->
          {#if step === 2}
            <text
              x={dots[entry].x + 10}
              y={dots[entry].y + 18}
              class="label"
              style="fill: #555"
            >
              start
            </text>
            <text
              x={dots[found].x + 10}
              y={dots[found].y + 20}
              class="label"
              style="fill: {PURPLE}"
            >
              closest found
            </text>
          {/if}

          <!-- the question -->
          <circle
            cx={query.x}
            cy={query.y}
            r="8"
            fill="white"
            stroke={PURPLE}
            stroke-width="4"
          />
          <text x={query.x + 14} y={query.y - 12} class="label" style="fill: {PURPLE}">
            your question
          </text>
        </svg>

        <div class="readout">
          <div class="method">{names[step]}</div>
          <div class="counter">
            Checked <b>{checked}</b> of {dots.length} dots
          </div>
          {#if step === 3}
            <div class="code">The question's code: <b>{qcode}</b></div>
          {/if}
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
    gap: 0.8rem;
    padding: 1rem 1.5rem;
    text-align: left;
  }

  .map {
    width: 100%;
    height: auto;
  }

  .dot {
    transition: fill 500ms ease, opacity 500ms ease, stroke 500ms ease;
  }

  .label {
    font-size: 11px;
    font-weight: bold;
    paint-order: stroke;
    stroke: white;
    stroke-width: 4px;
  }

  .readout {
    text-align: center;
  }

  .method {
    font-size: 20px;
    font-weight: bold;
    color: var(--primary);
  }

  .counter {
    font-size: 17px;
  }

  .code {
    font-size: 15px;
    margin-top: 0.2rem;
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
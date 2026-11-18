<script>
  import Scrolly from "./Scrolly.svelte";
  import { fly, fade } from "svelte/transition";

  // which step the reader is on (set by the scroll component)
  let value;
  $: step = value > 0 ? value : 0;

  const BLUE = "#2b8cbe";
  const PURPLE = "#7c5aed";

  // ---- Phase 1: getting ready ----
  const prep = [
    { title: "Policy documents", sub: "the handbook" },
    { title: "Chunks", sub: "small pieces" },
    { title: "Embeddings", sub: "numbers for meaning" },
    { title: "Vector database", sub: "stored and indexed" },
  ];

  // ---- a small map of stored chunks (illustration) ----
  const chunkDots = [
    { x: 46, y: 38 },
    { x: 60, y: 30 },
    { x: 38, y: 52 },
    { x: 58, y: 50 },
    { x: 70, y: 42 },
    { x: 140, y: 35 },
    { x: 155, y: 48 },
    { x: 132, y: 52 },
    { x: 150, y: 28 },
    { x: 90, y: 105 },
    { x: 105, y: 118 },
    { x: 78, y: 115 },
    { x: 120, y: 100 },
  ];
  const query = { x: 58, y: 44 };

  const dist = (a, b) => Math.hypot(a.x - b.x, a.y - b.y);
  const nearestIds = chunkDots
    .map((d, i) => ({ i, k: dist(d, query) }))
    .sort((a, b) => a.k - b.k)
    .slice(0, 3)
    .map((o) => o.i);

  // ---- Phase 2: what gets retrieved and answered ----
  const snippets = [
    "Employees can claim up to $500 for home office equipment.",
    "Receipts must be submitted within 30 days.",
    "Standing desks and chairs are covered.",
  ];

  const steps = [
    `<p><b>Phase 1: getting ready.</b> Before anyone asks anything, the system cuts the policy documents into chunks, turns each chunk into numbers, and stores them in a vector database. This happens once, ahead of time.</p>`,
    `<p><b>Phase 2: answering.</b> A user asks a question. It goes through the <i>same</i> embedding model, so it lands on the same map as the chunks.</p>`,
    `<p><b style="color:#2b8cbe">Retrieval.</b> The vector database finds the few chunks closest to the question.</p>`,
    `<p><b style="color:#e69f00">Augmentation.</b> Those chunks are added to the prompt, along with the question and some instructions, such as "Answer using only this policy."</p>`,
    `<p><b style="color:#2ca25f">Generation.</b> The chatbot writes its answer from what it was given, and can point to where it came from.</p>`,
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
        <!-- Phase 1 -->
        <div class="phase-title">
          Phase 1: Getting ready <span>(once, ahead of time)</span>
        </div>
        <div class="prep-row" class:compact={step >= 1}>
          {#each prep as p, i}
            <div
              class="box"
              class:lit={value >= 0}
              style="transition-delay: {i * 350}ms"
            >
              <div class="box-title">{p.title}</div>
              {#if step < 1}
                <div class="box-sub">{p.sub}</div>
              {/if}
            </div>
            {#if i < prep.length - 1}
              <span class="arrow">→</span>
            {/if}
          {/each}
        </div>

        <!-- Phase 2 -->
        {#if step >= 1}
          <div class="phase-title" in:fade={{ duration: 400 }}>
            Phase 2: Answering <span>(every time someone asks)</span>
          </div>

          <div class="ask-row" in:fly={{ y: 15, duration: 400 }}>
            <div class="q">
              What's the reimbursement policy for home office setup?
            </div>
            <span class="arrow">→</span>
            <div class="emb">[0.3, 0.8, …]</div>
          </div>

          <div class="middle" in:fly={{ y: 15, duration: 400 }}>
            <div class="mapbox" class:hide-mobile={step >= 3}>
              <svg viewBox="0 0 200 140" class="map">
                {#if step >= 2}
                  {#each nearestIds as id}
                    <line
                      x1={query.x}
                      y1={query.y}
                      x2={chunkDots[id].x}
                      y2={chunkDots[id].y}
                      stroke={BLUE}
                      stroke-width="2"
                      stroke-dasharray="4 3"
                    />
                  {/each}
                {/if}
                {#each chunkDots as d, i}
                  <circle
                    class="dot"
                    cx={d.x}
                    cy={d.y}
                    r="5"
                    style="fill: {step >= 2 && nearestIds.includes(i)
                      ? BLUE
                      : '#bbb'}; opacity: {step >= 2 && !nearestIds.includes(i)
                      ? 0.4
                      : 1}; stroke: #555; stroke-width: 1.5px"
                  />
                {/each}
                <circle
                  cx={query.x}
                  cy={query.y}
                  r="7"
                  fill="white"
                  stroke={PURPLE}
                  stroke-width="3.5"
                />
              </svg>
              <div class="map-label">the question lands among the chunks</div>
              {#if step >= 2}
                <span class="tag retrieval" in:fade={{ duration: 400 }}>
                  Retrieval
                </span>
              {/if}
            </div>

            {#if step >= 3}
              <div class="prompt" in:fly={{ x: 30, duration: 500 }}>
                <div class="p-line instr">Answer using only this policy.</div>
                {#each snippets as sn}
                  <div class="p-line chunk">{sn}</div>
                {/each}
                <div class="p-line quest">
                  Question: What's the reimbursement policy for home office
                  setup?
                </div>
                <span class="tag augmentation">Augmentation</span>
              </div>
            {/if}
          </div>

          {#if step >= 4}
            <div class="answer" in:fly={{ y: 20, duration: 500 }}>
              <div class="a-text">
                You can claim up to $500 for home office equipment, including
                standing desks and chairs. Send your receipts within 30 days.
              </div>
              <div class="a-source">Source: Home Office Setup Policy</div>
              <span class="tag generation">Generation</span>
            </div>
          {/if}
        {/if}
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
    gap: 0.6rem;
    padding: 1rem 1.2rem;
    text-align: left;
  }

  .phase-title {
    font-weight: bold;
    font-size: 15px;
  }

  .phase-title span {
    font-weight: normal;
    color: #555;
    font-size: 13px;
  }

  .prep-row {
    display: flex;
    align-items: stretch;
    gap: 0.3rem;
  }

  .arrow {
    align-self: center;
    font-size: 18px;
  }

  .box {
    flex: 1;
    border: 3px solid #bbb;
    background: white;
    padding: 0.5rem 0.4rem;
    text-align: center;
    opacity: 0.6;
    transition: all 500ms ease;
  }

  .box.lit {
    border-color: var(--primary);
    background: #e8e3ff;
    opacity: 1;
  }

  .prep-row.compact .box {
    padding: 0.25rem 0.3rem;
  }

  .box-title {
    font-weight: bold;
    font-size: 13px;
  }

  .box-sub {
    font-size: 11px;
    color: #555;
    margin-top: 0.15rem;
  }

  .ask-row {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  .q {
    flex: 1;
    border: 3px solid black;
    background: #e8e3ff;
    padding: 0.4rem 0.6rem;
    font-weight: bold;
    font-size: 13px;
  }

  .emb {
    font-family: monospace;
    font-size: 13px;
    border: 3px solid var(--primary);
    padding: 0.4rem 0.5rem;
    background: white;
  }

  .middle {
    display: flex;
    gap: 0.7rem;
    align-items: flex-start;
  }

  .mapbox {
    position: relative;
    flex: 0 0 44%;
    border: 3px solid black;
    background: white;
    padding: 0.4rem;
  }

  .map {
    width: 100%;
    height: auto;
    display: block;
  }

  .dot {
    transition: fill 500ms ease, opacity 500ms ease;
  }

  .map-label {
    font-size: 11px;
    color: #555;
    text-align: center;
    margin-top: 0.2rem;
  }

  .prompt {
    position: relative;
    flex: 1;
    border: 3px solid black;
    background: white;
    padding: 0.5rem;
    display: flex;
    flex-direction: column;
    gap: 0.3rem;
  }

  .p-line {
    font-size: 12px;
    line-height: 1.3;
    padding: 0.3rem 0.5rem;
  }

  .p-line.instr {
    background: #fdf0d5;
    font-weight: bold;
  }

  .p-line.chunk {
    background: #e3f1f8;
    border-left: 5px solid #2b8cbe;
  }

  .p-line.quest {
    background: #e8e3ff;
  }

  .answer {
    position: relative;
    border: 3px solid black;
    background: #dff3e6;
    padding: 0.6rem 0.8rem;
  }

  .a-text {
    font-size: 13px;
    line-height: 1.4;
  }

  .a-source {
    margin-top: 0.3rem;
    font-size: 12px;
    color: #555;
  }

  .tag {
    position: absolute;
    top: -14px;
    right: 10px;
    color: white;
    padding: 2px 10px;
    font-size: 13px;
    font-weight: bold;
  }

  .tag.retrieval {
    background: #2b8cbe;
  }

  .tag.augmentation {
    background: #e69f00;
  }

  .tag.generation {
    background: #2ca25f;
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


  @media screen and (max-width: 950px) {
    .panel {
      gap: 0.4rem;
      padding: 0.6rem 0.7rem;
    }

    .phase-title {
      font-size: 13px;
    }

    .phase-title span {
      font-size: 11px;
    }

    .prep-row .arrow {
      display: none;
    }

    .prep-row {
      gap: 0.25rem;
    }

    .box {
      padding: 0.3rem 0.2rem;
      min-width: 0;
    }

    .box-title {
      font-size: 11px;
    }

    .box-sub {
      font-size: 10px;
    }

    .q {
      font-size: 12px;
      padding: 0.3rem 0.5rem;
    }

    .emb {
      font-size: 11px;
      padding: 0.3rem;
    }

    .mapbox.hide-mobile {
      display: none;
    }

    .p-line {
      font-size: 11px;
      padding: 0.2rem 0.4rem;
    }

    .a-text {
      font-size: 12px;
    }
  }
</style>
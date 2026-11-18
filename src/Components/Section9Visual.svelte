<script>
  import Scrolly from "./Scrolly.svelte";

  // which step the reader is on (set by the scroll component)
  let value;
  $: step = value > 0 ? value : 0;

  // ---- the sample "handbook" (six sentences) ----
  const DOC =
    "Employees may work remotely on Fridays. Dogs are allowed in the office on Fridays. Sick leave is 10 days per year. Unused sick days do not roll over. Home office equipment up to $500 is reimbursed with receipts. Travel must be booked through the company portal.";

  // the sentence that answers the question
  const TS = DOC.indexOf("Dogs are allowed");
  const TE = DOC.indexOf(" Sick leave");

  const wholeSegs = [
    { t: DOC.slice(0, TS), hit: false },
    { t: DOC.slice(TS, TE), hit: true },
    { t: DOC.slice(TE), hit: false },
  ];

  // ---- fixed-size chunking with overlap ----
  let size = 56;
  let overlap = 0;

  // each step sets the chunk size and overlap it needs to show its point
  $: if (value === 1 || value === 2) {
    size = 56;
    overlap = 0;
  }
  $: if (value === 3) {
    size = 56;
    overlap = 16;
  }

  // the overlap can never be more than half the chunk (or 30 characters)
  $: maxOverlap = Math.min(30, Math.floor(size / 2));
  $: if (overlap > maxOverlap) overlap = maxOverlap;

  function makeChunks(sz, ov) {
    const out = [];
    const stride = sz - ov;
    for (let start = 0; start < DOC.length; start += stride) {
      const end = Math.min(start + sz, DOC.length);
      out.push({ start, end });
      if (end >= DOC.length) break;
    }
    return out;
  }

  $: chunks = makeChunks(size, overlap);

  const isHit = (c) => c.end > TS && c.start < TE;
  $: hitCount = chunks.filter(isHit).length;
  $: whole = chunks.some((c) => c.start <= TS && c.end >= TE);

  // split a chunk's text into pieces so the answer and the overlap
  // can be highlighted
  function segments(c, i) {
    const segs = [];
    for (let k = c.start; k < c.end; k++) {
      const hit = k >= TS && k < TE;
      const ov = i > 0 && k - c.start < overlap;
      const last = segs[segs.length - 1];
      if (last && last.hit === hit && last.ov === ov) last.t += DOC[k];
      else segs.push({ t: DOC[k], hit, ov });
    }
    return segs;
  }

  // ---- other ways to chunk the same six sentences ----
  const sentences = [
    "Remote on Fridays",
    "Dogs in the office",
    "Sick leave: 10 days",
    "Sick days don't roll over",
    "Equipment: $500",
    "Travel via portal",
  ];
  const methods = [
    { name: "Sentence", sub: "6 chunks", groups: [0, 1, 2, 3, 4, 5] },
    { name: "Paragraph", sub: "3 uneven chunks", groups: [0, 0, 0, 1, 1, 2] },
    { name: "Semantic", sub: "3 chunks by topic", groups: [0, 0, 1, 1, 2, 2] },
  ];
  const palette = [
    "#fde2e4",
    "#dfe7fd",
    "#fff1c1",
    "#d8f3dc",
    "#e8e3ff",
    "#fcd5ce",
  ];

  const steps = [
    `<p>Imagine the handbook stored as one entry. Ask about dogs, and the whole handbook comes back. The reader has to dig for the answer.</p>`,
    `<p>Now the handbook is cut into equal pieces of 56 characters, and the question only pulls back the pieces that matter. Real chunks are bigger, around 500 characters. We use tiny ones so the cuts are easy to see.</p>`,
    `<p>But look where the cut landed. The answer, <i>"Dogs are allowed in the office on Fridays,"</i> is split between two chunks, and neither one has the full meaning.</p>`,
    `<p><b>Overlap</b> fixes it. Each chunk now repeats the last 16 characters of the one before it, so the whole sentence appears in chunk 2.</p>`,
    `<p>Try it yourself. Drag the sliders and watch the chunks change. Too small, and the answer gets split. Too large, and chunks fill up with other topics.</p>`,
    `<p>Fixed-size cuts are not the only way. The same six sentences can be chunked by sentence, by paragraph, or by meaning. <b>Semantic</b> chunking cuts where the topic changes. <b>Agentic</b> chunking lets an AI model decide where to cut.</p>`,
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
        {#if step < 5}
          <div class="question">Can I bring my dog to work?</div>
        {/if}

        {#if step === 0}
          <div class="whole">
            <div class="whole-title">The whole handbook, stored as one entry</div>
            <p class="text">{#each wholeSegs as s}<span class:hit={s.hit}>{s.t}</span>{/each}</p>
            <span class="tag bad">Too much</span>
          </div>
          <div class="note">
            A real handbook has 50 pages. Here it's six sentences.
          </div>
        {/if}

        {#if step >= 1 && step <= 4}
          <div class="chunks">
            {#each chunks as c, i}
              <div class="chunk" class:hit={isHit(c)} class:dim={!isHit(c)}>
                <span class="num">{i + 1}</span>
                <p class="text">{#each segments(c, i) as s}<span class:hit={s.hit} class:ov={s.ov}>{s.t}</span>{/each}</p>
              </div>
            {/each}
          </div>

          {#if step >= 2}
            <div class="banner" class:good={whole} class:bad={!whole}>
              {#if whole}
                The full sentence sits in one chunk.
              {:else}
                The sentence is split across {hitCount} chunks.
              {/if}
            </div>
            <div class="legend">
              <span class="hit">the answer</span>
              <span class="ov">repeated (overlap)</span>
            </div>
          {/if}

          {#if step === 4}
            <div class="controls">
              <label>
                Chunk size: <b>{size}</b> characters
                <input type="range" min="50" max="120" step="2" bind:value={size} />
              </label>
              <label>
                Overlap: <b>{overlap}</b> characters
                <input type="range" min="0" max={maxOverlap} step="2" bind:value={overlap} />
              </label>
              <div class="note">{chunks.length} chunks</div>
            </div>
          {/if}
        {/if}

        {#if step === 5}
          <div class="compare">
            {#each methods as m}
              <div class="col">
                <div class="col-title">{m.name}</div>
                <div class="col-sub">{m.sub}</div>
                {#each sentences as s, i}
                  <div
                    class="row"
                    style="background: {palette[m.groups[i]]}; border-top: 4px solid {i > 0 && m.groups[i] !== m.groups[i - 1] ? 'black' : 'transparent'}"
                  >
                    {s}
                  </div>
                {/each}
              </div>
            {/each}
          </div>
          <div class="note">Same six sentences. Thick lines show where the cuts fall.</div>
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
    gap: 0.7rem;
    padding: 1rem 1.2rem;
    text-align: left;
  }

  .question {
    border: 3px solid black;
    background: #e8e3ff;
    padding: 0.5rem 0.8rem;
    font-weight: bold;
  }

  .text {
    margin: 0;
    font-size: 13px;
    line-height: 1.4;
    white-space: pre-wrap;
  }

  .text .hit,
  .legend .hit {
    background: #d9ceff;
  }

  .text .ov,
  .legend .ov {
    box-shadow: inset 0 -4px 0 #f2c200;
  }

  .whole {
    position: relative;
    border: 3px solid black;
    background: white;
    padding: 0.8rem;
  }

  .whole-title {
    font-weight: bold;
    margin-bottom: 0.5rem;
  }

  .tag {
    position: absolute;
    top: -14px;
    right: 12px;
    color: white;
    padding: 2px 10px;
    font-size: 14px;
    font-weight: bold;
  }

  .tag.bad {
    background: #d9534f;
  }

  .note {
    font-size: 14px;
    color: #555;
    text-align: center;
  }

  .chunks {
    display: flex;
    flex-direction: column;
    gap: 0.4rem;
    max-height: 38vh;
    overflow-y: auto;
    padding-right: 4px;
  }

  .chunk {
    display: flex;
    gap: 0.5rem;
    align-items: flex-start;
    border: 2px solid #bbb;
    background: white;
    padding: 0.3rem 0.5rem;
    transition: all 400ms ease;
  }

  .chunk.hit {
    border-color: var(--primary);
    background: #f7f5ff;
  }

  .chunk.dim {
    opacity: 0.45;
  }

  .num {
    font-weight: bold;
    font-size: 12px;
    color: white;
    background: #555;
    min-width: 1.3rem;
    text-align: center;
    padding: 1px 0;
  }

  .chunk.hit .num {
    background: var(--primary);
  }

  .banner {
    text-align: center;
    font-weight: bold;
    padding: 0.4rem;
    border: 3px solid black;
    transition: background 400ms ease;
  }

  .banner.good {
    background: #d8f3dc;
  }

  .banner.bad {
    background: #fde2e4;
  }

  .legend {
    display: flex;
    justify-content: center;
    gap: 1rem;
    font-size: 13px;
  }

  .legend span {
    padding: 0 6px;
  }

  .controls {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .controls label {
    display: flex;
    flex-direction: column;
    gap: 0.2rem;
    font-size: 15px;
  }

  .controls input {
    width: 100%;
  }

  .compare {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 0.6rem;
  }

  .col-title {
    font-weight: bold;
    text-align: center;
    font-size: 16px;
  }

  .col-sub {
    text-align: center;
    font-size: 12px;
    color: #555;
    margin-bottom: 0.4rem;
  }

  .row {
    font-size: 12px;
    padding: 0.4rem 0.5rem;
    line-height: 1.25;
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
<script>
  import Scrolly from "./Scrolly.svelte";
  import { fade } from "svelte/transition";

  // which step the reader is on (set by the scroll component)
  let value;

  const steps = [
    `<p>There are three common ways to get better answers from an AI. Each one solves a different problem.</p>`,
    `<p><b>Need: follow rules.</b> Prompt engineering gives the AI clear instructions, like a rule book.</p>`,
    `<p><b>Need: speak in a certain voice.</b> Fine-tuning trains the AI on many examples until the style comes naturally.</p>`,
    `<p><b>Need: know facts that change.</b> RAG looks up the current document. When the policy changes, you update the document, not the AI.</p>`,
  ];

  const rows = [
    {
      need: "Follow rules",
      tool: "Prompt engineering",
      desc: "Clear instructions, like a rule book",
    },
    {
      need: "Speak in a certain voice",
      tool: "Fine-tuning",
      desc: "Training on many examples of the style",
    },
    {
      need: "Know facts that change",
      tool: "RAG",
      desc: "Looks up the current document",
    },
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
      <div class="rows">
        {#each rows as row, i}
          <div class="row">
            <div
              class="need"
              class:show={value >= i + 1}
              class:lit={value === i + 1}
            >
              {row.need}
            </div>
            <div class="arrow" class:show={value >= i + 1}>→</div>
            <div class="tool" class:lit={value === i + 1}>
              <div class="tool-name">{row.tool}</div>
              <div class="tool-desc">{row.desc}</div>
              {#if value >= 3 && i === 1}
                <span class="tag bad" in:fade={{ duration: 400 }}>
                  Retrain needed
                </span>
              {/if}
              {#if value >= 3 && i === 2}
                <span class="tag good" in:fade={{ duration: 400 }}>
                  Just update the document
                </span>
              {/if}
            </div>
          </div>
        {/each}
        {#if value >= 3}
          <div class="update" in:fade={{ duration: 400 }}>
            The policy just changed.
          </div>
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

  .rows {
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 1.8rem;
    padding: 1.5rem;
    text-align: left;
  }

  .row {
    display: grid;
    grid-template-columns: 1fr auto 1.2fr;
    align-items: center;
    gap: 0.6rem;
  }

  .need {
    border: 2px dashed #999;
    padding: 0.5rem 0.7rem;
    opacity: 0;
    transition: all 400ms ease;
  }

  .need.show {
    opacity: 1;
  }

  .need.lit {
    border: 2px solid var(--primary);
    background: #e8e3ff;
    font-weight: bold;
  }

  .arrow {
    opacity: 0;
    font-size: 24px;
    transition: opacity 400ms ease;
  }

  .arrow.show {
    opacity: 1;
  }

  .tool {
    position: relative;
    border: 3px solid black;
    background: white;
    padding: 0.7rem 0.9rem;
    transition: all 400ms ease;
  }

  .tool.lit {
    border-color: var(--primary);
    background: #e8e3ff;
  }

  .tool-name {
    font-weight: bold;
  }

  .tool-desc {
    font-size: 14px;
    color: #555;
    margin-top: 0.2rem;
  }

  .tag {
    display: inline-block;
    margin-top: 0.5rem;
    color: white;
    padding: 2px 10px;
    font-size: 13px;
    font-weight: bold;
  }

  .tag.bad {
    background: #d9534f;
  }

  .tag.good {
    background: var(--primary);
  }

  .update {
    text-align: center;
    font-weight: bold;
    border: 3px solid black;
    background: #fdf0d5;
    padding: 0.5rem;
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
    .rows {
      gap: 0.8rem;
      padding: 0.8rem;
    }

    .row {
      grid-template-columns: 0.8fr auto 1.5fr;
      gap: 0.4rem;
    }

    .need {
      padding: 0.3rem 0.4rem;
      font-size: 13px;
    }

    .arrow {
      font-size: 18px;
    }

    .tool {
      padding: 0.5rem 0.6rem;
    }

    .tool-name {
      font-size: 14px;
    }

    .tool-desc {
      font-size: 12px;
    }

    .tag {
      font-size: 11px;
      padding: 2px 6px;
    }

    .update {
      font-size: 13px;
      padding: 0.3rem;
    }
  }
</style>
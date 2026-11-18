<script>
  import Scrolly from "./Scrolly.svelte";
  import { fade } from "svelte/transition";

  // which step the reader is on (set by the scroll component)
  let value;

  const docs = [
    "Travel expenses",
    "Holiday policy",
    "Sick leave",
    "Home office setup",
  ];

  const steps = [
    `<p>A colleague asks the same thing in different words: <i>"What's the allowance for working from home?"</i> The keywords are now <b style="color:#c2307a">allowance</b> and <b style="color:#0f9d8a">working from home</b>.</p>`,
    `<p>The system looks for those exact words in every document. None of them contain either one, so every document scores zero.</p>`,
    `<p>The right page is still there. It just says <b style="color:#c2307a">reimbursement</b> and <b style="color:#0f9d8a">home office</b>. Same meaning, different words, so the search can't see it.</p>`,
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
        <div class="question">What's the allowance for working from home?</div>
        <div class="keywords">
          <span class="chip k1">allowance</span>
          <span class="chip k2">working from home</span>
        </div>

        <div class="docs">
          {#each docs as name}
            <div class="doc" class:dim={value >= 1}>
              <div class="doc-name">{name}</div>
              <div class="counts">
                {#if value >= 1}
                  <span class="count k1">0</span>
                  <span class="count k2">0</span>
                  <span class="total">Total: 0</span>
                {/if}
              </div>
            </div>
          {/each}
        </div>

        {#if value >= 2}
          <div class="links" in:fade={{ duration: 500 }}>
            <div class="link-row">
              <span class="chip k1">allowance</span>
              <span class="dots"></span>
              <span class="chip k1">reimbursement</span>
            </div>
            <div class="link-row">
              <span class="chip k2">working from home</span>
              <span class="dots"></span>
              <span class="chip k2">home office</span>
            </div>
            <div class="same">Same meaning. Different words.</div>
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

  .panel {
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 1rem;
    padding: 1.5rem;
    text-align: left;
  }

  .question {
    border: 3px solid black;
    background: #e8e3ff;
    padding: 0.6rem 0.9rem;
    font-weight: bold;
  }

  .keywords {
    display: flex;
    gap: 0.6rem;
  }

  .chip {
    color: white;
    padding: 3px 12px;
    font-weight: bold;
    font-size: 15px;
  }

  .k1 {
    background: #c2307a;
  }

  .k2 {
    background: #0f9d8a;
  }

  .docs {
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
  }

  .doc {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border: 3px solid #bbb;
    background: white;
    padding: 0.6rem 0.9rem;
    min-height: 2.4rem;
    transition: all 500ms ease;
  }

  .doc.dim {
    opacity: 0.5;
    background: #e6e6e6;
  }

  .doc-name {
    font-weight: bold;
    flex: 1;
    min-width: 0;
    padding-right: 0.5rem;
  }

  .counts {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    flex-shrink: 0;
    white-space: nowrap;
  }

  .count {
    color: white;
    width: 1.8rem;
    text-align: center;
    padding: 2px 0;
    font-weight: bold;
  }

  .total {
    font-weight: bold;
    width: 4rem;
    text-align: right;
  }

  .links {
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
  }

  .link-row {
    display: flex;
    align-items: center;
    gap: 0.6rem;
  }

  .dots {
    flex: 1;
    border-top: 3px dotted var(--primary);
  }

  .same {
    text-align: center;
    color: var(--primary);
    font-weight: bold;
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
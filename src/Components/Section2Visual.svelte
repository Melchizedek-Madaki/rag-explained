<script>
  import Scrolly from "./Scrolly.svelte";
  import { fly, fade } from "svelte/transition";

  // which step the reader is on (set by the scroll component)
  let value;

  const steps = [
    `<p><span style="color:#2b8cbe;font-weight:bold">Retrieval:</span> out of all the policy documents, the page about home office setup is found.</p>`,
    `<p><span style="color:#e69f00;font-weight:bold">Augmentation:</span> that page is added to your question, so the prompt now carries the policy.</p>`,
    `<p><span style="color:#2ca25f;font-weight:bold">Generation:</span> the chatbot writes its answer from what it was given, so now it matches the policy.</p>`,
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
      <div class="flow">
        <div class="box">
          <div class="box-title">Policy documents</div>
          <div class="page">Travel expenses</div>
          <div class="page" class:picked={value >= 0}>Home office setup</div>
          <div class="page">Sick leave</div>
          {#if value >= 0}
            <span class="tag retrieval" in:fade={{ duration: 400 }}>
              Retrieval
            </span>
          {/if}
        </div>

        {#if value >= 1}
          <div class="box" in:fly={{ y: 20, duration: 400 }}>
            <div class="box-title">The prompt</div>
            <div class="part policy">
              Policy: Employees can claim up to $500 for home office equipment.
              Receipts are due within 30 days.
            </div>
            <div class="part question">
              Question: What's the reimbursement policy for setting up my home
              office?
            </div>
            <span class="tag augmentation">Augmentation</span>
          </div>
        {/if}

        {#if value >= 2}
          <div class="box" in:fly={{ y: 20, duration: 400 }}>
            <div class="box-title">The chatbot's answer</div>
            <div class="part answer">
              You can claim up to $500 for home office equipment. Send your
              receipts within 30 days.
            </div>
            <span class="tag generation">Generation</span>
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

  .flow {
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 1.5rem;
    padding: 1.5rem;
    text-align: left;
  }

  .box {
    position: relative;
    border: 3px solid black;
    padding: 1rem;
    background: white;
  }

  .box-title {
    font-weight: bold;
    margin-bottom: 0.5rem;
  }

  .page {
    border: 2px solid #bbb;
    padding: 0.3rem 0.6rem;
    margin-bottom: 0.4rem;
    color: #777;
    transition: all 400ms ease;
  }

  .page.picked {
    border-color: #2b8cbe;
    background: #e3f1f8;
    color: black;
    font-weight: bold;
  }

  .part {
    padding: 0.5rem 0.7rem;
    margin-bottom: 0.4rem;
    line-height: 1.4;
  }

  .part.policy {
    background: #fdf0d5;
  }

  .part.question {
    background: #e8e3ff;
  }

  .part.answer {
    background: #dff3e6;
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
    .flow {
      gap: 1rem;
      padding: 0.8rem;
    }

    .box {
      padding: 0.6rem;
    }

    .box-title {
      font-size: 13px;
      margin-bottom: 0.3rem;
    }

    .page {
      font-size: 13px;
      padding: 0.2rem 0.5rem;
    }

    .part {
      font-size: 13px;
      padding: 0.35rem 0.5rem;
    }
  }
</style>
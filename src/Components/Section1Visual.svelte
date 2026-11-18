<script>
  import Scrolly from "./Scrolly.svelte";
  import { fly, fade } from "svelte/transition";

  // which step the reader is on (set by the scroll component)
  let value;

  const steps = [
    `<p>You ask the chatbot a question about your company's policy.</p>`,
    `<p>It replies with a confident, generic answer. It sounds right, but it isn't from your policy.</p>`,
    `<p>The real answer exists in the policy document. The AI just can't see it.</p>`,
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
      <div class="chat">
  {#if value >= 0}
    <div class="bubble user" in:fly={{ y: 20, duration: 400 }}>
      🙂 What's the reimbursement policy for setting up my home office?
    </div>
  {/if}

  {#if value >= 1}
    <div class="bubble ai" in:fly={{ y: 20, duration: 400 }}>
      🤖 Most companies reimburse part of home office costs, up to a set limit,
      with receipts. Check with your manager for details.
    </div>
    <div class="tag" in:fade={{ duration: 400, delay: 300 }}>
      Sounds right. Not from your policy.
    </div>
  {/if}

  {#if value >= 2}
    <div class="doc" in:fade={{ duration: 500 }}>
      <div class="doc-title">📄 Home Office Setup Policy</div>
      <div class="line"></div>
      <div class="line short"></div>
      <div class="line"></div>
      <div class="doc-note">The AI has never seen this.</div>
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

.chat {
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

.bubble {
  max-width: 80%;
  padding: 0.75rem 1rem;
  border: 3px solid black;
  line-height: 1.4;
  font-size: 17px;
}

.bubble.user {
  align-self: flex-end;
  background: #e8e3ff;
}

.bubble.ai {
  align-self: flex-start;
  background: white;
}

.tag {
  align-self: flex-start;
  color: var(--primary);
  font-weight: bold;
  font-size: 15px;
}

.doc {
  margin-top: 1rem;
  border: 3px dashed #999;
  padding: 1rem;
  opacity: 0.55;
}

.doc-title {
  font-weight: bold;
  margin-bottom: 0.75rem;
}

.line {
  height: 8px;
  background: #bbb;
  margin-bottom: 8px;
}

.line.short {
  width: 60%;
}

.doc-note {
  margin-top: 0.5rem;
  font-size: 14px;
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
    .chat {
      gap: 0.6rem;
      padding: 0.8rem;
    }

    .bubble {
      max-width: 92%;
      padding: 0.5rem 0.7rem;
      font-size: 15px;
    }

    .tag {
      font-size: 13px;
    }

    .doc {
      margin-top: 0.3rem;
      padding: 0.6rem;
    }

    .doc-title {
      margin-bottom: 0.4rem;
    }

    .doc-note {
      font-size: 12px;
    }
  }
</style>
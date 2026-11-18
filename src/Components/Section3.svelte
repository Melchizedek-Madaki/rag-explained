<script>
  import Section3Visual from "./Section3Visual.svelte";
</script>

<h1 class="body-header">When RAG is not the answer</h1>

<p class="body-text">
  RAG is popular, but it isn't the fix for everything. There are three common
  ways to get better answers from an AI, and each solves a different problem.
</p>

<p class="body-text">
  Say we're building a chatbot for company policies, and we want three things
  from it.
</p>

<p class="body-text">
  <span class="bold">1. Follow rules.</span> It should never share personal
  employee details, and it should send salary questions to HR. The tool for
  this is <span class="bold">prompt engineering</span>: writing clear
  instructions that tell the AI how to behave. Think of it as a rule book.
</p>

<p class="body-text">
  <span class="bold">2. Speak in a certain voice.</span> Suppose we want it to
  sound like our CEO, with the same words, tone, and style. The tool for this
  is <span class="bold">fine-tuning</span>: training the AI on many examples of
  how that person writes and speaks, until the style becomes natural to it.
</p>

<p class="body-text">
  <span class="bold">3. Know the facts, and keep them current.</span> It should
  give the right answer about the home-office policy, even after the policy
  changes. The tool for this is <span class="bold">RAG</span>.
</p>

<Section3Visual />

<p class="body-text">
  Why not fine-tune the facts too? Because policies change. Each change means
  retraining, which is slow and expensive. The AI also can't show where an
  answer came from, and it only knows what it saw during training.
</p>

<p class="body-text">
  The rule of thumb: fine-tuning is for <em>how</em> the AI speaks, and RAG is
  for <em>what</em> it knows.
</p>

<p class="body-text">
  So RAG is the right tool for facts that change. But the chatbot still has a
  challenge: with hundreds of documents, how does it find the right page?
</p>
<script>
  import RecapStrip from "./RecapStrip.svelte";
</script>

<h1 class="body-header">Looking back</h1>

<p class="body-text">
  That's RAG. A chatbot can't know your private documents, so we give it the
  right page at the right moment.
</p>

<p class="body-text">
  To make that happen automatically, we cut documents into
  <span class="bold">chunks</span>, turn each chunk into
  <span class="bold">numbers that capture its meaning</span>, and store them in
  a <span class="bold">vector database</span> that can search quickly. When
  someone asks a question, we find the closest chunks, add them to the prompt,
  and let the chatbot write the answer.
</p>

<RecapStrip />

<p class="body-text">
  Underneath, it's the same three-part idea from the start:
  <span class="bold">retrieval</span>, <span class="bold">augmentation</span>,
  and <span class="bold">generation</span>.
</p>

<p class="body-text">
  Real systems go further. They rank results more carefully, mix keyword and
  meaning search, speed up repeated questions, and keep working when parts
  fail. We'll cover those in a follow-up article for readers who want to go
  deeper.
</p>

<p class="body-text">
  Thanks for reading. We hope RAG feels less mysterious now.
  <br /><br />
  The format of this article is inspired by
  <a class="on-end" href="https://mlu-explain.github.io/">MLU-Explain</a>. If
  you have comments or ideas, feel free to reach out
  <a class="on-end" href="https://x.com/madaki22842">directly</a>.
</p>

<style>
</style>
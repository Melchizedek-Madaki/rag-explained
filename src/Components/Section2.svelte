<script>
  import Section2Visual from "./Section2Visual.svelte";
</script>

<h1 class="body-header">The simplest version of RAG</h1>

<p class="body-text">
  Here's a fix you've probably used already, without knowing its name.
</p>

<p class="body-text">
  Say you open your company's policy document, find the page about home
  offices, and paste it into the chat before asking your question:
  <em>"Using this policy, what can I get reimbursed for my home office?"</em>
</p>

<p class="body-text">
  Now the chatbot answers correctly. It didn't get smarter. You just gave it
  the right page.
</p>

<p class="body-text">
  That is RAG, short for
  <span class="bold">Retrieval-Augmented Generation</span>. The name describes
  its three parts:
</p>

<p class="body-text">
  <span class="bold">Retrieval:</span> finding the right page.
</p>

<p class="body-text">
  <span class="bold">Augmentation:</span> adding that page to your question.
</p>

<p class="body-text">
  <span class="bold">Generation:</span> the chatbot writing its answer using
  what you gave it.
</p>

<Section2Visual />

<p class="body-text">
  But nobody wants to hunt through documents and paste pages by hand. A RAG
  system does that part automatically. First, though, is RAG always the right
  tool?
</p>
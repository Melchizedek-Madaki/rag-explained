<script>
  import Section10Visual from "./Section10Visual.svelte";
</script>

<h1 class="body-header">Putting it all together</h1>

<p class="body-text">
  We now have every piece. A RAG system works in two phases: one done
  <em>before</em> anyone asks a question, and one done <em>every time</em>
  someone does.
</p>

<p class="body-text">
  <span class="bold">Phase 1: Getting ready (done once, ahead of time).</span>
  Loading thousands of documents takes a while, so it happens before any user
  shows up.
</p>

<p class="body-text">
  <span class="bold">1. Chunk:</span> cut the policy documents into small
  pieces (say 500 characters, with 50 of overlap).
</p>
<p class="body-text">
  <span class="bold">2. Embed:</span> the embedding model turns every chunk
  into its list of numbers.
</p>
<p class="body-text">
  <span class="bold">3. Store:</span> the vectors go into the vector database,
  which indexes them.
</p>

<p class="body-text">
  <span class="bold">Phase 2: Answering (every time someone asks).</span>
</p>

<p class="body-text">
  <span class="bold">1. Ask:</span> the user types,
  <em>"What's the reimbursement policy for home office setup?"</em>
</p>
<p class="body-text">
  <span class="bold">2. Embed the question:</span> it goes through the
  <em>same</em> embedding model, so it lands on the same map as the chunks.
</p>
<p class="body-text">
  <span class="bold">3. Retrieve:</span> the vector database finds the few
  chunks closest to the question.
</p>
<p class="body-text">
  <span class="bold">4. Augment:</span> those chunks are added to the prompt,
  along with the question and some instructions, such as
  <em>"Answer using only this policy."</em>
</p>
<p class="body-text">
  <span class="bold">5. Generate:</span> the chatbot writes an answer from what
  it was given.
</p>

<Section10Visual />

<p class="body-text">
  Notice that these are the same three words from the start.
  <span class="bold">Retrieval</span> finds the pages,
  <span class="bold">augmentation</span> adds them to the prompt, and
  <span class="bold">generation</span> writes the answer. The rest of the
  system is what makes those three steps work at scale.
</p>

<p class="body-text">
  And that's the whole system. Let's look back at how far we've come.
</p>
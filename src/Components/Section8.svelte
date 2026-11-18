<script>
  import Section8Visual from "./Section8Visual.svelte";
</script>

<h1 class="body-header">Finding the right page fast</h1>

<p class="body-text">
  Comparing a question with one page is easy. But a real company might have
  500 documents. With 384 numbers each, that's about
  <span class="bold">192,000 multiplications</span> for every single question,
  like reading a phone book page by page.
</p>

<p class="body-text">
  A <span class="bold">vector database</span> solves this. It stores all the
  embeddings and builds an <span class="bold">index</span> ahead of time: a way
  of organizing the vectors so a search only looks in the right place. Think of
  a librarian who knows which shelf to check.
</p>

<p class="body-text">
  The trade-off is that the search is <em>approximate</em>. It nearly always
  finds the best match, and occasionally misses a close second. For RAG,
  that's a good deal. Three popular indexing methods work like this:
</p>

<p class="body-text">
  <span class="bold">IVF (inverted file): sort into neighborhoods first.</span>
  Before any question arrives, the vectors are grouped into neighborhoods of
  similar meaning: animal policies, health benefits, remote work. When a
  question comes in, the system finds the nearest neighborhoods and searches
  only inside them. (The name comes from keeping a list of the vectors inside
  each neighborhood.)
</p>

<p class="body-text">
  <span class="bold">HNSW: follow the connections.</span> Every vector is
  linked to its most similar neighbors, forming a network. The network has
  layers, like a road system: highways at the top, main roads in the middle,
  streets at the bottom. A search starts on the highway, hops toward the
  question's location, then drops down to finer layers until it reaches the
  closest matches. It's fast and accurate, which is why most vector databases
  use it by default.
</p>

<p class="body-text">
  <span class="bold"
    >LSH (locality-sensitive hashing): give similar things the same label.</span
  >
  Imagine drawing a few random lines across our map. For each vector, note
  which side of each line it falls on, which gives it a short code, like 1-0-1.
  Similar vectors tend to land on the same sides, so they get the same code.
  When a question comes in, give it a code and look only at vectors with that
  same code.
</p>

<Section8Visual />

<p class="body-text">
  Popular vector databases include <span class="bold">Chroma</span>, which is
  free and great for learning, <span class="bold">Pinecone</span>, a managed
  service used in production, and <span class="bold">Weaviate</span>.
</p>

<p class="body-text">
  So far, each "page" we stored was a single short sentence. But real documents
  run for dozens of pages. What do we actually store?
</p>
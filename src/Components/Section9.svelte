<script>
  import Section9Visual from "./Section9Visual.svelte";
</script>

<h1 class="body-header">Cutting documents into chunks</h1>

<p class="body-text">
  So far, each thing we stored was a single sentence. Real documents are much
  longer, like a 50-page employee handbook. We could store it as one entry, but
  then a question about remote work would return
  <span class="bold">the whole handbook</span>. The reader would have to dig
  through 50 pages to find the answer. This is the
  <span class="bold">precision problem</span>.
</p>

<p class="body-text">
  The fix is <span class="bold">chunking</span>: cutting the document into
  small, focused pieces and storing each piece on its own. Now the same
  question returns just the section on remote work.
</p>

<Section9Visual />

<p class="body-text">
  There are several ways to cut. Here are the main ones, from simplest to
  smartest.
</p>

<p class="body-text">
  <span class="bold">Fixed-size chunks.</span> Cut every 500 characters, no
  matter what. It's simple and predictable, but it can slice a sentence in
  half: <em>"Dogs are allowed"</em> in one chunk and
  <em>"in the office on Fridays"</em> in the next. Neither chunk has the full
  meaning.
</p>

<p class="body-text">
  <span class="bold">Overlap.</span> To fix that, let each chunk repeat the
  last bit of the one before it, say 50 characters. If a sentence gets split,
  the missing context shows up in both chunks.
</p>

<p class="body-text">
  <span class="bold">Sentence-based.</span> Each sentence is its own chunk.
  Nothing gets cut mid-sentence, but a single sentence can be too small to mean
  much on its own.
</p>

<p class="body-text">
  <span class="bold">Paragraph-based.</span> Each paragraph is a chunk.
  Paragraphs usually hold one idea, but they vary a lot in length.
</p>

<p class="body-text">
  <span class="bold">Semantic chunking.</span> Instead of cutting by length,
  cut where the <em>topic</em> changes. The system embeds each sentence and
  compares neighbors. While the meaning stays similar, the sentences stay
  together, and when the similarity drops, a new chunk begins.
</p>

<p class="body-text">
  <span class="bold">Agentic chunking.</span> An AI model reads the document
  and decides where to split and what belongs together, the way a person would.
  It's the most flexible and the most expensive.
</p>

<p class="body-text">
  Whichever method you pick, you're balancing two risks.
  <span class="bold">Too small</span>, and a chunk loses its context.
  <span class="bold">Too large</span>, and it's full of irrelevant text, so
  we're back to the precision problem. A common starting point is
  <span class="bold"
    >200 to 500 characters with 50 to 100 characters of overlap</span
  >, split at sentence boundaries. Then test with real questions to see whether
  the chunks actually contain the answers.
</p>

<p class="body-text">
  We now have all the pieces. Let's see how they work together, from a pile of
  documents to a finished answer.
</p>
<script>
  import Section6Visual from "./Section6Visual.svelte";
</script>

<h1 class="body-header">Turning meaning into numbers</h1>

<p class="body-text">
  Computers can't understand meaning the way we do, but they're very good with
  numbers. So we use a trick: turn each piece of text into a list of numbers
  that captures what it <em>means</em>. That list is called an
  <span class="bold">embedding</span>, and the AI model that makes it is an
  <span class="bold">embedding model</span>.
</p>

<p class="body-text">Take three sentences:</p>

<p class="body-text"><em>"Dogs are allowed in the office."</em></p>
<p class="body-text"><em>"Pets are welcome at work."</em></p>
<p class="body-text"><em>"You can work remotely on Fridays."</em></p>

<p class="body-text">
  The embedding model turns each one into a list of numbers. You can picture
  those numbers as coordinates, like a location on a map. Sentences with
  similar meanings get placed <span class="bold">close together</span>, and
  sentences with different meanings land <span class="bold">far apart</span>.
</p>

<p class="body-text">
  On our map, the first two sentences sit side by side, because both are about
  animals at work. The third sits far away, because it's about remote work.
</p>

<Section6Visual />

<p class="body-text">
  That fixes our problem from before. "Allowance" and "reimbursement" use
  different words, but they mean nearly the same thing, so they land close
  together. Now a search can find the right page by looking for what's
  <em>nearby</em> on the map.
</p>

<p class="body-text">
  Our map has two directions, so we can draw it. A real embedding model uses
  hundreds of numbers for each sentence, so
  the "map" has hundreds of directions. We can't picture that, but the idea is
  the same: similar meaning, close together.
</p>

<p class="body-text">
  But how do we measure "close" when the map has hundreds of directions? For
  that, we need a ruler.
</p>
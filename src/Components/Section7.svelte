<script>
  import Section7Visual from "./Section7Visual.svelte";
</script>

<h1 class="body-header">Measuring how close two meanings are</h1>

<p class="body-text">
  On our map, we could <em>see</em> that two sentences were close. A real map
  has hundreds of directions, so we need a ruler that works in any number of
  them. It's called the <span class="bold">dot product</span>, and it only uses
  multiplication and addition.
</p>

<p class="body-text">
  Let's shrink each sentence to just two numbers (real ones have hundreds):
</p>

<p class="body-text">
  <em>Dogs are allowed in the office:</em> (1, 5)
</p>
<p class="body-text">
  <em>Pets are welcome at work:</em> (2, 4)
</p>
<p class="body-text">
  <em>You can work remotely on Fridays:</em> (6, 1)
</p>

<Section7Visual />

<p class="body-text">
  To compare two sentences, multiply the numbers in matching positions, then
  add the results:
</p>

<p class="body-text">
  Dogs vs pets: (1 × 2) + (5 × 4) = 2 + 20 = <span class="bold">22</span>
</p>
<p class="body-text">
  Dogs vs remote: (1 × 6) + (5 × 1) = 6 + 5 = <span class="bold">11</span>
</p>
<p class="body-text">
  Pets vs remote: (2 × 6) + (4 × 1) = 12 + 4 = <span class="bold">16</span>
</p>

<p class="body-text">
  There's one catch: bigger numbers give bigger scores just for being big. So
  the last step adjusts for size, which squeezes the score to a scale where
  <span class="bold">1 means very similar and 0 means unrelated</span>. This
  adjusted version is called <span class="bold">cosine similarity</span>.
</p>

<p class="body-text">
  Dogs vs pets: <span class="bold">0.96</span>
</p>
<p class="body-text">
  Dogs vs remote: <span class="bold">0.35</span>
</p>
<p class="body-text">
  Pets vs remote: <span class="bold">0.59</span>
</p>

<p class="body-text">
  Dogs and pets score highest, just like on our map. With a real embedding
  model, the same pattern appears: similar sentences score around 0.73, and
  unrelated ones around 0.35.
</p>

<p class="body-text">
  You never do this by hand. A library such as NumPy does it in one line.
</p>

<p class="body-text">
  That's how we compare one question with one page. But a real company has
  thousands of pages, and checking them one by one would be painfully slow. Is
  there a smarter way?
</p>
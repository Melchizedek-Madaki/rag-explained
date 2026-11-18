<script>
  import Section5Visual from "./Section5Visual.svelte";
</script>

<h1 class="body-header">When the words don't match</h1>

<p class="body-text">
  Now suppose a colleague asks the same thing in different words:
  <em>"What's the allowance for working from home?"</em>
</p>

<p class="body-text">
  To you, it's the same question. But a keyword search looks for the exact
  words <span class="bold">allowance</span> and
  <span class="bold">working from home</span>. The policy page says
  <span class="bold">reimbursement</span> and
  <span class="bold">home office</span>, so none of those words match.
</p>

<table class="count-table">
  <thead>
    <tr>
      <th>Document</th>
      <th>"allowance"</th>
      <th>"working from home"</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>Travel expenses</td><td>0</td><td>0</td><td>0</td></tr>
    <tr><td>Holiday policy</td><td>0</td><td>0</td><td>0</td></tr>
    <tr><td>Sick leave</td><td>0</td><td>0</td><td>0</td></tr>
    <tr><td>Home office setup</td><td>0</td><td>0</td><td>0</td></tr>
  </tbody>
</table>

<p class="body-text">
  Every document scores zero. The right page is there, but the search can't
  see it, because it only matches words and doesn't understand what they mean.
</p>

<Section5Visual />

<p class="body-text">
  What we need is <span class="bold">semantic search</span>: a search that
  understands <em>meaning</em>. It should know that "allowance" and
  "reimbursement" are close in meaning, and that "working from home" and "home
  office" are about the same thing, even when no word matches.
</p>

<p class="body-text">
  But how can a computer understand meaning? It can't read the way we do. The
  trick is to turn meaning into numbers.
</p>

<style>
  .count-table {
    width: 100%;
    max-width: 600px;
    margin: 1.5rem auto;
    border-collapse: collapse;
    font-size: 0.95rem;
  }
  .count-table th,
  .count-table td {
    border: 2px solid black;
    padding: 6px 10px;
    text-align: center;
  }
  .count-table td:first-child,
  .count-table th:first-child {
    text-align: left;
  }
</style>
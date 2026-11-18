<script>
  import Section4Visual from "./Section4Visual.svelte";
</script>

<h1 class="body-header">The first way to find the page: keyword search</h1>

<p class="body-text">
  The chatbot now has a job: find the right page among hundreds of documents.
  The simplest idea is the one you already use with Ctrl+F: look for the exact
  words.
</p>

<p class="body-text">
  Take the question <em>"What's the reimbursement policy for home office
  setup?"</em> We pick out the important words:
  <span class="bold">reimbursement</span> and
  <span class="bold">home office</span>. Then the system goes through every
  document and counts how often each word appears.
</p>

<table class="count-table">
  <thead>
    <tr>
      <th>Document</th>
      <th>"reimbursement"</th>
      <th>"home office"</th>
      <th>Total</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>Travel expenses</td><td>3</td><td>0</td><td>3</td></tr>
    <tr><td>Holiday policy</td><td>0</td><td>0</td><td>0</td></tr>
    <tr><td>Sick leave</td><td>0</td><td>0</td><td>0</td></tr>
    <tr class="winner">
      <td>Home office setup</td><td>3</td><td>4</td><td>7</td>
    </tr>
  </tbody>
</table>

<Section4Visual />

<p class="body-text">
  The document with the highest total wins, and here that's
  <span class="bold">Home office setup</span>. The right page is found.
</p>

<p class="body-text">
  Real systems do this a little more cleverly. Two popular methods,
  <span class="bold">TF-IDF</span> and <span class="bold">BM25</span>, give
  less weight to words that appear in every document, because they don't help
  tell documents apart, and more weight to rare, distinctive words. The idea
  stays the same: match words, count them, and rank the documents.
</p>

<p class="body-text">
  This works well, as long as the page uses the exact words you searched for.
  But what if it doesn't?
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
  .winner {
    font-weight: bold;
    background: #e8e3ff;
  }
</style>
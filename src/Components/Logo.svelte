<script>
  export let iconColor = "black";
</script>

<div id="intro-icon">
  <svg
    width="30"
    height="30"
    viewBox="0 0 30 30"
    fill="none"
    stroke={iconColor}
    stroke-width="2"
    stroke-linecap="round"
    stroke-linejoin="round"
  >
    <path d="M5 3v22h7" />
    <path d="M5 3h11l6 6v5" />
    <path d="M16 3v6h6" />
    <path d="M9 12h6" />
    <path d="M9 17h3" />
    <circle cx="19" cy="21" r="5" />
    <path d="M22.8 24.8l4.2 4.2" />
  </svg>
  <h2 class="logo">Explained with <span id="mk">MK</span></h2>
</div>

<style>
  #intro-icon {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    padding-top: 0.5rem;
    padding-bottom: 1rem;
  }

  #intro-icon h2 {
    margin: 0;
    padding-left: 0.75rem;
    font-family: var(--font-heavy);
    font-weight: 800;
    font-size: 20px;
    text-transform: uppercase;
    letter-spacing: 2px;
    color: var(--primary);
  }

  #mk {
    color: black;
  }
</style>
<script>
  // becomes true once the strip scrolls into view, which starts the light-up
  let visible = false;

  function inview(node) {
    if (typeof IntersectionObserver === "undefined") {
      visible = true;
      return {};
    }
    const obs = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          visible = true;
          obs.disconnect();
        }
      },
      { threshold: 0.6 }
    );
    obs.observe(node);
    return {
      destroy() {
        obs.disconnect();
      },
    };
  }

  const PURPLE = "#7c5aed";
  const groups = [
    {
      title: "Getting ready",
      steps: [
        { name: "Chunk", sub: "cut the documents", color: PURPLE, tint: "#e8e3ff" },
        { name: "Embed", sub: "turn text into numbers", color: PURPLE, tint: "#e8e3ff" },
        { name: "Store", sub: "save and index", color: PURPLE, tint: "#e8e3ff" },
      ],
    },
    {
      title: "Answering",
      steps: [
        { name: "Retrieve", sub: "find the closest chunks", color: "#2b8cbe", tint: "#e3f1f8" },
        { name: "Augment", sub: "add them to the prompt", color: "#e69f00", tint: "#fdf0d5" },
        { name: "Generate", sub: "write the answer", color: "#2ca25f", tint: "#dff3e6" },
      ],
    },
  ];
</script>

<div class="strip" use:inview>
  {#each groups as g, gi}
    <div class="group">
      <div class="group-title">{g.title}</div>
      <div class="boxes">
        {#each g.steps as s, si}
          <div
            class="box"
            class:lit={visible}
            style="--c: {s.color}; --t: {s.tint}; transition-delay: {(gi * 3 + si) * 450}ms"
          >
            <div class="num">{gi * 3 + si + 1}</div>
            <div class="name">{s.name}</div>
            <div class="sub">{s.sub}</div>
          </div>
        {/each}
      </div>
    </div>
  {/each}
</div>

<style>
  .strip {
    display: flex;
    gap: 1rem;
    max-width: 700px;
    margin: 1.5rem auto;
    padding: 0 1rem;
    text-align: center;
  }

  .group {
    flex: 1;
  }

  .group-title {
    font-weight: bold;
    font-size: 15px;
    margin-bottom: 0.4rem;
  }

  .boxes {
    display: flex;
    gap: 6px;
  }

  .box {
    flex: 1;
    border: 3px solid #bbb;
    background: white;
    padding: 0.5rem 0.3rem;
    opacity: 0.55;
    transition: all 500ms ease;
  }

  .box.lit {
    border-color: var(--c);
    background: var(--t);
    opacity: 1;
  }

  .num {
    display: inline-block;
    min-width: 1.4rem;
    color: white;
    background: #999;
    font-weight: bold;
    font-size: 12px;
    transition: background 500ms ease;
    transition-delay: inherit;
  }

  .box.lit .num {
    background: var(--c);
  }

  .name {
    font-weight: bold;
    font-size: 14px;
    margin-top: 0.2rem;
  }

  .sub {
    font-size: 11px;
    color: #555;
    margin-top: 0.15rem;
    line-height: 1.2;
  }

  @media screen and (max-width: 600px) {
    .strip {
      flex-direction: column;
    }
  }
</style>
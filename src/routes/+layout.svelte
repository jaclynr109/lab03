<script>
  import { page } from "$app/stores";
  import { base } from "$app/paths";

  let localStorage = globalThis.localStorage ?? {};

  let pages = [
    { url: "/", title: "About" },
    { url: "/projects", title: "Projects" },
    { url: "/resume", title: "Resume" },
    { url: "/contact", title: "Contact" },
    { url: "/meta", title: "Meta"},
    { url: "https://github.com/jaclynr109", title: "Github" }
  ];

  let colorScheme = "light dark";

  if (localStorage.colorScheme) {
  colorScheme = localStorage.colorScheme;
  }
  
  let root = globalThis.document?.documentElement;

  $: root?.style.setProperty("color-scheme", colorScheme);

  $: localStorage.colorScheme = colorScheme;
</script>

<label class="color-scheme-switch">
  Theme:
  <select bind:value={colorScheme}>
    <option value="light dark">Automatic</option>
    <option value="light">Light</option>
    <option value="dark">Dark</option>
  </select>
</label>

<nav>
  {#each pages as p}
    <a
      href={p.url.startsWith("http") ? p.url : base + p.url}
      target={p.url.startsWith("http") ? "_blank" : null}
      rel={p.url.startsWith("http") ? "noreferrer" : null}
      class:current={p.url === "/"
        ? $page.url.pathname === (base + "/")
        : $page.url.pathname.startsWith(base + p.url)}
    >
      {p.title}
    </a>
  {/each}
</nav>

<slot />

<style>
  :root {
    /* Use "dark" temporarily to test, then change to "light dark" */
    color-scheme: light dark;
  }

  body {
    background: canvas;
    color: canvastext;
  }

  nav {
    --border-color: oklch(50% 10% 200 / 40%);

    display: flex;
    margin-bottom: 1.5em;
    border-bottom: 1px solid var(--border-color);
  }

  nav a {
    flex: 1;
    text-align: center;
    padding: 0.5em;
    text-decoration: none;
    color: inherit;
  }

  nav a.current {
    border-bottom: 0.4em solid var(--border-color);
    padding-bottom: 0.1em;
  }

  nav a:hover {
    border-bottom: 0.4em solid var(--color-accent);
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
  }
  .color-scheme-switch {
  position: absolute;
  top: 1rem;
  right: 1rem;

  display: inline-flex;
  align-items: center;
  gap: 4px;

  font-size: 80%;
}
</style>
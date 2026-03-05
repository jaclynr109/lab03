<script>
  import { page } from "$app/stores";
  import { base } from "$app/paths";

  let pages = [
    { url: "/", title: "About" },
    { url: "/projects", title: "Projects" },
    { url: "/resume", title: "Resume" },
    { url: "/contact", title: "Contact" },
    { url: "https://github.com/jaclynr109", title: "Github" }
  ];
</script>

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
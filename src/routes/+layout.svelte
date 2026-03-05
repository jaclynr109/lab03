<script>
  import { base } from '$app/paths';
</script>

<nav>
  <a href={`${base}/`}>Home</a>
  <a href={`${base}/projects`}>Projects</a>
  <a href={`${base}/contact`}>Contact</a>
  <a href={`${base}/resume`}>Resume</a>
  <a href="https://github.com/jaclynr109" target="_blank" rel="noreferrer">GitHub</a>
</nav>

<slot />
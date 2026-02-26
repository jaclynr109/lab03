<svelte:head>
  <title>Contact</title>
</svelte:head>
<nav>
  <a href="../">Home</a> 
  <a href="../projects/">Projects</a> 
  <a href="../contact/" class="current">Contact</a> 
  <a href="../cv/">Resume</a> 
  <a href="https://github.com/jaclynr109" target="_blank">GitHub</a>
</nav>
<h1>Contact</h1>
<form action="mailto:183286511+jaclynr109@users.noreply.github.com" method="GET" enctype="text/plain">
  <label>
      Email:
      <input type="email" name="email" required>
    </label>
  
    <label>
      Subject:
      <input type="text" name="subject" required>
    </label>
  
    <label>
      Message:
      <textarea name="body" rows="5" cols="30" required></textarea>
    </label>

    <button>Send</button>
  </form>

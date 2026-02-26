<svelte:head>
  <title>Resume</title>
</svelte:head>

<nav>
  <a href=".">Home</a>
  <a href="projects">Projects</a>
  <a href="contact">Contact</a>
  <a class="current" href="resume">Resume</a>
  <a href="https://www.github.com/jaclynr109" target="_blank" rel="noreferrer">
    GitHub
  </a>
</nav>

<div class="resume">
  <h1>Resume</h1>

  <p>
    <a
      href="https://www.linkedin.com/in/jaclyn-rambarran-1a150113b"
      target="_blank"
      rel="noopener noreferrer"
    >
      LinkedIn
    </a>
  </p>

  <section>
    <h2>Education</h2>

    <article>
      <header>
        <h3>Massachusetts Institute of Technology</h3>
        <p>MSc Candidate – Technology &amp; Policy</p>
        <p>
          <time datetime="2024">2024</time> – <time datetime="2026">2026</time>
        </p>
      </header>
      <p>Relevant coursework: Energy Systems Modeling, Econometrics, Optimization Methods</p>
    </article>
  </section>

  <section>
    <h2>Experience</h2>

    <article>
      <header>
        <h3>Energy Systems Researcher</h3>
        <p>MIT CEEPR</p>
        <p><time datetime="2024-08">August 2024</time> – Present</p>
      </header>
      <ul>
        <li>Developed production-cost models for California using PyPSA and GenX</li>
        <li>Analyzed V2G integration in CAISO markets</li>
        <li>Presented findings to academic and policy stakeholders</li>
      </ul>
    </article>

    <article>
      <header>
        <h3>Energy Program Evaluation Supervisor</h3>
        <p>Eversource Energy</p>
        <p>
          <time datetime="2022-11">November 2022</time> –
          <time datetime="2024-08">August 2024</time>
        </p>
      </header>
      <ul>
        <li>Managed statewide evaluations of Mass Save Programs</li>
      </ul>
    </article>
  </section>

  <section>
    <h2>Skills</h2>
    <ul>
      <li>Python (pandas, matplotlib, geopandas)</li>
      <li>Julia (JuMP, optimization)</li>
      <li>Energy systems modeling (GenX, PyPSA-USA)</li>
      <li>Data visualization</li>
    </ul>
  </section>
</div>
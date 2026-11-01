---
layout: default
title: About
permalink: /about/
---

<div class="about-header">
  {% include avatar.html size="lg" photo="/assets/images/profile-about.jpg" %}
  <div>
    <h1>{{ site.author.name }}</h1>
    <p class="title">SAP Technical Consultant · Claude Certified Architect</p>
  </div>
</div>

<div class="about-body">
  <p>
    Hi there! I'm Sagar, an SAP Technical Consultant with {% include experience.html %} years of experience
    in the SAP ecosystem. I'm passionate about exploring modern SAP technologies
    and sharing what I learn along the way.
  </p>

  <p>
    My journey in SAP started with core ABAP, and over the years, I've expanded
    into newer technologies like RAP (RESTful ABAP Programming), CDS Views,
    SAP HANA, and BTP. I believe in continuous learning and staying updated
    with the evolving SAP landscape.
  </p>

  <p>
    Beyond SAP, I'm deeply interested in Generative AI and its applications.
    I work with tools like LangChain, Python, Claude, and OpenAI to build
    intelligent solutions. I'm also a certified Claude Architect, which
    reflects my commitment to mastering AI technologies.
  </p>

  <p>
    This blog is my space to share thoughts, tutorials, and experiences —
    covering SAP development, tech explorations, and life lessons. I hope
    you find something useful here!
  </p>

  <h2>Skills</h2>

  <div class="chip-list">
    <span class="chip static">ABAP</span>
    <span class="chip static">RAP</span>
    <span class="chip static">CDS Views</span>
    <span class="chip static">SAP HANA</span>
    <span class="chip static">BTP</span>
    <span class="chip static">Joule</span>
    <span class="chip static">Python</span>
    <span class="chip static">LangChain</span>
    <span class="chip static">Claude</span>
    <span class="chip static">OpenAI</span>
    <span class="chip static">Gen AI</span>
  </div>

  <h2>Get in Touch</h2>

  <p>
    Want to connect? Feel free to reach out via
    <a href="mailto:{{ site.author.email }}">email</a> or find me on
    <a href="https://github.com/{{ site.author.github }}" target="_blank" rel="noopener">GitHub</a>.
  </p>
</div>

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

  <h2>Things I've Built</h2>

  <p>A few highlights from S/4HANA implementations and support projects for global manufacturing clients:</p>

  <ul class="built-list">
    <li>
      <strong>RAP mass-maintenance app for customer master data.</strong>
      Users upload an Excel file to update customer general and partner-function data, with
      prechecks, validations, determinations, side effects and authorization checks built in.
    </li>
    <li>
      <strong>Real-time SAP-to-Kafka event replication.</strong>
      Sales orders, deliveries, invoices and customer and material master data stream out of SAP
      as JSON events (using ASAPIO), so downstream systems get changes the moment they happen.
    </li>
    <li>
      <strong>Digitally signed Adobe forms.</strong>
      GST invoices signed automatically on the server through Adobe Document Services
      (<a href="{{ '/blog/digital-signatures-in-sap-adobe-forms/' | relative_url }}">here's how</a>),
      plus delivery notes, invoices, purchase orders, certificates of analysis and Brexit invoices
      with complex tax rules.
    </li>
    <li>
      <strong>Configurable sales order enhancement.</strong>
      Automatically replaces sold-to customer data with ship-to data on sales orders, with a
      configuration table deciding which fields are swapped.
    </li>
    <li>
      <strong>Editable ALV tool for legal control data.</strong>
      An object-oriented ALV report that loads data from a file or the screen, validates it,
      lets users edit it and posts the changes with a detailed log.
    </li>
    <li>
      <strong>Bank payment files for Peru.</strong>
      DME file formats built to local banking and regulatory requirements.
    </li>
    <li>
      <strong>S/4HANA migrations, the clean core way.</strong>
      Readiness assessments and custom code remediation with ATC, using released APIs, key user
      extensibility and code pushdown to keep code cloud-ready.
    </li>
    <li>
      <strong><a href="{{ '/projects/' | relative_url }}">Grahavani</a>.</strong>
      On the Gen AI side: a Vedic astrology web app whose AI readings are checked against the
      computed chart before anyone sees them.
    </li>
  </ul>

  <h2>Skills</h2>

  <h3 class="skill-group">SAP development</h3>
  <div class="chip-list">
    <span class="chip static">ABAP on HANA</span>
    <span class="chip static">RAP</span>
    <span class="chip static">CDS Views</span>
    <span class="chip static">AMDP</span>
    <span class="chip static">OData (SEGW)</span>
    <span class="chip static">Object-oriented ABAP</span>
    <span class="chip static">ALV Reports</span>
    <span class="chip static">Enhancements &amp; BAdIs</span>
    <span class="chip static">Fiori App Extensions</span>
    <span class="chip static">Data Dictionary</span>
  </div>

  <h3 class="skill-group">Forms &amp; integration</h3>
  <div class="chip-list">
    <span class="chip static">Adobe Forms</span>
    <span class="chip static">Digital Signatures (ADS)</span>
    <span class="chip static">SmartForms</span>
    <span class="chip static">IDocs</span>
    <span class="chip static">RFC</span>
    <span class="chip static">Kafka (ASAPIO)</span>
    <span class="chip static">BDC</span>
    <span class="chip static">DME</span>
  </div>

  <h3 class="skill-group">S/4HANA &amp; code quality</h3>
  <div class="chip-list">
    <span class="chip static">S/4HANA Migration</span>
    <span class="chip static">Clean Core</span>
    <span class="chip static">ATC</span>
    <span class="chip static">Performance Tuning</span>
    <span class="chip static">BTP</span>
    <span class="chip static">Joule</span>
  </div>

  <h3 class="skill-group">Gen AI</h3>
  <div class="chip-list">
    <span class="chip static">Python</span>
    <span class="chip static">LangChain</span>
    <span class="chip static">Claude</span>
    <span class="chip static">OpenAI</span>
    <span class="chip static">Next.js</span>
    <span class="chip static">TypeScript</span>
  </div>

  <h2>Get in Touch</h2>

  <p>
    Want to connect? Feel free to reach out via
    <a href="mailto:{{ site.author.email }}">email</a> or find me on
    <a href="https://github.com/{{ site.author.github }}" target="_blank" rel="noopener">GitHub</a>.
  </p>
</div>

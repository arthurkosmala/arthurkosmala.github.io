---
layout: default
title: Home
---

<header class="hero">
  <div class="hero__content container">
    {% if site.author.photo %}
    <figure class="profile-frame">
      <img src="{{ site.author.photo | relative_url }}" alt="Portrait of {{ site.author.name }}" width="200" height="267">
    </figure>
    {% endif %}
    <h1>{{ site.author.name }}</h1>
    <p class="subtitle">PhD Student • TUM/Meta</p>
    <p class="lead">
      I am a PhD student at the <a href="https://www.cs.cit.tum.de/en/daml/home/">TUM Data Analytics and Machine Learning Group</a> (advised by Stephan Günnemann) and external research collaborator with the <a href="https://ai.meta.com/research/">Meta FAIR</a> chemistry team. In my daily work, I try to develop new computational methods for graphs and molecules that address practical bottlenecks while being rooted in the elegance of physics.
    </p>
    <div class="cta-row">
      <a class="button" href="{{ site.author.scholar_url }}">Google Scholar</a>
      <a class="button button--ghost" href="{{ '/assets/CV.pdf' | relative_url }}" target="_blank" rel="noopener noreferrer">CV (PDF)</a>
      <a class="button button--ghost" id="email-link" href="#" data-user-b64="YS5rb3NtYWxh" data-domain-b64="dHVtLmRl">Email</a>
      <a class="button button--ghost" href="https://github.com/{{ site.author.github }}">GitHub</a>
      {% if site.author.twitter %}
      <a class="button button--ghost" href="https://x.com/{{ site.author.twitter }}">X/Twitter</a>
      {% endif %}
      {% if site.author.linkedin %}
      <a class="button button--ghost" href="{{ site.author.linkedin }}">LinkedIn</a>
      {% endif %}
    </div>
  </div>
</header>

<main class="container main-grid">
  <section>
    <div class="section-header">
      <h2>Publications</h2>
    </div>

    {% assign pubs = site.data.publications.items | sort: "year" | reverse %}
    {% assign pub_groups = pubs | group_by: "year" %}
    {% if pubs and pubs.size > 0 %}
      <div class="pub-groups">
        {% for group in pub_groups %}
          <div class="year-block" data-year="{{ group.name }}">
            <h3 class="year-heading">{{ group.name }}</h3>
            <div class="pub-list">
              {% for pub in group.items %}
          {% assign authors_raw = pub.authors | default: "Unknown authors" %}
          {% assign author_list = authors_raw | split: " and " %}
          {% capture authors %}{% for author in author_list %}{% if forloop.first %}{{ author }}{% elsif forloop.last %} and {{ author }}{% else %}, {{ author }}{% endif %}{% endfor %}{% endcapture %}
          {% assign authors = authors | strip %}
          {% assign authors = authors
            | replace: "Arthur Kosmala", "<span class='author-highlight'>Arthur Kosmala</span>"
            | replace: "†", "<sup>†</sup>"
          %}

              <article class="pub-item">
                <h4 class="pub-title">
              {% if pub.url %}
                <a href="{{ pub.url }}">{{ pub.title }}</a>
              {% else %}
                {{ pub.title }}
              {% endif %}
                </h4>
                <p class="pub-authors">{{ authors }}</p>
                <p class="pub-venue">{{ pub.venue | default: "Unknown venue" }}</p>
              {% if pub.media_gif or pub.media_video %}
                <div class="pub-media">
                  {% if pub.media_gif %}
                  <img class="pub-media__gif" src="{{ pub.media_gif | relative_url }}" alt="{{ pub.title }} — method overview" loading="lazy">
                  {% endif %}
                  {% if pub.media_video %}
                  <video class="pub-media__video" controls preload="metadata" playsinline>
                    <source src="{{ pub.media_video | relative_url }}" type="video/mp4">
                    Your browser does not support the video tag.
                  </video>
                  {% endif %}
                </div>
              {% endif %}
              </article>
              {% endfor %}
            </div>
          </div>
        {% endfor %}
      </div>
      <p class="pub-footnote"><span class="footnote-mark">*</span> Equal contribution. &nbsp; <span class="footnote-mark">&dagger;</span> Shared corresponding author.</p>
    {% endif %}
  </section>
</main>

<script>
  document.addEventListener("DOMContentLoaded", function () {
    var emailLink = document.getElementById("email-link");

    if (emailLink) {
      var user = atob(emailLink.dataset.userB64 || "");
      var domain = atob(emailLink.dataset.domainB64 || "");
      if (user && domain) {
        emailLink.href = "mailto:" + user + "@" + domain;
      } else {
        emailLink.remove();
      }
    }
  });
</script>

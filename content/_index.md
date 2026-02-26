---
layout: "home"
title: ""
---

<div class="home-hero">
  <img src="/images/profile.png" alt="My profile picture">
  
<p class="typing">
    <span id="typing-text"></span>
  </p>
</div>



<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-1QTPR27FK7"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-1QTPR27FK7');
</script>




<script>
  document.addEventListener("DOMContentLoaded", () => {
  const text = "Cybersecurity | Networking | Automation";
  const speed = 95;

  const element = document.getElementById("typing-text");
  let i = 0;

  if (!element) return;

  function typeWriter() {
    if (i < text.length) {
      element.textContent += text.charAt(i);
      i++;
      setTimeout(typeWriter, speed);
    }
  }

  typeWriter();
});
</script>

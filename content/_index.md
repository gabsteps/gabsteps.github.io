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

---
layout: page
permalink: /visuals/
title: visuals
description: A growing collection of cool projects.
nav: true
nav_order: 3
---

<h2>Visual Gallery</h2>
<div class="image-grid">
  {% assign visuals = site.static_files | where_exp: "item", "item.path contains 'assets/visuals/'" %}
  {% for file in visuals %}
    {% if file.extname == '.jpg' or file.extname == '.png' or file.extname == '.jpeg' %}
      <img src="{{ file.path | relative_url }}" alt="Visual" class="grid-image" onclick="openModal(this)">
    {% endif %}
  {% endfor %}
</div>

<!-- Modal -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close">&times;</span>
  <button id="prev" class="nav-button">←</button>
  <button id="next" class="nav-button">→</button>
  <div class="modal-img-wrapper">
    <img class="modal-content zoomable" id="modal-img">
    <div id="caption" class="caption-banner">Loading caption...</div>
  </div>
</div>

<style>
.image-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 10px;
  margin-top: 20px;
}

.grid-image {
  width: 100%;
  height: auto;
  cursor: pointer;
  border-radius: 8px;
  transition: transform 0.2s ease;
}

.grid-image:hover {
  transform: scale(1.03);
}

.modal {
  display: none;
  position: fixed;
  z-index: 1000;
  left: 0; top: 0;
  width: 100%; height: 100%;
  background-color: rgba(0,0,0,0.9);
  overflow: hidden;
}

.modal-img-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  overflow: auto;
  position: relative;
}

.modal-content {
  max-width: 100%;
  max-height: 100%;
  transition: transform 0.2s ease;
  transform-origin: center center;
  cursor: grab;
}

.caption-banner {
  position: absolute;
  bottom: 0;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  padding: 10px;
  font-size: 1rem;
  text-align: center;
  display: block;
  transition: opacity 0.3s ease;
}

.caption-banner.hidden {
  opacity: 0;
  pointer-events: none;
}

.close {
  position: absolute;
  top: 30px;
  right: 45px;
  color: #fff;
  font-size: 40px;
  font-weight: bold;
  cursor: pointer;
}

.nav-button {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  font-size: 2rem;
  background: rgba(0,0,0,0.5);
  border: none;
  color: white;
  cursor: pointer;
  padding: 10px;
  z-index: 1001;
}

#prev { left: 20px; }
#next { right: 20px; }
</style>

<script>
let scale = 1;
let isDragging = false;
let startX, startY;
let currentIndex = 0;
let imageElements = [];

document.addEventListener("DOMContentLoaded", () => {
  imageElements = Array.from(document.querySelectorAll('.grid-image'));
});

function openModal(img) {
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  const caption = document.getElementById("caption");
  modal.style.display = "block";
  modalImg.src = img.src;
  scale = 1;
  modalImg.style.transform = `scale(${scale})`;
  currentIndex = imageElements.indexOf(img);
  loadCaption(img.src);
}

function closeModal(event) {
  if (event.target.id === "modal" || event.target.classList.contains("close")) {
    document.getElementById("modal").style.display = "none";
  }
}

document.addEventListener("wheel", function (e) {
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  if (modal.style.display === "block") {
    e.preventDefault();
    scale += e.deltaY * -0.001;
    scale = Math.min(Math.max(0.5, scale), 5);
    modalImg.style.transform = `scale(${scale})`;
  }
}, { passive: false });

// Drag to pan
const modalImg = document.getElementById("modal-img");
modalImg.addEventListener("mousedown", (e) => {
  isDragging = true;
  startX = e.clientX - modalImg.offsetLeft;
  startY = e.clientY - modalImg.offsetTop;
  modalImg.style.cursor = 'grabbing';
});

document.addEventListener("mousemove", (e) => {
  if (isDragging) {
    modalImg.style.position = "relative";
    modalImg.style.left = `${e.clientX - startX}px`;
    modalImg.style.top = `${e.clientY - startY}px`;
  }
});

document.addEventListener("mouseup", () => {
  isDragging = false;
  modalImg.style.cursor = 'grab';
});

// Arrows navigation
document.getElementById("prev").addEventListener("click", () => navigate(-1));
document.getElementById("next").addEventListener("click", () => navigate(1));

function navigate(direction) {
  currentIndex = (currentIndex + direction + imageElements.length) % imageElements.length;
  const newImg = imageElements[currentIndex];
  openModal(newImg);
}

// Caption loading
function loadCaption(imageSrc) {
  const caption = document.getElementById("caption");
  const baseName = imageSrc.substring(imageSrc.lastIndexOf('/') + 1, imageSrc.lastIndexOf('.'));
  const txtPath = `/assets/visuals/${baseName}.txt`;

  fetch(txtPath)
    .then(res => res.ok ? res.text() : "No caption available.")
    .then(text => caption.innerText = text)
    .catch(() => caption.innerText = "No caption available.");
}

// Toggle caption on click
document.getElementById("modal-img").addEventListener("click", () => {
  const caption = document.getElementById("caption");
  caption.classList.toggle("hidden");
});

// Hide caption on hover
document.getElementById("modal-img").addEventListener("mouseenter", () => {
  document.getElementById("caption").classList.add("hidden");
});

document.getElementById("modal-img").addEventListener("mouseleave", () => {
  document.getElementById("caption").classList.remove("hidden");
});
</script>

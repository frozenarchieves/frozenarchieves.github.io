---
layout: page
permalink: /visuals/
title: visuals
description: A growing collection of cool projects.
nav: true
nav_order: 3
display_categories: [work, fun]
horizontal: false
---

<hr>
<h2>Visual Gallery</h2>
<div class="image-grid">
  {% assign visuals = site.static_files | where: "path", "/assets/visuals" %}
  {% for file in site.static_files %}
    {% if file.path contains 'assets/visuals/' and file.extname != '.txt' %}
      <img src="{{ file.path | relative_url }}" alt="Visual" class="grid-image" onclick="openModal('{{ file.path | relative_url }}')">
    {% endif %}
  {% endfor %}
</div>

<!-- Modal for full image view -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <div class="modal-controls">
    <span class="close" onclick="closeModal(event)">&times;</span>
    <button onclick="navigate(-1)">&#8592;</button>
    <button onclick="navigate(1)">&#8594;</button>
  </div>
  <div class="modal-img-wrapper">
    <img class="modal-content zoomable" id="modal-img">
    <div id="caption-banner" class="caption-banner">Caption text</div>
  </div>
</div>

<style>
body {
  margin: 0;
  overflow-x: hidden;
}

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
  flex-direction: column;
  justify-content: center;
  align-items: center;
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
  max-width: 90%;
  max-height: 90%;
  transition: transform 0.2s ease;
  transform-origin: center center;
  cursor: grab;
}

.caption-banner {
  position: absolute;
  bottom: 0;
  width: 100%;
  background: rgba(0, 0, 0, 0.6);
  color: white;
  text-align: center;
  padding: 10px;
  font-size: 16px;
  display: block;
  transition: opacity 0.3s;
}

.caption-banner.hidden {
  opacity: 0;
  pointer-events: none;
}

.modal-controls {
  position: absolute;
  bottom: 10px;
  width: 100%;
  display: flex;
  justify-content: center;
  gap: 20px;
  z-index: 1100;
}

.modal-controls button, .close {
  background: rgba(255, 255, 255, 0.2);
  color: #fff;
  border: none;
  padding: 10px 20px;
  font-size: 24px;
  cursor: pointer;
  border-radius: 5px;
}
</style>

<script>
let scale = 1;
let currentIndex = 0;
let images = [];
document.addEventListener("DOMContentLoaded", () => {
  images = Array.from(document.querySelectorAll(".grid-image")).map(img => img.src);
});

function openModal(src) {
  currentIndex = images.indexOf(src);
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  modal.style.display = "flex";
  modalImg.src = src;
  loadCaption(src);
  scale = 1;
  modalImg.style.transform = `scale(${scale}) translate(0px, 0px)`;
}

function closeModal(event) {
  if (event.target.id === "modal" || event.target.classList.contains("close")) {
    document.getElementById("modal").style.display = "none";
  }
}

function navigate(direction) {
  currentIndex = (currentIndex + direction + images.length) % images.length;
  openModal(images[currentIndex]);
}

function loadCaption(imgSrc) {
  const base = imgSrc.split("/").pop().split(".")[0];
  fetch(`/assets/visuals/${base}.txt`)
    .then(response => response.ok ? response.text() : Promise.resolve(""))
    .then(text => {
      document.getElementById("caption-banner").innerText = text;
    });
}

document.getElementById("modal-img").addEventListener("click", () => {
  const banner = document.getElementById("caption-banner");
  banner.classList.toggle("hidden");
});

document.addEventListener("wheel", function (e) {
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  if (modal.style.display === "flex") {
    e.preventDefault();
    scale += e.deltaY * -0.001;
    scale = Math.min(Math.max(0.5, scale), 5);
    modalImg.style.transform = `scale(${scale})`;
  }
}, { passive: false });
</script>

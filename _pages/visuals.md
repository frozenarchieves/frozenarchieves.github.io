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
  {% for file in site.static_files %}
    {% if file.path contains 'assets/visuals/' and file.extname != '.txt' %}
      <img src="{{ file.path | relative_url }}" alt="Visual" class="grid-image" onclick="openModal(this)">
    {% endif %}
  {% endfor %}
</div>

<!-- Modal for full image view -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close">&times;</span>
  <div class="modal-img-wrapper">
    <div class="modal-img-container">
      <img class="modal-content zoomable" id="modal-img">
      <div class="modal-caption" id="modal-caption"></div>
    </div>
    <div class="arrow left-arrow" onclick="prevImage(event)">&#10094;</div>
    <div class="arrow right-arrow" onclick="nextImage(event)">&#10095;</div>
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
  position: relative;
}

.modal-img-container {
  position: relative;
}

.modal-content {
  max-width: 100%;
  max-height: 100%;
  transition: transform 0.2s ease;
  transform-origin: center center;
  border-radius: 10px;
}

/* Caption overlay */
.modal-caption {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  background: rgba(0, 0, 0, 0.6);
  color: #fff;
  padding: 12px;
  font-size: 16px;
  text-align: center;
  box-sizing: border-box;
  opacity: 0;
  transition: opacity 0.3s ease;
  border-bottom-left-radius: 10px;
  border-bottom-right-radius: 10px;
  pointer-events: none;
}

.modal-img-container:hover .modal-caption {
  opacity: 1;
}

.close {
  position: absolute;
  top: 30px;
  right: 45px;
  color: #fff;
  font-size: 40px;
  font-weight: bold;
  cursor: pointer;
  z-index: 1001;
}

.arrow {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  font-size: 50px;
  color: #fff;
  cursor: pointer;
  padding: 10px;
  z-index: 1001;
  user-select: none;
}

.left-arrow { left: 20px; }
.right-arrow { right: 20px; }
</style>

<script>
let currentIndex = 0;
let imageElements = [];

document.addEventListener("DOMContentLoaded", () => {
  imageElements = Array.from(document.querySelectorAll(".grid-image"));
});

function openModal(img) {
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  const caption = document.getElementById("modal-caption");

  modal.style.display = "block";
  modalImg.src = img.src;
  currentIndex = imageElements.indexOf(img);
  loadCaption(img.src);
  scale = 1;
  modalImg.style.transform = `scale(${scale})`;
}

function closeModal(event) {
  if (event.target.id === "modal" || event.target.classList.contains("close")) {
    document.getElementById("modal").style.display = "none";
  }
}

function prevImage(e) {
  e.stopPropagation();
  currentIndex = (currentIndex - 1 + imageElements.length) % imageElements.length;
  const img = imageElements[currentIndex];
  document.getElementById("modal-img").src = img.src;
  loadCaption(img.src);
}

function nextImage(e) {
  e.stopPropagation();
  currentIndex = (currentIndex + 1) % imageElements.length;
  const img = imageElements[currentIndex];
  document.getElementById("modal-img").src = img.src;
  loadCaption(img.src);
}

function loadCaption(imageSrc) {
  const basePath = imageSrc.substring(0, imageSrc.lastIndexOf('.'));
  fetch(basePath + ".txt")
    .then(response => response.ok ? response.text() : "")
    .then(text => {
      document.getElementById("modal-caption").textContent = text;
    });
}

let scale = 1;
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
</script>

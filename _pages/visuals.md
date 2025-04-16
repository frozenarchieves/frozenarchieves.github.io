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

<h2>Visual Gallery</h2>
<div class="image-grid">
  {% assign visuals = site.static_files | where_exp: "file", "file.path contains 'assets/visuals/'" %}
  {% for file in visuals %}
    {% unless file.extname == ".txt" %}
      <img 
        src="{{ file.path | relative_url }}" 
        data-caption="{{ file.path | replace: file.extname, '.txt' | relative_url }}" 
        alt="Visual" 
        class="grid-image" 
        onclick="openModal({{ forloop.index0 }})">
    {% endunless %}
  {% endfor %}
</div>

<!-- Modal for full image view -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close" onclick="closeModal(event)">&times;</span>
  <div class="arrow left-arrow" onclick="navigate(-1)">&#10094;</div>
  <div class="modal-img-wrapper">
    <img class="modal-content zoomable" id="modal-img">
    <div id="modal-caption" class="modal-caption">Loading caption...</div>
  </div>
  <div class="arrow right-arrow" onclick="navigate(1)">&#10095;</div>
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
  padding-bottom: 80px; /* Makes room for caption above footer */
}

.modal-img-wrapper {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  overflow: auto;
}

.modal-content {
  max-width: 100%;
  max-height: 100%;
  transition: transform 0.2s ease;
  transform-origin: center center;
}

.modal-caption {
  position: fixed;
  bottom: 0;
  left: 0;
  background: rgba(0,0,0,0.6);
  color: #fff;
  padding: 10px 20px;
  font-size: 16px;
  width: 100%;
  text-align: center;
  box-sizing: border-box;
  z-index: 1001;
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
const images = Array.from(document.querySelectorAll('.grid-image'));
let currentIndex = 0;
let scale = 1;

function openModal(index) {
  currentIndex = index;
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  modal.style.display = "block";
  loadImage(index);
}

function closeModal(event) {
  if (event.target.id === "modal" || event.target.classList.contains("close")) {
    document.getElementById("modal").style.display = "none";
  }
}

function navigate(direction) {
  currentIndex = (currentIndex + direction + images.length) % images.length;
  loadImage(currentIndex);
}

function loadImage(index) {
  const img = images[index];
  const modalImg = document.getElementById("modal-img");
  const captionDiv = document.getElementById("modal-caption");
  scale = 1;

  modalImg.src = img.src;
  modalImg.style.transform = `scale(${scale})`;
  captionDiv.textContent = "Loading caption...";

  fetch(img.dataset.caption)
    .then(response => response.ok ? response.text() : Promise.reject('No caption found.'))
    .then(text => {
      captionDiv.textContent = text.trim();
    })
    .catch(() => {
      captionDiv.textContent = '';
    });
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

// Keyboard navigation
document.addEventListener("keydown", function (e) {
  const modal = document.getElementById("modal");
  if (modal.style.display === "block") {
    if (e.key === "ArrowRight") navigate(1);
    if (e.key === "ArrowLeft") navigate(-1);
    if (e.key === "Escape") closeModal({ target: { id: "modal" } });
  }
});

// Swipe support for touch devices
let touchStartX = 0;
document.getElementById("modal").addEventListener("touchstart", function (e) {
  touchStartX = e.changedTouches[0].screenX;
});

document.getElementById("modal").addEventListener("touchend", function (e) {
  const touchEndX = e.changedTouches[0].screenX;
  const diff = touchEndX - touchStartX;
  if (diff > 50) navigate(-1);
  else if (diff < -50) navigate(1);
});
</script>

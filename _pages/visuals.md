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

<!-- Modal Viewer -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close">&times;</span>
  <div class="modal-img-wrapper">
    <img class="modal-content zoomable" id="modal-img">
    <div id="caption-banner" class="caption-banner">Caption will appear here</div>
    <div class="nav-arrows">
      <span class="arrow left" onclick="prevImage(event)">&#10094;</span>
      <span class="arrow right" onclick="nextImage(event)">&#10095;</span>
    </div>
    <div id="loader" class="loader"></div>
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
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  overflow: auto;
}
.modal-content {
  max-width: 90%;
  max-height: 90%;
  transition: transform 0.2s ease;
  transform-origin: center center;
  user-select: none;
  cursor: grab;
}
.caption-banner {
  position: absolute;
  bottom: 0;
  width: 100%;
  background: rgba(0, 0, 0, 0.65);
  color: #fff;
  text-align: center;
  padding: 12px 16px;
  font-size: 1rem;
  font-family: sans-serif;
  transition: opacity 0.3s ease;
  opacity: 1;
  pointer-events: none;
}
.caption-banner.hidden {
  opacity: 0;
}
.nav-arrows .arrow {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  font-size: 3em;
  color: white;
  cursor: pointer;
  padding: 10px;
  z-index: 1001;
  user-select: none;
}
.arrow.left { left: 20px; }
.arrow.right { right: 20px; }
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
.loader {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border: 8px solid #f3f3f3;
  border-top: 8px solid #3498db;
  border-radius: 50%;
  width: 60px;
  height: 60px;
  animation: spin 1s linear infinite;
  display: none;
  z-index: 1002;
}
@keyframes spin {
  0% { transform: translate(-50%, -50%) rotate(0deg); }
  100% { transform: translate(-50%, -50%) rotate(360deg); }
}
</style>

<script>
let currentIndex = 0;
let images = [];
let scale = 1, currentX = 0, currentY = 0;
let captionVisible = true;
const modal = document.getElementById("modal");
const modalImg = document.getElementById("modal-img");
const caption = document.getElementById("caption-banner");

window.onload = function() {
  images = Array.from(document.querySelectorAll(".grid-image"));
};

function openModal(img) {
  currentIndex = images.indexOf(img);
  modal.style.display = "block";
  loadImage(currentIndex);
}

function closeModal(e) {
  if (e.target.id === "modal" || e.target.classList.contains("close")) {
    modal.style.display = "none";
  }
}

function loadImage(index) {
  const loader = document.getElementById("loader");
  const img = images[index];
  const imgSrc = img.src;
  const basePath = imgSrc.split('.').slice(0, -1).join('.');
  const txtPath = `${basePath}.txt`;

  scale = 1;
  currentX = 0;
  currentY = 0;
  setTransform();

  loader.style.display = "block";
  modalImg.style.display = "none";

  const tempImg = new Image();
  tempImg.onload = () => {
    modalImg.src = imgSrc;
    modalImg.style.display = "block";
    loader.style.display = "none";
    captionVisible = true;
    caption.classList.remove("hidden");
  };
  tempImg.src = imgSrc;

  fetch(txtPath)
    .then(res => res.ok ? res.text() : "No caption available.")
    .then(text => { caption.textContent = text; });

  preloadNext(index + 1);
  preloadNext(index - 1);
}

function preloadNext(index) {
  if (index >= 0 && index < images.length) {
    const preloadImg = new Image();
    preloadImg.src = images[index].src;
  }
}

function nextImage(e) {
  e.stopPropagation();
  if (currentIndex < images.length - 1) {
    currentIndex++;
    loadImage(currentIndex);
  }
}

function prevImage(e) {
  e.stopPropagation();
  if (currentIndex > 0) {
    currentIndex--;
    loadImage(currentIndex);
  }
}

document.addEventListener("wheel", function (e) {
  if (modal.style.display === "block") {
    e.preventDefault();
    scale += e.deltaY * -0.001;
    scale = Math.min(Math.max(0.5, scale), 5);
    setTransform();
  }
}, { passive: false });

modalImg.addEventListener("mousedown", startPan);
modalImg.addEventListener("mouseup", endPan);
modalImg.addEventListener("mouseleave", endPan);
modalImg.addEventListener("mousemove", pan);

let isPanning = false;
let startX, startY;

function startPan(e) {
  isPanning = true;
  startX = e.clientX - currentX;
  startY = e.clientY - currentY;
  modalImg.style.cursor = "grabbing";
}

function endPan() {
  isPanning = false;
  modalImg.style.cursor = "grab";
}

function pan(e) {
  if (!isPanning) return;
  currentX = e.clientX - startX;
  currentY = e.clientY - startY;
  setTransform();
}

function setTransform() {
  modalImg.style.transform = `translate(${currentX}px, ${currentY}px) scale(${scale})`;
}

modalImg.addEventListener("mouseenter", () => {
  caption.classList.add("hidden");
});

modalImg.addEventListener("click", (e) => {
  e.stopPropagation();
  captionVisible = !captionVisible;
  caption.classList.toggle("hidden", !captionVisible);
});
</script>

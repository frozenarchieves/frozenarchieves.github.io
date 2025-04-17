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
  {% for file in site.static_files %}
    {% if file.path contains 'assets/visuals/' and file.extname != '.txt' %}
      <img src="{{ file.path | relative_url }}" alt="Visual" class="grid-image" onclick="openModal(this)">
    {% endif %}
  {% endfor %}
</div>

<!-- Modal for full image view -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close">&times;</span>
  <span class="nav prev" onclick="navigate(-1)">&#10094;</span>
  <span class="nav next" onclick="navigate(1)">&#10095;</span>
  <div class="modal-img-wrapper">
    <div class="loader" id="loader"></div>
    <img class="modal-content zoomable" id="modal-img">
    <div id="caption-banner" class="caption-banner">Loading caption...</div>
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
}
.modal-content {
  max-width: 90%;
  max-height: 90%;
  transition: transform 0.2s ease;
}
.close {
  position: absolute;
  top: 20px;
  right: 40px;
  font-size: 40px;
  color: white;
  cursor: pointer;
}
.nav {
  cursor: pointer;
  position: absolute;
  top: 50%;
  color: white;
  font-size: 40px;
  padding: 20px;
  user-select: none;
  z-index: 1001;
}
.prev { left: 10px; }
.next { right: 10px; }

.caption-banner {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 10px 20px;
  background: rgba(0,0,0,0.7);
  color: white;
  text-align: center;
  font-size: 16px;
  transition: opacity 0.3s ease;
}
.caption-banner.hidden {
  opacity: 0;
  pointer-events: none;
}

/* Loader animation */
.loader {
  border: 6px solid #f3f3f3;
  border-top: 6px solid #888;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
  position: absolute;
  z-index: 1002;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.modal-content {
  cursor: grab;
}

</style>

<script>
const images = Array.from(document.querySelectorAll(".grid-image"));
let currentIndex = 0;
let scale = 1;
let captionVisible = true;
let isDragging = false;
let startX, startY, currentX = 0, currentY = 0;

const modalImg = document.getElementById("modal-img");
const caption = document.getElementById("caption-banner");
const modal = document.getElementById("modal");

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

function navigate(dir) {
  currentIndex = (currentIndex + dir + images.length) % images.length;
  loadImage(currentIndex);
}

function loadImage(index) {
  const loader = document.getElementById("loader");
  const img = images[index];
  const imgSrc = img.src;
  const basePath = imgSrc.split('.').slice(0, -1).join('.');
  const txtPath = `${basePath}.txt`;

  // Reset scale and pan
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
  };
  tempImg.src = imgSrc;

  fetch(txtPath)
    .then(res => res.ok ? res.text() : "No caption available.")
    .then(text => {
      caption.textContent = text;
      caption.classList.remove("hidden");
    });

  preloadNext(index + 1);
  preloadNext(index - 1);
}

function preloadNext(idx) {
  const realIndex = (idx + images.length) % images.length;
  const src = images[realIndex].src;
  const img = new Image();
  img.src = src;
}

document.addEventListener("wheel", function (e) {
  if (modal.style.display === "block") {
    e.preventDefault();
    scale += e.deltaY * -0.001;
    scale = Math.min(Math.max(0.5, scale), 5);
    setTransform();
  }
}, { passive: false });

modalImg.addEventListener("mousedown", (e) => {
  isDragging = true;
  startX = e.clientX - currentX;
  startY = e.clientY - currentY;
  modalImg.style.cursor = "grabbing";
});

window.addEventListener("mouseup", () => {
  isDragging = false;
  modalImg.style.cursor = "grab";
});

window.addEventListener("mousemove", (e) => {
  if (isDragging) {
    currentX = e.clientX - startX;
    currentY = e.clientY - startY;
    setTransform();
  }
});

modalImg.addEventListener("touchstart", (e) => {
  if (e.touches.length === 1) {
    isDragging = true;
    startX = e.touches[0].clientX - currentX;
    startY = e.touches[0].clientY - currentY;
  }
});
modalImg.addEventListener("touchend", () => { isDragging = false; });
modalImg.addEventListener("touchmove", (e) => {
  if (isDragging && e.touches.length === 1) {
    currentX = e.touches[0].clientX - startX;
    currentY = e.touches[0].clientY - startY;
    setTransform();
  }
});

function setTransform() {
  modalImg.style.transform = `translate(${currentX}px, ${currentY}px) scale(${scale})`;
}

// Toggle caption on image click
modalImg.addEventListener("click", () => {
  captionVisible = !captionVisible;
  caption.classList.toggle("hidden", !captionVisible);
});
modalImg.addEventListener("mouseenter", () => {
  caption.classList.add("hidden");
});
modalImg.addEventListener("mouseleave", () => {
  if (captionVisible) caption.classList.remove("hidden");
});

// Swipe nav
let touchStartX = null;
modal.addEventListener("touchstart", (e) => {
  touchStartX = e.changedTouches[0].screenX;
});
modal.addEventListener("touchend", (e) => {
  if (touchStartX === null) return;
  const diff = e.changedTouches[0].screenX - touchStartX;
  if (Math.abs(diff) > 50) navigate(diff > 0 ? -1 : 1);
  touchStartX = null;
});
</script>


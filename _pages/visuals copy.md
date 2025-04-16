---
layout: page
permalink: /visuals/
title: visuals
description: backup for visuals
nav: false
nav_order: 3
display_categories: [work, fun]
horizontal: false
---

<h2>Visual Gallery</h2>
<div class="image-grid">
  {% assign visuals = site.static_files | where_exp: "file", "file.path contains 'assets/visuals/'" %}
  {% for file in visuals %}
    <img src="{{ file.path | relative_url }}" alt="Visual" class="grid-image" onclick="openModal({{ forloop.index0 }})">
  {% endfor %}
</div>

<!-- Modal for full image view -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close" onclick="closeModal(event)">&times;</span>
  <div class="arrow left-arrow" onclick="navigate(-1)">&#10094;</div>
  <div class="modal-img-wrapper">
    <img class="modal-content zoomable" id="modal-img">
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

/* Modal styles */
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
}

.modal-content {
  max-width: 100%;
  max-height: 100%;
  transition: transform 0.2s ease;
  transform-origin: center center;
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

.left-arrow {
  left: 20px;
}

.right-arrow {
  right: 20px;
}
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
  modalImg.src = images[index].src;
  scale = 1;
  modalImg.style.transform = `scale(${scale})`;
}

function closeModal(event) {
  if (event.target.id === "modal" || event.target.classList.contains("close")) {
    document.getElementById("modal").style.display = "none";
  }
}

function navigate(direction) {
  currentIndex = (currentIndex + direction + images.length) % images.length;
  const modalImg = document.getElementById("modal-img");
  modalImg.src = images[currentIndex].src;
  scale = 1;
  modalImg.style.transform = `scale(${scale})`;
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
</script>

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
  <span class="close" onclick="closeModal(event)">&times;</span>
  <span class="arrow left-arrow" onclick="prevImage(event)">&#10094;</span>
  <span class="arrow right-arrow" onclick="nextImage(event)">&#10095;</span>
  <div class="modal-img-wrapper">
    <div class="modal-img-container" id="img-container">
      <img class="modal-content" id="modal-img">
      <div class="modal-caption" id="modal-caption"></div>
    </div>
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
  left: 0;
  top: 0;
  width: 100%;
  height: 100vh;
  background-color: rgba(0,0,0,0.9);
  overflow: hidden;
}

.modal-img-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  padding: 40px;
  box-sizing: border-box;
}

.modal-img-container {
  position: relative;
  max-height: 90vh;
  max-width: 90vw;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  max-width: 100%;
  max-height: 100%;
  transition: transform 0.2s ease;
  transform-origin: center center;
  border-radius: 10px;
  display: block;
}

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
  border-bottom-left-radius: 10px;
  border-bottom-right-radius: 10px;
  pointer-events: none;
  opacity: 1;
  transition: opacity 0.3s ease;
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

@media screen and (max-width: 768px) {
  .modal-caption {
    font-size: 14px;
    padding: 10px;
  }

  .arrow {
    font-size: 40px;
  }

  .close {
    font-size: 30px;
  }
}
</style>



<script>
let scale = 1;
let currentIndex = 0;
let imageFiles = [];
let captionVisible = true;

document.addEventListener("DOMContentLoaded", () => {
  imageFiles = Array.from(document.querySelectorAll(".grid-image"));
});

function openModal(img) {
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  const caption = document.getElementById("modal-caption");
  const container = document.getElementById("img-container");

  modal.style.display = "block";
  modalImg.src = img.src;
  scale = 1;
  modalImg.style.transform = `scale(${scale})`;
  captionVisible = true;
  caption.style.opacity = 1;

  currentIndex = imageFiles.findIndex(image => image.src === img.src);
  
  // Try loading caption
  const imgName = img.src.split("/").pop().split(".")[0];
  fetch(`/assets/visuals/${imgName}.txt`)
    .then(res => res.ok ? res.text() : "")
    .then(text => caption.textContent = text)
    .catch(() => caption.textContent = "");
}

function closeModal(event) {
  if (event.target.id === "modal" || event.target.classList.contains("close")) {
    document.getElementById("modal").style.display = "none";
  }
}

function prevImage(e) {
  e.stopPropagation();
  if (imageFiles.length === 0) return;
  currentIndex = (currentIndex - 1 + imageFiles.length) % imageFiles.length;
  openModal(imageFiles[currentIndex]);
}

function nextImage(e) {
  e.stopPropagation();
  if (imageFiles.length === 0) return;
  currentIndex = (currentIndex + 1) % imageFiles.length;
  openModal(imageFiles[currentIndex]);
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

document.getElementById("img-container").addEventListener("mouseenter", () => {
  document.getElementById("modal-caption").style.opacity = 0;
});

document.getElementById("img-container").addEventListener("click", (e) => {
  e.stopPropagation();
  const caption = document.getElementById("modal-caption");
  captionVisible = !captionVisible;
  caption.style.opacity = captionVisible ? 1 : 0;
});

document.addEventListener("keydown", function (e) {
  if (document.getElementById("modal").style.display === "block") {
    if (e.key === "ArrowRight") nextImage(e);
    else if (e.key === "ArrowLeft") prevImage(e);
    else if (e.key === "Escape") closeModal(e);
  }
});
</script>

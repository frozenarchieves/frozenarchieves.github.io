---
layout: page
title: visuals
permalink: assets/visuals/
description: A growing collection of cool projects.
nav: true
nav_order: 3
display_categories: [work, fun]
horizontal: false
---



Test visuals page
<!-- Modal for full image view -->
<div id="modal" class="modal" onclick="closeModal(event)">
  <span class="close">&times;</span>
  <div class="modal-img-wrapper">
    <img class="modal-content zoomable" id="modal-img">
  </div>
</div>

<style>
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
</style>

<script>
let scale = 1;

function openModal(img) {
  const modal = document.getElementById("modal");
  const modalImg = document.getElementById("modal-img");
  modal.style.display = "block";
  modalImg.src = img.src;
  scale = 1;
  modalImg.style.transform = `scale(${scale})`;
}

function closeModal(event) {
  // Only close if clicking outside the image
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
    scale = Math.min(Math.max(0.5, scale), 5); // Limit zoom range
    modalImg.style.transform = `scale(${scale})`;
  }
}, { passive: false });
</script>

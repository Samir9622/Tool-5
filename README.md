<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Image Compression Tool</title>
  <link rel="stylesheet" href="style.css"/>
</head>
<body>
  <div id="home-screen">
    <h1>Welcome to Image Compression Tool</h1>
    <button id="toolBtn">
      <img src="https://cdn-icons-png.flaticon.com/512/753/753345.png" alt="Tool Icon" />
      Launch Tool
    </button>
  </div>

  <div id="compressor-screen" class="hidden">
    <h2>Image Compression Tool</h2>
    <div id="upload-area">
      <input type="file" id="imageInput" accept="image/*" />
      <p>or drag & drop an image here</p>
    </div>
    <div id="preview">
      <img id="originalPreview" alt="Original Preview"/>
      <img id="compressedPreview" alt="Compressed Preview"/>
    </div>
    <div class="controls">
      <label for="quality">Compression Quality:</label>
      <input type="range" id="quality" min="10" max="100" value="80" />
    </div>
    <div class="actions">
      <button id="compressBtn">Compress</button>
      <button id="downloadBtn" disabled>Download</button>
    </div>
  </div>

  <script src="script.js"></script>
</body>
</html>
body {
  font-family: 'Segoe UI', sans-serif;
  margin: 0;
  padding: 0;
  background: #f4f4f4;
  text-align: center;
}

#home-screen, #compressor-screen {
  padding: 40px;
}

.hidden {
  display: none;
}

#toolBtn {
  background-color: #0c73b8;
  color: #fff;
  border: none;
  padding: 15px 25px;
  font-size: 18px;
  border-radius: 10px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 10px;
}

#toolBtn img {
  width: 32px;
  height: 32px;
}

#upload-area {
  border: 2px dashed #aaa;
  padding: 40px;
  margin: 20px auto;
  width: 300px;
  cursor: pointer;
  background-color: #fff;
}

#upload-area.dragover {
  background-color: #e0f7fa;
}

#upload-area input {
  display: none;
}

#preview {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin: 20px 0;
}

#preview img {
  max-height: 200px;
  max-width: 45%;
  border: 1px solid #ccc;
  background-color: #fff;
}

.controls, .actions {
  margin: 20px 0;
}

input[type=range] {
  width: 200px;
}
const toolBtn = document.getElementById("toolBtn");
const homeScreen = document.getElementById("home-screen");
const toolScreen = document.getElementById("compressor-screen");

toolBtn.onclick = () => {
  homeScreen.classList.add("hidden");
  toolScreen.classList.remove("hidden");
};

const uploadArea = document.getElementById("upload-area");
const imageInput = document.getElementById("imageInput");
const qualitySlider = document.getElementById("quality");
const compressBtn = document.getElementById("compressBtn");
const downloadBtn = document.getElementById("downloadBtn");

const originalPreview = document.getElementById("originalPreview");
const compressedPreview = document.getElementById("compressedPreview");

let originalFile;

uploadArea.addEventListener("click", () => imageInput.click());
uploadArea.addEventListener("dragover", e => {
  e.preventDefault();
  uploadArea.classList.add("dragover");
});
uploadArea.addEventListener("dragleave", () => uploadArea.classList.remove("dragover"));
uploadArea.addEventListener("drop", e => {
  e.preventDefault();
  uploadArea.classList.remove("dragover");
  imageInput.files = e.dataTransfer.files;
  handleImage(imageInput.files[0]);
});

imageInput.addEventListener("change", () => {
  handleImage(imageInput.files[0]);
});

function handleImage(file) {
  if (!file.type.startsWith("image/")) return;
  originalFile = file;
  originalPreview.src = URL.createObjectURL(file);
}

compressBtn.addEventListener("click", () => {
  if (!originalFile) return;

  const quality = qualitySlider.value;
  const reader = new FileReader();

  reader.onload = function (e) {
    const img = new Image();
    img.onload = function () {
      const canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;

      const ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);

      canvas.toBlob(
        blob => {
          const compressedUrl = URL.createObjectURL(blob);
          compressedPreview.src = compressedUrl;

          downloadBtn.href = compressedUrl;
          downloadBtn.download = "compressed.jpg";
          downloadBtn.disabled = false;
        },
        "image/jpeg",
        quality / 100
      );
    };
    img.src = e.target.result;
  };

  reader.readAsDataURL(originalFile);
});

downloadBtn.addEventListener("click", () => {
  downloadBtn.disabled = true;
});

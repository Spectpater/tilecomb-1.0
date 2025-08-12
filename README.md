<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Tilemap Combiner</title>
<style>
    body {
        font-family: sans-serif;
        background: #222;
        color: #fff;
        text-align: center;
        padding: 20px;
    }
    #dropzone {
        border: 2px dashed #888;
        padding: 40px;
        margin: 20px auto;
        width: 300px;
        background: #333;
    }
    canvas {
        margin-top: 20px;
        background: transparent;
        image-rendering: pixelated;
    }
    input {
        margin: 5px;
        padding: 5px;
        width: 80px;
    }
</style>
</head>
<body>
<h1>Tilemap Combiner</h1>
<p>Drop PNG tiles here → Choose size/columns → Download tilemap.</p>

<div id="dropzone">Drop PNG files here</div>
<div>
    Tile Size: <input type="number" id="tileSize" value="16">
    Columns: <input type="number" id="columns" value="4">
</div>
<canvas id="canvas"></canvas><br>
<button id="downloadBtn" style="display:none;">Download Tilemap</button>

<script>
const dropzone = document.getElementById('dropzone');
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
const downloadBtn = document.getElementById('downloadBtn');

let filesArray = [];

dropzone.addEventListener('dragover', e => {
    e.preventDefault();
    dropzone.style.borderColor = '#0f0';
});
dropzone.addEventListener('dragleave', e => {
    e.preventDefault();
    dropzone.style.borderColor = '#888';
});
dropzone.addEventListener('drop', e => {
    e.preventDefault();
    dropzone.style.borderColor = '#888';
    filesArray = Array.from(e.dataTransfer.files).filter(f => f.type.includes("png"));
    loadImages(filesArray);
});

function loadImages(files) {
    const imgs = [];
    let loaded = 0;

    files.forEach((file, index) => {
        const img = new Image();
        img.onload = () => {
            imgs[index] = img; // keep original order
            loaded++;
            if (loaded === files.length) {
                makeTilemap(imgs);
            }
        };
        img.src = URL.createObjectURL(file);
    });
}

function makeTilemap(imgs) {
    const tileSize = parseInt(document.getElementById('tileSize').value);
    const cols = parseInt(document.getElementById('columns').value);
    const rows = Math.ceil(imgs.length / cols);

    canvas.width = cols * tileSize;
    canvas.height = rows * tileSize;

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    imgs.forEach((img, i) => {
        const x = (i % cols) * tileSize;
        const y = Math.floor(i / cols) * tileSize;
        ctx.drawImage(img, x, y, tileSize, tileSize);
    });

    downloadBtn.style.display = 'inline-block';
}

downloadBtn.addEventListener('click', () => {
    const link = document.createElement('a');
    link.download = 'tilemap.png';
    link.href = canvas.toDataURL();
    link.click();
});
</script>
</body>
</html>

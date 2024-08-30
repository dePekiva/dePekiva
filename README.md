- 👋 Hi, I’m @dePekiva
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
dePekiva/dePekiva is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
/my-roblox-designer
    /assets
        /templates
            - shirt_template.png
            - pants_template.png
        /textures
            - texture1.png
            - texture2.png
    /src
        - index.html
        - styles.css
        - script.js
        - three.min.js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Roblox Clothing & Accessories Designer</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <header>
        <h1>Roblox Clothing & Accessories Designer</h1>
        <nav>
            <button onclick="showSection('clothing')">Clothing</button>
            <button onclick="showSection('accessories')">Accessories</button>
            <button onclick="showSection('preview')">3D Preview</button>
        </nav>
    </header>

    <!-- Sección de Diseño de Ropa -->
    <section id="clothing" class="editor-section">
        <h2>Clothing Designer</h2>
        <div class="editor-container">
            <canvas id="clothingCanvas" width="512" height="512"></canvas>
            <div class="controls">
                <label for="colorPicker">Color:</label>
                <input type="color" id="colorPicker" value="#000000">
                
                <label for="brushSize">Brush Size:</label>
                <input type="range" id="brushSize" min="1" max="20" value="5">

                <label for="shapes">Shapes:</label>
                <select id="shapes">
                    <option value="free">Freehand</option>
                    <option value="rectangle">Rectangle</option>
                    <option value="circle">Circle</option>
                </select>

                <button id="clearClothingCanvas">Clear</button>
                <button id="downloadClothingDesign">Download</button>
            </div>
        </div>
    </section>

    <!-- Sección de Diseño de Accesorios -->
    <section id="accessories" class="editor-section">
        <h2>Accessories Designer</h2>
        <div class="editor-container">
            <canvas id="accessoriesCanvas" width="512" height="512"></canvas>
            <div class="controls">
                <label for="accessoryColorPicker">Color:</label>
                <input type="color" id="accessoryColorPicker" value="#000000">
                
                <label for="accessoryBrushSize">Brush Size:</label>
                <input type="range" id="accessoryBrushSize" min="1" max="20" value="5">

                <label for="accessoryShapes">Shapes:</label>
                <select id="accessoryShapes">
                    <option value="free">Freehand</option>
                    <option value="rectangle">Rectangle</option>
                    <option value="circle">Circle</option>
                </select>

                <button id="clearAccessoriesCanvas">Clear</button>
                <button id="downloadAccessoryDesign">Download</button>
            </div>
        </div>
    </section>

    <!-- Sección de Previsualización 3D -->
    <section id="preview" class="editor-section">
        <h2>3D Preview</h2>
        <div id="preview3D" class="preview-container"></div>
    </section>

    <script src="three.min.js"></script>
    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f0f0f0;
}

header {
    background-color: #333;
    color: white;
    padding: 10px;
    text-align: center;
}

nav button {
    margin: 0 5px;
    padding: 10px 20px;
    background-color: #555;
    color: white;
    border: none;
    cursor: pointer;
}

nav button:hover {
    background-color: #777;
}

.editor-section {
    display: none;
    padding: 20px;
}

.editor-container {
    text-align: center;
}

canvas {
    border: 1px solid #ccc;
    background-color: #fff;
    cursor: crosshair;
}

.controls {
    margin-top: 10px;
}

button {
    margin-left: 5px;
    padding: 5px 10px;
    font-size: 14px;
}

.preview-container {
    width: 100%;
    height: 512px;
    background-color: #000;
}
document.addEventListener('DOMContentLoaded', () => {
    const sections = document.querySelectorAll('.editor-section');
    const showSection = (sectionId) => {
        sections.forEach(section => section.style.display = 'none');
        document.getElementById(sectionId).style.display = 'block';
    };
    showSection('clothing');

    // Canvas elements
    const clothingCanvas = document.getElementById('clothingCanvas');
    const accessoriesCanvas = document.getElementById('accessoriesCanvas');
    const ctxClothing = clothingCanvas.getContext('2d');
    const ctxAccessories = accessoriesCanvas.getContext('2d');

    // Variables para manejar el dibujo
    let drawing = false;
    let currentShape = 'free';
    let brushSize = 5;
    let currentColor = '#000000';

    // Lógica de dibujo para ropa
    clothingCanvas.addEventListener('mousedown', startDrawingClothing);
    clothingCanvas.addEventListener('mouseup', stopDrawingClothing);
    clothingCanvas.addEventListener('mousemove', drawOnClothing);

    // Lógica de dibujo para accesorios
    accessoriesCanvas.addEventListener('mousedown', startDrawingAccessories);
    accessoriesCanvas.addEventListener('mouseup', stopDrawingAccessories);
    accessoriesCanvas.addEventListener('mousemove', drawOnAccessories);

    // Control de pincel y color
    document.getElementById('colorPicker').addEventListener('input', e => currentColor = e.target.value);
    document.getElementById('brushSize').addEventListener('input', e => brushSize = e.target.value);
    document.getElementById('shapes').addEventListener('change', e => currentShape = e.target.value);
    
    // Previsualización 3D usando Three.js
    initThreeJS();

    // Implementación de funciones
    function startDrawingClothing(e) {
        drawing = true;
        ctxClothing.beginPath();
        ctxClothing.moveTo(e.offsetX, e.offsetY);
    }

    function stopDrawingClothing() {
        drawing = false;
    }

    function drawOnClothing(e) {
        if (!drawing) return;

        ctxClothing.lineWidth = brushSize;
        ctxClothing.strokeStyle = currentColor;

        if (currentShape === 'free') {
            ctxClothing.lineTo(e.offsetX, e.offsetY);
            ctxClothing.stroke();
        } else if (currentShape === 'rectangle') {
            ctxClothing.rect(e.offsetX, e.offsetY, brushSize, brushSize);
            ctxClothing.stroke();
        } else if (currentShape === 'circle') {
            ctxClothing.arc(e.offsetX, e.offsetY, brushSize, 0, Math.PI * 2);
            ctxClothing.stroke();
        }
    }

    // Funciones similares para accesorios
    function startDrawingAccessories(e) {
        drawing = true;
        ctxAccessories.beginPath();
        ctxAccessories.moveTo(e.offsetX, e.offsetY);
    }

    function stopDrawingAccessories() {
        drawing = false;
    }

    function drawOnAccessories(e) {
        if (!drawing) return;

        ctxAccessories.lineWidth = brushSize;
        ctxAccessories.strokeStyle = currentColor;

        if (currentShape === 'free') {
            ctxAccessories.lineTo(e.offsetX, e.offsetY);
            ctxAccessories.stroke();
        } else if (currentShape === 'rectangle') {
            ctxAccessories.rect(e.offsetX, e.offsetY, brushSize, brushSize);
            ctxAccessories.stroke();
        } else if (currentShape === 'circle') {
            ctxAccessories.arc(e.offsetX, e.offsetY, brushSize, 0, Math.PI * 2);
            ctxAccessories.stroke();
        }
    }

    // Inicialización de Three.js para la previsualización 3D
    function initThreeJS() {
        const previewContainer = document.getElementById('preview

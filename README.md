<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Universal Template Editor</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/fabric.js/5.3.1/fabric.min.js"></script>

<style>

*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    font-family:Arial;
    background:#f0f2f5;
}

.header{
    background:#111827;
    color:white;
    padding:15px;
    text-align:center;
    font-size:24px;
    font-weight:bold;
}

.toolbar{
    display:flex;
    flex-wrap:wrap;
    gap:10px;
    padding:15px;
    background:white;
    box-shadow:0 2px 10px rgba(0,0,0,0.1);
}

.toolbar button,
.toolbar input,
.toolbar select{
    padding:10px;
    border:none;
    border-radius:5px;
    background:#e5e7eb;
    cursor:pointer;
}

.toolbar button:hover{
    background:#d1d5db;
}

.canvas-container{
    display:flex;
    justify-content:center;
    align-items:center;
    margin-top:20px;
}

canvas{
    border:2px solid #ccc;
    background:white;
}

</style>
</head>

<body>

<div class="header">
Universal Template Editor
</div>

<div class="toolbar">

<input type="file" id="uploadFile">

<button onclick="addText()">Add Text</button>

<button onclick="addRectangle()">Rectangle</button>

<button onclick="addCircle()">Circle</button>

<button onclick="duplicateObject()">Duplicate</button>

<button onclick="deleteObject()">Delete</button>

<input type="color" id="colorPicker">

<select id="fontSize">
<option value="20">20</option>
<option value="30">30</option>
<option value="40">40</option>
<option value="50">50</option>
<option value="60">60</option>
</select>

<button onclick="bringFront()">Bring Front</button>

<button onclick="sendBack()">Send Back</button>

<button onclick="saveTemplate()">Save</button>

<button onclick="loadTemplate()">Load</button>

<button onclick="clearCanvas()">Clear</button>

<button onclick="exportPNG()">Export PNG</button>

<button onclick="exportJPG()">Export JPG</button>

</div>

<div class="canvas-container">
<canvas id="canvas" width="1200" height="700"></canvas>
</div>

<script>

const canvas = new fabric.Canvas('canvas');

canvas.selection = true;

document.getElementById('uploadFile').addEventListener('change', function(e){

    const file = e.target.files[0];

    if(!file) return;

    const reader = new FileReader();

    reader.onload = function(f){

        const data = f.target.result;

        fabric.Image.fromURL(data, function(img){

            img.set({
                left:100,
                top:100,
                cornerColor:'blue',
                borderColor:'red',
                cornerSize:10
            });

            img.scaleToWidth(400);

            canvas.add(img);

            canvas.setActiveObject(img);

        });

    };

    reader.readAsDataURL(file);

});

function addText(){

    const text = new fabric.Textbox('Edit Text',{

        left:100,
        top:100,
        width:300,
        fontSize:40,
        fill:'#000',
        fontWeight:'bold',
        editable:true

    });

    canvas.add(text);

    canvas.setActiveObject(text);

}

function addRectangle(){

    const rect = new fabric.Rect({

        left:150,
        top:150,
        fill:'red',
        width:200,
        height:100

    });

    canvas.add(rect);

}

function addCircle(){

    const circle = new fabric.Circle({

        radius:50,
        fill:'blue',
        left:200,
        top:200

    });

    canvas.add(circle);

}

function deleteObject(){

    const active = canvas.getActiveObject();

    if(active){

        canvas.remove(active);

    }

}

function duplicateObject(){

    const active = canvas.getActiveObject();

    if(!active) return;

    active.clone(function(cloned){

        cloned.set({

            left:active.left + 20,
            top:active.top + 20

        });

        canvas.add(cloned);

    });

}

document.getElementById('colorPicker').addEventListener('change', function(){

    const active = canvas.getActiveObject();

    if(active){

        active.set('fill', this.value);

        canvas.renderAll();

    }

});

document.getElementById('fontSize').addEventListener('change', function(){

    const active = canvas.getActiveObject();

    if(active && active.type === 'textbox'){

        active.set('fontSize', parseInt(this.value));

        canvas.renderAll();

    }

});

function bringFront(){

    const active = canvas.getActiveObject();

    if(active){

        canvas.bringToFront(active);

    }

}

function sendBack(){

    const active = canvas.getActiveObject();

    if(active){

        canvas.sendToBack(active);

    }

}

function saveTemplate(){

    const json = JSON.stringify(canvas.toJSON());

    localStorage.setItem('template_editor', json);

    alert('Template Saved');

}

function loadTemplate(){

    const saved = localStorage.getItem('template_editor');

    if(saved){

        canvas.loadFromJSON(saved, function(){

            canvas.renderAll();

            alert('Template Loaded');

        });

    }

}

function clearCanvas(){

    canvas.clear();

    canvas.backgroundColor = 'white';

}

function exportPNG(){

    const url = canvas.toDataURL({

        format:'png',
        quality:1

    });

    const a = document.createElement('a');

    a.href = url;

    a.download = 'design.png';

    a.click();

}

function exportJPG(){

    const url = canvas.toDataURL({

        format:'jpeg',
        quality:1

    });

    const a = document.createElement('a');

    a.href = url;

    a.download = 'design.jpg';

    a.click();

}

window.addEventListener('keydown', function(e){

    if(e.key === 'Delete'){

        deleteObject();

    }

});

</script>

</body>
</html>

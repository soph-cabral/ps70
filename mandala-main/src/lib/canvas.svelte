
<script lang="ts">
    import { onMount } from "svelte";
    import opentype from 'opentype.js';
  
    export let width = 800;
    export let height = 400;
    export let color = "#333";
    export let background = "#fff";
    export let inputText = "";
  
    let canvas;
    let context;
    let coords = [];
  
    let t, l;
  
    onMount(() => {
      context = canvas.getContext("2d");
      context.lineWidth = 4;
  
      handleSize();
    });
  
    const handleStart = ({ offsetX: x, offsetY: y }) => {
      coords.push({ x, y });
    };
  
    const handleEnd = () => {
      // Do nothing for now
    };
  
    const handleMove = ({ offsetX: x, offsetY: y }) => {
      coords.push({ x, y });
    };
  
    const handleSize = () => {
      const { top, left } = canvas.getBoundingClientRect();
      t = top;
      l = left;
    };
  
    const clear = () => {
      context.clearRect(0, 0, width, height);
      coords = [];
    };
  
    const save = () => {
      // Convert coords array to CSV string
      let csvContent = "data:text/csv;charset=utf-8,";
      csvContent += "X,Y\n"; // Column headers
      coords.forEach((coord) => {
        csvContent += `${coord.x},${coord.y}\n`;
      });
  
      // Create a link and trigger download
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "coords.csv");
      document.body.appendChild(link); // Required for Firefox
  
      link.click(); // This will download the file
      document.body.removeChild(link); // Clean up
    };
  
    $: {
      if (inputText) {
        draw(inputText);
      }
    }
    
    const draw = (text) => {
      context.clearRect(0, 0, width, height); // Clear the canvas before drawing
  
      // Load the font file
      opentype.load('/src/lib/FlightySingleLine-0W934.ttf', function(err, font) {
        if (err) {
          console.error('Font loading error:', err);
          return;
        }
  
        let x = 50; // Initial x position
        const y = 150; // Fixed y position
  
        // Iterate through each character in the text
        for (let i = 0; i < text.length; i++) {
          const char = text.charAt(i);
          const glyph = font.charToGlyph(char);
          const path = glyph.getPath(x, y, 150); // 48 is the font size
  
          // Create a Path2D object from the glyph path
          const path2D = new Path2D(path.toPathData());
  
          // Draw the Path2D object onto the canvas
          context.stroke(path2D);
  
          // Move x to the next character position
          x += glyph.advanceWidth * 150 / font.unitsPerEm;
          
          
          for (let j = 0; j < path.commands.length; j++) {
          const cmd = path.commands[j];
          if (cmd.type === 'M' || cmd.type === 'L') { // MoveTo or LineTo commands
            coords.push({ x: cmd.x, y: cmd.y });
          } else if (cmd.type === 'Q' || cmd.type === 'C') { // QuadraticCurveTo or CubicCurveTo commands
            coords.push({ x: cmd.x1, y: cmd.y1 });
            coords.push({ x: cmd.x2, y: cmd.y2 });
            coords.push({ x: cmd.x, y: cmd.y });
          }
        }
      }
    });
  };
  </script>
  
  <svelte:window on:resize={handleSize} />
  <div>
    <button on:click={clear} class="clear">Clear</button>
  </div>
  <div>
    <canvas
      id="canvas"
      {width}
      {height}
      style:background
      bind:this={canvas}
      on:mousedown={handleStart}
      on:touchstart={(e) => {
        const { clientX, clientY } = e.touches[0];
        handleStart({
          offsetX: clientX - l,
          offsetY: clientY - t,
        });
      }}
      on:mouseup={handleEnd}
      on:touchend={handleEnd}
      on:mouseleave={handleEnd}
      on:mousemove={handleMove}
      on:touchmove={(e) => {
        const { clientX, clientY } = e.touches[0];
        handleMove({
          offsetX: clientX - l,
          offsetY: clientY - t,
        });
      }}
    />
  </div>
  <button on:click={save}>Save</button>
  
  <style>
    canvas {
      border-radius: 15px;
    }
    button {
      border-radius: 10px;
      font-size: 20pt;
      background-color: black;
      border: none;
      color: green;
      padding: 15px 32px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-family: "Spectral", serif;
    }
    .clear {
      color: red;
    }
  </style>
  
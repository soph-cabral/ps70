<!-- Your page file -->
<script lang="ts">
    import Canvas from "$lib/canvas.svelte";
    import { onMount } from "svelte";
  
    let inputText = "";
    let canvas;
  
    onMount(() => {
      canvas = document.getElementById("canvas"); // Retrieve canvas element
    });
  
    const draw = (text, canvas) => {
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear the canvas before drawing
      ctx.font = "48px serif";
      ctx.fillText(text, 10, 50);
    };
  </script>
  
  <body>
    <h1>start</h1>
    <Canvas {draw} {inputText} />
    <h1>end</h1>
    <br />
  
    <input type="text" bind:value={inputText} placeholder="Enter text to draw" />
    <button on:click={() => draw(inputText, canvas)}> Draw </button>
  </body>
  
  <style>
    @import url("https://fonts.googleapis.com/css2?family=Spectral:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;1,200;1,300;1,400;1,500;1,600;1,700;1,800&display=swap");
    h1 {
      font-family: "Spectral", serif;
      color: white;
    }
    body {
      margin: 0;
      padding: 0;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: black;
    }
    * {
      font-family: "Spectral", serif;
    }
  
    input[type="text"] {
      border-radius: 5px;
      padding: 10px;
      font-size: 16px;
      margin-right: 10px;
    }
  </style>
  
  
  

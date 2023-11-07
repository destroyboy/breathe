<style>

  body {
    width:  100%;
    height: 100%;
    overflow: hidden;
    display: block;
    margin: 0;
  }

  canvas {
    margin: 0;
    border: none;
    width: 100%;
    height: 100%;
    display: block; /* this is IMPORTANT! */
  }

</style>
<script lang="ts">
  import { onMount } from "svelte"

  onMount(async () => {
    const canvas = <HTMLCanvasElement> document.getElementById("canvas");
    const ctx = canvas.getContext("2d");


    const observer = new ResizeObserver((entries) => {
      
      const entry = entries.find((entry) => entry.target === canvas)
      if (entry!=undefined) {
        canvas.width = entry.devicePixelContentBoxSize[0].inlineSize;
        canvas.height = entry.devicePixelContentBoxSize[0].blockSize;
      }

      ctx?.beginPath();
      ctx?.arc(2*100, 2*75, 2*50, 0, 2* 2 * Math.PI);
      ctx?.stroke();
    });

    observer.observe(canvas)
  })
</script>
<body>
    <canvas id="canvas"></canvas>
</body>


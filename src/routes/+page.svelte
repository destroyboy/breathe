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
        canvas.width = entry.devicePixelContentBoxSize[0].inlineSize
        canvas.height = entry.devicePixelContentBoxSize[0].blockSize
      }

      ctx?.beginPath();
      ctx?.arc(2*100, 2*75, 2*50, 0, 2* 2 * Math.PI);
      ctx?.stroke();
    });

    observer.observe(canvas)

  })

  function canvasMouseMove(e: MouseEvent) {
    const canvas = ( <HTMLCanvasElement>e.target )
    const ctx = canvas.getContext("2d");
    const clientWidth = canvas.getBoundingClientRect().right - canvas.getBoundingClientRect().left
    const clientHeight = canvas.getBoundingClientRect().bottom - canvas.getBoundingClientRect().top
    const x = e.pageX * canvas.width / clientWidth
    const y = e.pageY * canvas.height / clientHeight
    console.log(clientWidth, canvas.width )
    if (ctx) {
      const r = 0
      const g = 0
      const b = 0
      const a = 255
      ctx.fillStyle = "rgba("+r+","+g+","+b+","+(a/255)+")";
      ctx.fillRect( x, y,  2, 2 );
    }
    
    //console.log(e)
    //let x = e.pageX
    //let y = e.pageY
    
  }
</script>
<body>
    <canvas on:mousemove={canvasMouseMove} on:focus id="canvas"></canvas>
</body>


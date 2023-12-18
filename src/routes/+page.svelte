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

  function drawRoundedPolygon(  ctx: CanvasRenderingContext2D,
                                x: number,
                                y: number,
                                radius: number,
                                rotation: number,
                                cornerPercent: number,
                                color: string,
                                count: number ) {


    function getPolygonCorner( index: number, count: number ) {
      const angle = ( index + 0.5 ) * 2 * Math.PI / count
      return [ Math.sin( angle ), Math.cos( angle ) ]
    }

    function blend( p1: number[], p2: number[], t: number ) {
      return [ p1[ 0 ] * ( 1 - t ) + p2[ 0 ] * ( t ),
               p1[ 1 ] * ( 1 - t ) + p2[ 1 ] * ( t ) ]
    }

    ctx.save( )
    ctx.translate( x, y )
    ctx.scale( radius, radius )
    ctx.rotate( rotation * Math.PI / 180 )
    ctx.beginPath( )

    const points = []

    for ( let i = 0; i < count; i++ )
      points.push( getPolygonCorner( i, count ) )
    
    for ( let i = 0; i < count; i++ ) {

      const prevCorner = points[ ( i + 0 ) % count ]
      const thisCorner = points[ ( i + 1 ) % count ]
      const nextCorner = points[ ( i + 2 ) % count ]

      const q1 = blend( thisCorner, prevCorner, cornerPercent / 200 )
      const q2 = blend( thisCorner, nextCorner, cornerPercent / 200 )

      ctx.lineTo( q1[ 0 ], q1[ 1 ] );
      ctx.quadraticCurveTo( thisCorner[ 0 ], thisCorner[ 1 ], q2[ 0 ], q2[ 1 ] ) 
    }
    
    ctx.closePath( );
    ctx.shadowBlur = 50
	  ctx.shadowColor=  color
	  ctx.shadowOffsetX = ctx.shadowOffsetY = 0
    ctx.fillStyle = color
    ctx.fill( );
    ctx.restore( )
}

  onMount(async () => {
    const canvas = <HTMLCanvasElement> document.getElementById("canvas");
    const ctx: CanvasRenderingContext2D  = canvas.getContext("2d")!;

    function doDraw() {
      ctx.canvas.width  = window.innerWidth;
      ctx.canvas.height = window.innerHeight;
      drawRoundedPolygon(ctx, canvas.width/2, canvas.height/2, canvas.height/4, 45, 33, "#E49641", 6)
    }

    function step(time: number) {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      doDraw()
      requestAnimationFrame(step)
    }

    requestAnimationFrame(step)
  })
</script>
<body>
    <canvas style="position:absolute;" on:focus id="canvas"></canvas>
</body>
<link href='https://fonts.googleapis.com/css?family=Open Sans' rel='stylesheet'>
<link href='https://fonts.googleapis.com/css?family=Montserrat' rel='stylesheet'>
<link href='https://fonts.googleapis.com/css?family=Roboto' rel='stylesheet'>
<link href='https://fonts.googleapis.com/css?family=B612 Mono' rel='stylesheet'>
 
<style>

  body {
    width:  100%;
    height: 100%;
    overflow: hidden;
    display: block;
    margin: 0;
    font-family: 'B612 Mono';font-size: 22px;
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

  function drawRoundedRegularPolygon(
    ctx: CanvasRenderingContext2D,
    radius: number,
    rotation: number,
    cornerPercent: number,
    shadowBlur: number,
    color: string,
    shadowColor: string, 
    numberOfCorners: number ) {

    function getRegularPolygonCorner( index: number, numberOfCorners: number ): number[] {
      const angle = ( index + 0.5 ) * 2 * Math.PI / numberOfCorners
      return [ Math.sin( angle ), Math.cos( angle ) ]
    }

    const corners = []

    for (let i=0; i<numberOfCorners; i++)
      corners.push( getRegularPolygonCorner( i, numberOfCorners) )


    ctx.save( )
    ctx.scale( radius, radius )
    ctx.rotate( rotation * Math.PI / 180 )

    drawRoundedPolygon( ctx, cornerPercent, corners )

    ctx.shadowBlur = shadowBlur
	  ctx.shadowColor = shadowColor
	  ctx.shadowOffsetX = ctx.shadowOffsetY = 0
    ctx.fillStyle = color
    ctx.fill( )
    ctx.restore( )
  }

  function drawRoundedPolygon(  ctx: CanvasRenderingContext2D,
                                roundingPercent: number,
                                vertex: number[][] ) {

    function lerp( p1: number[], p2: number[], t: number ): number[] {
      return [ p1[ 0 ] * ( 1 - t ) + p2[ 0 ] * ( t ),
               p1[ 1 ] * ( 1 - t ) + p2[ 1 ] * ( t ) ]
    }

    ctx.beginPath( )
    
    for ( let i = 0; i < vertex.length; i++ ) {

      const prevCorner = vertex[ ( i + 0 ) % vertex.length ]
      const thisCorner = vertex[ ( i + 1 ) % vertex.length ]
      const nextCorner = vertex[ ( i + 2 ) % vertex.length ]

      const q1 = lerp( thisCorner, prevCorner, roundingPercent / 200 )
      const q2 = lerp( thisCorner, nextCorner, roundingPercent / 200 )

      ctx.lineTo( q1[ 0 ], q1[ 1 ] );
      ctx.quadraticCurveTo( thisCorner[ 0 ], thisCorner[ 1 ], q2[ 0 ], q2[ 1 ] ) 
    }
    
    ctx.closePath( );

  }

  function drawText( ctx: CanvasRenderingContext2D, text: string, color: string, scale: number ) {
    ctx.font = "1px B612 Mono";
    ctx.fillStyle = "white"
    const metrics = ctx.measureText( text )
    const textWidth = metrics.width
    const actualHeight = 0.72
    ctx.shadowBlur = 10
    ctx.shadowColor = "white"
    ctx.shadowOffsetX = ctx.shadowOffsetY = 0
    ctx.save( )
    ctx.scale( scale, scale )
    ctx.fillStyle = color
    ctx.fillText( text, (0-textWidth)/2, (0+actualHeight)/2)
    ctx.restore( )
  }

  onMount(async () => {
    const canvas: HTMLCanvasElement = <HTMLCanvasElement> document.getElementById("canvas");
    const ctx: CanvasRenderingContext2D = canvas.getContext("2d")!;

    function step(time: number) {
      console.log( time )
      draw( ctx, time )
      requestAnimationFrame(step)
    }

    requestAnimationFrame(step)
  })

  function draw( ctx: CanvasRenderingContext2D, time: number ) {
      ctx.canvas.width  = window.innerWidth;
      ctx.canvas.height = window.innerHeight;
      const shortestSide = ctx.canvas.width < ctx.canvas.height ? ctx.canvas.width : ctx.canvas.height
      ctx.save( )
      ctx.translate( ctx.canvas.width/2, ctx.canvas.height/2 )
      ctx.scale( shortestSide / 4, shortestSide / 4)
      drawRoundedRegularPolygon( ctx, 1.3, 45, 33, 20, "#C34D1A", "#00000020", 6 )
      drawRoundedRegularPolygon( ctx, 1.2, 45, 33, 20, "#DD8B31", "#00000020", 6 )
      drawRoundedRegularPolygon( ctx, 1.1, 45, 33, 20, "#EEBD6D", "#00000020", 6 )
      drawRoundedRegularPolygon( ctx, 1.0, 45, 33, 20, "#F2D8AD", "#00000020", 6 )
      drawText( ctx, ( Math.floor( time / 1000 )%30 ).toString( ), "#EAAB4D", 1 )
      ctx.restore( )
    }

</script>
<body>
    <canvas style="position:absolute;" on:focus id="canvas"></canvas>
</body>
<link href='https://fonts.googleapis.com/css?family=Open Sans' rel='stylesheet'>
<link href='https://fonts.googleapis.com/css?family=Montserrat' rel='stylesheet'>
 
<style>

  body {
    width:  100%;
    height: 100%;
    overflow: hidden;
    display: block;
    margin: 0;
    font-family: 'Montserrat';font-size: 22px;
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
    x: number,
    y: number,
    radius: number,
    rotation: number,
    cornerPercent: number,
    shadowBlur: number,
    color: string,
    numberOfCorners: number ) {

    function getRegularPolygonCorner( index: number, numberOfCorners: number ): number[] {
      const angle = ( index + 0.5 ) * 2 * Math.PI / numberOfCorners
      return [ Math.sin( angle ), Math.cos( angle ) ]
    }

    const corners = []

    for (let i=0; i<numberOfCorners; i++)
      corners.push( getRegularPolygonCorner( i, numberOfCorners) )


    ctx.save( )
    ctx.translate( x, y )
    ctx.scale( radius, radius )
    ctx.rotate( rotation * Math.PI / 180 )

    drawRoundedPolygon( ctx, cornerPercent, corners )

    ctx.shadowBlur = shadowBlur
	  ctx.shadowColor=   color
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

  onMount(async () => {
    const canvas = <HTMLCanvasElement> document.getElementById("canvas");
    const ctx: CanvasRenderingContext2D  = canvas.getContext("2d")!;

    function doDraw() {
      ctx.canvas.width  = window.innerWidth;
      ctx.canvas.height = window.innerHeight;
      drawRoundedRegularPolygon(ctx, canvas.width/2, canvas.height/2, canvas.height/2, 45, 33, 50, "#E49641", 3)
      ctx.font = "200px Montserrat";
      ctx.fillStyle = "white"
      const text = "12"
      const metrics = ctx.measureText( text )
      const textWidth = metrics.width
      const fontHeight = metrics.fontBoundingBoxAscent + metrics.fontBoundingBoxDescent
      const actualHeight = metrics.actualBoundingBoxAscent + metrics.actualBoundingBoxDescent
      ctx.shadowBlur = 10
	    ctx.shadowColor = "white"
	    ctx.shadowOffsetX = ctx.shadowOffsetY = 0
      ctx.fillText( text, (canvas.width-textWidth)/2, (canvas.height+actualHeight)/2)
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
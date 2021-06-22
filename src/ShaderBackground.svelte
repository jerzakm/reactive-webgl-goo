<script>
  import * as twgl from 'twgl.js'
  import { onMount } from 'svelte'

  const vs = `
    attribute vec4 position;

    void main() {
      gl_Position = position;
    }
  `

  const fs = `
  precision mediump float;

  uniform vec2 resolution;
  uniform float time;
  uniform vec2 positions[64];
  const int maxBlobs = 64;

  float roundLookingBlob(vec2 fragCoord, vec2 tPos, float r) {
    vec2 pos = fragCoord.xy/resolution.xy ;
    pos.x*=  resolution.x / resolution.y ;
    return pow(max(1.0-length(pos-tPos), 0.0) , r);
  }

  void main() {
    vec2 positions[64];
    const int maxBlobs = 64;
	  float v = 0.0;

    positions[0] = vec2(0.01, 0.01);
    positions[10] = vec2(0.5, 1.);
    positions[32] = vec2(0.5, 0.5);
    positions[20] = vec2(2.0, 0.9);

    for(int i=0;i<maxBlobs;++i)
    {
        if(positions[i].x>0. && positions[i].y > 0.){
            float iFloat = float(i);
            vec2 bubblePos = positions[i];
            v+=roundLookingBlob(gl_FragCoord.xy, bubblePos, 6.0);
        }
    }
    v = clamp((v-0.5)*1000.0, 0.0, 1.0);
    //v = 1.0;
    //float color = cos(time * gl_FragCoord.x) * 1.0;
    //v *= color;
    //float r = 1.0 + gl_FragCoord.y *sin(time * 0.5) +  0.0001 * (2.0 * sin(time * 1.0)) ;
    float r =
        -1.0 * 1.0 *sin(time)
        - 2.0* cos(1.0 * time) * gl_FragCoord.x / resolution.x * gl_FragCoord.y / resolution.y;
    float g = 0.0 - 0.5 * cos(2.0 * time) *  gl_FragCoord.y / resolution.y; //1.0* sin(time) - r + 0.8;
    float b = 4.0 + sin(time) - g + 0.8;
	  gl_FragColor = vec4(r * v, v * g, v * b, 1.0);
  }
  `

  let canvas

  onMount(() => {
    const gl = canvas.getContext('webgl')
    const programInfo = twgl.createProgramInfo(gl, [vs, fs])

    const mousePosition = [0.0, 0.0]

    const arrays = {
      position: [-1, -1, 0, 1, -1, 0, -1, 1, 0, -1, 1, 0, 1, -1, 0, 1, 1, 0],
    }
    const bufferInfo = twgl.createBufferInfoFromArrays(gl, arrays)

    function render(time) {
      twgl.resizeCanvasToDisplaySize(gl.canvas)
      gl.viewport(0, 0, gl.canvas.width, gl.canvas.height)

      const uniforms = {
        time: time * 0.001,
        resolution: [gl.canvas.width, gl.canvas.height],
      }

      gl.useProgram(programInfo.program)
      twgl.setBuffersAndAttributes(gl, programInfo, bufferInfo)
      twgl.setUniforms(programInfo, uniforms)
      twgl.drawBufferInfo(gl, bufferInfo)

      requestAnimationFrame(render)
    }
    requestAnimationFrame(render)
  })
</script>

<canvas bind:this={canvas} />

<style>
  canvas {
    position: fixed;
    width: 100vw;
    height: 100vh;
    z-index: -1;
  }
</style>

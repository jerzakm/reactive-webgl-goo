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
  uniform vec2 mousePos;

  float roundLookingBlob(vec2 fragCoord, vec2 tPos, float r) {
    vec2 uv = -.0 + 1.0*fragCoord.xy / resolution.xy;
	  uv.x *=  resolution.x / resolution.y;


    vec2 pos = fragCoord.xy/resolution.xy ;

    return pow(max(1.1-length(tPos-uv), 0.0) , r);
  }

  void main() {
    vec2 positions[8];
    const int maxBlobs = 8;
	  float v = 0.0;

    positions[0] = vec2(0.01, 0.01);
    positions[1] = vec2(0.5, 1.);
    positions[2] = vec2(0.3, 0.3);
    positions[3] = vec2(0.0, 1.3);
    positions[4] = vec2(1.2, 0.1);
    positions[5] = vec2(0.7, 0.7);
    positions[6] = vec2(1.4, 0.9);
    positions[7] = vec2(0., 0.5);

    for(int i=0;i<maxBlobs;++i)
    {
        if(positions[i].x>0. && positions[i].y > 0.){
            float iFloat = pow(float(i),2.);
            vec2 bubblePos = positions[i];
            bubblePos.x *= sin(time*0.005*iFloat);
            bubblePos.y += cos(time*0.0005 *iFloat*iFloat);
            v+=roundLookingBlob(gl_FragCoord.xy, bubblePos, 5.0);
        }
    }
    // Mouse

    v+=roundLookingBlob(gl_FragCoord.xy, mousePos, 15.0);
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

    vec3 color = vec3(.0);

    if(v>.5) {
      color = vec3(0.);
    } else {
      //color = 1.0;
      color = vec3(r * v, v * g, v * b);
    }

	  gl_FragColor = vec4(color, 1.-v);
  }
  `

  let canvas

  let mousePos = [0, 0]

  onMount(() => {
    const gl = canvas.getContext('webgl')
    const programInfo = twgl.createProgramInfo(gl, [vs, fs])

    window.addEventListener('mousemove', (e) => {
      mousePos = [
        (e.clientX / window.innerWidth) *
          (window.innerWidth / window.innerHeight),
        (window.innerHeight - e.clientY) / window.innerHeight,
      ]
    })

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
        mousePos,
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
    z-index: 5;

    pointer-events: none;
  }
</style>

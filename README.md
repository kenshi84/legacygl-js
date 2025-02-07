# legacygl.js #

[https://legacygl-js.glitch.me/](https://legacygl-js.glitch.me/)

**legacygl.js** is a small library based on WebGL for enabling the good-old-days legacy 
OpenGL style coding using the deprecated APIs (e.g., glBegin, glPushMatrix, GL_QUADS, glNewList).

### Hello World 3D ###

```
#!html
<html>
<head>
<title>legacygl.js Demo: Hello World 3D</title>
<script src="https://rawcdn.githack.com/toji/gl-matrix/v3.3.0/dist/gl-matrix-min.js"></script>
<script src="https://bb.githack.com/kenshi84/legacygl.js/raw/master/gl-matrix-util.js"></script>
<script src="https://bb.githack.com/kenshi84/legacygl.js/raw/master/legacygl.js"></script>
<script src="https://bb.githack.com/kenshi84/legacygl.js/raw/master/drawutil.js"></script>
<script src="https://bb.githack.com/kenshi84/legacygl.js/raw/master/camera.js"></script>
<script src="https://bb.githack.com/kenshi84/legacygl.js/raw/master/util.js"></script>
<script type="text/javascript">
var gl;
var canvas;
var legacygl;
var drawutil;
var camera;

function draw() {
    gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
    // projection and camera position
    mat4.perspective(legacygl.uniforms.projection.value, Math.PI / 6, canvas.aspect_ratio(), 0.1, 1000);
    camera.lookAt(legacygl.uniforms.modelview.value);
    // draw zx-grid
    legacygl.color(0.5, 0.5, 0.5);
    drawutil.zxgrid(50);
    // draw quad
    mat4.rotateY_ip(legacygl.uniforms.modelview.value, 1.1);
    legacygl.color(0, 0.7, 1);
    legacygl.begin(legacygl.QUADS);
    legacygl.vertex(1, 0, 0);
    legacygl.vertex(0.5, 1, 0);
    legacygl.vertex(0, 1, 0.5);
    legacygl.vertex(0, 0, 1);
    legacygl.end();
};
function init() {
    // OpenGL context
    canvas = document.getElementById("canvas");
    gl = canvas.getContext("experimental-webgl");
    if (!gl)
        alert("Could not initialize WebGL!");
    var vertex_shader_src = "\
        attribute vec3 a_vertex;\
        attribute vec3 a_color;\
        varying vec3 v_color;\
        uniform mat4 u_modelview;\
        uniform mat4 u_projection;\
        void main(void) {\
            gl_Position = u_projection * u_modelview * vec4(a_vertex, 1.0);\
            v_color = a_color;\
        }\
        ";
    var fragment_shader_src = "\
        precision mediump float;\
        varying vec3 v_color;\
        void main(void) {\
            gl_FragColor = vec4(v_color, 1.0);\
        }\
        ";
    legacygl = get_legacygl(gl, vertex_shader_src, fragment_shader_src);
    legacygl.add_uniform("modelview", "Matrix4f");
    legacygl.add_uniform("projection", "Matrix4f");
    legacygl.add_vertex_attribute("color", 3);
    drawutil = get_drawutil(gl, legacygl);
    camera = get_camera(canvas.width);
    camera.eye = [40, 20, 30];
    // event handlers
    canvas.onmousedown = function(evt) {
        camera.start_moving(this.get_mousepos(evt), evt.shiftKey ? "zoom" : evt.ctrlKey ? "pan" : "rotate");
    };
    canvas.onmousemove = function(evt) {
        if (camera.is_moving()) {
            camera.move(this.get_mousepos(evt));
            draw();
        }
    };
    document.onmouseup = function(evt) {
        if (camera.is_moving())
            camera.finish_moving();
    };
    // init OpenGL settings
    gl.viewport(0, 0, canvas.width, canvas.height);
    gl.enable(gl.DEPTH_TEST);
    gl.clearColor(1, 1, 1, 1);
};
</script>
</head>
<body onload="init(); draw();">
    <h2>legacygl.js Demo: Hello World 3D</h2>
    <canvas id="canvas" width="640" height="480" style="border:1px solid #000000;"></canvas>
    <h3>Camera Control:</h3>
    <ul>
        <li>Drag: Rotate</li>
        <li>Shift+Drag: Zoom</li>
        <li>Ctrl+Drag: Pan</li>
    </ul>
</body>
</html>
```
https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/hello3d.html

### Demos ###
- Hello World 2D
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/hello2d.html
- Pick/Move Objects in 3D
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/pick3d.html
- Pick/Move Objects in 2D
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/pick2d.html
- Z-Buffer
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/z-buffer.html
- Display List
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/displist.html
- Mesh Viewer
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/meshviewer.html
- Texturing
    - https://bb.githack.com/kenshi84/legacygl.js/raw/master/demo/texture.html

### Tutorial ###
- [Managing Shader Variables](doc/managing-shader-variables.md)

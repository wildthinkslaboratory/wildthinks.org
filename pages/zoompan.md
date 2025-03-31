.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.
.

# :::: loading
This page is reading telescope files. It's worth the wait!
# ::::




```javascript /autoplay/kiosk
//smartdown.import=../../assets/libs/fits.js
//smartdown.import=https://biodiv.github.io/contactjs/assets/js/contact.min.js

this.div.style.width = '100%';
this.div.style.height = '100%';
this.div.style.margin = 'auto';
this.div.innerHTML = `<canvas id="appCanvas"></canvas>`;
let canvas = document.getElementById("appCanvas"); 
let context = canvas.getContext("2d");
canvas.width  = window.innerWidth;
canvas.height = window.innerHeight;


function sizeCanvas() {
  canvas.width  = window.innerWidth;
  canvas.height = window.innerHeight;
}
sizeCanvas();





let min = 0.1;
let max = 60;

async function getImageData(filenameBase) {
  return getImageDataFromFITS(filenameBase);
}

smartdown.showDisclosure('loading', '', 'center,lightbox');
let data = await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f187n_i2d_sci');
smartdown.hideDisclosure('loading', '', '');



function getValue(value) {
  let c = 0;
  if (value > max) c = 255;
  else {
      if (value > min) {
        c = (Math.round((value - min) / (max - min) * 255));
      }
  }
  return c;
}



let r = data.length;
let c = 0;
if (r > 0) { c = data[0].length; }
console.log(r, c);
let zoom = 1.0;


function draw() {

  let imagedata = context.createImageData(canvas.width, canvas.height);
  let w = canvas.width;
  let h = canvas.height;
  let zc = c/zoom;
  let zr = r/zoom;
  let dx = c - zc;
  let dy = r - zr;

  for (let y=0; y<canvas.height; y++) {
    for (let x=0; x<canvas.width; x++) {

      let ydown = h - y;
      let ny = h < r ? dy + Math.floor(ydown / h * zr) : ydown;
      let nx = w < c ? dx + Math.floor((x / canvas.width) * zc) : x; 
      let pixelindex = (y * canvas.width + x) * 4;
      imagedata.data[pixelindex+0] = 0;
      imagedata.data[pixelindex+1] = 0;
      imagedata.data[pixelindex+2] = 0;
      imagedata.data[pixelindex+3] = 255;
      if (ny < r && nx < c) {
          imagedata.data[pixelindex+1] += getValue(data[ny][nx]);
          imagedata.data[pixelindex+2] += getValue(data[ny][nx]);
      }
    }
  }
  context.putImageData(imagedata, 0, 0);
}


window.addEventListener('resize', function(event){
  sizeCanvas();
  draw();
});



draw();
```


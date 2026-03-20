Explore a large FITS file

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


# :::: intro
# --outlinebox int
### Explore a Large FITS file
- zoom in and out
- drag and drop to pan
# --outlinebox
# ::::

# :::: panel
# --outlinebox p
zoom level [](:!zoomLevel)
[open image in new tab](:=jpg=jpg+1)
# --outlinebox
# ::::

```javascript /autoplay/module/kiosk
// notice that this playable has the module keyword in it.  
// This creates an environment where we can load javascript modules.  

// load the fits library
//smartdown.import=../../assets/libs/fits.js

// contact.js is the library that implements all the touch event functionality for zoom and panning
import {
Press, Pan, Pinch, PointerListener
} from 'https://biodiv.github.io/contactjs/assets/js/contact.js';




// because this is a module, we need to put all our code in the start function
// this will be executed when you push play on the playable (or when it autostarts).
export default async function start(playable, env) {
  const log = this.log;

  log('start', this);

  smartdown.showDisclosure('panel','','transparent,bottomright,closeable,draggable,shadow,outline');
  smartdown.showDisclosure('intro','','transparent,center,closeable,draggable,shadow,outline');


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


/////////////////////////////////////  drawing the FITS image ///////////////////////////////

  // take a value between some min and max and scale it to a value between 0 and 255
  function getColorValue(value, min, max) {
    let c = 0;
    if (value > max) c = 255;
    else {
        if (value > min) {
            c = (Math.round((value - min) / (max - min) * 255));
        }
    }
    return c;
  }


  let fits_min = 0.1;
  let fits_max = 8.0;
  let zoom = 1.0;
  let xshift = 0;
  let yshift = 0;
  let maxzoom;
  let minzoom;
  let data;
  let fits_height;
  let fits_width;
  smartdown.setVariable('zoomLevel', zoom);

  function draw() {

    if (typeof fits_width === 'undefined') { return; } // don't draw if the files aren't loaded

    let imagedata = context.createImageData(canvas.width, canvas.height);
    
    for (let y=0; y<canvas.height; y++) {
        for (let x=0; x<canvas.width; x++) { // loop through canvas pixels
          let nx = Math.floor(x * zoom) + xshift; 
          let ny = Math.floor(y * zoom) + yshift;

          let pixelindex = (y * canvas.width + x) * 4;  // set the default pixel color to black
          imagedata.data[pixelindex+0] = 0;
          imagedata.data[pixelindex+1] = 0;
          imagedata.data[pixelindex+2] = 0;
          imagedata.data[pixelindex+3] = 255;

          if (ny < fits_height && nx < fits_width && ny >= 0 && nx >= 0) {  
            imagedata.data[pixelindex+0] += getColorValue(data[ny][nx],fits_min, fits_max);
            imagedata.data[pixelindex+1] += getColorValue(data[ny][nx],fits_min, fits_max);
            imagedata.data[pixelindex+2] += getColorValue(data[ny][nx],fits_min, fits_max);
          }
      }
    }
    context.putImageData(imagedata, 0, 0);

    smartdown.setVariable('zoomLevel', zoom);
  }


  window.addEventListener('resize', function(event){
    sizeCanvas();
    draw();   
  });


//////////////////////////////// TOUCH EVENTS //////////////////////////////////////////

  let panActive = false;
  var pinchActive = false;
  let animationFrameId = null;
  let ticking = false;

  let options = {
    "supportedGestures" : [Press, Pan, Pinch]
  };

  let pointerListener = new PointerListener(this.div, options);


  this.div.addEventListener('press', function(event){
    console.log('press');
  });

  this.div.onmousedown = function(e) {  
    setTimeout(function(){
      panActive = true;
    }, 100);
  }

  // this.div.ondblclick = function(e) {

  //   console.log('double click');
  // }

  const debouncedZoom = (waitTimeForDraw, waitTimeForBookmark) => {
    let drawTimeout = null;
    let bookmarkTimeout = null;
    let totalDeltaY = 0;

    return (event) => {
      window.clearTimeout(drawTimeout);
      window.clearTimeout(bookmarkTimeout);
      totalDeltaY += event.deltaY;
      event.preventDefault();

      drawTimeout = window.setTimeout(() => {
        const scale = (1.0 - 0.001 * totalDeltaY);
        totalDeltaY = 0;
        // xshift += Math.floor(canvas.width * zoom * (1 - (1/scale)) / 2);
        // yshift += Math.floor(canvas.height * zoom * (1 - (1/scale)) / 2);  
        zoom /= scale;
        draw(); 
      }, waitTimeForDraw);

      bookmarkTimeout = window.setTimeout(() => {
      }, waitTimeForBookmark);
    };
  }

  this.div.onwheel = debouncedZoom(6, 500);

  // // End: Events related to desktop and web usage.

  let prev_panx = 0;
  let prev_pany = 0;
  let save_panx = 0;
  let save_pany = 0;
  this.div.addEventListener('panstart', function(event) {
    if (!pinchActive) {
      panActive = true;
      console.log('pan started');
    }
  });

  this.div.addEventListener('pan', function(event){
    if (panActive == true) {

      // event.detail.global.deltaX is the total pixels in x direction traveled during this pan
      let x = event.detail.global.deltaX - prev_panx;  
      let y = event.detail.global.deltaY - prev_pany;
      prev_panx = event.detail.global.deltaX;
      prev_pany = event.detail.global.deltaY;
      
      xshift -= Math.floor(x * zoom);
      yshift -= Math.floor(y * zoom);
      draw();    
    }
  });


  this.div.addEventListener('panend', function(event){
    if (panActive) {
      panActive = false; 
      console.log('pan ended');
    }
  });

  // this.div.addEventListener('swipeleft', function(event){
  //   if (panActive) {

  //     panActive = false;
  //   }

  //   console.log('swipe left');
  // });


  // this.div.addEventListener('swiperight', function(event){
  //   if (panActive) {

  //     panActive = false;
  //   }
  //   console.log('swipe right');
  // });


  let prev_scale = 1;
  this.div.addEventListener('pinchstart', function(event){
    pinchActive = true;
    prev_panx = 0;
    prev_pany = 0;
    prev_scale = 0;

    console.log('pinch start');
  });

  this.div.addEventListener('pinch', function(event){
    if (pinchActive == true){
      let x = event.detail.global.deltaX - prev_panx;
      let y = event.detail.global.deltaY - prev_pany;
      prev_panx = event.detail.global.deltaX;
      prev_pany = event.detail.global.deltaY;
      let scale = (event.detail.live.scale + 7.0) / 8;

      // xshift -= Math.floor(x * zoom);
      // yshift -= Math.floor(y * zoom);

      // xshift += canvas.width * zoom * (1 - scale) / 2;
      // yshift += canvas.height * zoom * (1 - scale) / 2;  
      // zoom *= scale;
      draw();             
    } 
  });

  // this doesn't ever trigger
  this.div.addEventListener('pinchend', function(event){
    console.log('pinch end');
    pinchActive = false; 
  });

  let weShouldStopDefaultScrollAndZoom = true;
  this.div.addEventListener('touchmove', ev => {
    if (weShouldStopDefaultScrollAndZoom) {
      ev.preventDefault();
      ev.stopImmediatePropagation();
    };
  }, { passive: false });



//////////////////////////  export your image  ///////////////////////////////////////

  smartdown.setVariable('jpg', 0);
  this.dependOn.jpg = () => {
    if (env.jpg > 0){
      let x = window.open();
      const p = x.document.createElement('p');
      let img = new Image();
      img.src = canvas.toDataURL('image/jpg');
      x.document.body.appendChild(img);
    }
  }


///////////////////////////  read in the data files  ////////////////////////////////

  // fetch('https://upload.wikimedia.org/wikipedia/commons/7/77/Delete_key1.jpg')
  //   .then(res => res.blob()) // Gets the response and returns it as a blob
  //   .then(blob => {
  //     // Here's where you get access to the blob
  //     // And you can use it for whatever you want
  //     // Like calling ref().put(blob)

  //     // Here, I use it to make an image appear on the page
  //     let objectURL = URL.createObjectURL(blob);
  //     let myImage = new Image();
  //     myImage.src = objectURL;
  //     document.getElementById('myImg').appendChild(myImage)
  // });

  async function getImageData(filenameBase) {
    return getImageDataFromFITS(filenameBase);
  }

  data = (await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f090w_i2d_sci'));
  //data = (await getImageData('https://drive.google.com/file/d/1SKYjZyfdCiJryE1ZU5KBrFoOQ4NKEnTC/view?usp=share_link'));


  fits_height = data.length;
  fits_width = 0;
  if (fits_height > 0) { fits_width = data[0].length; }

  zoom = fits_width / canvas.width;
  maxzoom = zoom;
  minzoom = 1.0;

  draw();

}

```

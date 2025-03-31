Transfer infrared light captured by the JWST into light from the visual spectrum to make a cool image.

- filter descriptions
- tooltips appear in wrong place
- copyright in middle of page
- size issues
- new images
- color circles
- library
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
### Telescope Intro
This is [Messier 74](https://en.wikipedia.org/wiki/Messier_74) also known as the Phantom Galaxy. It has been constructed with javascript on this website with data directly from the [James Webb Space Telescope's](https://webb.nasa.gov/) MIRI instrument. You can change the color assignments for each filter as well as the stretch function. Play around and see what you can make.
Check out [Notes](/pages/telescopeNotes) if you want to learn more about how I did this.
# --outlinebox
# ::::


# :::: loading
This page is reading telescope files.  Please be patient.
# ::::


# :::: panel
# --outlinebox p
F770W [](:XuseF770W) [](:-color1/0/5/0.1)[show settings](:=filter0=true)
F1000W [](:XuseF1000W) [](:-color2/0/5/0.1)[show settings](:=filter1=true)
F1130W [](:XuseF1130W) [](:-color3/0/5/0.1)[show settings](:=filter2=true)
F2100W [](:XuseF2100W) [](:-color4/0/5/0.1)[show settings](:=filter3=true)

---

Use JSON [](:XuseJSON)
Use Image 1 (Big) [](:XuseImage1)

Image1 0 (Izzy): `jw02107-o039_t018_miri_f1000w_i2d.fits`
Image1 1 (Big): `jw02107-c1019_t018_miri_f1000w_i2d.fits`

# --outlinebox
# ::::

#### `parseFITSHeader` and `parseFITSImage`

Copied from:
- https://github.com/LHSnow/jsFITS/blob/master/src/fits-parser.js

This uses Smartdown's `/module` playable qualifier to ingest an ES6 module into the runtime.



```javascript /autoplay/kiosk
//smartdown.import=/assets/libs/fits.js

const m53Image = {
  min: [10.0, 28.0, 42.0, 245.0],
  max: [25.0, 36.0, 65.0, 260.0],
};
const otherImage = {
  min: [1.0, 1.0, 1.0, 1.0],
  max: [100.0, 100.0, 225.0, 225.0],
};

const images = [
  m53Image,
  otherImage,
];
let imageIndex = 1;
let useFITS = true;

let dataNames = ['f770w', 'f1000w', 'f1130w', 'f2100w'];

let stretchFunction = ['x', 'x', 'x', 'x'];
let actualStretchFunction = [];
for (let i = 0; i < 4; i++){
  actualStretchFunction.push(new Function('x', 'return ' + stretchFunction[i] + ';'));
}
let activeFilter = 0;
let dataArrays = [];
smartdown.showDisclosure('panel','','transparent,bottomright,draggable,shadow,outline');
// smartdown.showDisclosure('intro','','transparent,topleft,closeable,draggable,shadow,outline');
smartdown.setVariable('useF770W', true);
smartdown.setVariable('useF1000W', false);
smartdown.setVariable('useF1130W', false);
smartdown.setVariable('useF2100W', false);
smartdown.setVariable('redraw',false);
smartdown.setVariable('color1', 1);
smartdown.setVariable('color2', 3);
smartdown.setVariable('color3', 5);
smartdown.setVariable('color4', 0);
smartdown.setVariable('setFilter', dataNames[activeFilter]);
smartdown.setVariable('curveFunction', stretchFunction[activeFilter]);
smartdown.setVariable('min', images[imageIndex].min[activeFilter]);
smartdown.setVariable('max', images[imageIndex].max[activeFilter]);
smartdown.setVariable('saveSettings', false);
smartdown.setVariable('drawHistogram', false);
smartdown.setVariable('filter0', 'false');
smartdown.setVariable('filter1', 'false');
smartdown.setVariable('filter2', 'false');
smartdown.setVariable('filter3', 'false');


async function getImageDataFromJSON(filenameBase) {
  const res = await fetch(`${filenameBase}.json`);
  const array = await res.json();
  return array;
}



async function getImageData(filenameBase) {
  return useFITS ?
    getImageDataFromFITS(filenameBase) :
    getImageDataFromJSON(filenameBase);
}

smartdown.showDisclosure('loading','','center,lightbox');

if (imageIndex === 0) {
  dataArrays.push(await getImageData('assets/data/test1'));
  // dataArrays.push(await getImageData('assets/data/jw02107-o039_t018_miri_f1000w_i2d'));
  // dataArrays.push(await getImageData('assets/data/jw02107-o039_t018_miri_f1130w_i2d'));
  // dataArrays.push(await getImageData('assets/data/jw02107-o039_t018_miri_f2100w_i2d'));
}
if (imageIndex === 1) {
  dataArrays.push(await getImageData('../../assets/data/jw02107-o039_t018_miri_f1000w_i2d'));
  // dataArrays.push(await getImageData('assets/data/jw02107-c1019_t018_miri_f1130w_i2d'));
  // dataArrays.push(await getImageData('assets/data/jw02107-c1019_t018_miri_f2100w_i2d'));
  // dataArrays.push(await getImageData('assets/data/jw02107-c1019_t018_miri_f770w_i2d'));
}

smartdown.hideDisclosure('loading','','');


let nrows = dataArrays[0].length;
let ncols = 0;
if (nrows > 0) { ncols = dataArrays[0][0].length; }


this.div.style.width = '100%';
this.div.style.height = '100%';
this.div.style.margin = 'auto';
this.div.innerHTML = `<canvas id="appCanvas"></canvas>`
let canvas = document.getElementById("appCanvas"); 
let context = canvas.getContext("2d");
canvas.width  = window.innerWidth;
canvas.height = window.innerHeight;


function sizeCanvas() {
  canvas.width  = window.innerWidth;
  canvas.height = window.innerHeight;
}
sizeCanvas();


function drawHistogram() {
  let div = document.getElementById('div_playable_2')
  let data2d = dataArrays[activeFilter];
  let histData = [];
  let f = new Function('x', 'return ' + env.curveFunction + ';');
  let min = env.min;
  let max = env.max;
  for (let r=0; r < data2d.length; r++) {
    for (let c=0; c < data2d[0].length; c++) {
      let value = f(data2d[r][c]);
      if (value >= min && value <= max) { 
        histData.push(value);
      }
    }
  }
  let trace = {
    x: histData,
    type: 'histogram',
    name: 'Telescope Data'
  };
  let data = [trace];
  Plotly.newPlot(div, data);
}


function updateFilterVariables() {
  smartdown.setVariable('setFilter', dataNames[activeFilter]);
  smartdown.setVariable('curveFunction', stretchFunction[activeFilter]);
  smartdown.setVariable('min', images[imageIndex].min[activeFilter]);
  smartdown.setVariable('max', images[imageIndex].max[activeFilter]);
}


function saveFilterVariables() {
  stretchFunction[activeFilter] = env.curveFunction;
  actualStretchFunction[activeFilter] = new Function('x', 'return ' + stretchFunction[activeFilter] + ';');
  images[imageIndex].min[activeFilter] = env.min;
  images[imageIndex].max[activeFilter] = env.max;
}


function spectrumProcess(number){
  let answer = [0,0,0]
  if (number <= 1 && number >= 0){
    answer[0] = 1 - number
    answer[2] = 1
  }
  if (number <= 2 && number > 1){
    answer[1] = number - 1
    answer[2] = 1
  }
  if (number <= 3 && number > 2){
    answer[2] = 3 - number
    answer[1] = 1
  }
  if (number <= 4 && number > 3){
    answer[0] = number - 3
    answer[1] = 1
  }
  if (number <= 5 && number > 4){
    answer[1] = 5 - number
    answer[0] = 1
  }
  if (number <= 6 && number > 5){
    answer[2] = number-5
    answer[0] = 1
  }
  return answer
}


function getValue(value, i) {
  let c = 0;
  let newvalue = actualStretchFunction[i](value);
  let newmax = images[imageIndex].max[i];
  let newmin = images[imageIndex].min[i];
    if (newvalue > newmax) c = 255;
    else {
        if (newvalue > newmin) {
            c = (Math.round((newvalue - newmin) / (newmax - newmin) * 255))/activeFunctions();
        }
    }
    return c;
}


function activeFunctions() {
  let f = 0;
  if (env.useF770W)  {f++;}
  if (env.useF1000W) {f++;}
  if (env.useF1130W) {f++;}
  if (env.useF2100W) {f++;}
  return f;
}


let xshift = 0; // 600; // 1150;
let yshift = 0; // 200; // 50;


function draw() {
  let zoomOut = 1.0;
  let f1color = spectrumProcess(env.color1)
  let f2color = spectrumProcess(env.color2)
  let f3color = spectrumProcess(env.color3)
  let f4color = spectrumProcess(env.color4)
  let imagedata = context.createImageData(canvas.width, canvas.height);

  // Adjust xshift and yshift so that image is upper left of canvas

  const scaledX = Math.floor(ncols / zoomOut);
  const scaledY = Math.floor(nrows / zoomOut);
  const xGap = canvas.width - scaledX;
  const yGap = canvas.height - scaledY;

  let adjustedXShift = xGap > 0 ? (xshift - xGap) : -xGap;
  let adjustedYShift = yGap > 0 ? (yshift - yGap) : yshift;

  console.log('nrows,ncols', nrows, ncols);
  console.log('scaledY,scaledX', scaledY, scaledX);
  console.log('canvas.height, canvas.width', canvas.height, canvas.width);
  console.log('yGap,xGap', yGap, xGap);
  console.log('adjustedYShift, adjustedXShift', adjustedYShift, adjustedXShift);

  for (let y=0; y<canvas.height; y++) {
    for (let x=0; x<canvas.width; x++) {
      let ny = canvas.height - y + adjustedYShift;
      let nx = x + adjustedXShift + xGap;
      nx *= zoomOut;
      ny *= zoomOut;
    
      let pixelindex = (y * canvas.width + x) * 4;
      imagedata.data[pixelindex+0] = 0;
      imagedata.data[pixelindex+1] = 0;
      imagedata.data[pixelindex+2] = 0;
      imagedata.data[pixelindex+3] = 255;
      if (nx < 0) {
        imagedata.data[pixelindex+1] = 100;
      } else if (ny < 0) {
        imagedata.data[pixelindex+2] = 255;
      }
      else if (ny < nrows && nx < ncols) {
        if (env.useF770W){
          imagedata.data[pixelindex+0] += (getValue(dataArrays[0][ny][nx],0)*f1color[0]);
          imagedata.data[pixelindex+1] += (getValue(dataArrays[0][ny][nx],0)*f1color[1]);
          imagedata.data[pixelindex+2] += (getValue(dataArrays[0][ny][nx],0)*f1color[2]);
        }
        if (env.useF1000W){
          imagedata.data[pixelindex+0] += (getValue(dataArrays[1][ny][nx],1)*f2color[0]);
          imagedata.data[pixelindex+1] += (getValue(dataArrays[1][ny][nx],1)*f2color[1]);
          imagedata.data[pixelindex+2] += (getValue(dataArrays[1][ny][nx],1)*f2color[2]);
        }
        if (env.useF1130W){
          imagedata.data[pixelindex+0] += (getValue(dataArrays[2][ny][nx],2)*f3color[0]);
          imagedata.data[pixelindex+1] += (getValue(dataArrays[2][ny][nx],2)*f3color[1]);
          imagedata.data[pixelindex+2] += (getValue(dataArrays[2][ny][nx],2)*f3color[2]);
        }
        if (env.useF2100W){
          imagedata.data[pixelindex+0] += (getValue(dataArrays[3][ny][nx],3)*f4color[0]);
          imagedata.data[pixelindex+1] += (getValue(dataArrays[3][ny][nx],3)*f4color[1]);
          imagedata.data[pixelindex+2] += (getValue(dataArrays[3][ny][nx],3)*f4color[2]);
        }
      }
      else {
        imagedata.data[pixelindex+0] = 100;
      }
    }
  }
  context.putImageData(imagedata,0,0);
  console.log('end draw');
}


window.addEventListener('resize', function(event){
  sizeCanvas();
  draw();
});

this.dependOn = ['filter0','filter1', 'filter2', 'filter3', 'saveSettings','drawHistogram','redraw'];
this.depend = function() {

  // here's the repeated code that should be fixed
  if (env.filter0 == true) {
    smartdown.setVariable('filter0', false);
    activeFilter = 0;
    updateFilterVariables();
    drawHistogram();
    smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
  }

  if (env.filter1 == true) {
    smartdown.setVariable('filter1', false);
    activeFilter = 1;
    updateFilterVariables();
    drawHistogram();
    smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
  }

  if (env.filter2 == true) {
    smartdown.setVariable('filter2', false);
    activeFilter = 2;
    updateFilterVariables();
    drawHistogram();
    smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
  }

  if (env.filter3 == true) {
    smartdown.setVariable('filter3', false);
    activeFilter = 3;
    updateFilterVariables();
    drawHistogram();
    smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
  }

  // these events are triggered by the histogram popup
  if (env.saveSettings == true) {
    smartdown.setVariable('saveSettings', false);
    saveFilterVariables();  
  }

  if (env.drawHistogram == true) {
    smartdown.setVariable('drawHistogram', false);
    drawHistogram();  
  }
  draw();
  // if (env.redraw == true){
  //   smartdown.setVariable('redraw',false);
  //   draw();
  // }
}


draw()



```
# :::: filterSettings
# --aliceblue
active filter: [](:!setFilter) [redraw histogram](:=redrawHistogram=true) [Save and Close](:=close=true)
min [](:?min|number) max [](:?max|number)
stretch function: [](:?curveFunction) [formatting tips](::formatting)
# :::: formatting
Enter a single variable function using variable `x`.  Functions need to be written in javascript.  
| Expression  | Javascript |
| ----------- | ----------- |
| $\ln(x)$          | `Math.log(x)`       |
| $x^5$                | `Math.exp(x,5)`      |
| $\text{asinh}(x)$  | `Math.asinh(x)`    |
You can find a list of javascript **Math** functions [here](https://www.w3schools.com/jsref/jsref_obj_math.asp).
# ::::
# --aliceblue

```javascript /plotly/autoplay

this.div.style.width = '100%';
this.div.style.height = '100%';
this.div.style.margin = 'auto';


smartdown.setVariable('redrawHistogram', false);
smartdown.setVariable('close', false);


this.dependOn = ['redrawHistogram','close'];
this.depend = function() {
  if (env.redrawHistogram == true) {
    smartdown.setVariable('redrawHistogram', false);
    smartdown.setVariable('saveSettings', true);
    smartdown.setVariable('drawHistogram', true);
  }
  if (env.close == true) {
    smartdown.setVariable('close', false);
    smartdown.setVariable('saveSettings', true);
    smartdown.hideDisclosure('filterSettings','','');
  }
}

```
# ::::
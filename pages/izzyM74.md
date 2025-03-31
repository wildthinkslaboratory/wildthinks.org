0. make files smaller
  - try cv2 lib
1. move the new data files to izzy's data folder
2. add the fits lib to blog-kit library 
3. adapt izzy's m74 post to use the new library
4. adapt cosmic cliffs to use the new library
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

# :::: filters
![](/assets/images/nircam_filters.png)
# ::::


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
[](:!c1|markdown) [](:-color1/0/5/0.1) F770W [](:XuseF770W) [show settings](:=filter0=true) 
[](:!c1|markdown) [](:-color2/0/5/0.1) F1000W [](:XuseF1000W) [show settings](:=filter1=true) 
[](:!c1|markdown) [](:-color3/0/5/0.1) F1130W [](:XuseF1130W) [show settings](:=filter2=true) 
[](:!c1|markdown) [](:-color4/0/5/0.1) F2100W [](:XuseF2100W) [show settings](:=filter3=true) 
[export](:=download=true) [filters](::filters/button,bottomleft,draggable,closeable) [notes](::intro/transparent,button,topleft,draggable,closeable)

# --outlinebox
# ::::


```javascript /autoplay/kiosk
//smartdown.import=/assets/libs/fits.js


let dataNames = ['f770w', 'f1000w', 'f1130w', 'f2100w'];

// let min = [10.0, 28.0, 42.0, 245.0];
// let max = [25.0, 36.0, 65.0, 260.0];

// cosmic cliffs initial min max
let min = [1.0, 0.1, 1.0, 0.1];
let max = [20.0, 10.0, 100.0, 6.0];

let stretchFunction = ['x', 'x', 'x', 'x'];
let actualStretchFunction = [];
for (let i = 0; i < 4; i++){
  actualStretchFunction.push(new Function('x', 'return ' + stretchFunction[i] + ';'));
}
let activeFilter = 0;
let dataArrays = [];
smartdown.showDisclosure('panel','','transparent,bottomright,draggable,shadow,outline');
smartdown.showDisclosure('intro','','transparent,topleft,closeable,draggable,shadow,outline');
smartdown.setVariable('useF770W', true);
smartdown.setVariable('useF1000W', true);
smartdown.setVariable('useF1130W', false);
smartdown.setVariable('useF2100W', false);
smartdown.setVariable('redraw',false);
smartdown.setVariable('color1', 1);
smartdown.setVariable('color2', 3);
smartdown.setVariable('color3', 5);
smartdown.setVariable('color4', 0);
smartdown.setVariable('setFilter', dataNames[activeFilter]);
smartdown.setVariable('curveFunction', stretchFunction[activeFilter]);
smartdown.setVariable('min', min[activeFilter]);
smartdown.setVariable('max', max[activeFilter]);
smartdown.setVariable('saveSettings', false);
smartdown.setVariable('drawHistogram', false);
smartdown.setVariable('filter0', 'false');
smartdown.setVariable('filter1', 'false');
smartdown.setVariable('filter2', 'false');
smartdown.setVariable('filter3', 'false');


async function getImageData(filename) {
  const res = await fetch(filename);
  const array = await res.json();
  return array;
}

async function getImageData(filenameBase) {
  return getImageDataFromFITS(filenameBase);
}


// smartdown.showDisclosure('loading','','center,lightbox');
// dataArrays.push(await getImageData('../../assets/data/jw02107-o039_t018_miri_f770w_i2d_sci'));
// dataArrays.push(await getImageData('../../assets/data/jw02107-o039_t018_miri_f1000w_i2d_sci'));
// dataArrays.push(await getImageData('../../assets/data/jw02107-o039_t018_miri_f1130w_i2d_sci'));
// dataArrays.push(await getImageData('../../assets/data/jw02107-o039_t018_miri_f2100w_i2d_sci'));
// smartdown.hideDisclosure('loading','','');


smartdown.showDisclosure('loading','','center,lightbox');
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_f444w-f470n_i2d_match'));
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f200w_i2d_match'));
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f090w_i2d_match'));
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f335m_i2d_match'));
smartdown.hideDisclosure('loading','','');

    

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
  smartdown.setVariable('min', min[activeFilter]);
  smartdown.setVariable('max', max[activeFilter]);
}


function saveFilterVariables() {
  stretchFunction[activeFilter] = env.curveFunction;
  actualStretchFunction[activeFilter] = new Function('x', 'return ' + stretchFunction[activeFilter] + ';');
  min[activeFilter] = env.min;
  max[activeFilter] = env.max;
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
  let newmax = max[i];
  let newmin = min[i];
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
  if (env.useF770W) {f++; }
  if (env.useF1000W) {f++;}
  if (env.useF1130W) {f++;}
  if (env.useF2100W) {f++;}
  return f;
}

// assume all the arrays have same dimensions
// we should put a check in for this
let r = dataArrays[0].length;
let c = 0;
if (r > 0) { c = dataArrays[0][0].length; }
console.log(r, c);

function draw() {

  if (!allow_redraw) { return; }
  allow_redraw = false;
  setTimeout(function() { allow_redraw = true; }, 500);


  let f1color = spectrumProcess(env.color1);
  let f2color = spectrumProcess(env.color2);
  let f3color = spectrumProcess(env.color3);
  let f4color = spectrumProcess(env.color4);
  let imagedata = context.createImageData(canvas.width, canvas.height);
  let w = canvas.width;
  let h = canvas.height;

  for (let y=0; y<canvas.height; y++) {
      for (let x=0; x<canvas.width; x++) {

        // changing this code to fit the image to the viewer's screen
        // we just scale the pixel position the same position in the 
        // data array and round to the nearest integer to get an array index
        // It's sort of like sampling due to some round off error
        let ydown = h - y;
        let ny = h < r ? Math.floor(ydown / h * r) : ydown;
        let nx = w < c ? Math.floor((x / canvas.width) * c) : x; 

        let pixelindex = (y * canvas.width + x) * 4;
        imagedata.data[pixelindex+0] = 0;
        imagedata.data[pixelindex+1] = 0;
        imagedata.data[pixelindex+2] = 0;
        imagedata.data[pixelindex+3] = 255;
        if (ny < r && nx < c) {
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
    }
  }
  context.putImageData(imagedata,0,0);
}


window.addEventListener('resize', function(event){
  sizeCanvas();
  draw();
});






function exportImage() {

  const imgData = canvas.toDataURL("image/jpg");

  let x = window.open();
  setTimeout( function(params) {
    let [x, imgData] = params;
    console.log('opening tab');
    let iframe = "<iframe width='100%' height='100%' src='" + imgData + "'></iframe>"
    x.document.open();
    x.document.write(iframe);
    x.document.close();
  }, 1500, [x, imgData]);
}



let allow_redraw = true;
smartdown.setVariable('download', false);

function buildColor(r, g, b) {
  const rPadded = r.toString(16).padStart(2, '0');
  const gPadded = g.toString(16).padStart(2, '0');
  const bPadded = b.toString(16).padStart(2, '0');
  return `#${rPadded}${gPadded}${bPadded}`;
}


this.dependOn.color1 = () => {
  let rgb = spectrumProcess(env.color1);
  for (let i=0; i < rgb.length; i++) { rgb[i] = Math.floor(rgb[i] * 255); }

  const colorName = buildColor(rgb[0], rgb[1], rgb[2]);
  console.log(colorName);
  const color = `![swatch](${colorName})`;
  smartdown.set('c1', color);
  draw()
};


// this.dependOn = ['download','filter0','filter1', 'filter2', 'filter3', 'saveSettings','drawHistogram'];
// this.depend = function() {

//   if (env.download == true) {
//     smartdown.setVariable('download', false);
//     exportImage(canvas);
//   }

//   // here's the repeated code that should be fixed
//   if (env.filter0 == true) {
//     smartdown.setVariable('filter0', false);
//     activeFilter = 0;
//     updateFilterVariables();
//     drawHistogram();
//     smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
//   }

//   if (env.filter1 == true) {
//     smartdown.setVariable('filter1', false);
//     activeFilter = 1;
//     updateFilterVariables();
//     drawHistogram();
//     smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
//   }

//   if (env.filter2 == true) {
//     smartdown.setVariable('filter2', false);
//     activeFilter = 2;
//     updateFilterVariables();
//     drawHistogram();
//     smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
//   }

//   if (env.filter3 == true) {
//     smartdown.setVariable('filter3', false);
//     activeFilter = 3;
//     updateFilterVariables();
//     drawHistogram();
//     smartdown.showDisclosure('filterSettings','','center,closeable,lightbox');
//   }

//   // these events are triggered by the histogram popup
//   if (env.saveSettings == true) {
//     smartdown.setVariable('saveSettings', false);
//     saveFilterVariables();  
//   }

//   if (env.drawHistogram == true) {
//     smartdown.setVariable('drawHistogram', false);
//     drawHistogram();  
//   }


// }


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
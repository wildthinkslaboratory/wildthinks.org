Transfer infrared light captured by the JWST into light from the visual spectrum to make a cool image.


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
These are the cosmic cliffs of the [Carina Nebula](https://en.wikipedia.org/wiki/Carina_Nebula). This app displays telescope data from the [James Webb Space Telescope's](https://webb.nasa.gov/) NIRCam instrument. I've downloaded the original [FITS](https://fits.gsfc.nasa.gov/fits_home.html) files from the [MAST archive](https://mast.stsci.edu/portal/Mashup/Clients/Mast/Portal.html). I used the [astropy](https://www.astropy.org/) and [reproject](https://reproject.readthedocs.io/en/stable/#) python libraries to reduce the size of some of the FITS files so they can be downloaded to your browser more quickly. You can see the scripts I used [here](https://github.com/izzymones/fits-file-processing).  This page reads FITS files directly so what you are seeing is the actual data from the telescope, not a jpeg or some other image format.  

You can change the color assignments for each filter. I also display a histogram for each filter data.  You can choose the minimum and maximum data values and choose a stretch function if you want. Play around and see what you can make.
[Notes](/pages/telescopeNotes).
*Page Source: [`/pages/moretesting.md`](/pages/moretesting.md#-blank)*
# --outlinebox
# ::::

# :::: filters
![](../../assets/images/nircam_filters.png)
# ::::

# :::: loading
This page is reading telescope files. [](:!numLoaded) / 6 files loaded. It's worth the wait!
# ::::


# :::: panel
# --outlinebox p
[](:!c1|markdown) [](:-color1/0/5/0.1) F090W [](:XuseFilter1) [:gear:](:=filter0=filter0+1)
[](:!c2|markdown) [](:-color2/0/5/0.1) F187N [](:XuseFilter2) [:gear:](:=filter1=filter1+1)
[](:!c3|markdown) [](:-color3/0/5/0.1) F200W [](:XuseFilter3) [:gear:](:=filter2=filter2+1)
[](:!c4|markdown) [](:-color4/0/5/0.1) F335M [](:XuseFilter4) [:gear:](:=filter3=filter3+1)
[](:!c5|markdown) [](:-color5/0/5/0.1) F444W [](:XuseFilter5) [:gear:](:=filter4=filter4+1)
[](:!c6|markdown) [](:-color6/0/5/0.1) F470N [](:XuseFilter6) [:gear:](:=filter5=filter5+1)
[Notes](::intro/button,transparent,topleft,closeable,draggable) [Filters](::filters/button,transparent,bottomleft,closeable,draggable) [jpg](:=jpg=jpg+1)
# --outlinebox
# ::::


```javascript /autoplay/kiosk
smartdown.importCssCode(
`
.reducedfont {
  font-size: 18px;
}
`);

const controlPanel = document.getElementById('panel');
controlPanel.classList.add('reducedfont');



//smartdown.import=../../assets/libs/fits.js
let dataNames = ['f090w', 'f187n', 'f200w', 'f335m', 'f444w', 'f470n'];
let min = [0.1, 0.1, 0.1, 0.1, 0.1, 0.1];
let max = [8.0, 85.0, 60.0, 40.0, 15.0, 75.0];
let stretchFunction = ['x', 'x', 'x', 'x', 'x', 'x'];
let actualStretchFunction = [];

for (let i = 0; i < 6; i++){
  actualStretchFunction.push(new Function('x', 'return ' + stretchFunction[i] + ';'));
}

let activeFilter = 0;
let dataArrays = [];
smartdown.showDisclosure('intro', '', 'transparent,topleft,closeable,draggable,shadow,outline');
smartdown.showDisclosure('panel', '', 'transparent,bottomright,draggable,shadow,outline');
smartdown.setVariable('useFilter1', true);
smartdown.setVariable('useFilter2', true);
smartdown.setVariable('useFilter3', true);
smartdown.setVariable('useFilter4', true);
smartdown.setVariable('useFilter5', true);
smartdown.setVariable('useFilter6', true);
smartdown.setVariable('jpg', 0);
smartdown.setVariable('color1', 1);
smartdown.setVariable('color2', 2);
smartdown.setVariable('color3', 3);
smartdown.setVariable('color4', 4.5);
smartdown.setVariable('color5', 5);
smartdown.setVariable('color6', 4);
smartdown.setVariable('setFilter', dataNames[activeFilter]);
smartdown.setVariable('curveFunction', stretchFunction[activeFilter]);
smartdown.setVariable('min', min[activeFilter]);
smartdown.setVariable('max', max[activeFilter]);
smartdown.setVariable('saveSettings', 0);
smartdown.setVariable('drawHistogram', 0);
smartdown.setVariable('filter0', 0);
smartdown.setVariable('filter1', 0);
smartdown.setVariable('filter2', 0);
smartdown.setVariable('filter3', 0);
smartdown.setVariable('filter4', 0);
smartdown.setVariable('filter5', 0);
smartdown.setVariable('numLoaded', 0);




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


function drawHistogram() {
  if (dataArrays.length == 0) { return; }
  let div = document.getElementById('div_playable_2');
  let data2d = dataArrays[activeFilter];
  let histData = [];
  let f = new Function('x', 'return ' + env.curveFunction + ';');
  let min = env.min;
  let max = env.max;
  for (let r = 0; r < data2d.length; r++) {
    for (let c = 0; c < data2d[0].length; c++) {
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
  draw();
}

window.saveFilterVariables = saveFilterVariables;
window.drawHistogram = drawHistogram;



function spectrumProcess(number){
  let answer = [0, 0, 0];
  if (number <= 1 && number >= 0){
    answer[0] = 1 - number;
    answer[2] = 1;
  }
  if (number <= 2 && number > 1){
    answer[1] = number - 1;
    answer[2] = 1;
  }
  if (number <= 3 && number > 2){
    answer[2] = 3 - number;
    answer[1] = 1;
  }
  if (number <= 4 && number > 3){
    answer[0] = number - 3;
    answer[1] = 1;
  }
  if (number <= 5 && number > 4){
    answer[1] = 5 - number;
    answer[0] = 1;
  }
  if (number <= 6 && number > 5){
    answer[2] = number - 5;
    answer[0] = 1;
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
            c = (Math.round((newvalue - newmin) / (newmax - newmin) * 255));
        }
    }
    return c;
}


function activeFunctions() {
  let f = 0;
  if (env.useFilter1) {f++;}
  if (env.useFilter2) {f++;}
  if (env.useFilter3) {f++;}
  if (env.useFilter4) {f++;}
  if (env.useFilter5) {f++;}
  if (env.useFilter6) {f++;}
  return f;
}



let r;
let c;
let allow_redraw = true;


function draw() {

  // // put this code at the beginning of your draw function

  // if (!allow_redraw) { return; }   // if we don't have draw permission, return without drawing
  // allow_redraw = false;            // if we're here we had draw permission, we're going to draw
  //                                  // so turn drawing off for after we finish our draw
  // setTimeout(function() { allow_redraw = true; }, 250);  // after half a second turn draw back on

  let f1color = spectrumProcess(env.color1);
  let f2color = spectrumProcess(env.color2);
  let f3color = spectrumProcess(env.color3);
  let f4color = spectrumProcess(env.color4);
  let f5color = spectrumProcess(env.color5);
  let f6color = spectrumProcess(env.color6);
  let imagedata = context.createImageData(canvas.width, canvas.height);

  // we have the canvas width and height w, h
  // the fits file rows and columns c, r
  // then we have the picture dimensions w, canvas_r

  let w = canvas.width;
  let h = canvas.height;
  let canvas_r = w < c ? Math.floor(r * w / c) : r;  
  let yshift = canvas_r > r ? Math.floor((canvas_r - r) / 2) : 0;

  for (let y=0; y<canvas.height; y++) {
      for (let x=0; x<canvas.width; x++) {
        // changing this code to fit the image to the viewer's screen
        // we just scale the pixel position the same position in the 
        // data array and round to the nearest integer to get an array index
        // It's sort of like sampling due to some round off error
        let nx = w < c ? Math.floor((x / w) * c) : x; 
        let ny = 0;
        if (canvas_r < r) { 
          ny =  Math.floor(y / canvas_r * r); // we need to scale ny to the r scale
          if (y < canvas_r) { ny = r - ny; }  // if we're still on the picture invert it 
                                              // (the picture is upsidedown relative to y direction)

        } else { ny = h - y + yshift; }
      

        let pixelindex = (y * w + x) * 4;
        imagedata.data[pixelindex+0] = 0;
        imagedata.data[pixelindex+1] = 0;
        imagedata.data[pixelindex+2] = 0;
        imagedata.data[pixelindex+3] = 255;
        if (ny < r && nx < c) {
          if (env.useFilter1){
            imagedata.data[pixelindex+0] += (getValue(dataArrays[0][ny][nx],0)*f1color[0]);
            imagedata.data[pixelindex+1] += (getValue(dataArrays[0][ny][nx],0)*f1color[1]);
            imagedata.data[pixelindex+2] += (getValue(dataArrays[0][ny][nx],0)*f1color[2]);
          }
          if (env.useFilter2){
            imagedata.data[pixelindex+0] += (getValue(dataArrays[1][ny][nx],1)*f2color[0]);
            imagedata.data[pixelindex+1] += (getValue(dataArrays[1][ny][nx],1)*f2color[1]);
            imagedata.data[pixelindex+2] += (getValue(dataArrays[1][ny][nx],1)*f2color[2]);
          }
          if (env.useFilter3){
            imagedata.data[pixelindex+0] += (getValue(dataArrays[2][ny][nx],2)*f3color[0]);
            imagedata.data[pixelindex+1] += (getValue(dataArrays[2][ny][nx],2)*f3color[1]);
            imagedata.data[pixelindex+2] += (getValue(dataArrays[2][ny][nx],2)*f3color[2]);
          }
          if (env.useFilter4){
            imagedata.data[pixelindex+0] += (getValue(dataArrays[3][ny][nx],3)*f4color[0]);
            imagedata.data[pixelindex+1] += (getValue(dataArrays[3][ny][nx],3)*f4color[1]);
            imagedata.data[pixelindex+2] += (getValue(dataArrays[3][ny][nx],3)*f4color[2]);
          }
          if (env.useFilter5){
            imagedata.data[pixelindex+0] += (getValue(dataArrays[4][ny][nx],4)*f5color[0]);
            imagedata.data[pixelindex+1] += (getValue(dataArrays[4][ny][nx],4)*f5color[1]);
            imagedata.data[pixelindex+2] += (getValue(dataArrays[4][ny][nx],4)*f5color[2]);
        }
          if (env.useFilter6){
            imagedata.data[pixelindex+0] += (getValue(dataArrays[5][ny][nx],5)*f6color[0]);
            imagedata.data[pixelindex+1] += (getValue(dataArrays[5][ny][nx],5)*f6color[1]);
            imagedata.data[pixelindex+2] += (getValue(dataArrays[5][ny][nx],5)*f6color[2]);
        }
      }
    }
  }
  context.putImageData(imagedata, 0, 0);
}

window.addEventListener('resize', function(event){
  sizeCanvas();
  draw();
  console.log('resize and draw');
});


// this is the new exportImage function that works
function exportImage() {

  // this function was taking too long.  Instead of waiting for this function to finish, it was 
  // going ahead and trying to write the imgData to the new tab.  But the data wasn't there yet.
  // This resulted in a blank page if the page was full resolution.
  // It doesn't happen at all on my computer because my CPU might be a little faster.
  const imgData = canvas.toDataURL("image/jpg");

  let x = window.open();

  // // the solution is to wrap the display part of the function in a timeout function.  It will wait 1.5
  // // seconds before running the display code.  This gives the canvas a little more time to return
  // // the data.  There's probably a better way to do this with Promises and Asynch functions 
  // // but we'd need to look up the right way to do it.
  // setTimeout( function(params) {
  //   let [x, imgData] = params;
  //   console.log('opening tab');
  //   let iframe = "<iframe width='100%' height='100%' src='" + imgData + "'></iframe>"
  //   x.document.open();
  //   x.document.write(iframe);
  //   x.document.close();
  // }, 5000, [x, imgData]);
  x.write('<img src="'+ imgData +'"/>');
}


function prepareHistogram(filter) {
  if (dataArrays.length == 0) { return; }
  activeFilter = filter;
  updateFilterVariables();
  drawHistogram();
  smartdown.showDisclosure('filterSettings', '', 'center,closeable,lightbox');
} 


// this.dependOn = ['download','filter0', 'filter1', 'filter2', 'filter3', 'filter4', 'filter5', 'saveSettings','drawHistogram','redraw'];


this.dependOn.filter0 = () => {
  console.log('filter0');
  prepareHistogram(0);
} 

this.dependOn.filter1 = () => {
  prepareHistogram(1);
} 

this.dependOn.filter2 = () => {
  prepareHistogram(2);
} 

this.dependOn.filter3 = () => {
  prepareHistogram(3);
} 

this.dependOn.filter4 = () => {
  prepareHistogram(4);
} 

this.dependOn.filter5 = () => {
  prepareHistogram(5);
} 



this.dependOn.jpg = () => {
  if (env.jpg > 0) {
    let x = window.open();
    const p = x.document.createElement('p');
    let img = new Image(); 
    img.src = canvas.toDataURL("image/jpg"); 
    x.document.body.appendChild(img);  
  }
}

this.dependOn.useFilter1 = draw;
this.dependOn.useFilter2 = draw;
this.dependOn.useFilter3 = draw;
this.dependOn.useFilter4 = draw;
this.dependOn.useFilter5 = draw;
this.dependOn.useFilter6 = draw;

function buildColorString(r, g, b) {
  const rPadded = r.toString(16).padStart(2, '0');
  const gPadded = g.toString(16).padStart(2, '0');
  const bPadded = b.toString(16).padStart(2, '0');
  return `#${rPadded}${gPadded}${bPadded}`;
}

function buildColor(n) {
  let rgb = spectrumProcess(n);
  for (let i=0; i < rgb.length; i++) { rgb[i] = Math.floor(rgb[i] * 255); }
  const colorName = buildColorString(rgb[0], rgb[1], rgb[2]); 
  return `![swatch](${colorName})`;
}



this.dependOn.color1 = () => {
  const color = buildColor(env.color1);
  smartdown.set('c1', color);
  draw()
};

this.dependOn.color2 = () => {
  const color = buildColor(env.color2);
  smartdown.set('c2', color);
  draw()
};

this.dependOn.color3 = () => {
  const color = buildColor(env.color3);
  smartdown.set('c3', color);
  draw()
};

this.dependOn.color4 = () => {
  const color = buildColor(env.color4);
  smartdown.set('c4', color);
  draw()
};

this.dependOn.color5 = () => {
  const color = buildColor(env.color5);
  smartdown.set('c5', color);
  draw()
};

this.dependOn.color6 = () => {
  const color = buildColor(env.color6);
  smartdown.set('c6', color);
  draw()
};

async function getImageData(filenameBase) {
  return getImageDataFromFITS(filenameBase);
}




smartdown.showDisclosure('loading', '', 'center,lightbox');
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f090w_i2d_match'));
smartdown.setVariable('numLoaded', env.numLoaded + 1);
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f187n_i2d_match'));
smartdown.setVariable('numLoaded', env.numLoaded + 1);
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f200w_i2d_match'));
smartdown.setVariable('numLoaded', env.numLoaded + 1);
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f335m_i2d_match'));
smartdown.setVariable('numLoaded', env.numLoaded + 1);
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f444w_i2d_match'));
smartdown.setVariable('numLoaded', env.numLoaded + 1);
dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_f444w-f470n_i2d_match'));
smartdown.setVariable('numLoaded', env.numLoaded + 1);
smartdown.hideDisclosure('loading', '', '');

r = dataArrays[0].length;
c = 0;
if (r > 0) { c = dataArrays[0][0].length; }
console.log(r, c);

draw();
```
# :::: filterSettings
# --aliceblue
active filter: [](:!setFilter) [redraw histogram](:=redrawHistogram=redrawHistogram+1) [Save and Close](:=close=close+1)
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


smartdown.setVariable('redrawHistogram', 0);
smartdown.setVariable('close', 0);

this.dependOn.redrawHistogram = () => {
  if (window.saveFilterVariables) {
    window.saveFilterVariables();
    window.drawHistogram();   
  }
}

this.dependOn.close = () => {
  if (window.saveFilterVariables) {
    window.saveFilterVariables();
    smartdown.hideDisclosure('filterSettings', '',  '');
  }
}


// const swatch1 = document.getElementById('OUTPUT_3');
// let paragraphs = swatch1.getElementsByTagName("p");
// console.log(paragraphs.length);
// for (let i=paragraphs.length-1; i>=0; i--) { 
//   let p = paragraphs[i];
//   p.parentNode.removeChild(p);
//   console.log(i);
// }
// console.log(swatch1);


```
# ::::

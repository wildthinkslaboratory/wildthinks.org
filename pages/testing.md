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
These are the cosmic cliffs of the [Carina Nebula](https://en.wikipedia.org/wiki/Carina_Nebula). It has been constructed with javascript on this website with data directly from the [James Webb Space Telescope's](https://webb.nasa.gov/) NIRCam instrument. I found the data available for free on [MAST observations](https://mast.stsci.edu/portal/Mashup/Clients/Mast/Portal.html). You can change the color assignments for each filter as well as the stretch function. Play around and see what you can make.
[Notes](/pages/telescopeNotes).
# --outlinebox
# ::::

# :::: filters
![](../../assets/images/NIRCam_filters_modules.png)
# ::::

# :::: loading
This page is reading telescope files. It's worth the wait!
# ::::


# :::: panel
# --outlinebox p
F090W [](:XuseFilter1) [](:-color1/0/5/0.1)[show settings](:=filter0=filter0+1)
F187N [](:XuseFilter2) [](:-color2/0/5/0.1)[show settings](:=filter1=true)
F200W [](:XuseFilter3) [](:-color3/0/5/0.1)[show settings](:=filter2=true)
F335M [](:XuseFilter4) [](:-color4/0/5/0.1)[show settings](:=filter3=true)
F444W [](:XuseFilter5) [](:-color5/0/5/0.1)[show settings](:=filter4=true)
F470N [](:XuseFilter6) [](:-color6/0/5/0.1)[show settings](:=filter5=true)
[Redraw](:=redraw=true) [Notes](::intro/button,transparent,topleft,closeable,draggable) [Filters](::filters/button,transparent,bottomleft,closeable,draggable) 
# --outlinebox
# ::::


```javascript /autoplay/kiosk
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
smartdown.setVariable('redraw',false);
smartdown.setVariable('download', false);
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
smartdown.setVariable('saveSettings', false);
smartdown.setVariable('drawHistogram', false);
smartdown.setVariable('filter0', 0);
smartdown.setVariable('filter1', 'false');
smartdown.setVariable('filter2', 'false');
smartdown.setVariable('filter3', 'false');
smartdown.setVariable('filter4', 'false');
smartdown.setVariable('filter5', 'false');


async function getImageData(filenameBase) {
  return getImageDataFromFITS(filenameBase);
}




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









this.dependOn.filter0 = () =>  {
  // prepareHistogram(0);
  console.log('filter 0 dependOn called');
} 

this.dependOn.filter1 = () =>  {
  // prepareHistogram(1);
  console.log('filter 1 dependOn called');
} 



dataArrays.push(await getImageData('../../assets/data/jw02731-o001_t017_nircam_clear-f090w_i2d_match'));


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

```javascript /plotly/xautoplay
this.div.style.width = '100%';
this.div.style.height = '100%';
this.div.style.margin = 'auto';


// smartdown.setVariable('redrawHistogram', false);
// smartdown.setVariable('close', false);


// this.dependOn = ['redrawHistogram', 'close'];
// this.depend = function() {
//   if (env.redrawHistogram == true) {
//     smartdown.setVariable('redrawHistogram', false);
//     smartdown.setVariable('saveSettings', true);
//     smartdown.setVariable('drawHistogram', true);
//   }
//   if (env.close == true) {
//     smartdown.setVariable('close', false);
//     smartdown.setVariable('saveSettings', true);
//     smartdown.hideDisclosure('filterSettings', '',  '');
//   }
// }
```
# ::::

```javascript /autoplay
const myDiv = this.div;
myDiv.style.width = '100%';
myDiv.style.height = '100%';
myDiv.style.margin = 'auto';
myDiv.innerHTML = `<iframe src="https://www.desmos.com/calculator/rqvx3utuef" width="100%" style="min-height:600px"></iframe>`

```


# Swatch Test

- [Color](:!currentColor)
- [](:!currentColorSwatchMarkdown|markdown)

## Swatch Buttons (static)

- [![swatch](magenta) magenta](:=colorName='magenta')
- [![swatch](cyan) cyan](:=colorName='cyan')
- [![swatch](yellow) yellow](:=colorName='yellow')


## Swatch Buttons (dynamic)


**R** [](:-R/0/255/1)
**G** [](:-G/0/255/1)
**B** [](:-B/0/255/1)


```javascript /playable/autoplay
smartdown.set({
  R: 0,
  G: 0,
  B: 0,
});

function buildColor(r, g, b) {
  const rPadded = r.toString(16).padStart(2, '0');
  const gPadded = g.toString(16).padStart(2, '0');
  const bPadded = b.toString(16).padStart(2, '0');
  return `#${rPadded}${gPadded}${bPadded}`;
}

const colorComponentChanged = () => {
  console.log('colorComponentChanged: ', env.R, env.G, env.B);
  const colorHex = buildColor(env.R, env.G, env.B);
  smartdown.set('currentColor', colorHex);

  const colorSwatchMarkdown = `![swatch](${colorHex})`;
  smartdown.set('currentColorSwatchMarkdown', colorSwatchMarkdown);
};
this.dependOn.R = colorComponentChanged;
this.dependOn.G = colorComponentChanged;
this.dependOn.B = colorComponentChanged;

this.dependOn.colorName = () => {
  const colorName = env.colorName;

  console.log('colorName: ', colorName);
  smartdown.set('currentColor', colorName);
  const colorSwatchMarkdown = `![swatch](${colorName})`;
  smartdown.set('currentColorSwatchMarkdown', colorSwatchMarkdown);
};

```
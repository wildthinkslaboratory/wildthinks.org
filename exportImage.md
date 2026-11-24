```javascript
function exportImage() {
  const imgData = canvas.toDataURL("image/jpg");
  let iframe = "<iframe width='100%' height='100%' src='" + imgData + "'></iframe>"
  let x = window.open();
  x.document.open();
  x.document.write(iframe);
  x.document.close();


}

smartdown.setVariable('download', false);

this.dependOn = ['download'];
this.depend = function() {

  if (env.download == true) {
    smartdown.setVariable('download', false);
    exportImage();
  }

}



```
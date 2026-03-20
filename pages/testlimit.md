```javascript /autoplay
smartdown.setVariable("formula", "hello");
smartdown.setVariable("push", false);

this.dependOn = ["push"];
this.depend = function () {
  console.log("depend called");
  if (env.push == true) {
    console.log("button pushed");
    smartdown.setVariable("push", false);
  }
};
```

[button](:=push=true)
[](:!formula|markdown)

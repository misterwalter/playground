# Plugins

Naming conentions:

file: `playground.pluginname.js`

code: `PLAYGROUND.PluginName = function() { }`

## Extending application functionality

If you simply want to add a functionality that works the same way as `this.tween` or `this.loadImage` (application wise) just extend its prototype.

```javascript
PLAYGROUND.Application.prototype.getRandomNumber = function(max) {

  return Math.random() * max;

};
```

## Automated plugins

Plugins are constructors that get automatically instantiated at application creation time. Plugins are event driven - in particular they listen to PLAYGROUND.Application events.

## Basic plugin

```javascript
PLAYGROUND.MyPlugin = function(app) {

  this.app = app;

  /* listen to the events of choice */

  app.on("postrender", this.postrender.bind(this));
  app.on("enterstate", this.enterstate.bind(this));
  
};

/* tell playground to automatically call new PLAYGROUND.MyPlugin when application is being created */

PLAYGROUND.MyPlugin.plugin = true;

/* put the functionality in prototype */

PLAYGROUND.MyPlugin.prototype = {

  /* triggered every time application enters certain state */

  enterstate: function(data) {

    data.state.bunny = new MYLIBRARY.Bunny;

  },

  /* triggered after everything has been rendered */  

  postrender: function() {

    this.app.layer.clear("rgba(0, 0, 255, 0.5)");

  }

};
```

## Integrating loaders

Unfortunately every library/renderer has its own objects and loaders that you will need to adapt to playground's workflow (if you care about consistency - that is).

Let's assume there is no `loadImage` method and we want to add one that will be used this way:

```javascript
playground({

  create: function() {
    
    this.loadImage("candy");

  },

  ready: function() {

    console.log(this.images.candy);

  }

});
```

First of all you must obey user's preference of assets path. To do so you can utilize 

```javascript
this.getAssetEntry(key, folder, defaultExtension);
```

example:

```javascript
var entry = this.getAssetEntry("candy", "images", "png");
```

Regarding `this.paths.base` the value of entry will be as following:

```javascript
{

  /* the key that you should use to store the asset */

  key: "candy",

  /* url for you to load */

  url: "images/candy.png",

  /* path without extension - it's useful when you have to load two files
     for one entry - for example spritesheet would be .png + .json
  */

  path: "images/candy",

  /* extension dedicted from key vs defaultExtension */

  ext: "png"
}
```

Why this `defaultExtension`?

Because in playground user can override it and use folders to group keys.

```javascript
var entry = this.getAssetEntry("candies/red.jpg", "images", "png");
```

```javascript
{
  key: "sweets/red",
  url: "images/sweets/red.jpg",
  path: "images/sweets/red.jpg",
  ext: "jpg"
}
```


```javascript
PLAYGROUND.Application.prototype.loadImage = function(name) {

};
```
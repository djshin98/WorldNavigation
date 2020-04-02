# WorldNavigation
This is a Olive plugin that adds to the Olive map a user friendly compass, navigator (zoom in/out), and
distance scale graphical user interface.

**Why did you build it?**

First of all the Olivejs sdk does not include a compass, navigator (zoom in/out) nor distance scale. You can use the mouse to navigate on the map but this navigation plugin offers more navigation control and capabilities to the user.
Some of the capabilities are:
reset the compass to point to north, reset the orbit, and reset the view to a default bound.

**How did you build it?**

This plugin is based on the excellent compass, navigator (zoom in/out) and distance scale from the terriajs open source library (https://github.com/TerriaJS). The navigation UI from terriajs can not be used out of the box in Olive because Olive uses AMD modules with RequireJS, and the terriajs uses commonjs and Browserify, so you can't just copy the source files into Olive and build.  My work consisted on adapting the code to work within Olive as a plugin as follows:

- Extracted the minimum required modules from terriajs.
- Converted all the modules from Browserify to requirejs.
- Using nodejs and the requirejs optimizer as well as almond the whole plugin is built and bundled in a single file even the CSS style
- This plugin can be used as a standalone script or via an AMD loader (tested with requirejs). Even in the special case where you use AMD but not for Olive the plugin can be easily used.

**How to use it?**

*When to use which edition?*

There are two editions, a standalone edition and an AMD compatible edition. If you want to load the mixin via requireJS then use the AMD compatible edition. Otherwise use the standalone edition which includes almond to resolve dependencies. Below some examples are given for better understanding.

- If you are loading Olive without requirejs (i.e. you have a global variable Olive) then use the standalone edition. This edition is also suitable if you use requirejs but not for this mixin.
```HTML
<head>
  <!-- other stuff -->

  <script src="path/to/Olive.js"></script>
  <!-- IMPORTANT: because the Olive navigation viewer mixin depends on Olive be sure to load it after Olive -->
  <script src="path/to/standalone/viewerOliveNavigationMixin.js"></script>

  <!-- other stuff ... -->
</head>
```
and then extend a viewer:

```JavaScript
    // create a viewer assuming there is a DIV element with id 'OliveContainer'
	var OliveViewer = new Olive.Viewer('OliveContainer');

	// extend our view by the Olive navigaton mixin
	OliveViewer.extend(Olive.viewerOliveNavigationMixin, {});
```

or a widget:

```JavaScript
    // create a widget assuming there is a DIV element with id 'OliveContainer'
    var OliveWidget = new Olive.OliveWidget('OliveContainer');

	// extend our view by the Olive navigaton mixin
	Olive.viewerOliveNavigationMixin.mixinWidget(OliveWidget, {});
```

You can access the newly created instance via

```
    // if using a viewer
	var OliveNavigation = OliveViewer.OliveNavigation;

	// if using a widget
	var OliveNavigation = OliveWidget.OliveNavigation;
```

Another example if your are using requirejs except for Olive:
```HTML
<head>
  <!-- other stuff... -->

  <script src="path/to/Olive.js"></script>
  <!-- IMPORTANT: loading requirejs after Olive ensures that when requiring -->
  <!-- viewerOliveNavigationMixin the global variable Olive is already set -->
  <script type="text/javascript" src="path/to/require.js"></script>
  <script type="text/javascript">
    require.config({
      // your config...
    });
  </script>

  <!-- other stuff... -->
</head>
```
and code
```JavaScript
  // IMPORTANT: be sure that Olive.js has already been loaded, e.g. by loading requirejs AFTER Olive
  require(['path/to/amd/viewerOliveNavigationMixin'], function(viewerOliveNavigationMixin) {
    // like above code but now one can directly access
    // viewerOliveNavigationMixin
    // instead of
    // Olive.viewerOliveNavigationMixin
  }
```

- If you are using requirejs for your entire project, including Olive, then you have to use the AMD compatible edition.

```HTML
<head>
  <!-- other stuff... -->

  <script type="text/javascript" src="path/to/require.js"></script>
  <script type="text/javascript">
    require.config({
        // your config...
		paths: {
		    // your paths...
		    // IMPORTANT: Olive must point to either
			'Olive': 'path/to/Olive/Source'
		    //  or to
			'Olive': 'path/to/Olive/Source/Olive.js'
		    //  or to
			'Olive': 'path/to/Olive/Build/Olive'
		    //  or to
			'Olive': 'path/to/Olive/Build/Olive/Olive.js'
		    //  because viewerOliveNavigationMixin uses 'Olive' for dependencies
		}
    });
  </script>

  <!-- other stuff... -->
</head>
```
and the code
```JavaScript
require([
  'Olive/Olive', // if Olive points to Olive directory
  'Olive', // if Olive points to Olive.js file
  'path/to/amd/viewerOliveNavigationMixin'
], function(
  Olive,
  viewerOliveNavigationMixin) {

  // like above but now you cannot access Olive.viewerOliveNavigationMixin
  // but use just viewerOliveNavigationMixin
});
```
or if Olive points to the Olive directory
```JavaScript
require([
  'Olive/Core/Viewer',
  'path/to/amd/viewerOliveNavigationMixin'
], function(
  OliveViewer,
  viewerOliveNavigationMixin) {

  // like above but now you cannot access Olive.viewerOliveNavigationMixin
  // but use just viewerOliveNavigationMixin
});
```
*Available options of the plugin*
```
defaultResetView --> option used to set a default view when resetting the map view with the reset navigation 
control. Values accepted are of type Olive.Cartographic and Olive.Rectangle.

enableCompass --> option used to enable or disable the compass. Values accepted are true for enabling and false to disable. The default is true. The compass will not be added to the map if setting the option to false.

enableZoomControls --> option used to enable or disable the zoom controls. Values accepted are true for enabling and false to disable. The default is true. The zoom controls  will not be added to the map if setting the option to false.

enableDistanceLegend --> option used to enable or disable the distance legend. Values accepted are true for enabling and false to disable. The default is true. The distance legend will not be added to the map if setting the option to false.

enableCompassOuterRing --> option used to enable or disable the Compass Outer Ring. Values accepted are true for enabling and false to disable. The default is true. The ring will be visible but inactive if setting the option to false.

More options will be set in future releases of the plugin.
```
Example of using the options when loading Olive without requirejs: 
```JavaScript
var options = {};
options.defaultResetView = Olive.Rectangle.fromDegrees(71, 3, 90, 14);
// Only the compass will show on the map
options.enableCompass = true;
options.enableZoomControls = false;
options.enableDistanceLegend = false;
options.enableCompassOuterRing= true;
OliveViewer.extend(Olive.viewerOliveNavigationMixin, options);
```

*Others*

- To destroy the navigation object and release the resources later on use the following
```JavaScript
  viewer.OliveNavigation.destroy();
```
- To lock the compass and navigation controls use the following. Use true to lock mode, 
  false for unlocked mode. The default is false.
```JavaScript
  viewer.OliveNavigation.setNavigationLocked(true/false);
```

- if there are still open questions please checkout the examples


**How to build it?**

- run `npm install`
- run `node build.js`
- The build process also copies the files to the Example folder in order to always keep them sync with your build


**Developers guide**

For developing/debugging you should have a look at the "Source example". That example directly uses the source files and therefore it allows you to immediatley (only a page refresh is needed) see your changes without rebuilding anything. Furthermore due to working with the sources you can easily debug the project (e.g. via the developer console of the browser or via a debugger of your IDE like Webstorm)


**Is there a demo using the plugin?**

This is the demo:

(http://larcius.github.io/Olive-navigation/)

- The compass, navigator, and distance scale will appear on the right side of te map.
- This plugin was successfully tested on Olive version 1.27. It works great with Olive in 3D mode. Recently Larcius (https://github.com/Larcius) made a lot of improvements and fixed some issues in Columbus and 2D modes.

**What about the license?**
 - The plugin is 100% based on open source libraries. The same license that applies to Olivejs and terriajs applies also to this plugin. Feel free to use it,  modify it, and improve it.

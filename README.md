#FontDetect
Current version: **3.0.1**

The FontDetect JavaScript library provides a singleton class that can tell you which font is being used for a particular element, or detect whether a font face has been loaded in your browser. It can also poll for the font being loaded and call a callback function when it loads - or another callback if it doesn't load within a timeout period.

##How to Use FontDetect
The first step is to download the [current version of fontdetect.zip](http://www.atomicjetpacks.com/projects/fontdetect/fontdetect.zip) and extract the script file (fontdetect.js or fontdetect.min.js). (Or clone this repo, of course.)

The script tag can be placed after the `</body>` if you wish.

```<script type='text/javascript' src='/path/to/fontdetect.js'></script>```

FontDetect provides three methods you can call. The first method, `FontDetect.isFontLoaded`, returns immediately with true or false depending on whether the specified font is present. This is most useful when you want to make sure an internal browser font is present:

```
<style>
#theContainerDiv {
    font-family: Trebuchet MS, Verdana, sans-serif;
}
</style>

if (!FontDetect.isFontLoaded('Trebuchet MS'))
{   // Assume we fell back to Verdana, so adjust the column width.
    $('#theContainerDiv').css('width', 500);
}
```

The `FontDetect.whichFont` method is more useful: It tells you exactly which font in the font stack is being used for a given element. In other words, it tells you what `getComputedStyle(element).getPropertyValue("font-family")` should return but doesn't. (Or in jQuery, what you would expect `$(element).css("font-family")` to return.)

```
<style>
#theContainerDiv {
    font-family: Oxygen, Verdana, Arial, sans-serif;
}
</style>

var wContainer;

switch (FontDetect.whichFont('#theContainerDiv'))
{
    case 'Oxygen':  wContainer = 500; break;
    case 'Verdana': wContainer = 480; break;
    default:        wContainer = 510; break;
}
$('#theContainerDiv').css('width', wContainer);
```

Finally, there's `FontDetect.onFontLoaded`. This waits asynchronously until the font gets loaded, or until a timeout period ends. Either way it calls a callback function that you specify:

```
$(document).ready(function(){
    FontDetect.onFontLoaded ('MyCoolFont', onMyCoolFontLoaded, onItDidntLoad, {msTimeout: 3000});
});

function onMyCoolFontLoaded (fontname)
{
    alert (fontname + ' looks good!');
}

function onItDidntLoad (fontname)
{
    alert (fontname + ' didn\'t load within 3 seconds');
}
```

When you call `FontDetect.onFontLoaded`, you can specify how often to poll for the font (default is 100 ms, or 10 times/second); how long to try before giving up (default is 2 seconds), and the callback functions on successful or failed loads.

##Try it out

[The original FontDetect project page](http://www.atomicjetpacks.com/project/fontdetect/how-to-detect-if-a-font-really-loaded) has a nifty demo of the library in action. It's been updated to use the latest version of the library.

##API Reference
###isFontLoaded
**FontDetect.isFontLoaded (fontname)**

This method determines whether a given font is loaded.

Required params | Type | Description
:-----|:-----|:-----
fontname | string | The name of the font to check for.

Returns|Description
:-----|:-----
boolean | true if the font has been loaded, else false if it didn't load or isn't provided by the browser.

###onFontLoaded
**FontDetect.onFontLoaded (fontname, onLoad, onFail, {msInterval: ms between polls , msTimeout: how long to keep polling} )**

This method polls for the existence of a font in your browser. When a font gets loaded or a timeout period has passed, it will call the specified callback function(s).

Required params|Type|Description
:-----|:-----|:-----
fontname | string | The fontname of the webfont you want to detect.
onLoad | function | A function to be called when the font gets loaded. Use null if you don't have one.
onFail | function | A function to be called if the font doesn't get loaded within the timeout period. Use null if you don't have one.
You must specify at least one callback: onLoad or onFail. The callback functions take one parameter: the fontname that was detected.

Optional named params in { }|Type|Description
:-----|:-----|:-----
msInterval | integer | How many milliseconds between polling for the font. Default is 100ms.
msTimeout | integer | How many milliseconds to keep polling before giving up & calling the onFail callback. Default is 2000ms.

###whichFont
**FontDetect.whichFont (element)**

This method determines which font, out of the fonts specified in the element's font stack, is actually being used to render it.

Required params|Type|Description
:-----|:-----|:-----
element | string or object | The element to examine. If jQuery is loaded we assume this is a jQuery selector string or object. If it's not loaded we expect this to be a DOM element.

Returns| Description
:-----|:-----
string | The name of the font that is being used for this element.

##Submitting code changes
If you've fixed a bug or came up with a great addition to the API, I welcome your code submissions! To make things smoother, please follow these guidelines:

- Create an Issue for the bug or feature you're submitting, if there isn't one already.
- If someone is proposing a new feature or a change to the existing API, please chime in in the comments for that issue. It's important that the library evolves in a way that the userbase feels is useful and straightforward to use.
- Submit to the latest Stage branch, not Master. This way we can test it & possibly integrate more than one bugfix or feature as a group.
- The project's coding convention is for Allman-style bracing, where { and } are in the same column as their controlling statement.
- Please use Hungarian notation for internal variables, and prefix any parameters with "p_". Use the existing code as a guide for what prefixes to use. 
- Don't assume that jQuery is loaded. If it's a jQuery-specific feature, at least make sure your code degrades gracefully if it's not there. You can test for jQuery with `if (window.jQuery)`. (See _getCss for an example of how to handle this.)

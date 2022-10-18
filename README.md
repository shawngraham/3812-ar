# 3812-ar


## Make target images aka tracking images
1 - decide what your target images are going to be (ie the things that you are going to augment)

2 - rename them in numerical order, 000_target.png, 001_target.png etc

3 - upload all at once at [https://hiukim.github.io/mind-ar-js-doc/tools/compile](https://hiukim.github.io/mind-ar-js-doc/tools/compile)

4 - examine the results - look for the red circles to indicate that the tool has found many points of interest. more is always better. If it looks pretty sparse, you might have to modify your images (pick new ones, increase contrasts on the existing ones) and try again

5 - double check that your images are in the correct order 

6 - download the targets.mind file


## Get the Basic Repo
7 - go to this repo and hit the 'fork' button to _clone_ my repository to yours.

8 - replace my trackers with your own: upload the targets.mind file so that it overwrites mine. You should have something like this:

```
|
|-gravestone 2
|-index.html
|-readme.md
|-targets.mind
```

## Add your image targets

9 - we're going to edit the index.html file so that we swap out the existing image targets for the ones you just created. But first I want you to notice a few things.

In the <head> part of the file - the code between <head> and </head> tags, there is a series of <script> tags that are telling the browser to go to these locations, and get the javascript code that knows how to do AR. DO NOT MESS WITH ANYTHING HERE. For reference, they look like this:

```

  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <script src="https://cdn.jsdelivr.net/gh/hiukim/mind-ar-js@1.1.5/dist/mindar-image.prod.js"></script>
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/donmccurdy/aframe-extras@v6.1.1/dist/aframe-extras.min.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/hiukim/mind-ar-js@1.1.5/dist/mindar-image-aframe.prod.js"></script>
  </head>

```

The augmented reality consists of us telling the browser where to find the tracking images (the image targets) and what kind of content to associate with them.

10 - Look for this line within the <body> section of the index.html:

```
<a-scene mindar-image="imageTargetSrc: https://cdn.jsdelivr.net/gh/hiukim/mind-ar-js@1.1.5/examples/image-tracking/assets/band-example/band.mind;" color-space="sRGB" renderer="colorManagement: true, physicallyCorrectLights" vr-mode-ui="enabled: false" device-orientation-permission-ui="enabled: false">
```

The first bit `<a-scene` tells the browser, 'here are the parameters for doing AR'. The next element, `mindar-image` tells the browser where to find the tracking images. Right now, it is set to find a file called `band.mind` at a different website. But since we just uploaded our file to the same location as this index.html, we can strip out that https://cdn etc to just be our target.mind file. Do that now. **nb** keep the final ;", and do not alter any of the other elements in this line. It'll look like this:

```
<a-scene mindar-image="imageTargetSrc: targets.mind;" color-space="sRGB" renderer="colorManagement: true, physicallyCorrectLights" vr-mode-ui="enabled: false" device-orientation-permission-ui="enabled: false">
```

11 - Now commit your changes. **If** you want your app to track more than one image at a time (ie, display two or more augmentations at once, if two or more trackers are visible), modify with maxtrack: `imageTargetSrc: targets.mind; maxTrack: 2"`

## make your web app live

12 - Go to the repo settings, and under the Pages option, make your repo live on the web.

## test it

13 - It'll take a couple of minutes, but once the repo is published on github.io, load the page on your phone or tablet. If everything is good, you'll be asked to allow the site to access your camera. Say yes. If you can see through the camera, and you get a scanning animation, look at your tracking image so that the image is in the middle of the frame. Your augmentation should appear! 

But if you get a blue screen with three loading dots, and your camera view doesn't appear, there's an error in your code you need to fix.

## How does the code attach the AR asset to the tracking image?

If you look at the code for the basic experience, you'll see that inside the <a-scene> </ascene> tags we've also defined some assets within the  <a-assets> tag, <a-asset-item> and <a-video>. For each one, we have assigned an id (so we can reference it later). The first is graveModel, and it references a .gltf file of mine that I downloaded from Sketchfab.com. I downloaded the zip file, and placed the whole unzipped folder inside this repo. The `src="` bit points to the .gltf file inside that folder - but for the .gltf file to work, you need everything else that's in that folder.

The next <a-asset-item> points to a location outide this repo with another gltf file. The final <a-video> points to a video that is IN this repo; you can't hotlink to youtube. 

```
      <a-assets>
        <a-asset-item id="graveModel" src="gravestone2/scene.gltf"></a-asset-item>
        <a-asset-item id="raccoonModel" src="https://cdn.jsdelivr.net/gh/hiukim/mind-ar-js@1.1.5/examples/image-tracking/assets/band-example/raccoon/scene.gltf"></a-asset-item>
        <a-video id="testMovie" src="me.mp4" width="16" height="9" position="0 0 -20"></a-video>
      </a-assets>

```

Having defined our assets, we now tell the webapp which tracking image to go to. Remember that the computer starts counting at 0 rather than 1, which is why I reminded you to name your files `000_image.png,` `001_image.png` etc.

```
      <a-entity mindar-image-target="targetIndex: 0">
        <a-gltf-model rotation="0 0 0 " position="0 -0.25 0" scale="0.05 0.05 0.05" src="#graveModel" animation-mixer>
      </a-entity>
      <a-entity mindar-image-target="targetIndex: 1">
        <a-gltf-model rotation="0 90 0 " position="0 -0.25 0" scale="0.05 0.05 0.05" src="#bearModel" animation-mixer>
      </a-entity>
      <a-entity mindar-image-target="targetIndex: 2"> 
        <a-video alpha-video width="1" height="1.32" rotation="0 0 0" position="0 0 0.3" src="#testMovie" material="transparent: true" play="true"></a-video>
      </a-entity>  
  ```

Here, we use `<a-entity mindar-image-target="targetIndex: 0">`  to say, hey, here's how we want you to display the graveModel on the screen or hey, we want you to attach the video to the third target (targetIndex: 2) and lay it out like this.

Do you see how you could add other assets to your webapp, and how to tie them to the different target images?

        
## Location Based AR
        
See this [from my digital archaeology course](https://digiarch.netlify.app/week/11/augmented-reality/) to get a handle on making your AR appear based on location.

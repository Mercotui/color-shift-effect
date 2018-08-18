# Color Shift Effect

[![License: CC BY 4.0](https://licensebuttons.net/l/by/4.0/80x15.png)](https://creativecommons.org/licenses/by/4.0/)

This is an exercise in learning Blender special effects video editing.
The repository contains all files needed to create the following output:

![Alt Text](output/test_output.gif)

It also features the blend files for some other experiments, in [`./experiments/*`](experiments).

I have chosen to omit any source video files, to keep the repo from being insanely large,
you can source your own, mine were all recorded on a 2013 smartphone known for its horrible camera.

# Legal

I choose to release the project files and images found in this repository under the Creative Commons Attribution 4.0 license.
A copy of this license can be found at `./LICENSE`.

# How it's made

This is a step by step reference, mostly for future me, as to how I created this effect.

## Getting Set Up

Some boilerplate actions before the creative part starts.

### Clean Slate

Open blender to an empty project, and select the video editing interface.
First we need a black background:
-   On the timeline, select frame \#1
-   Click `Add->Effect Strip->Color`
-   Right click the newly created Strip
-   Extend to desired frame count

### Importing the video

Next up is importing a source file:
-   Again select frame \#1
-   Click `Add->Video`, or in this case `Add->Image`
-   Select the desired source file.
-   In case of an image, extend to desired frame count
-   Under the properties editor, set the dimensions to match the aspect ratio of the source

## The effects

Lets first take a moment to write down what we want.
-   Each channel of the RGB color space of the original image should start separated,
    translated to form a tessellation
-   The channels should start to move, overlapping each-other, the colors being added up
-   Once the channels are all centered, and perfectly overlap,
    and all summed channels produce the original image, stop moving

I want to note that isolating the channels, and translating them requires 2 nested actions.
This is easily done by using Meta-Strips, that can be nested.
Also, first well start with a single RGB channel, and then duplicate the work for the other 2 channels

### Separating the channels

So to start with separating the channels:
-   Select the imported source strip
-   Click `Strip->Make Meta Strip`
-   Press tab while selecting a meta strip to edit it
-   We need to nest twice, so again select the, now nested, imported source strip
-   Again click `Strip->Make Meta Strip`
-   Edit this 2nd nested strip
-   Click `Add->Effect Strip->Color`
-   Edit the RGB values of the new color effect to `R=1 G=0 B=0`
-   Set the Blend mode to `Multiply`


### Duplicating for the other channels

Since we now have the translated red channel, we want to add the green and blue channels.
-   Go back to the top level
-   Select the meta strip for the translated red channel
-   CTRL C, CTRL V, CTRL V
-   Edit the color filters of the 2 new meta strips to be `R=0 G=1 B=0` and `R=0 G=0 B=1` respectively
-   Edit the transformations to the desired locations

### Adding the animation

TODO, KEYFRAMES, and grouping them

## Exporting

Last step is creating the video file, or animated GIF.

### Exporting to MP4 H.264

To create a video:
-   In properties editor, go to output
-   Set file format to `ffmpeg video`
-   Under Encoding, select the preset `H264 in MP4`
-   Under Render, hit `Animation` and wait for all frames to be exported

### Exporting to GIF

Using ffmpeg, we can run the following commands.

```
ffmpeg -y -i file.mp4 -vf palettegen palette.png
ffmpeg -y -i file.mp4 -i palette.png -filter_complex paletteuse -r 10 -s 320x480 file.gif
```

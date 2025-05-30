---
title: ffmpeg | shira.at
toc: true
header-includes:
    <link rel="stylesheet" href="/style.css">
---

# About

ffmpeg is a commandline tool to convert media (audio, video) into different formats, manipulate or compress them, put filters on, etc. .  

This is a list of ffmpeg flags and commands I commonly use.  
This is neither a complete list nor a guide on how to use ffmpeg.  
If the code doesn't start with `ffmpeg` then it's only the middle part.  
This middle part needs to be inserted `ffmpeg -i input.mp4 <here> output.mp4`.  

# Common flags

## Compress video 

Should be between 20 and 28 for good quality.  
The lower the bigger the filesize.  
51 is max compression and looks very bad.  
A good value is 25 for compression/filesize balance.  
The default value is 23.  

    ffmpeg -i input.mp4 -crf 25 output.mp4

## Faster starting for web videos

Using the `-movflags +faststart` parameters puts specific metadata infront of the file, which allows for faster starting when received via e.g. a browser.  
This does not raise the filesize.

    ffmpeg -i input.mp4 -movflags +faststart output.mp4

## Remove Audio / Video

    -an
    -vn

### Example: Convert video to audio

    ffmpeg -i input_video.mp4 -vn output_audio.mp3

## Change Codecs

vcodec changes the video's codec and acodec the audio one.  
copy means it takes the input straight to the output. This makes it way faster.

Do not copy the codec if you cut a video length. This will create errors because it HAS TO recode the beginning of the cut' video or else it will create an error-like video.

Examples for video and audio codecs:

    -vcodec h264
    -vcodec libx265
    -vcodec copy

    -acodec aac
    -acodec mp3
    -acodec copy

## Convert Music to lower bitrate

    -b:a 96k

## Set the preset / speed

Speed and Quality are always opposite of each other.  
Longer compression makes smaller filesizes, faster makes worse compression.

The default is medium. slow takes 40% longer, slower 100% longer than medium.  
On my test, with a 1min clip, the filesize difference between medium and slow was ~1MB (18.7 -> 17.5)

    -preset slow

## Tuning for specific videos

With h264 video codec (which is mostly used in mp4) you can set a custom tuning to use for your videos. Example tuning options:

    - film  , for high quality
    - animation  , for cartoons
    - stillimage  , for slideshows
    - grain  , to preserve film grain
    - fastdecode  , to view faster on slower devices

Example usage:

    ffmpeg -i input.mp4 -vcodec h264 -tune film output.mp4

## Aspect ratio

    -aspect 16:9

## Double Speed of video (0.5 = x2)

    -vf "setpts=0.5*PTS"

## Double audio speed

    -filter:a "atempo=2.0"

## Set bitrate

    -b:v 1M
    -b:a 96k

## Volume of audio

    ffmpeg -i input.mp3 -af 'volume=1.5' output.mp3

## Change scale of video
iw = width, ih = height, -1 means auto  
The first example command halfs the width (and height automatically)  
The second one sets the width and height (and thus the aspect ratio) explicitly  

    -vf scale="iw/2:-1"
    -vf scale="1280:720"
    # what also works for scaling to HD is:
    -vf scale="1280:-1"

## Crop (Cut out part of a) Video
w = width of output video  
x = left top startpoint for the rectangle

    ffmpeg -i input.mp4 -filter:v "crop=w:h:x:y" output.mp4


## Video to images

Names are image-001.png, image-002.png, ...  
-r = framerete in which screenshots are taken, default=25  
%3d = name, 001 - 999  

    ffmpeg -i input.mp4 -r 1 -f image2 image-%3d.png

## Add Text to the video

    -vf drawtext="text='MyTextHere': fontcolor=white: fontsize=24: x=(w-text_w)/2: y=(h-text_h)/2"

## Combine 2 videos

    ffmpeg -i in_one.mp4 -c copy -bsf:v h264_mp4toannexb -f mpegts _1.ts
    ffmpeg -i in_two.mp4 -c copy -bsf:v h264_mp4toannexb -f mpegts _2.ts
    ffmpeg -i "concat:_1.ts|_2.ts" -c copy -bsf:a aac_adtstoasc together.mp4

## Convert a video from 30 to 60 FPS

    -filter:v "minterpolate='fps=60'"

## Set the target framerate of the video

    -r 60

## Make 2 input files side by side

    ffmpeg -i 30.mp4 -i 60.mp4 -filter_complex hstack comparison.mp4

## Cut Video 

The time either in 00:01:23.000 or in seconds format, here seconds are used  
This example would cut the video, so that only seconds 3 to 8 plays  
aka. 5 seconds long output video
-t = duration, -ss = starttime

    ffmpeg -i input.mp4 -ss 3 -t 5 output.mp4

## Burn in subtitles

    ffmpeg -i in.mkv -vf subtitles=in.mkv out.mp4

## Convert a folder from mp4 to mp3

    for i in *.mp4; do ffmpeg -i "$i" "$(basename "$i" .mp4)".mp3; done

Another example with converting music from webm/opus to m4a/aac:

    for i in *.webm; do bn=$(basename "$i" ".webm"); ffmpeg -i "$i" -c:a aac "$bn.m4a"; rm "$i"; done

## Create a thumbnail from video

This creates a picture from the first frame of the video.  
There are more complex thumbnail generation commands out there.

    ffmpeg -i "input.mp4" -vf "thumbnail" -frames:v 1 input_thumbnail.jpg

## Convert an image

You can use ffmpeg to convert between image formats too

    ffmpeg -i "input.jpg" output.png


## Remove noise (like TV static) from video

This can remove noise (e.g. flickering of pixels in the background) and "soften" videos, reducing filesize but also removing a few fine-grained details

    ffmpeg -i "input.mp4" -vf "atadenoise" output.mp4


# Complex Example Commands

## Convert gif to mp4 file

Source: [https://web.dev/replace-gifs-with-videos/](https://web.dev/replace-gifs-with-videos/)  

    ffmpeg -i my-animation.gif -vf "crop=trunc(iw/2)*2:trunc(ih/2)*2" -b:v 0 -crf 25 -f mp4 -vcodec libx264 -pix_fmt yuv420p my-animation.mp4

## Create a meme video with white bar and text ontop of a video

    ffmpeg -i inputfile.mp4 -filter_complex \
    "[0:v]pad=iw:ih+100:0:(oh-ih)/2+50:color=white, \
     drawtext=text='Me coming to my wife's funeral':fontsize=42:x=(w-tw)/2:y=(100-th)/2" \
    outputfile.mp4

## Meme video with top and bottom white bars and text

    ffmpeg -i input.mp4 -filter_complex \
    "[0:v]pad=iw:ih+100:0:(oh-ih)/2:color=white, \
     drawtext=text='ONE DOES NOT SIMPLY':fontsize=24:x=(w-tw)/2:y=(50-th)/2, \
     drawtext=text='STOP ME FROM FILTERING':fontsize=24:x=(w-tw)/2:y=h-25-(th/2)" \
    output.mp4

## Convert 15fps mkv with subtitles to mp4

The input file is a 15fps and full HD .mkv video with external subtitles and unoptimized, weird encodings.  
This command makes it into a 30fps normal HD .mp4 video with the subtitles burned into the video and encodings that are optimized and widely acknowledged (h264/aac).

    ffmpeg -i input.mkv -vf "subtitles=input.mkv,scale=1280:720" -crf 25 -vcodec h264 -acodec aac -r 30 output.mp4



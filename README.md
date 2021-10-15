# How to: Digitize Hi8 tapes

Recently, I digitized a dozen or so Hi8 tapes using cheap hardware from Amazon.

The process works by replaying the Hi8 tapes using a camcorder. The SVideo and audio on
the camcorder is connected to a SVideo to HDMI converter. The HDMI signal is captured on
a computer with a HDMI to USB capture card.

# Setup
- Camcorder
- SVideo to HDMI Converter
- BlueAVS HDMI to USB Capture Card
- Ubuntu 20.04 (using `ffmpeg`)

Connect the camcorder outputs to the SVideo to HDMI converter. Convert the SVideo to HDMI converter
to the HDMI to USB capture card. Connect the HDMI to USB capture card to the computer.

```
Camcorder ==> SVideo to HDMI ==> HDMI to USB ==> Computer
```

# Capture

1. Load a Hi8 tape into the camcorder. Rewind the tape to the beginning.
2. Start playback on the camcorder.
3. Begin recording by running the following command:
   ```
   timeout -s INT 125m ffmpeg -f alsa -i default -f v4l2 -framerate 30 -video_size 720x480 -i /dev/video0 recording.mkv
   ```

   This command captures the audio and video from the capture card for 125 minutes, and then stops.
   Hi8 tapes are about 2 hours long, so 125 minutes leaves a minimal amount of "emptiness" at the end.

   You may want to open the `recording.mkv` file in VLC after a few minutes to verify the media is being captured
   correctly.
4. When the playback completes, you'll have a large `recording.mkv` media file.
5. If smart phone playback is desired, convert the media file into a more shareable format:
   ```
   ffmpeg -i recording.mkv \
       -vcodec libx264 -crf 28 -vprofile high -vlevel 3.0 -pix_fmt yuv420p -movflags +faststart \
       -acodec aac recording.mp4
   ```

   Note: `-crf 28` controls the quality. The default is `23`. Lower values result in
   better quality, but larger file sizes.

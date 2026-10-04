# The pitch video

`make_pitch_cut.py` joins three clips into the 55-second pitch video: a lab test, the replay on real flight data and the simulated crossing.

The clips and the finished video are not in the repository. They are attached to the release [Videos, Demo Day 2026](https://github.com/dwn97/taipei-drift/releases/tag/videos-2026-10-04).

To render the video yourself, put `position_video.mp4`, `jury_forest_gap.mp4` and `demo.mp4` from the release into this folder and run:

```bash
python make_pitch_cut.py        # writes TaipeiDrift_pitch_cut.mp4 and a 720p copy
```

It needs numpy, opencv-python-headless, pillow, imageio-ffmpeg, fonttools and brotli.

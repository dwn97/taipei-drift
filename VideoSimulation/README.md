# The pitch video

`make_pitch_cut.py` joins three clips into the 55-second pitch video: the cart test, the replay of the real flight and the simulated crossing.

The clips are not in the repository. Two of them are attached to the release [Videos, Demo Day 2026](https://github.com/dwn97/taipei-drift/releases/tag/videos-2026-10-04): `position_video.mp4` (the cart test) and `demo.mp4` (the simulated crossing).

The third, `jury_forest_gap.mp4`, is made from the photos of the real flight. Their licence is not stated, so neither that clip nor the finished pitch video is published here. [docs/real-flight.md](../docs/real-flight.md) says how to rebuild the clip from the photos.

With all three clips in this folder:

```bash
python make_pitch_cut.py        # writes TaipeiDrift_pitch_cut.mp4 and a 720p copy
```

It needs numpy, opencv-python-headless, pillow, imageio-ffmpeg, fonttools and brotli.

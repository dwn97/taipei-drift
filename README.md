# Taipei Drift

**We stay on course when GNSS is jammed.**

Navigation for low-cost drones without GNSS. Built by team Taipei Drift at the Taiwan Defense Tech Hackathon 2026, organised by EDTH and the Unmanned Vehicles R&D Center at National Taiwan University, 2 to 4 October 2026 in Taipei. Our entry for Challenge 2: navigation and drift correction in GNSS-denied environments.

This is a weekend prototype. Nothing in it has been flown by us: the results come from a simulator and from the replay of one recorded flight. The section "What is real and what is simulated" says which is which.

## The problem

Once GNSS is jammed, a low-cost drone has only its inertial sensors left, and they drift fast. On 30 flights of the Mid-Air dataset the position is 50 m off after 36 seconds in the median ([docs/findings.md](docs/findings.md)). The drone can no longer hold its route or come home.

## What we built

- **Camera and a free aerial map.** The downward camera measures how the drone moves over the ground. Every 300 m its picture is compared with an aerial photo stored on board, which gives an absolute position. A check refuses matches that do not fit, and the navigator states its own error in metres.
- **Heading from the sun** (simulated sensor). A slit over a row of pixels reads the direction of the sun, and with date and time that gives the heading. No magnet disturbs it.
- **Ships' radio over water** (simulated). Where the camera has nothing to match, bearings on the AIS transmissions of ships hold the position.
- **A range finder** (simulated, used in the demo flight only). It measures the height above ground, which the camera needs to turn image motion into metres when the drone flies low.
- **One filter** fuses these with the inertial sensors and the barometer.

## Results

| Test | Result | Where |
|---|---|---|
| Simulated flights over real aerial imagery of Wufeng, Taiwan, 4.3 km without GNSS. Two sealed flights, run once after the code was frozen | 12 m and 32 m median error, no wrong fix accepted | [docs/simulation-results.md](docs/simulation-results.md) |
| The development flight with inertial sensors and barometer only, for comparison | 3 km median error | [docs/simulation-results.md](docs/simulation-results.md) |
| Replay of a real drone flight over Miaoli, Taiwan, 4 km without GNSS | 3.3 m median error; dead reckoning alone 54 m; of 1,646 map fixes none more than 10 m wrong | [docs/research/tuniu-level2-results.md](docs/research/tuniu-level2-results.md) |
| Simulated crossing between two islands, GNSS lost on the way (the demo video) | 4 to 10 m median error in five of six repeat flights, 24 m in the sixth | [docs/simulation-results.md](docs/simulation-results.md) |
| Computing on one laptop CPU core | 11 ms per camera frame, 0.07 to 2 s per map fix | `baseline/scripts/time_navigator.py` |

## Videos

The videos are attached to the release [Videos, Demo Day 2026](https://github.com/dwn97/taipei-drift/releases/tag/videos-2026-10-04), so that the repository stays small:

- `TaipeiDrift_pitch_cut.mp4`: the 55-second pitch video in three parts: a lab test, the replay on real flight data and the simulated crossing. A 720p copy is there too.
- `position_video.mp4`, `jury_forest_gap.mp4`, `demo.mp4`: the three clips it is cut from.

The simulated crossing alone is also in the repository: [demo/TaipeiDrift_demo_pitch_720p.mp4](demo/TaipeiDrift_demo_pitch_720p.mp4).

## What is real and what is simulated

- **Real:** the aerial imagery, the photos and the RTK track of the Miaoli flight, the lab video.
- **Simulated:** every flight in the simulator, the sun sensor, the ships' radio, the range finder, and the barometer in the Miaoli replay.
- **In the demo flight** the autopilot steers by the true position. Our estimate runs alongside and is scored against it.
- **Not built:** the sun sensor and the radio direction finder exist as a design and a simulation.

## Limits

- **Night:** an ordinary camera sees nothing. It would need a thermal camera against the same map.
- **Fog, or ground rebuilt since the map was made:** the map match fails, and the status says so.
- **Jammed from take-off:** not covered. The navigator calibrates itself on a stretch with GNSS.
- **Open water without ships' radio:** the estimate drifts until land is in view again.

## What is in the repository

| Folder | Content |
|---|---|
| [baseline/](baseline/) | The IMU-only baseline and the camera navigator: map matching with the check that refuses bad fixes |
| [vio/](vio/) | Visual-inertial navigation on the Mid-Air dataset with an error-state Kalman filter |
| [sim/](sim/) | The simulator: ROS 2 Jazzy and Gazebo Harmonic in Docker, with the drone, its sensors, the islands, the ships and the dashboards |
| [scripts/](scripts/) | Replay, scoring and figure scripts for the simulated flights |
| [experiments/](experiments/) | Research experiments on Taiwan data and public datasets |
| [TRN/](TRN/) | A separate study: navigation by terrain height with a laser altimeter over Taiwan's mountains, in simulation |
| [TestVideo/](TestVideo/) | Camera positioning from video, tested on a cart in the lab |
| [VideoSimulation/](VideoSimulation/) | The script that cuts the pitch video; its clips are in the release |
| [demo/](demo/) | The simulated crossing as a video, with its logged data |
| [docs/](docs/) | Results, method, plan, research notes and the pitch deck |
| [PPT/](PPT/) | An earlier deck and the sensor cost report |
| [outputs/](outputs/) | Logged simulator runs |

## Where to start reading

- [docs/simulation-results.md](docs/simulation-results.md): every simulated result, stage by stage
- [docs/research/tuniu-level2-results.md](docs/research/tuniu-level2-results.md): the replay on real flight data
- [docs/status-saturday.md](docs/status-saturday.md): the state of the work on Saturday evening
- [docs/findings.md](docs/findings.md): what we measured on the first night and what it changed
- [docs/method.md](docs/method.md): how image matching and the filter work, with worked numbers
- [docs/landscape.md](docs/landscape.md): existing products and their limits
- [docs/brief.md](docs/brief.md) and [docs/PLAN.md](docs/PLAN.md): what the challenge asks for, and our plan
- [docs/TaipeiDrift_Pitch.pdf](docs/TaipeiDrift_Pitch.pdf): the pitch deck as handed in for Demo Day, 17 pages; [docs/pitch-offline/](docs/pitch-offline/) is an earlier version of it as a web page that runs without internet
- [data/README.md](data/README.md): datasets and licences; [research/](research/): links to the two key papers

## Setup

Requires [uv](https://docs.astral.sh/uv/). On an Apple Silicon Mac, use the native arm64 build of uv.

```bash
uv sync                              # creates .venv with Python 3.12 and the libraries
source .venv/bin/activate
```

The datasets are not in the repository. [data/README.md](data/README.md) says how to get Mid-Air (about 10 GB, through a form and `scripts/fetch_midair.sh`) and ALTO (1.73 GB, from Dropbox in a browser).

The simulator runs in Docker and needs nothing else installed: see [sim/README.md](sim/README.md).

## Team

Alessandro Di Piano, Dan Anfernee Diaz, Dustin Nothwang, Felix Zukunft, Ilhan Neuville, rychardsandreireyes.

## About this copy

This is a public copy of the team's working repository as of 4 October 2026, published without the working history and the team's internal notes. Some documents mention branches and files of the working repository that are not part of this copy.

## Licence

The team's own work in this repository is under the [PolyForm Noncommercial License 1.0.0](LICENSE). In short: you may read, run and change it for non-commercial purposes. For commercial use, ask the team by opening an issue in this repository.

Third-party material keeps its own licence: the fonts of the offline deck (SIL Open Font License), the drone photo in the deck (PX4 docs, CC BY 4.0), and the datasets and images listed in [data/README.md](data/README.md).

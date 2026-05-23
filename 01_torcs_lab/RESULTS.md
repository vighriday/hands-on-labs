# My TORCS Lab Notes

**Me:** Hriday, 19, CS student, India.

**My submission:** NeuroPit, a cognitive twin for F1 drivers. Repo: [vighriday/neuropit-may-2026](https://github.com/vighriday/neuropit-may-2026)

## How I came to this lab

Built NeuroPit for the May challenge first. It reads real F1 telemetry (FastF1, Abu Dhabi 2021 by default) and emits nine cognitive state scores plus a prescription. Saw the lab was about an autonomous driver and the motorsport angle pulled me in even though I had already shipped.

Going through it after my submission was in. Reading and reflection pass, not a driving pass. Being straight about that up top because the lab itself says the point is honest engagement.

## What I read

In the order the lab tells you to read it.

- `00_intro.md`
- `README_LAB.md`
- `01_torcs_lab.md` (Tasks 1 to 5)

Parameter block from the guide:

```python
TARGET_SPEED = 100
STEER_GAIN = 30
CENTERING_GAIN = 0.20
BRAKE_THRESHOLD = 0.9
ENABLE_TRACTION_CONTROL = True
```

Five knobs. That is the surface area.

## What I did not do

Did not install TORCS, did not run `torcs_jm_par.py`. Windows setup is a real install and the container path needs Docker which I had just cleaned off my disk to free space. Noting this so the reflections below are weighted correctly.

## Mapping the loop to NeuroPit

Lab describes a sense, decide, act loop:

1. TORCS sends sensors (speed, position, angle, track distance).
2. Python decides steering, throttle, brake, gear.
3. Commands go back, car moves, next tick.

NeuroPit has the same shape one layer up.

1. FastF1 emits telemetry per driver per lap.
2. Worker runs nine scoring functions (stress, confidence, fatigue, cognitive load, attention, strategic pressure, panic, emotional drift, failure forecast).
3. Prescription worker emits a recommendation, gateway audits, dashboard broadcasts.

Same loop. Different actuator. TORCS turns a wheel. NeuroPit nudges a human.

## Heuristics versus models

Lab is clear this is rule based control, not ML. Intelligence sits in the constants.

My prescription scoring is also heuristic. Every weight is in one dataclass file (`src/backend/common/weights.py`) so it is auditable and tuneable in one place instead of magic numbers across workers. Had been a little defensive about that being a non-ML choice. Reading a serious lab treat tuned heuristics as a primary teaching example was a good reframe.

## What I would change first if I ran it

Guide says one variable at a time. I would follow that.

1. **Baseline.** Touch nothing. Watch the car at `TARGET_SPEED=100`.
2. **`TARGET_SPEED` to 150.** Guide suggests this. Predicted failure: corner overshoot.
3. **`BRAKE_THRESHOLD` to 0.7.** Brake earlier to survive the higher speed.
4. **Leave `STEER_GAIN` alone.** Wrong gain spins the car. No learning value in cranking it before I know the baseline.
5. **Traction control off last.** Only with a stable car under the other changes.

Predictions, not results.

## What I noticed about the lab

Setup is in a separate folder. Lab body reads like a lab, not a sysadmin guide. Most tutorials force you through a hostile install before you can learn anything. Pulling install out into a side door is a kinder design choice. Filing for the next time I write dev docs.

Success criteria are deliberately soft ("car moves without crashing", "you observed a visible change"). Matches the learning-over-performance framing and makes the lab safe to be honest in. Which is why I am being honest in this file.

## Cross pollination back to NeuroPit

- **Single variable discipline.** Lab keeps saying change one knob, observe, move on. NeuroPit has ten weight dataclasses. I have not always been disciplined about tuning them one at a time. Cheap to adopt.
- **Sim feed as negative control.** Could pipe a TORCS frame into my Kafka schema. Expected output: garbage, because no human behind a sim car. But the inference layer failing loudly on sim data would be a useful sanity check that it is not hallucinating emotions on anything you feed it.

## Honest closing note

Read the lab end to end. Did not run the simulator. Reflections above are about the material and the mapping back to my own project. Filing this fork as a record of engagement, not a completed driving exercise.

If I get a quiet weekend after the deadline I will install it, run the baseline, and come back with actual numbers.

Hriday

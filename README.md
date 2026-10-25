# Multimodal Eye-Strain Dataset

This repository contains processed CSV data for research into eye-strain
signals. It contains dataset files and this documentation only; it does not
contain the application or model implementation.

## Files

| File | Rows | Description |
|---|---:|---|
| `dataset/train.csv` | 19,113 | Processed training split |
| `dataset/test.csv` | 2,426 | Processed test split |
| `dataset/train_engineered.csv` | 19,113 | Training split with derived features |
| `dataset/test_engineered.csv` | 2,426 | Test split with derived features |

The engineered files contain the same records as their corresponding
non-engineered files, with additional columns. Rows are ordered as recorded;
the data was logged at approximately one-second intervals.

## Data and privacy

The source data contains measurements derived from eye/face landmarks, screen
lighting, keyboard/mouse activity, and the project's eye-strain scoring logic.
No raw images or video are included. Exact timestamps, source filenames, user
IDs, and original session IDs have been removed from the published CSVs.
Relative session time (`session_duration`) is retained. Removing direct
identifiers does not guarantee that behavioral measurements cannot be
re-identified; do not attempt to identify or link participants.

Use these data only where the required participant consent, ethics approval,
and applicable data-protection requirements are in place. The strain labels
and score are research signals, not medical diagnoses. This dataset is small
and should not be assumed to represent a broader population.

## Field descriptions

The four CSVs share the fields below. Engineered CSVs also contain the
additional derived fields in the second table.

| Field(s) | Description |
|---|---|
| `session_duration` | Relative elapsed time within a recording session, in seconds. |
| `ear` | Eye Aspect Ratio (EAR), computed from eye landmarks. |
| `blink_count` | Cumulative detected blink count within the session. |
| `blink_rate` | Detected blinks per minute over the recent one-minute interval. |
| `gaze_x`, `gaze_y` | Horizontal and vertical iris offsets relative to the eye centers, in the pipeline's stored coordinate units. |
| `gaze_deviation` | Magnitude-based gaze-deviation measure derived from the horizontal and vertical offsets. |
| `head_pitch`, `head_yaw`, `head_roll` | Head-pose angle estimates derived from facial landmarks. |
| `face_detected` | Whether a face was detected for the row. |
| `luminance` | Image brightness estimate. |
| `light_variance` | Variation in the measured image-lighting values. |
| `glare_detected` | Whether the pipeline flagged glare. |
| `keystrokes_per_min`, `backspace_per_min`, `clicks_per_min` | Keyboard or mouse activity rates per minute. |
| `mouse_dist_per_min` | Mouse movement distance per minute, in the pipeline's stored units. |
| `vfq_score` | Project-computed 0–100 eye-strain score from weighted feature signals; it is not a validated clinical questionnaire score. |
| `strain_label` | Categorical project label: `NORMAL`, `LOW`, `MODERATE`, `HIGH`, or `SEVERE`. |
| `strain_label_enc` | Numeric encoding of `strain_label`: `NORMAL`=0, `LOW`=1, `MODERATE`=2, `HIGH`=3, `SEVERE`=4. |
| `strain_trend` | Categorical trend label: `IMPROVING`, `STABLE`, or `WORSENING`. |
| `strain_trend_enc` | Numeric trend encoding: `IMPROVING`=-1, `STABLE`=0, `WORSENING`=1. |
| `ear_norm`, `blink_rate_norm`, `gaze_deviation_norm`, `head_pitch_norm`, `head_yaw_norm`, `head_roll_norm`, `luminance_norm`, `light_variance_norm`, `keystrokes_per_min_norm`, `backspace_per_min_norm`, `mouse_dist_per_min_norm`, `clicks_per_min_norm` | Min-max normalized versions of the corresponding measurements. In this release, normalization was performed before the train/test split; this can leak information from the test set and should be addressed when conducting new evaluations. |

### Additional fields in engineered CSVs

| Field(s) | Description |
|---|---|
| `ear_roll_mean`, `ear_roll_std` | Rolling mean and standard deviation of EAR over up to 30 rows. |
| `blink_rate_roll_mean`, `blink_rate_roll_std` | Rolling mean and standard deviation of blink rate over up to 30 rows. |
| `gaze_deviation_roll_mean`, `gaze_deviation_roll_std` | Rolling mean and standard deviation of gaze deviation over up to 30 rows. |
| `head_pitch_roll_mean`, `head_pitch_roll_std` | Rolling mean and standard deviation of head pitch over up to 30 rows. |
| `vfq_score_roll_mean`, `vfq_score_roll_std` | Rolling mean and standard deviation of the score over up to 30 rows. |
| `blink_irregularity` | Absolute difference between the row's blink rate and the session-wide mean blink rate. This uses the whole session, not only past observations. |
| `gaze_instability` | Rolling standard deviation of gaze deviation over up to 30 rows. |
| `fatigue_progression` | Row position normalized from 0 to 1 across the session. |
| `ear_trend` | Slope of EAR over up to the preceding 60 rows; a negative value indicates decreasing EAR. |
| `quantum_feature_0`–`quantum_feature_3` | Four bounded outputs of the project's quantum feature-fusion circuit; these are derived model features, not direct measurements. |

Rolling features use up to 30 rows (approximately 30 seconds at the nominal
sampling rate). The `ear_trend` feature uses up to 60 rows. Some engineered
features use information from later rows in the same session, so they are not
strictly causal for real-time prediction.

## Suggested use

For evaluation, keep the provided train/test split intact. When creating a new
evaluation, fit any scaling or feature-selection steps on the training data
only, and avoid using whole-session or future-derived features for a
real-time prediction task.

## Citation

Please cite the associated paper or project publication when available. No
author or participant personal details are included in this README.

# UIT-MGA

**A multimodal mobile-game affect dataset with multi-perspective continuous annotations**

UIT-MGA is a mobile-centered affective-computing dataset collected through direct play on participants' own Android phones. It synchronizes gameplay imagery, detailed game and device-state telemetry, and consented voice-chat audio with continuous Valence-Arousal annotations from the player and two independent peer observers.

The dataset is designed to support research on temporal player affect, first- versus third-person annotation perspectives, consensus-label construction, context-dependent modality value, and multimodal learning under heterogeneous personal-device conditions.

## Highlights

- Direct bring-your-own-device (BYOD) Android gameplay.
- Two purpose-built and structurally contrasting games:
  - **ShootingGame:** an offline single-player side-scrolling shooter.
  - **KartGame:** an online two-player kart racer with competitive and social context.
- Synchronized gameplay frames, rich telemetry, and voice-chat audio.
- Continuous Valence and Arousal traces from **Self**, **Peer 1**, and **Peer 2**.
- Three consensus references: **EWE**, **Weighted**, and **Soft**.
- Standardized participant-grouped evaluation splits shared across label constructions.
- Player-facing video used as annotation evidence but excluded from the public modeling release.

## Dataset organization

The release uses pseudonymous participant and session identifiers. The two game subsets are stored under `Data_Game1/` and `Data_Game2/`.

```text
UIT-MGA/
|-- Data_GameShooting/                         # ShootingGame
|   `-- T000/                           # Pseudonymous participant ID
|       |-- 20260815_094217/            # Timestamped session directory
|       |   |-- *_EWE.csv
|       |   |-- *_Weighted.csv
|       |   `-- *_Soft.csv
|       `-- 20260815_094217.zip          # Modalities for the same session
|-- Data_GameKart/                         # KartGame
|   `-- T000/
|       |-- <session_timestamp>/
|       |   |-- *_EWE.csv
|       |   |-- *_Weighted.csv
|       |   `-- *_Soft.csv
|       `-- <session_timestamp>.zip
|-- LICENSE
|-- LICENSE-CODE
`-- README.md
```

Each session archive contains the high-volume synchronized streams:

```text
<session_timestamp>.zip
|-- VoiceChat/
|   `-- *.wav
|-- Telemetry/
|   `-- *.csv
`-- ScreenRecording/
    `-- <stream_id>/
        `-- *.jpg
```

The label CSV files remain directly inspectable outside the archive, while the media and telemetry streams are packaged by session.

## Annotation references

Each player-level recording was annotated after play using the [MATAN browser-based annotation platform](https://github.com/hoanghd-uit/MATAN). The player provided the **Self** trace, and two independently assigned peers provided **Peer 1** and **Peer 2** traces. Valence and Arousal were annotated separately as continuous values on `[-1, 1]`.

The release provides three derived consensus references:

| Reference | Construction | Intended use |
| --- | --- | --- |
| **EWE** | Evaluator Weighted Estimator using evaluator reliability | Primary benchmark target |
| **Weighted** | Reliability-weighted aggregation based on leave-one-annotator-out comparisons | Sensitivity to heterogeneous annotator reliability |
| **Soft** | Arithmetic mean of the three source traces | Equal-weight consensus baseline |

The source perspectives and derived consensus references must not be treated as additional interchangeable raters when computing inter-rater or cross-perspective reliability.

## Corpus-level metadata

### `participants.csv`

Contains approved participant-level information under pseudonymous identifiers, including demographic, self-reported gaming-profile, and device-summary fields.

### `sessions.csv`

Contains the mapping between participant and session identifiers, game, timing, duration, device type, modality availability, and the shared-match identifier used to connect paired KartGame recordings where applicable.

## Telemetry schema

Telemetry fields are divided into control, device-state, interaction, and game-specific context. Exact columns can differ between the two games; the header of each `Telemetry/*.csv` file is authoritative.

| Field group | Representative fields | Notes |
| --- | --- | --- |
| Control and alignment | `timestamp_unix`, `session_id`, `engine_tick`, `time_passed` | Used for synchronization and auditing; excluded from predictive features |
| Device state | `device_type`, `fps`, `battery_level` | Describes heterogeneous personal-device conditions |
| Shared interaction | `input_intensity`, `input_diversity`, `idle_time`, `activity`, `movement`, `score` | Captures player activity and input summaries |
| ShootingGame context | Player position/state, health, weapons, room or dungeon context, enemies, boss events | Captures progression and combat consequences |
| KartGame context | Kart dynamics, steering, drifting, boosts, item use, collisions, race progress, opponent context | Captures competitive and multiplayer events |

Identifiers, raw timestamps, engine ticks, elapsed-time fields, window indices, and device strings must not be used as predictive features in the reported benchmark.

## Recommended evaluation protocol

UIT-MGA contains temporally ordered Valence-Arousal traces. Do **not** create a random train-test split over individual time windows, because neighboring windows from the same participant or session are temporally dependent.

Use the provided assignments in `splits/` and follow these rules:

1. Keep all windows from a participant in the same fold.
2. Keep all windows from a session in the same fold.
3. For paired KartGame recordings, ensure that the same `shared_match_id` never occurs in both training and test data.
4. Use identical folds for EWE, Weighted, and Soft targets.
5. Fit normalization, feature selection, fusion weights, and every learned parameter using the training portion of each fold only.
6. Evaluate ShootingGame and KartGame separately unless a cross-game experiment is explicitly intended.

The reference benchmark segments sessions into non-overlapping 5-second windows and reports the concordance correlation coefficient (CCC) and mean absolute error (MAE). CCC measures agreement in temporal variation and calibration, while MAE measures pointwise error.

## Getting started

1. Clone this repository.
2. Download the dataset archive from the DOI-backed release once available.
3. Extract `Data_Game1/`, `Data_Game2/`, `participants.csv`, and `sessions.csv` into the repository root without changing the participant/session hierarchy.
4. Use the predefined partitions under `splits/`.
5. Run the preprocessing and baseline entry points documented inside `scripts/`.

Dataset archive and DOI:

- **Archive:** `[DATASET ARCHIVE URL]`
- **DOI:** `[DATASET DOI]`

## Privacy and responsible use

- Participant and session identifiers are pseudonymous.
- Voice-chat audio is included only for participants who consented to its public distribution.
- Player-facing video was available to annotators but is **not** part of the public modeling release.
- The dataset license grants copyright and applicable database rights only. It does not grant publicity, privacy, personality, trademark, or other rights that the licensors cannot grant.
- Users remain responsible for complying with applicable law and their institution's research-ethics requirements when working with human-derived data.
- Users are strongly requested not to attempt participant re-identification or link pseudonymous records to external identities.

## License

This repository uses separate licenses for data and software:

- Dataset content, including labels, metadata, gameplay frames, telemetry, and distributed voice audio: **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)**. See [`LICENSE`](LICENSE).
- Original software in `scripts/` and other explicitly marked code: **MIT License**. See [`LICENSE-CODE`](LICENSE-CODE).
- Third-party models, libraries, and assets remain subject to their respective licenses.

## Citation

Citation metadata will be updated when the dataset DOI and paper publication details are available. Until then, cite the repository as follows:

```bibtex
@misc{hoang2026uitmga,
  title        = {UIT-MGA: A Multimodal Mobile Gaming Dataset with Multi-Perspective Continuous Annotations},
  author       = {Hoang, Ha Dang and Hop, Do Trong},
  year         = {2026},
  howpublished = {Dataset and code repository},
  url          = {[REPOSITORY URL]},
  note         = {Dataset DOI forthcoming}
}
```

## Acknowledgments

This research was supported by the VNUHCM-University of Information Technology's Scientific Research Support Fund.

## Contact

- Ha Dang Hoang: `26948007@grad.uit.edu.vn`
- Do Trong Hop: `hopdt@uit.edu.vn`

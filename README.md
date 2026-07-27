# BGMI AI Commentary MVP

Minimal local web app for uploading an esports gameplay video and generating an AI-style commentary timeline.

This version is a deployable MVP. It analyzes sampled frames for motion, killfeed-region activity, and healthbar pressure, then turns those events into commentary and offline WAV speech. Optional CSV/JSON match logs can add player names, weapons, grenades, and event types.

## Setup

```powershell
cd "C:\Bgmi project\bgmi_commentary_deploy"
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
python -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
```

Then open:

```text
http://127.0.0.1:8000
```

## Outputs

Each uploaded video creates a folder under `outputs/` containing:

- `commentary.txt`
- `commentary.srt`
- `commentary.wav`
- `events.json`

## Names And Weapons

The app can mention names and guns if you upload a match log with the video. Supported columns:

- `timestamp_sec`
- `killer`
- `victim`
- `weapon` or `gun` or `firearm`
- `action` or `event` or `type`

Your existing `C:\Bgmi project\Data\match_killfeed.csv` already has names, so upload it in the optional log field. For firearms and grenades, add a `weapon` column or plug in a trained OCR/detector later.

## How To Improve Accuracy

For production-quality commentary, train or add these models:

- Killfeed OCR/detector: detect killfeed row boxes and read player names.
- HUD detector: detect healthbar, alive count, team count, minimap danger zone.
- Fight classifier: classify sampled clips as rotate, loot, spray, knock, finish, revive, clutch.
- Speech layer: send generated lines to a TTS engine and mux audio back into video.

## Training Guns And Throwables

Use the `Train Model` section in the UI:

1. Upload a short clip where one weapon or throwable is clearly visible.
2. Add a label such as `M416`, `AKM`, `Frag Grenade`, `Smoke`, `Stun`, or `Molotov`.
3. Choose the screen region, usually `Weapon HUD` or `Throwable HUD`.
4. Extract grayscale samples.
5. Repeat for several labels, then click `Train classifier`.

The local classifier is saved to `models/weapon_utility_knn.yml`. For best results, add many clips per class from different matches, resolutions, skins, and lighting conditions.

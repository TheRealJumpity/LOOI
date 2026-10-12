# LOOI v2.1

A browser version of the LOOI desk robot, built for PC (and phones): round animated eyes, camera face tracking, an always-listening "Hey Looi" microphone, a tiny AI brain that runs on your own device, and a username/password login. One static file, so it runs on GitHub Pages.

## Deploy on GitHub Pages

1. Create a public repository (e.g. `looi`).
2. Upload `index.html` to the repository root.
3. **Settings → Pages**, Source: *Deploy from a branch*, `main` and `/ (root)`, then save.
4. Open `https://<your-username>.github.io/looi/` after a minute.

Camera and microphone need HTTPS (GitHub Pages is HTTPS) or `localhost`.

## What happens when you open it

1. **Sign in / create an account** (username + password, no email).
2. **Brain loading screen**: a looping tower-building animation plays while the AI model downloads and starts. The menu and buttons stay hidden until the brain is ready.
3. Click **Wake up LOOI** and allow camera and microphone.
4. Say **"Hey Looi"** and then your request. He listens continuously for the wake phrase, chirps when he hears it, and takes what you say next as the request. Either "Hey Looi, what time is it" in one go, or "Hey Looi" and then the request. Anything said without the wake phrase is ignored.

## The brain

- Model: **SmolLM2-135M-Instruct** (official `HuggingFaceTB` repo), the smallest usable chat model. Download is about 118 MB (4-bit, on GPUs with fp16) or 137 MB (8-bit, on the CPU).
- It runs entirely **on your device** with Transformers.js (WebGPU when available, otherwise WASM on the CPU). Your messages are never sent to an AI server.
- The model downloads **once** from the Hugging Face CDN and is cached by the browser, so later starts are fast and work offline (the page's JavaScript libraries load from jsDelivr).
- It is a very small model: answers are short and simple. The CPU path is slower than WebGPU (a few seconds per reply).
- If loading fails (no internet on first run, unsupported browser) you can retry or continue with a tiny keyword-based offline brain.
- Settings → *Delete downloaded brain* clears the cached model.

## Eyes and tracking

- Round eyes with spring-eased expressions, squash-and-stretch while looking around, drift, blinking, and idle behaviours.
- Camera: real face detection (MediaPipe BlazeFace, tiny model) makes his eyes follow your face. If that can't load, it falls back to smoothed motion tracking. Video never leaves your device.
- Priority of where he looks: finger/mouse drag, driving, mouse movement, tilt, then your face, then idle glances.
- He greets you when you appear, startles at loud noises, and sleeps in the dark or after 2 minutes of inactivity.

## PC controls

| Key | Action |
| --- | --- |
| `M` | menu |
| `F` | fullscreen |
| `Esc` | close menu / clock |
| `Space` | poke |
| Arrows / `WASD` | drive around the "desk" (screen) |

Move the mouse and his eyes follow it; click-and-drag to steer his eyes; hold a click to make him fall in love. Voice commands: "dance", "what time is it", "timer for 2 minutes", "be happy", "take a photo", "spin", "show the clock", "go to sleep".

## Honest limitations

- **Login is local.** GitHub Pages has no server, so accounts and chat history are in your browser's `localStorage`. Passwords are salted and hashed (PBKDF2), but this is a convenience lock, not real security, and accounts don't sync between devices.
- **No physical robot.** Driving, spinning and edge detection are simulated on screen.
- **Voice recognition** uses the browser's built-in engine (Chrome / Edge / Safari). Chrome sends that audio to Google's speech service, which is why it needs an internet connection. Firefox has no support, so type in the chat box instead.
- First-time load needs internet for the brain, the face-tracking library and its model.

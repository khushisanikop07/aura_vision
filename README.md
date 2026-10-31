# AI Object Detector — Aura Vision

Real-time object detection in the browser. The app opens your webcam, runs a
pretrained YOLO model on every frame, and draws a box around each object it
recognises with its name and confidence percentage.

Everything runs on your own machine. The video never leaves the browser and is
never uploaded anywhere.

## Features

- Live webcam preview with glowing bounding boxes, labels and confidence %
- Start / stop detection, camera on / off, screenshot (saves a PNG), clear
- Live detection list, statistics (objects found, FPS, average confidence,
  highest confidence), scene activity level (LOW / MEDIUM / HIGH)
- Detection history with timestamps
- Simple plain-language scene analysis panel
- Sign-in (email + password, or Google) and an MCP server so AI assistants can
  connect and ask what the detector can see

## Requirements

- Node.js 18 or newer
- A webcam, and a browser that allows camera access (Chrome, Edge, Firefox, Safari)

## Install and run

```sh
npm install --legacy-peer-deps
npm run dev
```

The `--legacy-peer-deps` flag is needed because plain `npm install` hits a known
npm bug while resolving `vitest`'s peer dependencies. It does not change which
packages are installed.

Then open the URL the terminal prints (usually http://localhost:8080), press
**START CAMERA**, allow the camera, then press **START DETECTION**.

The first detection run downloads the model file (`public/yolov8n.onnx`, about
12 MB). After that it is cached and starts instantly.

## Other commands

```sh
npm run build      # production build
npm run preview    # serve the production build locally
npm run lint       # eslint
npm run format     # prettier
npm run test       # vitest
```

## How the object detection works (simple version)

1. Every frame of your webcam video is copied onto a 640 x 640 square image.
2. The model (YOLOv8n, "nano") makes 8,400 guesses per frame. Each guess is a
   box plus a score for each of the 80 everyday objects it was trained on —
   person, cup, laptop, dog and so on.
3. Guesses scoring under 45% are thrown away, and duplicate boxes sitting on
   the same object are merged into one.
4. The remaining boxes are drawn over the video. This repeats as fast as your
   laptop can manage, usually 10–30 frames per second.

All of this happens inside your browser using ONNX Runtime Web, so your camera
feed stays on your device.

## Project layout

```
src/
  lib/yolo.ts              model loading, per-frame detection, box merging
  lib/labels.ts            the 80 object names the model knows
  lib/mcp/                 MCP tools exposed to AI assistants
  routes/index.tsx         the main detector page
  routes/login.tsx         sign-in page
  components/lab/          camera view, buttons, panels, history
public/yolov8n.onnx        the AI model file
```

## Environment variables

`.env` holds the backend address and a **publishable** key. Publishable keys are
safe to commit — they are designed to be visible in a browser. There are no
secret keys in this repository.

## Notes

- The camera only works on `localhost` or an HTTPS page. Browsers block camera
  access on plain `http://` addresses other than localhost.
- Sign-in and the MCP server rely on the hosted backend configured in `.env`.
  The detector itself works with or without them.

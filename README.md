# ∿ Polarity Inverter Pro

A production-ready, PWA-compatible single-page web application for real-time audio polarity inversion with intelligent feedback suppression. Designed specifically for mobile browsers with support for background operation, Bluetooth audio routing, and automatic gain control (AGC).

## Features

- **Real-Time Polarity Inversion** — Flip audio phase instantaneously using the Web Audio API `GainNode` at `-1.0` with smooth parameter ramps to eliminate clicks
- **Mono/Stereo Support** — Automatically detects and processes the microphone's native channel configuration (1 or 2 channels)
- **AGC Guard™** — Multi-layer feedback prevention system:
  - Fast-attack dynamics compressor (12:1 ratio, 3ms attack)
  - Brickwall limiter (hard ceiling at -1 dBFS)
  - Sustained energy detector that automatically ducks output to 5% when feedback oscillation is detected, then smoothly restores level
- **Bluetooth Audio Ready** — Respects system audio routing; automatically detects active Bluetooth outputs on supported browsers (Chrome Android)
- **Background Operation** — Uses the Screen Wake Lock API, visibilitychange resilience, and audio context lifecycle management to maintain monitoring when the phone is locked or the app is backgrounded
- **PWA Installable** — Includes inline Web App Manifest and mobile-optimized viewport settings for "Add to Home Screen" deployment
- **Zero Dependencies** — Pure vanilla HTML5, CSS3, and ES6+; no build step required
- **Privacy First** — All audio processing occurs locally in the browser. No audio data is transmitted, stored, or leaves the device

## Quick Start

### Prerequisites
- A modern mobile browser (Chrome, Safari, Edge, Samsung Internet)
- HTTPS origin (required for `getUserMedia()` microphone access)
- Optional: Bluetooth speaker paired to your device

### Installation
1. Clone or download this repository
2. Serve `index.html` over HTTPS:
   ```bash
   # Local development with Python
   python3 -m http.server 8000
   
   # Or use npx
   npx serve .

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
   ```
3. On your smartphone, open the served URL
4. Tap **"Add to Home Screen"** in your browser menu for optimal background performance
5. Tap **Start Monitoring** and grant microphone permission

### Recommended Setup for Background Use
- **iOS Safari**: Install to Home Screen first. If audio stops when locking the screen, ensure "Silent Mode" is off and start the app from the home screen icon rather than the browser tab.
- **Android Chrome**: Install to Home Screen. Audio routing to Bluetooth is handled automatically by the OS; use the volume panel to confirm the active output device.
- **Wake Lock**: The app automatically requests a screen wake lock when monitoring starts to prevent sleep-induced audio suspension.

## Technical Architecture

### Audio Processing Graph
```
MediaStreamSource → InputGain → HighPassFilter(80Hz) → InverterGain(±1) 
→ Compressor(AGC) → Limiter(-1dB) → OutputGain → Analyser → Destination
```

### AGC Guard Algorithm
The feedback detector maintains a rolling 200ms history of output RMS levels. When any of the following conditions are met, the output gain is ducked exponentially to 5%:

- **Sustained High**: All samples in the 200ms window exceed the AGC threshold minus 6 dB
- **Extreme Peak**: Instantaneous level exceeds -6 dBFS

Recovery occurs automatically when levels drop 12 dB below the threshold for a sustained period. Attack is immediate; release follows a slow 50ms exponential envelope to prevent pumping.

### Bluetooth Detection
On browsers supporting `navigator.mediaDevices.enumerateDevices()` and `AudioContext.setSinkId()`, the app polls for audio output devices containing "bluetooth" in their label and surfaces this in the UI. On iOS, audio routing is managed exclusively via Control Center.

## Browser Support

| Feature | Chrome/Edge | Safari iOS | Firefox | Samsung Internet |
|---------|-------------|------------|---------|------------------|
| Microphone Access | ✅ | ✅ | ✅ | ✅ |
| Web Audio API | ✅ | ✅ | ✅ | ✅ |
| Wake Lock API | ✅ | ✅ | ❌ | ✅ |
| Bluetooth Detection | ✅ (Android) | ❌ | ❌ | ✅ |
| Background Audio | ✅* | ✅** | ✅* | ✅* |

\* Requires "Add to Home Screen" or tab persistence  
\*\* Requires Home Screen installation and non-Silent Mode

## File Structure

```
/
├── index.html          # Complete single-file application (HTML + CSS + JS)
└── README.md           # This file
```

The entire application is intentionally contained in a single HTML file for portability, easy hosting on static platforms (Cloudflare Pages, GitHub Pages, Netlify), and offline caching.

## Privacy & Security

- No external network requests are made after initial page load
- No audio is recorded to disk, uploaded, or transmitted
- Microphone access is requested via standard browser permission prompts and can be revoked at any time via browser settings
- The app functions entirely within the browser's secure sandbox

## License

MIT License — Free for personal and commercial use.

---

**Disclaimer**: This tool is intended for acoustic testing, audio engineering workflows, and hearing assistance scenarios. Users are responsible for complying with local laws regarding audio monitoring and recording. Always verify your output volume before enabling polarity inversion to prevent equipment damage or hearing injury.

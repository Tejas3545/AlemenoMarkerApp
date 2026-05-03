# AlemenoMarkerApp — Custom Marker Detection & Extraction

An Android app built with **React Native + Expo** that detects custom square markers using **OpenCV 4.10.0**, extracts them with perspective correction, and displays 20 captured markers in a grid.

---

## 📱 Features

- Live camera feed using **React Native Vision Camera v5**
- Native **OpenCV** integration via a React Native NativeModule
- **Perspective warp** to extract and straighten markers to 300×300px
- **Orientation correction** using corner brightness analysis
- Captures **20 unique markers** from 20 different frames
- Clean dark-mode UI with real-time scan counter

---

## 🏗️ Architecture

```
Camera (takePhoto every 600ms)
    ↓
RNFS.readFile → base64 JPEG
    ↓
MarkerDetector.detectMarkersInJpeg() [NativeModule]
    ↓
OpenCV Pipeline:
  1. Scale image to max 1280px
  2. Adaptive threshold + Canny edge detection (combined)
  3. findContours → approxPolyDP (detect quads)
  4. Filter by size & aspect ratio
  5. getPerspectiveTransform + warpPerspective → 300×300px
  6. correctOrientation() — rotate to canonical position
  7. Return as base64 JPEG data URIs
    ↓
React Native FlatList grid (4 columns × 5 rows)
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Framework | React Native (Expo SDK 54) |
| Build | EAS Build (cloud Android APK) |
| Camera | react-native-vision-camera v5 |
| Computer Vision | OpenCV 4.10.0 (Android SDK) |
| Native Bridge | React Native NativeModule (Kotlin) |
| File I/O | @dr.pogodin/react-native-fs |

---

## 🔧 Key Native Files

- `android/app/src/main/java/com/anonymous/AlemenoMarkerApp/MarkerDetectorModule.kt` — OpenCV detection logic
- `android/app/src/main/java/com/anonymous/AlemenoMarkerApp/MarkerDetectorPluginPackage.kt` — RN module registration
- `android/app/src/main/java/com/anonymous/AlemenoMarkerApp/MainApplication.kt` — OpenCV init + package registration
- `eas-build-pre-install.sh` — Downloads OpenCV SDK at build time

---

## 🚀 Building the APK

```bash
npm install
eas build -p android --profile preview
```

The `eas-build-pre-install.sh` script automatically downloads and integrates the OpenCV 4.10.0 Android SDK during the cloud build.

---

## 📋 Detection Algorithm

1. **Preprocessing**: Downscale to ≤1280px, convert to grayscale
2. **Edge Detection**: Combined Adaptive Threshold + Canny (handles various lighting)
3. **Contour Finding**: `findContours` with `RETR_LIST`
4. **Quad Filtering**: `approxPolyDP` → exactly 4 vertices, min 30×30px, aspect ratio 0.5–2.0
5. **Perspective Correction**: `getPerspectiveTransform` + `warpPerspective` → 300×300px output
6. **Orientation**: Rotate so the brightest corner quadrant is top-left (matches Marker 1's pip)

---

## 👤 Author

**Tejas Solanki** — Alemeno Frontend Internship Assignment

# ROADMAP.md — SemanticImage

## 12-Month Vision

Transform SemanticImage from a mature single-module library into a comprehensive on-device semantic vision platform supporting visionOS, advanced multi-object segmentation, and real-time streaming capabilities.

---

### Quarter 1 (Months 1–3): Foundation & Quality

**Theme: Technical Debt & Modernization**

- [ ] Bump minimum deployment target from iOS 14 to iOS 16, removing all `#available(iOS 15.0, *)` fallback paths and the bundled Core ML segmentation model
- [ ] Migrate `Package.swift` to swift-tools-version:5.9 with structured concurrency support
- [ ] Replace all `DispatchSemaphore` usage in `faceRectangles`/`humanRectangles`/`animalRectangles` with `async/await`
- [ ] Add comprehensive unit tests covering all 10+ public API methods (currently only a stub test exists)
- [ ] Introduce protocol-based architecture (`SegmentationProvider`, `DetectionProvider`) for testability and future extensibility
- [ ] Add Swift DocC documentation for all public APIs

### Quarter 2 (Months 4–6): Platform Expansion

**Theme: visionOS & Multi-Object Support**

- [ ] Add visionOS 1.0 platform target with RealityKit integration for 3D scene segmentation
- [ ] Implement multi-object segmentation using `VNGeneratePersonSegmentationRequest` with threshold tuning
- [ ] Add person instance segmentation (individual mask per person in group photos)
- [ ] Support `VNSegmentationRequest` (iOS 17+) for class-specific segmentation beyond persons
- [ ] Add async/await overloads for all video processing completion handler APIs
- [ ] Implement `AsyncSequence`-based video frame stream for reactive processing pipelines

### Quarter 3 (Months 7–9): Performance & Streaming

**Theme: Real-Time & GPU Optimization**

- [ ] Implement Metal-accelerated CIFilter pipeline for video background replacement (replacing CPU-bound `CIContext.createCGImage`)
- [ ] Add real-time camera feed segmentation with `AVCaptureSession` integration
- [ ] Implement temporal coherence for video segmentation (frame-to-frame mask smoothing to reduce flicker)
- [ ] Add configurable model precision (FP16 vs FP32) via `MLModelConfiguration`
- [ ] Benchmark and optimize memory usage for 4K video processing
- [ ] Add `Sendable` conformance to all public types for Swift 6 strict concurrency

### Quarter 4 (Months 10–12): Intelligence & Ecosystem

**Theme: Smart Features & Developer Experience**

- [ ] Integrate `Apple Intelligence` semantic understanding (iOS 18+) for scene-aware background suggestions
- [ ] Add smart crop with face-aware composition (rule-of-thirds, face proximity detection)
- [ ] Implement depth map generation using `VNDetectRectanglesRequest` + monocular depth estimation
- [ ] Add custom model loading API allowing users to swap in their own `.mlmodelc` segmentation models
- [ ] Create a SwiftUI modifier (`View.segmentedBackground(...)`) for declarative background replacement
- [ ] Publish benchmark suite and regression tests as a separate SPM target

---

## Technical Debt

| Priority | Item | Impact | Effort |
|----------|------|--------|--------|
| **P0** | `DispatchSemaphore` in multi-result methods blocks calling threads | Deadlock risk, thread starvation | Medium |
| **P0** | No error propagation — all failures silently return `nil`/`[]` | Poor debuggability, silent failures | Medium |
| **P0** | Force-unwraps throughout (`url!`, `pixelBufferOut!`) | Crash risk in production | Low |
| **P1** | Duplicated background scaling/cropping logic across 5+ methods | Maintainability, bug surface | Medium |
| **P1** | `print()` used for error logging instead of structured logging | Debug pollution, no log levels | Low |
| **P1** | Video processing writes to `Documents/` with date-based filenames | Disk bloat, no cleanup | Low |
| **P2** | `saliencyMask` uses `segmentationRequest` (Core ML) instead of native saliency API | Inconsistent with iOS 15+ capabilities | Low |
| **P2** | No thread-safe access to lazy `VNRequest` properties | Potential data races under concurrency | Medium |
| **P2** | Auto-generated `segmentation.swift` model wrapper is outdated | Dead code if iOS 16+ minimum adopted | Low |

## Future Features

### Short-Term (Next 6 Months)
- Depth-aware background blur (gaussian blur intensity proportional to estimated depth)
- Person counting and group size detection from segmentation masks
- Batch image processing API for gallery-wide operations
- HEIC/HEIF output support for better compression

### Medium-Term (6–12 Months)
- Live activity segmentation for SwiftUI camera views
- Cross-platform shared core (Swift 6 concurrency model) for macOS Catalyst and visionOS
- On-device training for personalized segmentation (e.g., always segment *my* pet)
- Video stabilization integrated with person tracking

### Long-Term (12+ Months)
- Scene graph generation from segmented objects (spatial relationship modeling)
- Multi-modal segmentation (text-prompt-based object selection using on-device LLM)
- ARKit scene reconstruction integration for 3D compositing
- Automatic background replacement suggestions using on-device semantic understanding

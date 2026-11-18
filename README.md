# DRISHTIYANA – Urban Intelligence Platform (Resume Brief for Claude)


## What it is
- **Name:** DRISHTIYANA – Urban Intelligence Platform (Smart India Hackathon / SIH 2026 project, team project)
- **One-liner:** Turns city buses into mobile road sensors. A phone on a bus streams live camera video and GPS. Edge AI finds potholes and counts traffic. Each detection is geo-tagged and sent to a city admin dashboard for verification and repair work orders.
- **Problem:** Cities find road damage (potholes, cracks, waterlogging) through slow manual surveys and citizen complaints. DRISHTIYANA automates detection using buses that already run the routes every day.

## How it works
1. **Bus Sensing Unit (mobile web app):** phone camera → live WebRTC stream; continuous GPS (`watchPosition`) with UTC timestamps.
2. **Edge Monitor Station:** receives the stream and samples frames at 5 FPS while playback stays at 30 FPS. Frames go to a Python Edge AI service.
3. **Edge AI pipeline (Python, FastAPI):**
   - OpenCV preprocessing: auto brightness/gamma, CLAHE contrast enhancement, sharpening.
   - Custom-trained **YOLOv8** pothole detector (also exported to ONNX), confidence ≥ 0.80.
   - Separate **YOLO11** vehicle detector (car, motorcycle, bus, truck) for traffic-density analytics.
   - **Redis** candidate tracker: groups detections of the same pothole across frames (IoU + center distance + time gap). Keeps the best evidence frame (30 s TTL, falls back to FakeRedis/in-memory).
   - GPS correlation: matches each frame timestamp to the nearest GPS point (≤ 2 s).
   - Rule-based **risk & priority scoring** (0–100), weighted: confidence 35, severity/bbox area 35, recurrence 20, vehicle speed 10. Output levels: LOW, MEDIUM, HIGH, CRITICAL.
4. **Upload mode:** batch-processes prerecorded road videos (.mp4/.avi/.mov/.webm) with GPS files (.csv/.json). It syncs frames to GPS from FPS timing and replays video and map route together.
5. **Backend (Node.js / Express / Socket.IO):** WebRTC signaling and 50+ REST endpoints (sessions, GPS, edge events, uploads via Multer, admin, citizen, auth). Uses HTTPS with mkcert certificates for mobile camera access and is deployed on Render.
6. **Database (Supabase / PostgreSQL):** 7 tables: bus_sessions, gps_locations, pothole_events, events, vehicle_observations, work_orders, department_reports. Uses indexes and RLS, plus idempotent/duplicate-safe event ingestion.
7. **City Command Center (Admin Portal):** Leaflet GIS map with category markers and heatmap. Admins triage events by category (Roads, Traffic, Safety, etc.) and verify/reject them. They generate department reports and work orders, track status, and view analytics and traffic-density summaries. Login is secured.
8. **Citizen Portal:** citizen login, live city-bus tracking, and a nearby-buses lookup with reverse geocoding.
9. **Testing:** 30+ automated test/acceptance scripts covering the full pipeline: WebRTC signaling, GPS pipeline, uploads, video-timestamp sync, risk scoring, Supabase E2E persistence, and admin/citizen UI.

## Tech stack / skills
- **AI/CV:** Python, YOLOv8, YOLO11 (Ultralytics), PyTorch, OpenCV (CLAHE, enhancement), ONNX, NumPy
- **Backend:** Node.js, Express.js, Socket.IO, FastAPI, Uvicorn, REST APIs, Multer
- **Real-time:** WebRTC, WebSockets, Geolocation API
- **Data:** Supabase (PostgreSQL), SQL, Redis
- **Frontend:** HTML, CSS, JavaScript, Leaflet.js (GIS maps, heatmaps), responsive dashboards
- **Other:** HTTPS/TLS (mkcert), Render deployment, Git, automated E2E testing
- **Concepts:** Edge computing, computer vision, object detection, geospatial data, real-time streaming, smart cities, system design

## Size (for context, not for the resume)
About 20k lines across a Node server (about 2.9k lines), a Python edge AI service, and multiple web portals (admin portal about 5.3k lines of JS).

## Sample resume entry (draft)
**DRISHTIYANA – AI-Powered Urban Road Intelligence Platform** | *Smart India Hackathon 2026*
*Python, YOLOv8, OpenCV, FastAPI, Node.js, Express, Socket.IO, WebRTC, Supabase (PostgreSQL), Redis, Leaflet.js*
- Built a smart-city platform that turns city buses into mobile road sensors. It streams live phone camera video and GPS over WebRTC to an edge AI station that detects potholes and geo-tags them in real time.
- Built an edge CV pipeline using OpenCV enhancement (CLAHE, gamma, sharpening) and a custom YOLOv8 detector (≥0.80 confidence). Added Redis-based multi-frame tracking to remove duplicate detections and nearest-timestamp GPS matching (≤2 s).
- Built a weighted risk/priority scoring engine (0–100) and YOLO11 vehicle counting for traffic-density analytics. Results are stored in Supabase/PostgreSQL across 7 tables.
- Shipped an admin Command Center (Leaflet GIS map, heatmaps, event verification, department work orders, analytics) and a citizen bus-tracking portal. The system has 50+ REST APIs, is deployed on Render, and is covered by 30+ automated E2E tests.

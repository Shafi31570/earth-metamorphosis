
# 🛰️ Nova Matrics - VI (Earth-Metamorphosis) | NISAR Mission Dashboard

**NASA Space Apps Challenge 2026**

![Status](https://img.shields.io/badge/Status-Live-green)
![Python](https://img.shields.io/badge/Python-3.14-blue)
![Next.js](https://img.shields.io/badge/Next.js-15-black)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 📌 Project Overview

This project is developed for the **NASA Space Apps Challenge 2026**, focusing on the challenge No. 06:

> **"Dancing with the SARs"** — Analyze Earth's surface changes using NISAR (NASA-ISRO Synthetic Aperture Radar) data.

We process L-band SAR data to detect changes in:
- 🌊 Floods and water bodies
- 🏔️ Landslides and surface deformation
- 🧊 Glaciers and ice sheets
- 🌳 Vegetation and forest cover

**Our Goal:** Build an interactive visualization system that processes real NISAR SAR data to detect surface changes, specifically focusing on flood and vegetation analysis in Bangladesh, a riverine and agricultural country.

---

## 🏗️ Architecture & Live Demo
*   **Frontend:** Next.js + Cesium 3D Globe (Deployed on **Vercel**)
*   **Backend:** FastAPI + Python (Running on local machine via **Cloudflare Quick Tunnel** for the live demo due to cloud RAM limits on free tiers)
*   **Live Frontend URL:** https://earth-metamorphosis.vercel.app/
*   **Backend API URL:**  https://attempted-legislature-findarticles-declare.trycloudflare.com

> ⚠️ **Important Note for Judges:** The backend is currently running on a local machine connected via a Cloudflare tunnel. If the live analysis does not load, please refer to the video demonstration or follow the "Run Locally" guide below.

---

## 📡 NASA Data Sources Used
1.  **NISAR L2 GCOV:** Level 2 Geocoded Complex data (NASA Earthdata).
2.  **NASA Earthdata CMR API:** Used for searching and downloading the NISAR granules.
3.  **NASA GIBS:** Used for the MODIS live true-color overlay.

---

## ⚙️ How the System Works
1.  User draws a custom AOI (Area of Interest) on the 3D globe.
2.  Frontend sends a WKT polygon to the backend.
3.  Backend queries NASA Earthdata CMR API to find matching NISAR granules.
4.  Auto-pick selects the optimal "Before" and "After" pair.
5.  **D-SAR-D Pipeline** runs:
    *   Downloads HDF5 files.
    *   Extracts backscatter (HH & HV).
    *   Calculates the difference (change detection).
    *   Generates a flood mask, GeoTIFF, and transparent red overlay.
6.  Results, charts, and the red mask are visualized on the Cesium 3D globe.

---

## 🚀 Quick Start (Run Locally for Judges/Developers)
If the live demo's backend is down, you can run the entire project locally:
### 1. Backend Setup
```bash
cd backend
python -m venv venv
# Windows:
venv\Scripts\activate
# Mac/Linux:
# source venv/bin/activate

pip install -r requirements.txt

# Add your NASA Earthdata credentials to a `.netrc` file in your home directory
# machine urs.earthdata.nasa.gov login <YOUR_USERNAME> password <YOUR_PASSWORD>

python main.py
```
*(The backend will start at http://localhost:8000)*

### 2. Frontend Setup
```bash
cd frontend
npm install
npm run dev
```
*(The frontend will start at http://localhost:3000)*

---

## 🔄 Reconnecting the Cloudflare Tunnel (Troubleshooting)
If the live backend URL is down, the tunnel has expired. Follow these steps to get a new URL:

1. Ensure the backend is running locally.
2. Run this command in a new terminal: 
   `& "C:\Program Files (x86)\cloudflared\cloudflared.exe" tunnel --url http://localhost:8000`
3. Copy the new `https://....trycloudflare.com` link.
4. Update the `NEXT_PUBLIC_API_BASE_URL` in Vercel's environment variables and redeploy.

---

## 🧠 Development Challenges & Solutions (How We Built It)
Building a real-time SAR processing pipeline within a 48-hour hackathon came with major challenges. Here is how we solved them:

1.  **The "Slow Data" Issue:** Initially, using `earthaccess.search_data` took 30-90 seconds or hung entirely because NISAR has a massive catalog. 
    *   *Solution:* We bypassed the heavy library search and implemented a **direct NASA CMR API query** using the specific `granule_ur` parameter. This reduced the lookup time to ~1 second.
2.  **The "Cloud RAM" Limit:** Railway and Vercel free tiers do not have enough RAM to process 1.5GB HDF5 files and render matplotlib images. It caused Out-Of-Memory (OOM) crashes.
    *   *Solution:* We shifted the heavy backend to run locally and used a **Cloudflare Tunnel** to expose the local API to our Vercel frontend. This provided the best of both worlds: a public live URL and unlimited local processing power.
3.  **The "Zoom & Draw" Coordinate Bug:** When users drew a small box (zoomed in), the system was analyzing the whole preset district instead.
    *   *Solution:* We updated our Zustand store to prioritize the user's `drawnBbox` over the default preset, and modified the Cesium Globe to render the image overlay dynamically exactly at the custom AOI's coordinates.
4.  **Image Overlay Rendering:** Serving heavy PNGs from an ephemeral server caused 404 errors on refresh.
    *   *Solution:* We implemented a **Base64 encoding** logic on the backend. The PNG masks are converted to Base64 Data URLs and sent directly within the JSON response, eliminating the need for persistent file storage on the server.

---

## 👥 Team: Nova Matrics - VI

| Name                            | Role                                                     |
| :------------------------------ | :------------------------------------------------------- |
| **Md. Sahadat Hossen Tanim**    | Team Leader, AI/ML                                       |
| **Md. Shafaet Ullah**           | Backend System Designer, API Integrator & Git Controller |
| **Ahmed Fardin**                | Frontend, Data Analyst, Editor & Team Former             |
| **Ridwan Ahmed**                | Frontend & Concept Designer                              |
| **Md. Shakhawat Hossain Shuvo** | Researcher & API Integrator                              |
| **Umme Habiba Islam**           | Presenter, Script Writer & Story Teller                  |

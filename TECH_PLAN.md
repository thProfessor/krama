# Tech Implementation Plan: Krama - The Voice-First Inventory Ledger

This document outlines the technical architecture, technology stack, and implementation phases for **Krama**, the Voice-Powered Inventory Management Application.

---

## 1. System Architecture

The application will follow a modern, cloud-native architecture designed for low latency and high scalability.

### Core Architecture Components
1.  **Mobile/Web Frontend**:
    - **Voice Capture**: A dedicated voice recording module with streaming capabilities.
    - **Dashboard**: A real-time data visualization layer for inventory levels.
    - **Global Search**: A fast-indexed search bar with autocomplete.
2.  **API Gateway**: A central entry point for all frontend requests.
3.  **Voice Intelligence Engine**:
    - **STT (Speech-to-Text)**: Converts raw audio into text. Supports **Hindi, English, and Hinglish** out-of-the-box.
    - **NLP Parsing**: An LLM-based service (GPT-4o or specialized Indic LLMs like Sarvam AI) that extracts "intent" (in/out), "item name", and "weight/quantity" from multi-lingual inputs.
4.  **Inventory Backend Logic**:
    - Manages CRUD operations for inventory items.
    - Implements the "Alert" logic (threshold-based notifications).
5.  **Image Service**:
    - Handles uploads to S3/Cloud Storage.
    - Integrates with a computer vision API (e.g., Google Vision or OpenAI GPT-4o) to automatically tag and suggest images for items.
6.  **Real-time Synchronization**: WebSockets (Socket.io) or Firebase Realtime Database to update the dashboard immediately after a voice command is processed.

---

## 2. Technology Stack

| Layer | Recommended Technology |
| :--- | :--- |
| **Frontend (Mobile)** | React Native (iOS/Android) with i18next for localization. |
| **Frontend (Dashboard)** | Next.js with Tailwind CSS & Shadcn UI. |
| **Backend** | Node.js (TypeScript/Express) or Python (FastAPI). |
| **Database** | PostgreSQL (Relational data) + Redis (Caching/Real-time). |
| **Voice Processing** | **OpenAI Whisper v3** or **Google Chirp** (Best-in-class for Indic languages). |
| **NLP & Translation** | **GPT-4o** (Handles Hinglish/Hindi flawlessly) or **Sarvam AI / Bhashini API**. |
| **Image Storage** | AWS S3 or Google Cloud Storage. |
| **Notifications** | Firebase Cloud Messaging (FCM) or Twilio (SMS). |
| **Visualizations** | Recharts or D3.js. |

---

## 3. Data Schema (Key Models)

### `InventoryItem`
- `id`: UUID
- `name`: String (Stored in English/Standardized)
- `local_names`: JSON (Map of language codes to local names, e.g., `{"hi": "तांबा"}`)
- `category`: String
- `current_weight`: Decimal
- `unit`: String (e.g., kg, lbs, units)
- `low_stock_threshold`: Decimal
- `image_url`: String
- `last_updated`: Timestamp

### `Transaction`
- `id`: UUID
- `item_id`: Reference
- `type`: Enum (IN / OUT)
- `quantity_change`: Decimal
- `timestamp`: Timestamp
- `source`: Enum (VOICE / MANUAL)

---

## 4. Implementation Roadmap

### Phase 1: MVP Core (4 Weeks)
- Set up project structure and database schema.
- Implement basic CRUD for inventory items via a web dashboard.
- Integrate AWS S3 for manual image uploads.

### Phase 2: Voice Intelligence (4 Weeks)
- Develop the Voice Recording module.
- Implement the "Voice Transaction Engine" (STT + LLM parsing).
- Example input: *"Added 50 kilograms of Aluminum Scraps to stock."* -> Parsed as: `action: IN, item: Aluminum Scraps, quantity: 50, unit: kg`.

### Phase 3: Dashboard & Search (3 Weeks)
- Build real-time visual charts (bar charts for stock levels).
- Implement global search with fuzzy matching.
- Set up the low-stock alert logic (background workers).

### Phase 4: AI Image Cataloging & Refinement (3 Weeks)
- Integrate automated image suggestions based on item name.
- Real-time mobile notifications.
- Final UI/UX polish.

---

## 5. Security & Reliability
- **Auth**: JWT-based authentication with Role-Based Access Control (RBAC).
- **Concurrency**: Implement optimistic locking to prevent race conditions during simultaneous voice updates.
- **Offline Support**: Local caching for mobile users in environments with poor connectivity.

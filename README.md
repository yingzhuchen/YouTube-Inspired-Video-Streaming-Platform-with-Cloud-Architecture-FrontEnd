# YouTube-Inspired-Video-Streaming-Platform-with-Cloud-Architecture-FrontEnd
A high-performance, full-stack video streaming platform with cloud architecture.This repository contains the Frontend source code, built with Vue.js and Element UI.

# 📖 Introduction
This project is the frontend interface for a scalable video streaming ecosystem, designed to handle high-concurrency user interactions including real-time danmu (bullet chat), video uploading, and social community features.The platform is built to replicate the core experience of YouTube. It connects to a robust Microservices Backend (Spring Boot, Spring Cloud, Redis, RocketMQ) to support 800+ read requests per second and deliver low-latency interactions.

# ✨ Key Features
## 🎬 Content Consumption
Immersive Player: Integrated XGPlayer to provide a robust HTML5 video playback experience.
Real-time comment: WebSocket-based bullet chat system that allows users to send and receive time-synced comments overlaid on videos.
Smart Recommendations: Homepage featuring a dynamic carousel and categorized video grids (Anime, Tech, Gaming, etc.).
Interactive Controls: Support for Like, Coin, and Collect (Triple Action), as well as Following/Unfollowing content creators.

# 🛠️ Creator Studio
Advanced Uploading:
    File Slicing: Large video files are sliced on the client side to ensure reliable uploading.
    MD5 Deduplication: Instant upload capability by calculating and checking file hashes (MD5) against the server.
    Cover Generation: Automatic video frame capture using HTML5 Canvas for thumbnail selection.
Content Management: Intuitive interface for editing video titles, tags, descriptions, and categories.

# 👤 User Community
User System: Secure registration and login via modal dialogs, utilizing JWT for session management.
Moments (Social Feed): A social hub where users can post text/image updates and view activities from creators they follow.
History: Automatically tracks watched videos with precise timestamp recording.
Comments: A nested comment system supporting replies and threading.
Collections: Organize favorite videos into custom groups.

# 🛠️ Tech StackCategory
| Category | Technology | Description |
| Framework | Vue.js 2.x | Core frontend framework |
| UI Library | Element UI | Desktop component library for layout and controls |
| CSS Preprocessor | Less | Styled components and Scoped CSS |
| Video Player | XGPlayer | HTML5 video player with Danmu plugin support |
| Real-time | WebSocket | For live Danmu (bullet chat) transmission |
| Routing/State | Vue Router & Vuex | SPA routing and centralized state management |
| HTTP Client | Axios | For RESTful API consumption |

# 📂 Project Structuresrc/
├── api/                 # API request modules (videoApi, userApi, etc.)
├── assets/              # Static assets (images, icons)
├── components/          # Reusable Vue components
│   ├── CommonHeader.vue       # Global navigation bar
│   ├── LoginDialog.vue        # Authentication modal
│   ├── PostVideo.vue          # Video upload & form component
│   ├── VideoComment.vue       # Comment section logic
│   └── ...
├── utils/               # Utility functions
│   ├── fileUtils.js           # File slicing & MD5 calculation
│   └── userUtils.js           # User session helpers
├── views/               # Page components
│   ├── MainPage.vue           # Homepage layout
│   ├── VideoDetail.vue        # Video player page
│   ├── UserMoments.vue        # Social feed page
│   └── UserHistory.vue        # Watch history page
├── App.vue
└── main.js
# 📸 ScreenshotsHomepage
Video Player<img src="homepage.jpg" alt="Homepage" width="400"/><img src="video.jpg" alt="Video Player" width="400"/>Carousel & Video GridXGPlayer with Danmu & InteractionsUpload StudioLogin<img src="upload video.jpg" alt="Upload" width="400"/><img src="login in.jpg" alt="Login" width="400"/>Drag & Drop UploadAuthentication ModalComments<img src="comment.png" alt="Comments" width="400"/>Nested Reply System🚀 Getting StartedPrerequisitesNode.js (v14+ recommended)npm or yarnInstallation
## 1. Clone the repository
git clone https://github.com/yourusername/video-platform-frontend.git

## 2. Enter the project directory
cd video-platform-frontend

## 3. Install dependencies
npm install
Development# Compiles and hot-reloads for development
npm run serve
Production Build# Compiles and minifies for production
npm run build

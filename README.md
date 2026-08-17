# 🛒 Marketplace Application with VoIP Signalling

A full-featured Android marketplace platform built with Kotlin, consisting of two companion apps — a **Buyer App** and a **Seller App** — that communicate in real-time via Firebase. The platform includes a custom **WebSocket-based VoIP calling system** that lets buyers and sellers communicate directly through voice calls.

---

## 📱 Apps Overview

### 🧑‍💼 Seller App (`SELLERMS4`)
Allows sellers to manage their online shop, list products, handle incoming orders, and receive voice calls from buyers.

### 🛍️ Buyer App (`BuyerMS4`)
Allows buyers to browse seller shops, add products to cart, place orders, track order status, and initiate VoIP calls to sellers.

---

## ✨ Features

### Shared Features (Both Apps)
- 🔐 **Firebase Authentication** — Email/password login & registration
- 📞 **VoIP Calling** — Real-time voice calls via WebSocket signalling server
- 🖼️ **Image Upload/Display** — AWS S3 integration for product images (via Glide)
- 🔔 **Push Notifications** — In-app order status notifications
- 👤 **User Profile Management** — Edit profile info and avatar

### Seller App
- 📊 **Dashboard** — Overview of total products and orders
- 📦 **Product Management** — Add, edit, and delete products with images, price, and stock
- 🧾 **Order Management** — View incoming orders, accept/reject with inventory validation
- 🏪 **Shop Setup** — Create and manage a storefront
- ✅ **Inventory Validation** — Prevents over-selling by validating stock before accepting orders

### Buyer App
- 🏬 **Shops Browser** — Discover all available seller shops
- 🛒 **Shopping Cart** — Add items across multiple sellers with quantity control
- 📋 **Order Placement** — Place orders and track their status in real-time
- 🚚 **Delivery Fee Calculator** — Dynamic delivery fee estimation
- 📝 **Order History** — View and track all past and active orders

---

## 🏗️ Architecture & Tech Stack

| Category             | Technology                           |
|----------------------|--------------------------------------|
| Language             | Kotlin                               |
| Min SDK              | API 24 (Android 7.0)                 |
| Target SDK           | API 36                               |
| Database             | Firebase Realtime Database           |
| Authentication       | Firebase Auth                        |
| File Storage         | AWS S3 (`aws-android-sdk-s3:2.81.0`) |
| Image Loading        | Glide 4.16.0                         |
| VoIP Signalling      | Java-WebSocket 1.5.3                 |
| HTTP Client          | OkHttp 4.12.0                        |
| Async                | Kotlin Coroutines 1.7.3              |
| UI Components        | Material Design, ViewPager2, RecyclerView, CardView |
| Firestore (Seller)   | Firebase Firestore KTX               |

---

## 📡 VoIP Signalling

The VoIP system uses a **custom WebSocket signalling server** to establish peer-to-peer voice calls between buyers and sellers.

**How it works:**
1. User connects to the signalling server with an IP address and port
2. A unique user ID is registered with the server
3. Calls are initiated by sending a `call_request` signal to the remote user's ID
4. Audio is captured via `AudioRecord` and streamed in real-time
5. Incoming audio is played via `AudioTrack`

**Features:**
- 🔇 Mute / Unmute
- 🔊 Speaker toggle
- 📶 Connection status indicator
- ⚡ Coroutine-based non-blocking audio streaming

---

## 🗄️ Firebase Database Structure

```
Firebase Realtime Database
├── Seller/
│   └── {sellerId}/
│       ├── Products/
│       │   └── {productId}/
│       │       ├── name
│       │       ├── price
│       │       ├── stock         ← validated: non-negative integer
│       │       └── imageUrl
│       └── ...
└── Orders/
    └── {orderId}/
        ├── buyerId
        ├── sellerId
        ├── status               ← pending | accepted | preparing | ready | delivered | rejected
        └── items/
            └── {itemId}/
                ├── productId
                └── quantity     ← validated: positive integer
```

### Security Rules
Firebase security rules enforce that:
- Only authenticated users can read products and orders
- Only the owning seller can write/update their own products and stock
- Order status is strictly validated against allowed enum values

---

## 🚀 Getting Started

### Prerequisites
- Android Studio (Hedgehog or later recommended)
- JDK 11+
- A Firebase project with **Realtime Database** and **Authentication** enabled
- An AWS S3 bucket for image storage
- A running **WebSocket signalling server** for VoIP functionality

### Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/Mostafa-ElShinawi/Marketplace_Application.git
   cd Marketplace_Application
   ```

2. **Configure Firebase**
   - Go to the [Firebase Console](https://console.firebase.google.com/)
   - Create a project (or use an existing one)
   - Add two Android apps: one for the Buyer app and one for the Seller app
   - Download the `google-services.json` for each and place them in:
     - `BuyerMS4/app/google-services.json`
     - `SELLERMS4/app/google-services.json`
   - Apply the Firebase Database rules from `SELLERMS4/database.rules.json`

3. **Configure AWS S3**
   - Update the S3 configuration in `S3Config.kt` (Buyer app) with your bucket name, region, and credentials

4. **Open in Android Studio**
   - Open either `BuyerMS4/` or `SELLERMS4/` as a separate project in Android Studio

5. **Build and Run**
   ```bash
   ./gradlew assembleDebug
   ```

### VoIP Server
To enable VoIP calling, you need a running WebSocket signalling server. In the app, enter the server's **IP address** and **port** in the VoIP screen before connecting.

---

## 📂 Project Structure

```
signalling/
├── BuyerMS4/                   # Buyer Android application
│   └── app/src/main/java/com/example/signallingms1/
│       ├── LoginActivity.kt        # Auth entry point
│       ├── HomeActivity.kt         # Main navigation host
│       ├── ShopsFragment.kt        # Browse all shops
│       ├── ProductsFragment.kt     # View products in a shop
│       ├── CartFragment.kt         # Shopping cart
│       ├── CartManager.kt          # Cart state management
│       ├── OrdersFragment.kt       # Order history & tracking
│       ├── VoIPCallActivity.kt     # Voice call screen
│       ├── InventoryManager.kt     # Stock tracking
│       ├── DeliveryFeeCalculator.kt
│       └── NotificationHelper.kt
│
└── SELLERMS4/                  # Seller Android application
    ├── database.rules.json         # Firebase security rules
    └── app/src/main/java/com/example/Seller/
        ├── LoginActivity.kt        # Auth entry point
        ├── HomeActivity.kt         # Main navigation host
        ├── DashboardFragment.kt    # Sales overview dashboard
        ├── ProductsActivity.kt     # Product CRUD management
        ├── ShopActivity.kt         # Shop configuration
        ├── OrdersFragment.kt       # Incoming order management
        ├── OrderDetailsFragment.kt # Accept/reject with stock validation
        ├── VoIPCallActivity.kt     # Voice call screen
        ├── InventoryValidator.kt   # Prevents over-selling
        └── NotificationHelper.kt
```

---

## 🔒 Permissions

| Permission              | Buyer App | Seller App | Purpose                      |
|-------------------------|:---------:|:----------:|------------------------------|
| INTERNET                | ✅        | ✅         | Network access               |
| ACCESS_NETWORK_STATE    | ✅        | ✅         | Check connectivity           |
| RECORD_AUDIO            | ✅        | ✅         | VoIP microphone access       |
| MODIFY_AUDIO_SETTINGS   | ✅        | ✅         | Speaker/earpiece switching   |
| POST_NOTIFICATIONS      | ✅        | ✅         | Order status notifications   |
| READ_EXTERNAL_STORAGE   | ✅        | ✅         | Image selection              |
| CAMERA                  | ❌        | ✅         | Product photo capture        |
| WAKE_LOCK               | ❌        | ✅         | Keep screen on during calls  |

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is for academic/educational purposes. All rights reserved to the project contributors.

---

> Built with ❤️ using Kotlin, Firebase, and WebSockets.

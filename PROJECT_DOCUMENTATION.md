# X4G Project Documentation

## 📋 Overview

**X4G** is a modern, fast gateway for VLESS tunneling over WebSocket + XHTTP (Siz10a Ultra), with a beautiful management dashboard and the ability to create dedicated links with traffic limits. It's designed to be deployed on Railway.

**Version:** v9.2  
**Framework:** FastAPI + Uvicorn  
**Platform:** Railway (deployable)  
**Language:** Python 3.x  
**Storage:** In-memory (JSON file at `/data`)

---

## 📁 Project Structure

```
HVPN/
├── main.py              # (NOT PRESENT in repo - main application logic, imported by others)
├── pages.py             # HTML/CSS/JS for Login, Dashboard, and Public Sub pages
├── relay_vless.py       # VLESS WebSocket relay logic (tunnel)
├── xhttp_siz10.py       # XHTTP Ultra Transport (packet-up / stream-up modes)
├── requirements.txt     # Python dependencies
└── README.md            # Persian documentation
```

---

## 🎨 NEW Minimal Light Theme (Complete Redesign)

### Dashboard — CSS Custom Properties
```css
:root{
  --bg:#F8FAFC;              /* Light gray-blue background */
  --bg2:#F1F5F9;             /* Sidebar / secondary bg */
  --bg3:#E2E8F0;             /* Elevated surfaces */
  --card:#FFFFFF;            /* Pure white cards */
  --card-b:rgba(0,0,0,0.05); /* Subtle card borders */
  --card-bh:rgba(99,102,241,0.12); /* Hover borders (indigo) */
  --accent:#6366F1;          /* Indigo primary accent */
  --accent2:#818CF8;         /* Lighter indigo */
  --accent-d:rgba(99,102,241,0.07); /* Indigo dim bg */
  --green:#10B981; --green-bg:rgba(16,185,129,0.08); --green-t:#059669;
  --red:#EF4444; --red-bg:rgba(239,68,68,0.07); --red-t:#DC2626;
  --amber:#F59E0B; --amber-bg:rgba(245,158,11,0.07); --amber-t:#D97706;
  --purple:#8B5CF6; --purple-bg:rgba(139,92,246,0.07); --purple-t:#7C3AED;
  --t1:#0F172A;              /* Near-black primary text */
  --t2:#475569;              /* Secondary text */
  --t3:#94A3B8;              /* Muted text */
  --sidebar-w:248px;
  --radius:14px;             /* Cleaner, smaller radius */
  --shadow:0 1px 3px rgba(0,0,0,0.04),0 1px 2px rgba(0,0,0,0.06);
  --shadow-lg:0 4px 6px rgba(0,0,0,0.04),0 10px 30px rgba(0,0,0,0.08);
}
```

### Dashboard — Dark Theme Override
```css
[data-theme="dark"]{
  --bg:#0F172A; --bg2:#1E293B; --bg3:#334155;
  --card:#1E293B;
  --card-b:rgba(255,255,255,0.06); --card-bh:rgba(99,102,241,0.18);
  --accent:#818CF8; --accent2:#6366F1; --accent-d:rgba(129,140,248,0.1);
  --green:#34D399; --green-bg:rgba(52,211,153,0.1); --green-t:#10B981;
  --red:#F87171; --red-bg:rgba(248,113,113,0.1); --red-t:#EF4444;
  --amber:#FCD34D; --amber-bg:rgba(252,211,77,0.1); --amber-t:#F59E0B;
  --purple:#A78BFA; --purple-bg:rgba(167,139,250,0.1); --purple-t:#8B5CF6;
  --t1:#F1F5F9; --t2:#CBD5E1; --t3:#64748B;
  --shadow:0 1px 3px rgba(0,0,0,0.2),0 1px 2px rgba(0,0,0,0.3);
  --shadow-lg:0 4px 6px rgba(0,0,0,0.3),0 10px 30px rgba(0,0,0,0.4);
}
```

### Login Page — CSS Custom Properties
```css
:root{
  --bg:#F8FAFC;              /* Light gray background */
  --card:#FFFFFF;            /* White card */
  --accent:#6366F1;          /* Indigo accent */
  --text:#0F172A;            /* Near-black text */
  --dim:#94A3B8;             /* Muted text */
  --mid:#64748B;             /* Medium text */
  --border:rgba(0,0,0,0.06); /* Subtle borders */
}
```

### Button Styles (Dashboard)
```css
.btn-p{background:var(--accent);color:#fff;box-shadow:0 2px 8px rgba(99,102,241,.25)}
.btn-p:hover{background:#4F46E5;box-shadow:0 4px 12px rgba(99,102,241,.3)}
.btn-o{background:transparent;border:1px solid var(--card-b);color:var(--t2)}
.btn-o:hover{background:var(--accent-d);border-color:rgba(99,102,241,.2)}
.btn-g{background:var(--accent-d);color:var(--accent2);border:1px solid rgba(99,102,241,.12)}
.btn-g:hover{background:rgba(99,102,241,.15)}
.btn-d{background:var(--red-bg);color:var(--red-t);border:1px solid rgba(239,68,68,.15)}
.btn-pur{background:var(--purple-bg);color:var(--purple-t);border:1px solid rgba(139,92,246,.15)}
.btn-amber{background:var(--amber-bg);color:var(--amber-t);border:1px solid rgba(245,158,11,.15)}
```

### Key Changes Made
| Element | Before | After |
|---------|--------|-------|
| **Background** | `#060f1d` (dark navy) | `#F8FAFC` (light gray) |
| **Cards** | `#0d1b2e` (dark) | `#FFFFFF` (white) |
| **Accent** | `#3B82F6` (blue) | `#6366F1` (indigo) |
| **Primary text** | `#E8F4FF` (white-ish) | `#0F172A` (near-black) |
| **Sidebar** | `#0a1628` (dark) | `#F1F5F9` (light gray) |
| **Borders** | `rgba(59,130,246,0.13)` | `rgba(0,0,0,0.05)` |
| **Shadows** | Heavy (0 4px 24px) | Micro (0 1px 3px) |
| **Border radius** | 16px | 14px |
| **Theme toggle** | `[data-theme="light"]` override | `[data-theme="dark"]` override |
| **Buttons** | Gradient blue/purple | Solid indigo |
| **Login bg** | Dark with orbs | Light with subtle gradient |

### What Still Needs Updating
- **Public Sub Page** (`get_public_page_html`) — still has old dark theme colors

---

## 🔧 Core Modules

### 1. `pages.py` (2362 lines)
Contains all HTML/CSS/JS as Python string constants:

- **`LOGO_B64`** - Base64 encoded X4G logo image
- **`LOGIN_HTML`** - Login page (redesigned: minimal light)
- **`DASHBOARD_HTML`** - Full management dashboard (redesigned: minimal light with dark mode)
- **`get_public_page_html(uuid_key)`** - Public subscription page (still has old theme)

### 2. `relay_vless.py` (211 lines)
VLESS WebSocket relay tunnel — unchanged.

### 3. `xhttp_siz10.py` (468 lines)
XHTTP Ultra Transport — unchanged.

### 4. `main.py` (NOT PRESENT)
Referenced by both `relay_vless.py` and `xhttp_siz10.py`.

---

## 🔌 API Endpoints (from code analysis)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/login` | Login with password |
| GET | `/api/me` | Check authentication |
| POST | `/api/logout` | Logout |
| GET | `/stats` | Get dashboard stats |
| GET | `/api/links` | List all configs |
| POST | `/api/links` | Create new config |
| PATCH | `/api/links/{uuid}` | Update config (toggle, edit, reset) |
| DELETE | `/api/links/{uuid}` | Delete config |
| GET | `/api/subs` | List subscription groups |
| POST | `/api/subs` | Create subscription group |
| PATCH | `/api/subs/{sub_id}` | Update group (link_ids) |
| DELETE | `/api/subs/{sub_id}` | Delete group |
| GET | `/api/activity` | Get activity logs |
| GET | `/api/connections` | Get active connections |
| POST | `/api/change-password` | Change admin password |
| GET | `/api/public/sub/{uuid_key}` | Public sub page data |
| GET | `/sub-all` | Full subscription (admin) |
| GET | `/sub-group/{uuid_key}` | Group subscription |
| WS | `/ws/{uuid}` | WebSocket tunnel |
| GET | `/xhttp-siz10/{mode}/{uuid}/{session_id}` | XHTTP downlink |
| POST | `/xhttp-siz10/packet-up/{uuid}/{session_id}/{seq}` | XHTTP packet-up |
| POST | `/xhttp-siz10/stream-up/{uuid}/{session_id}` | XHTTP stream-up |
| GET | `/dashboard` | Dashboard page |
| GET | `/login` | Login page |

---

## 🚀 Features

1. **VLESS over WebSocket / XHTTP** tunnel
2. **Internal HTTP Proxy**
3. **Full Management Dashboard** (stats, traffic charts, live connections)
4. **Unlimited Links** with per-link traffic limits (MB/GB)
5. **Instant Enable/Disable** for each link
6. **QR Code Output** for each link
7. **Configurable Fingerprint (uTLS)** per config
8. **Configurable ALPN** per config
9. **Configurable Port** per config (not just 443)
10. **Concurrent IP/User Limit** per config
11. **Subscription Groups** with public pages
12. **Password-Protected** public pages
13. **Dark/Light Theme** (NEW: indigo-based dark mode)
14. **Persian (RTL)** UI

---

## ⚠️ Important Notes

- All data is stored **in-memory** and resets on restart
- Default password hint: `123456`
- `main.py` is missing from the current working directory
- Public sub page still has old dark theme (needs update)
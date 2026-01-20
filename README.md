# FieldVoice Pro - Voice-Powered Construction Field Reporting System

## Overview

**FieldVoice Pro** is a mobile-optimized web application designed for construction field documentation. It enables Resident Project Representatives (RPRs), field engineers, and inspectors to quickly document daily work activities using native device dictation, generating professional DOT-compliant reports.

### Primary Use Case
- **Target Users**: Construction inspectors, Resident Project Representatives (RPRs), field engineers
- **Industry Focus**: DOT (Department of Transportation) construction projects
- **Deployment**: Configurable for any construction project

### Key Value Propositions
- Save 1+ hour daily on field documentation
- Ensure DOT compliance with structured reporting
- GPS-verified, timestamped reports for legal protection
- Optimized for mobile field use with native keyboard dictation support

---

## Technology Stack

### Frontend (100% Client-Side)
| Technology | Purpose | Version/Source |
|------------|---------|----------------|
| **Tailwind CSS** | Utility-first CSS framework | CDN (`cdn.tailwindcss.com`) |
| **Font Awesome** | Icon library | v6.4.0 CDN |
| **Vanilla JavaScript** | Core application logic | ES6+ |

### External APIs & Services
| Service | Purpose | Authentication |
|---------|---------|----------------|
| **Open-Meteo API** | Real-time weather data | None (free, no key required) |

### Browser APIs Used
| API | Purpose |
|-----|---------|
| MediaDevices API | Camera access |
| Geolocation API | GPS coordinates for photos/weather |
| Canvas API | Image compression |
| localStorage | Client-side data persistence |
| Service Worker API | Offline caching and PWA support |

### Architecture
- **Type**: Static Single Page Application (SPA)
- **Backend**: None - fully client-side
- **Database**: Browser localStorage only
- **Deployment**: Any static web server (HTTPS required for media APIs)

---

## Project Structure

```
/
├── index.html              # Home dashboard / main entry point
├── quick-interview.html    # Daily report flow (streamlined field entry)
├── review.html             # AI Kit - text refinement & editing via n8n webhook
├── report.html             # Print-ready PDF report viewer/generator
├── editor.html             # Photo editor & section-specific editing
├── permissions.html        # System setup, permission testing (mic, camera, GPS)
├── permission-debug.html   # Permission debugging and troubleshooting utility
├── settings.html           # Project configuration & data management
├── landing.html            # Marketing/onboarding landing page
├── sw.js                   # Service worker for PWA offline support
├── manifest.json           # PWA manifest with app metadata and icons
├── assets/                 # Favicon and browser icon assets
│   ├── favicon.ico         # Standard favicon for browsers
│   ├── favicon-16x16.png   # Small favicon
│   ├── favicon-32x32.png   # Standard favicon
│   ├── apple-touch-icon.png      # iOS home screen icon
│   ├── android-chrome-192x192.png  # Android Chrome icon
│   └── android-chrome-512x512.png  # Android Chrome large icon
├── icons/                  # PWA app icons directory
│   ├── icon.svg            # Source SVG icon (construction/microphone themed)
│   ├── icon-72x72.png      # App icon for various device sizes
│   ├── icon-96x96.png
│   ├── icon-128x128.png
│   ├── icon-144x144.png
│   ├── icon-152x152.png
│   ├── icon-192x192.png
│   ├── icon-384x384.png
│   └── icon-512x512.png
└── README.md               # This documentation file
```

### Page Descriptions

| Page | Lines | Purpose |
|------|-------|---------|
| `index.html` | ~678 | Simplified dashboard with single-action interface, weather display, and navigation |
| `quick-interview.html` | ~1,350 | Streamlined report with 7 sections: Weather, Work Summary, Issues, Inspections, Safety, Visitors, Photos |
| `review.html` | ~1,296 | AI Kit - side-by-side original vs. AI-refined text comparison with manual editing, n8n webhook integration |
| `report.html` | ~883 | Professional PDF-ready report with submit functionality, streamlined navigation |
| `editor.html` | ~674 | Photo capture with GPS embedding, section-specific editing interface |
| `permissions.html` | ~1,596 | Permission testing (mic, camera, GPS), iOS-specific instructions for native dictation |
| `permission-debug.html` | ~1,074 | Debugging utility for troubleshooting permission issues |
| `settings.html` | ~374 | Project settings, inspector name, Setup button, data export/clear functions |
| `landing.html` | ~1,560 | Marketing page with feature overview and onboarding |

---

## Data Model

### Report Data Structure (localStorage)

```javascript
{
  // Metadata
  meta: {
    createdAt: "2025-01-14T08:00:00.000Z",  // ISO timestamp
    interviewCompleted: false,               // Report finalization status
    reportType: "quick" | "full",            // Report type selected
    currentStep: 0,                          // Progress tracking
    version: 2,                              // Schema version
    naMarked: {                              // Sections marked as "Not Applicable"
      issues: false,
      inspections: false,
      visitors: false,
      photos: false
    }
  },

  // Project Overview (user-configurable via Settings)
  overview: {
    projectName: "Your Project Name",
    noabProjectNo: "12345",
    cnoSolicitationNo: "N/A",
    date: "1/14/2025",
    startTime: "6:00 AM",
    endTime: "4:00 PM",
    shiftDuration: "10.00 hours",
    location: "Project Location",
    engineer: "Engineering Firm",
    contractor: "Prime Contractor Name",
    noticeToProceed: "Start Date",
    contractDuration: "X days",
    expectedCompletion: "End Date",
    contractDayNo: "Day X of Y",
    weatherDays: "0 days",
    completedBy: "Inspector Name, RPR",
    weather: {
      highTemp: "78°F",
      lowTemp: "62°F",
      precipitation: "0.00\"",
      generalCondition: "Partly Cloudy",  // From Open-Meteo API
      jobSiteCondition: "Dry",            // User input: "Dry" or "Wet"
      adverseConditions: "N/A"
    }
  },

  // Contractors on site
  contractors: [
    { name: "Contractor Name", trade: "General Contractor", count: 12 }
  ],

  // Work activities documented
  activities: [
    { contractor: "General", trade: "General", narrative: "Continued grading operations..." }
  ],

  // Personnel headcounts
  operations: [
    {
      contractor: "Contractor Name",
      trade: "Earthwork",
      superintendents: 1,
      foremen: 2,
      operators: 4,
      laborers: 6,
      others: 0,
      total: 13
    }
  ],

  // Equipment on site
  equipment: [
    { contractor: "Contractor Name", type: "Excavator", model: "CAT 336", quantity: 2, status: "Active" }
  ],

  // Issues and delays
  generalIssues: [
    "Utility conflict at Station 45+00 requiring redesign"
  ],

  // QA/QC inspections and testing
  qaqcNotes: [
    "Concrete cylinders cast for barrier wall pour"
  ],

  // Safety information
  safety: {
    hasIncidents: false,      // Incident occurred today
    noIncidents: true,        // Explicitly confirmed no incidents
    notes: [
      "Toolbox talk conducted: Heat stress awareness"
    ]
  },

  // Visitors and communications
  visitorsRemarks: [
    "DOT inspector on site 10:00 AM - 2:00 PM"
  ],

  // Photo documentation
  photos: [
    {
      id: "photo_1705234567890_0",
      url: "data:image/jpeg;base64,...",  // Compressed base64 image
      caption: "",
      timestamp: "2025-01-14T14:30:00.000Z",
      date: "1/14/2025",
      time: "2:30:00 PM",
      gps: {
        lat: 29.9934,
        lng: -90.2580,
        accuracy: 10  // meters
      },
      fileName: "IMG_1234.jpg",
      fileSize: 2500000,
      fileType: "image/jpeg"
    }
  ],

  // Edited versions of text (populated after review.html editing)
  refinedData: {
    weather: "Site conditions were dry with partly cloudy skies...",
    activities: "The contractor continued earthwork operations...",
    issues: "A utility conflict was identified at Station 45+00...",
    inspections: "Quality control testing included casting of concrete cylinders...",
    safety: "No safety incidents were reported. A toolbox talk on heat stress...",
    visitors: "DOT representative conducted a site inspection..."
  }
}
```

### localStorage Keys

| Key Pattern | Purpose |
|-------------|---------|
| `fieldvoice_report_YYYY-MM-DD` | Primary storage for date-specific reports |
| `fieldvoice_report` | Legacy/backup key for compatibility |
| `fvp_settings` | User's project configuration settings |
| `fvp_mic_granted` | Microphone permission status flag |
| `fvp_loc_granted` | Location permission status flag |
| `fvp_onboarded` | First-time onboarding completed flag |
| `fvp_banner_dismissed` | Permission warning banner dismissed |
| `fvp_banner_dismissed_date` | Timestamp of banner dismissal (24hr reset) |

---

## Application Workflows

### 1. New Report Workflow

```
[index.html] User opens app
     │
     ├─► First visit? ─► [permissions.html] Setup permissions
     │
     ├─► Check for existing report
     │    ├─► No report: Show "Begin Daily Report" button
     │    ├─► In progress: Show progress bar, "Continue" button
     │    └─► Completed: Show "View Report" / "Edit" options
     │
     └─► User clicks "Begin Daily Report"
          │
          └─► [quick-interview.html]
```

### 2. Interview/Documentation Flow

```
[quick-interview.html]
     │
     ├─► Expandable section cards for each field:
     │    ├─► Weather & Site Conditions
     │    ├─► Work Summary (activities)
     │    ├─► Issues & Delays
     │    ├─► QA/QC Inspections
     │    ├─► Safety
     │    ├─► Visitors & Communications
     │    └─► Progress Photos
     │
     ├─► Each section supports:
     │    ├─► Text input (manual typing or keyboard dictation)
     │    ├─► Mark as N/A (skip section)
     │    └─► Real-time preview updates
     │
     ├─► Progress bar shows completion percentage
     │
     └─► User clicks "Finish" ─► [review.html] (AI Kit)
```

### 3. Voice Input Flow (Native Keyboard Dictation)

```
User enters text in any input field
     │
     ├─► User taps microphone button on device keyboard:
     │    ├─► iOS: Uses Siri dictation (Settings → Keyboard → Enable Dictation)
     │    ├─► Android: Uses Google Voice Typing
     │    ├─► Desktop: Uses OS-level dictation if available
     │    └─► Transcribed text appears directly in input field
     │
     └─► Requirements:
          ├─► Microphone permission granted to browser
          ├─► Device dictation feature enabled in OS settings
          └─► Internet connection (for cloud-based transcription)

Note: The app relies exclusively on native keyboard dictation for voice input,
providing consistent, reliable behavior across all platforms without custom
microphone buttons.
```

### 4. AI Kit Flow (review.html)

```
[review.html] Loaded with report data (AI Kit)
     │
     ├─► Display side-by-side: Original | AI Refined
     │
     ├─► User can:
     │    ├─► Click "Refine All" ─► Process all sections
     │    ├─► Click individual "Refine" ─► Process single section
     │    ├─► Manually edit either column
     │    ├─► Export training data for prompt refinement (database icon)
     │    └─► Export self-contained HTML report with photos
     │
     ├─► Refinement Process (via n8n webhook):
     │    ├─► Send original text + section name + report context
     │    ├─► n8n workflow processes and returns refined text
     │    └─► Display with typing animation
     │
     ├─► AI Refinement Rules (enforced by n8n workflow):
     │    ├─► Never invent or add information
     │    ├─► Keep all facts, quantities, names exactly as stated
     │    ├─► Convert informal language to professional tone
     │    ├─► Format for DOT documentation standards
     │    └─► Highlight safety concerns appropriately
     │
     ├─► HTML Export Process:
     │    ├─► Generate self-contained HTML with embedded CSS
     │    ├─► Photos include metadata overlay (GPS, timestamp)
     │    ├─► iOS PWA: Open native share sheet via Web Share API
     │    └─► Desktop/Browser: Download as .html file
     │
     └─► User clicks "Continue" ─► [report.html] for submission
```

### 5. Report Generation Flow

```
[report.html] Loaded with report data
     │
     ├─► Navigation bar with:
     │    ├─► Home button (return to dashboard)
     │    ├─► Back to AI Kit link
     │    └─► Submit button
     │
     ├─► Render 4-page professional report:
     │    ├─► Page 1: Project Overview + Daily Work Summary
     │    ├─► Page 2: Personnel Table + Equipment Table
     │    ├─► Page 3: Issues, Visitors, QA/QC, Safety + Signature
     │    └─► Page 4: Photo Gallery (if photos exist)
     │
     ├─► User clicks "Submit"
     │    ├─► Confirmation modal appears
     │    └─► Report submitted via n8n webhook
     │
     └─► Print CSS ensures proper formatting:
          ├─► Page breaks at correct locations
          ├─► 8.5" x 11" page format
          ├─► 0.5" margins
          └─► Color-accurate printing
```

### 6. Photo Capture Flow

```
User taps photo capture (camera icon)
     │
     ├─► Browser requests camera permission
     │
     ├─► User takes photo or selects from library
     │
     ├─► Processing:
     │    ├─► Request GPS coordinates (Geolocation API)
     │    ├─► Read file as base64 data URL
     │    ├─► Compress image (max 1200px width, 70% quality JPEG)
     │    ├─► If storage near limit ─► Try higher compression (800px, 50%)
     │    ├─► Check localStorage quota
     │    └─► If quota exceeded ─► Remove oldest photo, warn user
     │
     ├─► Create photo object with:
     │    ├─► Unique ID (timestamp-based)
     │    ├─► Compressed base64 URL
     │    ├─► GPS coordinates (if available)
     │    ├─► Date/time stamp
     │    └─► Original file metadata
     │
     └─► Save to report.photos array ─► Update display
```

---

## Configuration

### Project Configuration

Project details are configured via `settings.html` and stored in localStorage under the `fvp_settings` key. Users can configure:

- Project Name
- Project Number
- Location
- Notice to Proceed Date
- Contract Duration (Days)
- Prime Contractor Name
- Engineering Firm Name
- Inspector Name
- Default Start/End Times

### Custom Theme Colors (Tailwind)

```javascript
tailwind.config = {
    theme: {
        extend: {
            colors: {
                'dot-navy': '#0a1628',      // Primary dark blue
                'dot-blue': '#1e3a5f',      // Secondary blue
                'dot-slate': '#334155',     // Neutral slate
                'dot-orange': '#ea580c',    // Warning/action orange
                'dot-yellow': '#f59e0b',    // Accent yellow
                'safety-green': '#16a34a',  // Success/safety green
            }
        }
    }
}
```

### n8n Webhook Configuration

The application uses n8n webhooks for AI text refinement and report submission. Configure webhook URLs in the code:

**Webhook Endpoints (in AI Kit `review.html` and `report.html`):**
- **N8N_REFINE_WEBHOOK**: Endpoint for AI text refinement requests
- **N8N_SUBMIT_WEBHOOK**: Endpoint for submitting completed reports

**Webhook Request Format (Refine):**
```javascript
{
    section: "weather|activities|issues|inspections|safety|visitors|additionalNotes",
    originalText: "User's original text",
    reportContext: {
        projectName: "Project Name",
        reporterName: "Inspector Name",
        date: "1/14/2025"
    }
}
```

**Expected Response:**
```javascript
{
    refinedText: "Professionally refined text"
}
```

---

## Permission Requirements

### Required Permissions

| Permission | Purpose | How to Test |
|------------|---------|-------------|
| **Microphone** | Native keyboard dictation support | `permissions.html` → Enable Microphone → Start Test |
| **Camera** | Photo documentation | `editor.html` or inline photo capture |
| **Geolocation** | GPS for weather & photo timestamps | Auto-requested on first weather sync |

### iOS Dictation Requirements

iOS uses Siri for keyboard dictation. To enable:

1. **Dictation Enabled**: Settings → General → Keyboard → Enable Dictation
2. **Microphone Permission**: Allow Safari to access microphone when prompted

### Android Dictation Requirements

Android uses Google Voice Typing. Ensure:

1. **Google Voice Typing**: Enabled in keyboard settings
2. **Microphone Permission**: Allow browser to access microphone when prompted

---

## Storage & Limitations

### localStorage Limits
- **Typical limit**: 5-10MB per domain
- **Photo impact**: Each compressed photo ~50-200KB
- **Report without photos**: ~5-20KB

### Storage Management
- Automatic image compression (1200px → 800px if needed)
- JPEG quality reduction (70% → 50% if needed)
- Oldest photo removal when quota exceeded
- Clear warnings displayed to user

### Report History
- Up to 30 past reports stored
- Accessed via "Archives" on home dashboard
- Date-keyed storage prevents conflicts

---

## PWA & Offline Support

FieldVoice Pro is a fully installable Progressive Web App (PWA) that works offline when saved to the home screen on mobile devices.

### Installation

**iOS (Safari):**
1. Open the app in Safari
2. Tap the Share button (box with arrow)
3. Scroll down and tap "Add to Home Screen"
4. Tap "Add" to confirm

**Android (Chrome):**
1. Open the app in Chrome
2. Tap the menu (three dots)
3. Tap "Install app" or "Add to Home Screen"
4. Confirm installation

### Offline Capabilities

| Feature | Offline Support | Notes |
|---------|-----------------|-------|
| **App Loading** | Full | All HTML, CSS, JS cached by service worker |
| **View Existing Reports** | Full | Reports stored in localStorage |
| **Create/Edit Reports** | Full | All data saved locally |
| **Photo Capture** | Full | Photos stored as base64 in localStorage |
| **Weather Sync** | None | Requires internet (shows "Offline" status) |
| **AI Refinement** | None | Requires internet (shows error message) |
| **Report Submission** | None | Requires internet (shows error message) |
| **Print/PDF Export** | Full | Uses browser print functionality |

### Service Worker Details

**File:** `sw.js`

**Cache Strategy:**
- **Static Assets (Cache-First):** HTML files, manifest, icons cached on install
- **CDN Assets:** Tailwind CSS and Font Awesome cached with CORS handling
- **API Calls (Network-First):** Weather and webhook calls attempt network first, return JSON error when offline

**Cache Versioning:**
```javascript
const CACHE_VERSION = 'v1.2.0';
const CACHE_NAME = `fieldvoice-pro-${CACHE_VERSION}`;
```

To force a cache update, increment the version number in `sw.js`.

**Cached Files:**
- All 9 HTML files
- `manifest.json`
- App icons (192x192, 512x512)
- Tailwind CSS CDN
- Font Awesome CSS and webfonts

### Manifest Configuration

**File:** `manifest.json`

```json
{
    "name": "FieldVoice Pro",
    "short_name": "FieldVoice",
    "display": "standalone",
    "orientation": "portrait-primary",
    "background_color": "#0a1628",
    "theme_color": "#0a1628",
    "start_url": "/index.html"
}
```

### Offline UI Indicators

**Yellow Banner:** A slide-down banner appears at the top of all pages when the device goes offline:
- Message: "You are offline - Some features may be unavailable"
- Automatically hides when connection is restored
- Uses CSS transitions for smooth animation

**Feature-Specific Messages:**
- Weather sync: Shows "Offline" with wifi-slash icon
- Report submission: Toast message "You are offline - Please connect to the internet to submit your report"
- AI refinement: Toast message "You are offline - AI refinement requires internet connection"

### PWA Meta Tags (All HTML Files)

Each HTML file includes:
```html
<!-- PWA Meta Tags -->
<link rel="manifest" href="./manifest.json">
<link rel="icon" type="image/x-icon" href="./assets/favicon.ico">
<link rel="icon" type="image/png" sizes="32x32" href="./assets/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="./assets/favicon-16x16.png">
<link rel="apple-touch-icon" sizes="180x180" href="./assets/apple-touch-icon.png">
<meta name="theme-color" content="#0a1628">

<!-- iOS PWA Meta Tags -->
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="FieldVoice">
```

### Safe Area Support

All pages include CSS for iOS notch/Dynamic Island support:
```css
body {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
    padding-left: env(safe-area-inset-left);
    padding-right: env(safe-area-inset-right);
}
```

### App Icons

Icons are located in `/icons/` with a construction/microphone themed design using the app's color palette:
- **Navy (#0a1628):** Background
- **Orange (#ea580c):** Microphone body
- **Yellow (#f59e0b):** Microphone stand and accents
- **Green (#16a34a):** Checkmark badge

Available sizes: 72x72, 96x96, 128x128, 144x144, 152x152, 192x192, 384x384, 512x512

---

## Error Handling

### Dictation/Speech Errors
| Error | Meaning | Recovery |
|-------|---------|----------|
| Microphone permission denied | User blocked mic access | Prompt user to enable in browser/system settings |
| Dictation not available | OS dictation feature disabled | Guide user to enable dictation in device settings |
| Network error | Connectivity issue for cloud transcription | Check internet connection |

### Photo Errors
| Error | Recovery |
|-------|----------|
| Camera permission denied | Prompt user to enable in browser/system settings |
| GPS unavailable | Continue without coordinates, mark as "No GPS" |
| Storage quota exceeded | Remove oldest photo, try higher compression |
| Invalid file type | Reject with error message |

### Webhook Errors
| Scenario | Recovery |
|----------|----------|
| Webhook URL not configured | Configure N8N_REFINE_WEBHOOK and N8N_SUBMIT_WEBHOOK in code |
| Server error (5xx) | Check n8n workflow status, retry |
| Request timeout (30s) | Check network connection, retry |
| Invalid response | Verify n8n workflow returns expected JSON format |
| Network failure | Check internet connection, retry |

---

## Browser Compatibility

| Browser | Support Level | Notes |
|---------|---------------|-------|
| **Chrome (Desktop/Android)** | Full | Best experience, all features work |
| **Firefox** | Full | All features work |
| **Safari (Desktop)** | Full | All features work |
| **Safari (iOS)** | Full | Requires dictation enabled in iOS settings for voice input |
| **Edge** | Full | All features work |

### Requirements
- **HTTPS**: Required for MediaDevices API (camera, microphone)
- **JavaScript**: Required (no graceful degradation)
- **localStorage**: Required for data persistence

---

## Development Notes

### No Build Process
This is a static HTML/JS/CSS application with no build tools, bundlers, or package managers. Simply serve the files via any HTTP/HTTPS server.

### Testing Locally
```bash
# Python 3
python -m http.server 8000

# Node.js (npx)
npx serve .

# Then open https://localhost:8000 (or use ngrok for HTTPS)
```

### Modifying Project Configuration
1. Edit `INITIAL_OVERVIEW` object in `index.html` and `quick-interview.html`
2. Update project details in `settings.html` form defaults
3. Modify report template headers in `report.html`

### Adding New Report Sections
1. Add section HTML to `quick-interview.html`
2. Add corresponding data field to report object structure
3. Update `renderSection()` function for display
4. Add refinement support in `review.html`
5. Add export rendering in `report.html`

---

## File Size Reference

| File | Lines | Size (approx) |
|------|-------|---------------|
| index.html | 678 | 32 KB |
| quick-interview.html | 1,350 | 77 KB |
| review.html | 1,296 | 65 KB |
| report.html | 883 | 44 KB |
| editor.html | 674 | 32 KB |
| permissions.html | 1,596 | 81 KB |
| permission-debug.html | 1,074 | 53 KB |
| settings.html | 374 | 20 KB |
| landing.html | 1,560 | 80 KB |
| sw.js | 205 | 7 KB |
| manifest.json | 65 | 2 KB |
| icons/ | - | ~3 KB |
| assets/ | - | ~325 KB |
| **Total** | **~9,755** | **~821 KB** |

---

## Security Considerations

### Data Privacy
- All report data stored locally in browser
- Data only sent to configured n8n webhook endpoints for AI refinement and report submission
- Photos stored as base64 in localStorage (never uploaded unless explicitly shared)
- GPS coordinates embedded in photos for audit purposes

### Webhook Security
- Webhook URLs should be configured with appropriate authentication in the n8n workflow
- Data transmitted includes report text content for AI refinement
- Consider using HTTPS endpoints and API key authentication in production

### HTTPS Requirement
- Camera, microphone, and geolocation APIs require secure context (HTTPS)
- Development with localhost is allowed by browsers

---

## Common Modifications

### Change Project Details
Edit in `index.html` lines 275-300 and `quick-interview.html` lines 597-604.

### Add New Contractor
Modify the `contractors` array in the report object, or use the Full Report flow which includes a contractor management interface.

### Customize Report Styling
Edit the print styles in `report.html` (lines 10-20) and the report template structure.

### Adjust Photo Compression
Modify `compressImage()` function parameters in `quick-interview.html` (line 634):
```javascript
// Current: maxWidth = 1200, quality = 0.7
await compressImage(rawDataUrl, 1200, 0.7);
```

### Add New Weather Codes
Extend the `weatherCodes` object in `index.html` (lines 418-433) with additional Open-Meteo weather code mappings.

---

## Recent Changes

### Self-Contained HTML Report Export (Latest)
- Replaced text export with professional self-contained HTML report
- HTML reports include embedded photos, metadata, and full styling
- Reports are portable and can be viewed offline in any browser
- Export button in AI Kit generates a complete, shareable report file

### Web Share API Support for iOS PWA
- Added native share sheet integration when running as installed PWA on iOS
- Users can share HTML reports directly to Files, AirDrop, email, or other apps
- Graceful fallback to download for non-PWA or desktop browsers
- Improved UX with toast notifications during share process

### Photo Export with Metadata Overlay
- Photos in exported reports now include metadata overlays
- Overlay displays GPS coordinates, date/time stamps, and photo index
- Metadata is burned into the exported images for permanent documentation
- High-quality JPEG export (95% quality) preserves photo detail

### Training Data Export
- Added export functionality for AI prompt training and refinement
- Exports original vs. refined text pairs for each section
- Useful for improving AI refinement prompts over time
- Accessible via database icon in AI Kit header

### AI Review Renamed to AI Kit
- The review page has been rebranded from "AI Review" to "AI Kit"
- Updated page title and header to reflect the new branding
- Cleaner interface with improved button layout

### Report Page Streamlining
- Removed Edit and Print buttons from report.html for a cleaner interface
- Report page now focuses on Submit functionality with streamlined navigation
- Navigation includes Home button, Back to AI Kit link, and Submit button

### Navigation Improvements
- Added Home buttons to key pages for easier navigation
- Improved workflow tracking throughout the application
- Better integration between AI Kit and Report pages

### Dashboard Simplification
- Simplified the home dashboard to a single-action interface
- Setup functionality moved to the Settings page for cleaner UX

### Voice Input Streamlining
- Removed dedicated microphone buttons throughout the app
- Voice input now relies exclusively on native keyboard dictation (iOS Siri, Android Google Voice)
- This approach provides better reliability and consistency across devices

### iOS Support Improvements
- Added safe-area CSS insets for proper display on devices with notch/Dynamic Island
- Fixed PWA standalone mode navigation issues on iOS

### Favicon & Branding
- Added proper favicon assets in the `/assets/` directory
- Includes favicon.ico, PNG favicons (16x16, 32x32), and apple-touch-icon
- All HTML pages now reference the new favicon assets

### Report Workflow Improvements
- Improved report status tracking to accurately reflect workflow state
- Fixed post-submission flow to allow starting fresh reports after submission

### PWA Enhancements
- Fixed PWA paths for GitHub Pages subdirectory hosting
- Updated webhook URLs to production endpoints

---

## Summary

FieldVoice Pro is a sophisticated, production-ready field documentation system that:
- **Fully installable as a PWA** - Works offline when saved to home screen on mobile devices
- **Simplified single-action dashboard** - Streamlined interface for quick daily report creation
- **AI Kit integration** - Side-by-side text refinement with training data export for prompt improvement
- **Self-contained HTML export** - Generate portable, professional reports with embedded photos and metadata
- **Web Share API support** - Native share sheet integration on iOS PWA for easy report distribution
- Operates primarily client-side with optional n8n webhook integration for AI features
- Supports voice-first data entry via native keyboard dictation with AI enhancement
- Generates professional, DOT-compliant reports (HTML export and PDF print)
- **Complete offline support** for report creation, editing, and viewing (weather sync and AI features require internet)
- Uses n8n webhooks for AI text refinement and report submission
- Manages browser storage efficiently with automatic compression
- **Service worker caching** ensures fast load times and airplane mode compatibility
- **Safe-area support** for modern iOS devices with notch/Dynamic Island
- **Streamlined navigation** with Home buttons and improved workflow tracking

The codebase is mature (~9,755 lines including PWA infrastructure), well-structured, and includes comprehensive error handling for real-world field conditions including graceful offline degradation.

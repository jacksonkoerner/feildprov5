# FieldVoice Pro - Voice-Powered Construction Field Reporting System

## Overview

**FieldVoice Pro** is a voice-first, mobile-optimized web application designed for construction field documentation. It enables Resident Project Representatives (RPRs), field engineers, and inspectors to quickly document daily work activities using voice-to-text transcription with AI refinement, generating professional DOT-compliant reports.

### Primary Use Case
- **Target Users**: Construction inspectors, Resident Project Representatives (RPRs), field engineers
- **Industry Focus**: DOT (Department of Transportation) construction projects, specifically LADOTD compliance
- **Current Project**: North-South Connector Road Phase 2 at Louis Armstrong New Orleans International Airport (MSY)

### Key Value Propositions
- Save 1+ hour daily on field documentation
- Ensure LADOTD (Louisiana Department of Transportation) compliance
- GPS-verified, timestamped reports for legal protection
- Voice-assisted documentation optimized for noisy construction environments

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
| **Groq Whisper API** | Speech-to-text transcription (iOS fallback) | Bearer token (user's API key) |
| **Groq LLM API** | AI text refinement for professional reports | Bearer token (user's API key) |
| **Open-Meteo API** | Real-time weather data | None (free, no key required) |

### Browser APIs Used
| API | Purpose |
|-----|---------|
| Web Speech Recognition API | Native browser speech-to-text |
| MediaDevices API | Camera & microphone access |
| Geolocation API | GPS coordinates for photos/weather |
| AudioContext API | Microphone level testing/visualization |
| Canvas API | Image compression |
| localStorage | Client-side data persistence |

### Architecture
- **Type**: Static Single Page Application (SPA)
- **Backend**: None - fully client-side
- **Database**: Browser localStorage only
- **Deployment**: Any static web server (HTTPS required for media APIs)

---

## Project Structure

```
/V3/
├── index.html              # Home dashboard / main entry point
├── quick-interview.html    # Quick report flow (~3 min, essential fields)
├── interview.html          # Full report flow (~5+ min, comprehensive)
├── review.html             # AI-powered review & text refinement
├── report.html             # Print-ready PDF report viewer/generator
├── editor.html             # Photo editor & section-specific editing
├── permissions.html        # System setup, permission testing, API key config
├── settings.html           # Project configuration & data management
├── landing.html            # Marketing/onboarding landing page
└── README.md               # This documentation file
```

### Page Descriptions

| Page | Lines | Purpose |
|------|-------|---------|
| `index.html` | ~740 | Main dashboard showing project info, weather, report status, and navigation |
| `quick-interview.html` | ~1,460 | Streamlined report with 7 sections: Weather, Work Summary, Issues, Inspections, Safety, Visitors, Photos |
| `interview.html` | ~1,196 | Full report with additional sections: Contractors, Personnel Headcounts, Equipment |
| `review.html` | ~858 | Side-by-side original vs. AI-refined text comparison with manual editing |
| `report.html` | ~595 | 4-page professional PDF-ready report with print styles |
| `editor.html` | ~847 | Photo capture with GPS embedding, section-specific editing interface |
| `permissions.html` | ~1,119 | Permission testing (mic, camera, GPS), Groq API key setup, iOS-specific instructions |
| `settings.html` | ~268 | Project settings, inspector name, data export/clear functions |
| `landing.html` | ~1,467 | Marketing page with feature overview and onboarding |

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

  // Project Overview
  overview: {
    projectName: "North-South Connector Road Phase 2",
    noabProjectNo: "1291",
    cnoSolicitationNo: "N/A",
    date: "1/14/2025",
    startTime: "6:00 AM",
    endTime: "4:00 PM",
    shiftDuration: "10.00 hours",
    location: "Louis Armstrong New Orleans Int'l Airport",
    engineer: "Garver, LLC",
    contractor: "Hunt, Gibbs, Boh, & Metro (HGBM)",
    noticeToProceed: "June 27th, 2025",
    contractDuration: "467 days",
    expectedCompletion: "October 7th, 2026",
    contractDayNo: "Day 201 of 467",
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

  // Contractors on site (Full report only)
  contractors: [
    { name: "HGBM", trade: "General Contractor", count: 12 }
  ],

  // Work activities documented
  activities: [
    { contractor: "General", trade: "General", narrative: "Continued grading operations..." }
  ],

  // Personnel headcounts (Full report only)
  operations: [
    {
      contractor: "HGBM",
      trade: "Earthwork",
      superintendents: 1,
      foremen: 2,
      operators: 4,
      laborers: 6,
      others: 0,
      total: 13
    }
  ],

  // Equipment on site (Full report only)
  equipment: [
    { contractor: "HGBM", type: "Excavator", model: "CAT 336", quantity: 2, status: "Active" }
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
    "LADOTD inspector on site 10:00 AM - 2:00 PM"
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

  // AI-refined versions of text (populated after review.html refinement)
  refinedData: {
    weather: "Site conditions were dry with partly cloudy skies...",
    activities: "Contractor HGBM continued earthwork operations...",
    issues: "A utility conflict was identified at Station 45+00...",
    inspections: "Quality control testing included casting of concrete cylinders...",
    safety: "No safety incidents were reported. A toolbox talk on heat stress...",
    visitors: "LADOTD representative conducted a site inspection..."
  }
}
```

### localStorage Keys

| Key Pattern | Purpose |
|-------------|---------|
| `fieldvoice_report_YYYY-MM-DD` | Primary storage for date-specific reports |
| `fieldvoice_report` | Legacy/backup key for compatibility |
| `groq_api_key` | User's Groq API key for Whisper & LLM |
| `fvp_mic_granted` | Microphone permission status flag |
| `fvp_loc_granted` | Location permission status flag |
| `fvp_onboarded` | First-time onboarding completed flag |
| `fvp_banner_dismissed` | Permission warning banner dismissed |
| `fvp_banner_dismissed_date` | Timestamp of banner dismissal (24hr reset) |
| `fvp_speech_failed` | Speech Recognition failure tracking |

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
          └─► [Report Type Modal]
               ├─► "Quick Report" ─► [quick-interview.html]
               └─► "Full Report" ─► [interview.html]
```

### 2. Interview/Documentation Flow

```
[quick-interview.html] or [interview.html]
     │
     ├─► Expandable section cards for each field:
     │    ├─► Weather & Site Conditions
     │    ├─► Work Summary (activities)
     │    ├─► Issues & Delays
     │    ├─► QA/QC Inspections
     │    ├─► Safety
     │    ├─► Visitors & Communications
     │    ├─► Progress Photos
     │    └─► (Full only) Contractors, Headcounts, Equipment
     │
     ├─► Each section supports:
     │    ├─► Text input (manual typing)
     │    ├─► Voice recording (microphone button)
     │    ├─► Mark as N/A (skip section)
     │    └─► Real-time preview updates
     │
     ├─► Progress bar shows completion percentage
     │
     └─► User clicks "Finish" ─► [review.html]
```

### 3. Voice Recording Flow

```
User taps microphone button
     │
     ├─► Device Detection
     │    ├─► iOS Safari? ─► Use Groq Whisper API
     │    ├─► Other browser ─► Use Web Speech Recognition API
     │    └─► SpeechRecognition failed before? ─► Fall back to Whisper
     │
     ├─► Whisper API Path (iOS/fallback):
     │    ├─► Check for API key (prompt setup if missing)
     │    ├─► Start MediaRecorder (audio/webm or audio/mp4)
     │    ├─► Show recording indicator ("Recording audio...")
     │    ├─► User stops recording
     │    ├─► Upload audio blob to Groq Whisper API
     │    └─► Receive transcription, apply to section
     │
     └─► Web Speech Recognition Path (Chrome, Firefox, Edge):
          ├─► Start recognition (continuous mode)
          ├─► Show live transcription
          ├─► User stops recording
          └─► Apply final transcript to section
```

### 4. AI Refinement Flow (review.html)

```
[review.html] Loaded with report data
     │
     ├─► Display side-by-side: Original | AI Refined
     │
     ├─► User can:
     │    ├─► Click "Refine All" ─► Process all sections
     │    ├─► Click individual "Refine" ─► Process single section
     │    └─► Manually edit either column
     │
     ├─► Refinement Process:
     │    ├─► Check for Groq API key
     │    ├─► Send original text + section-specific prompt
     │    ├─► Groq LLM returns professional version
     │    └─► Display with typing animation
     │
     ├─► AI Refinement Rules (enforced via prompt):
     │    ├─► Never invent or add information
     │    ├─► Keep all facts, quantities, names exactly as stated
     │    ├─► Convert informal language to professional tone
     │    ├─► Format for DOT documentation standards
     │    └─► Highlight safety concerns appropriately
     │
     └─► User clicks "Export" ─► [report.html]
```

### 5. Report Generation Flow

```
[report.html] Loaded with report data
     │
     ├─► Render 4-page professional report:
     │    ├─► Page 1: Project Overview + Daily Work Summary
     │    ├─► Page 2: Personnel Table + Equipment Table
     │    ├─► Page 3: Issues, Visitors, QA/QC, Safety + Signature
     │    └─► Page 4: Photo Gallery (if photos exist)
     │
     ├─► User clicks "Print / PDF"
     │    └─► Browser print dialog (save as PDF)
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

### Hardcoded Project Configuration

The current project details are hardcoded in `index.html` and `quick-interview.html`:

```javascript
const INITIAL_OVERVIEW = {
    projectName: "North-South Connector Road Phase 2",
    noabProjectNo: "1291",
    location: "Louis Armstrong New Orleans Int'l Airport",
    engineer: "Garver, LLC",
    contractor: "Hunt, Gibbs, Boh, & Metro (HGBM)",
    noticeToProceed: "June 27th, 2025",
    contractDuration: "467 days",
    expectedCompletion: "October 7th, 2026"
};
```

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

### Groq API Configuration

Users must obtain their own API key from [console.groq.com](https://console.groq.com) and enter it on the Permissions page.

**Models Used:**
- **Whisper**: `whisper-large-v3-turbo` (speech-to-text)
- **LLM**: Default Groq model for text refinement

---

## Permission Requirements

### Required Permissions

| Permission | Purpose | How to Test |
|------------|---------|-------------|
| **Microphone** | Voice recording for transcription | `permissions.html` → Enable Microphone → Start Test |
| **Camera** | Photo documentation | `editor.html` or inline photo capture |
| **Geolocation** | GPS for weather & photo timestamps | Auto-requested on first weather sync |

### iOS Safari Special Requirements

iOS Safari uses Apple's Siri servers for Web Speech Recognition, requiring:

1. **Siri Enabled**: Settings → Siri & Search → Enable "Hey Siri" or "Press Side Button"
2. **Dictation Enabled**: Settings → General → Keyboard → Enable Dictation
3. **Privacy Settings**: Settings → Privacy & Security → Speech Recognition → Safari enabled

If these fail, the app falls back to **Groq Whisper API** (requires API key).

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

## Error Handling

### Speech Recognition Errors
| Error Code | Meaning | Recovery |
|------------|---------|----------|
| `not-allowed` | Permission denied | Prompt user to enable in system settings |
| `service-not-allowed` | iOS Siri/Dictation disabled | Show iOS help panel, offer Whisper fallback |
| `network` | Connectivity issue | Retry or fallback to Whisper |
| `aborted` | User cancelled | No action needed |

### Photo Errors
| Error | Recovery |
|-------|----------|
| Camera permission denied | Prompt user to enable in browser/system settings |
| GPS unavailable | Continue without coordinates, mark as "No GPS" |
| Storage quota exceeded | Remove oldest photo, try higher compression |
| Invalid file type | Reject with error message |

### API Errors
| Scenario | Recovery |
|----------|----------|
| No API key | Prompt user to enter on Permissions page |
| Invalid API key | Show error, prompt re-entry |
| Rate limited | Show error, suggest waiting |
| Network failure | Retry with exponential backoff |

---

## Browser Compatibility

| Browser | Support Level | Notes |
|---------|---------------|-------|
| **Chrome (Desktop/Android)** | Full | Best experience, all features work |
| **Firefox** | Full | All features work |
| **Safari (Desktop)** | Full | All features work |
| **Safari (iOS)** | Partial | Requires Siri/Dictation OR Groq API key for voice |
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
1. Add section HTML to `quick-interview.html` and/or `interview.html`
2. Add corresponding data field to report object structure
3. Update `renderSection()` function for display
4. Add refinement support in `review.html`
5. Add export rendering in `report.html`

---

## File Size Reference

| File | Lines | Size (approx) |
|------|-------|---------------|
| index.html | 740 | 28 KB |
| quick-interview.html | 1,460 | 58 KB |
| interview.html | 1,196 | 48 KB |
| review.html | 858 | 34 KB |
| report.html | 595 | 24 KB |
| editor.html | 847 | 34 KB |
| permissions.html | 1,119 | 45 KB |
| settings.html | 268 | 11 KB |
| landing.html | 1,467 | 59 KB |
| **Total** | **~8,550** | **~341 KB** |

---

## Security Considerations

### Data Privacy
- All report data stored locally in browser (never sent to external servers except Groq APIs)
- Photos stored as base64 in localStorage (never uploaded unless explicitly shared)
- GPS coordinates embedded in photos for audit purposes

### API Key Storage
- Groq API key stored in localStorage (accessible to browser only)
- Key never transmitted except to Groq's official API endpoints
- Users responsible for their own API key security

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

## Summary

FieldVoice Pro is a sophisticated, production-ready field documentation system that:
- Operates entirely client-side with no backend dependencies
- Supports voice-first data entry with AI enhancement
- Generates professional, DOT-compliant PDF reports
- Works offline (except for weather sync and AI features)
- Handles iOS Safari limitations gracefully with Groq API fallbacks
- Manages browser storage efficiently with automatic compression

The codebase is mature (~8,550 lines), well-structured, and includes comprehensive error handling for real-world field conditions.

# Saviour Digital Discipline — Kotlin Android App

Convert the Stitch UI design project **"Saviour Digital Discipline System"** (ID: `7602448406776374593`) into a native Android application using **Kotlin + Jetpack Compose + Material 3**.

## Source Stitch Screens (5 visible screens)

| # | Screen ID | Title | Description |
|---|-----------|-------|-------------|
| 1 | `c05d9051a6c2433aa6493388e0357560` | **Dashboard (Premium Dark)** | Main hub showing deep work stats, CTA to start focus session |
| 2 | `c0946e69d51a45e3a7934f9f52ce0a10` | **Focus Timer (Premium Dark)** | Circular countdown timer with focus mode controls |
| 3 | `4f7c8659ab0243b0a1a609e2426aaf58` | **Breathe Mindfulness (Premium Dark)** | 4-2-6 breathing cycle animation |
| 4 | `62a26cb09f534088b37ca7910dec1bc5` | **Blocker Config (Premium Dark)** | Restriction hub: domain filter, YouTube filter, Reels/Shorts toggles |
| 5 | `b4da762a2c1e4b67a47ae2407e6afa4a` | **Elite Syndicate (Premium Dark)** | Leaderboard showing top focus performers |

## User Review Required

> [!IMPORTANT]
> **Android SDK & Build Tools**: This plan targets **API 26+ (Android 8.0+)** with `compileSdk = 35`. Let me know if you need a different minimum API level.

> [!IMPORTANT]
> **Backend / Data Persistence**: This initial version will use **local state only** (in-memory + ViewModel). No Firebase/Room database is wired in yet. The existing `firestore.rules` file in your downloads suggests you may want Firebase later — should I integrate Firebase from the start?

> [!WARNING]
> **App Blocking Functionality**: The "Blocker Hub" screen has toggles for blocking apps/domains. Real app-blocking requires Android's `UsageStatsManager`, `AccessibilityService`, or `DevicePolicyManager` — all of which require special permissions and are restricted on newer Android versions. This initial version will implement the **UI only** with mock toggle states. Real blocking logic would be a separate phase.

## Open Questions

1. **Firebase Integration** — Should I wire up Firestore for the leaderboard and user data, or keep everything local for now?
2. **App Blocking** — Do you want placeholder blocking logic, or just the UI screens?
3. **Additional screens** — You have ~40+ hidden screens in the Stitch project. Should I only implement the 5 visible ones, or also include specific hidden ones?

---

## Proposed Changes

### Project Initialization

#### [NEW] Android project scaffolding

Create a new Android project using Kotlin DSL with:
- **Package**: `com.saviourlabs.saviour`
- **Min SDK**: 26 (Android 8.0)
- **Target/Compile SDK**: 35
- **Jetpack Compose BOM**: `2025.01.01`
- **Material 3** with custom theme
- **Navigation Compose** for screen routing

---

### Design System / Theme

Maps directly from the Stitch design tokens.

#### [NEW] `ui/theme/Color.kt`
- Map all named colors from the Stitch design system (`primary: #4f378a`, `primaryContainer: #6750a4`, `surface: #fdf7ff`, etc.)
- Define both light and dark color schemes (Stitch screens are labeled "Premium Dark")

#### [NEW] `ui/theme/Type.kt`
- Manrope font family
- Headlines: 40/32/24px → `headlineLarge`, `headlineMedium`, `headlineSmall`
- Body: 18/16px → `bodyLarge`, `bodyMedium`
- Labels: 12px caps, 14px caption → `labelSmall`, `labelMedium`

#### [NEW] `ui/theme/Shape.kt`
- Standard `RoundedCornerShape(18.dp)` for cards
- Pill shape `RoundedCornerShape(999.dp)` for bottom nav, chips
- `8.dp` for smaller elements

#### [NEW] `ui/theme/Theme.kt`
- `SaviourTheme` composable wrapping `MaterialTheme`
- Dynamic dark/light support (default dark for "Premium Dark" aesthetic)
- Glassmorphism-inspired surface tints via custom `CompositionLocal`

---

### Navigation

#### [NEW] `navigation/SaviourNavGraph.kt`
- 5 routes: `dashboard`, `focusTimer`, `breathe`, `blockerHub`, `leaderboard`
- `NavHost` with animated transitions (slide + fade)

#### [NEW] `navigation/BottomNavBar.kt`
- Floating pill-shaped bottom navigation (matches Stitch design: `border-radius: 999px`)
- 5 icons: dashboard, insights (timer), adjust (breathe), group (leaderboard), flag (blocker)
- Glassmorphism background with blur effect

---

### Screens

#### [NEW] `ui/screens/DashboardScreen.kt`
- **Header**: "SAVIOUR" branding with greeting
- **Deep Work Card**: Large circular progress showing `0m` deep work completed
- **CTA Section**: "Ready for a serious session?" with gradient "Start Focus" button
- **Quick Stats Row**: Streak days, total focus time, sessions count
- **Today's Schedule**: List of upcoming focus blocks

#### [NEW] `ui/screens/FocusTimerScreen.kt`
- **Circular Timer**: Large animated ring with countdown (25:00 default)
- **Controls**: Start/Pause/Reset buttons with gradient styling
- **Session Type Selector**: Deep Work, Short Break, Long Break chips
- **Current Session Info**: Task name, elapsed time, focus score

#### [NEW] `ui/screens/BreatheScreen.kt`
- **Breathing Circle**: Animated expanding/contracting circle
- **Phase Display**: "Inhale" / "Hold" / "Exhale" with phase indicator
- **Cycle Info**: "4-2-6 Cycle" label
- **Session Controls**: Start/stop breathing exercise
- **Canvas animation**: Smooth scale + opacity transitions synced to breath phases

#### [NEW] `ui/screens/BlockerHubScreen.kt`
- **Header**: "Restriction Hub" with subtitle
- **Domain Filter Section**: Search bar + list of blocked domains with remove buttons
- **Active Blocks**: Cards showing currently blocked apps/sites
- **YouTube Filter**: Toggle for strict mode, allowlist channels list
- **Social Toggles**: "Disable Reels" and "Disable Shorts" toggle rows with descriptions

#### [NEW] `ui/screens/LeaderboardScreen.kt`
- **Header**: "Elite Syndicate" with subtitle "Top performers dominating the focus state"
- **Podium**: Top 3 users with avatar, rank badge, and focus hours
- **Leaderboard List**: Scrollable list with rank, avatar, name, focus score, streak
- **User's Position**: Highlighted card showing current user's rank

---

### Shared Components

#### [NEW] `ui/components/GlassCard.kt`
- Reusable glassmorphism card with `backdrop-filter` equivalent
- Uses `Modifier.blur()` + semi-transparent surface colors
- Consistent `18.dp` corner radius

#### [NEW] `ui/components/GradientButton.kt`
- Primary CTA button with accent gradient fill
- Glow effect on press using `Modifier.shadow()`

#### [NEW] `ui/components/CircularProgress.kt`
- Animated circular progress indicator for timer and stats
- Canvas-drawn arc with gradient stroke

#### [NEW] `ui/components/ToggleRow.kt`
- Toggle switch row for blocker settings
- Title, description, and Material 3 Switch

---

### Data Layer (Local)

#### [NEW] `data/models/FocusSession.kt`
- Data class: `id`, `startTime`, `duration`, `type`, `completed`

#### [NEW] `data/models/BlockerConfig.kt`
- Data class: `blockedDomains`, `youtubeStrict`, `disableReels`, `disableShorts`, `allowlistedChannels`

#### [NEW] `data/models/LeaderboardEntry.kt`
- Data class: `rank`, `name`, `avatarUrl`, `focusHours`, `streak`

#### [NEW] `viewmodel/DashboardViewModel.kt`
- Holds: total deep work time, streak, quick stats
- Mock data for initial version

#### [NEW] `viewmodel/FocusTimerViewModel.kt`
- CountDownTimer logic, session state management
- Start/pause/reset actions

#### [NEW] `viewmodel/BlockerViewModel.kt`
- Toggle states, domain list management

---

### Entry Point

#### [NEW] `MainActivity.kt`
- Single-activity architecture
- Sets up `SaviourTheme` + `SaviourNavGraph`
- Edge-to-edge display with system bar handling

---

## Project File Structure

```
app/src/main/java/com/saviourlabs/saviour/
├── MainActivity.kt
├── navigation/
│   ├── SaviourNavGraph.kt
│   └── BottomNavBar.kt
├── ui/
│   ├── theme/
│   │   ├── Color.kt
│   │   ├── Type.kt
│   │   ├── Shape.kt
│   │   └── Theme.kt
│   ├── screens/
│   │   ├── DashboardScreen.kt
│   │   ├── FocusTimerScreen.kt
│   │   ├── BreatheScreen.kt
│   │   ├── BlockerHubScreen.kt
│   │   └── LeaderboardScreen.kt
│   └── components/
│       ├── GlassCard.kt
│       ├── GradientButton.kt
│       ├── CircularProgress.kt
│       └── ToggleRow.kt
├── data/
│   └── models/
│       ├── FocusSession.kt
│       ├── BlockerConfig.kt
│       └── LeaderboardEntry.kt
└── viewmodel/
    ├── DashboardViewModel.kt
    ├── FocusTimerViewModel.kt
    └── BlockerViewModel.kt
```

## Verification Plan

### Automated Tests
- Build the project with `./gradlew assembleDebug` to ensure it compiles
- Run `./gradlew lint` for static analysis

### Manual Verification
- Take screenshots of each screen from the emulator to visually compare against the Stitch designs
- Verify navigation between all 5 screens via the bottom nav bar
- Test the focus timer start/pause/reset flow
- Test the breathing animation cycle
- Verify toggle states in the blocker hub

# Flutter Resume Builder — Desktop App

A professional-grade **Flutter Windows desktop** resume builder with ATS scoring, live PDF preview, **15 ultra-premium templates**, and a 4-panel layout.

---

## Screenshots

| Panel | Description |
|-------|-------------|
| Left Sidebar | Section navigation with completion indicator |
| Center Form | Context-sensitive form for each resume section |
| Right Preview | Live PDF-quality template preview |
| Right ATS Panel | Real-time ATS score gauge and improvement tips |

---
<img width="1914" height="1003" alt="image" src="https://github.com/user-attachments/assets/3c31835a-81c4-4422-8af7-44352cb7dfa0" />




## Prerequisites

| Tool | Minimum Version |
|------|----------------|
| Flutter | 3.19.0 |
| Dart | 3.3.0 |
| Windows | 10 (build 1903+) |
| Visual Studio | 2022 with "Desktop development with C++" workload |

---

## Local Package Dependency

This app depends on a **local** `flutter_resume_builder` package at the relative path `../flutter_resume_builder`.

```
D:\winforms_generator\
├── App_Flutter_Resume_Builder\   ← this app
└── flutter_resume_builder\       ← local package (must exist)
```

Make sure the package folder exists before running `flutter pub get`.

---

## Getting Started

```powershell
# 1. Navigate to the app folder
cd D:\winforms_generator\App_Flutter_Resume_Builder

# 2. Install dependencies
flutter pub get

# 3. Run in debug mode
flutter run -d windows

# 4. Build release EXE
flutter build windows --release
```

The release executable is at:
```
build\windows\x64\runner\Release\flutter_resume_builder.exe
```

---

## Project Structure

```
lib/
├── core/
│   ├── constants/       app_constants.dart
│   ├── models/          custom_section.dart  (app-local model)
│   ├── providers/       resume_provider.dart, ats_score_provider.dart,
│   │                    navigation_provider.dart, template_provider.dart,
│   │                    app_settings_provider.dart
│   ├── services/        hive_storage_service.dart, autosave_service.dart
│   └── theme/           app_colors.dart, app_theme.dart
├── features/
│   ├── personal_info/   personal_info_screen.dart
│   ├── contact/         contact_screen.dart
│   ├── summary/         summary_screen.dart
│   ├── experience/      experience_screen.dart
│   ├── education/       education_screen.dart
│   ├── skills/          skills_screen.dart
│   ├── projects/        projects_screen.dart       ← exports DialogHeader/DialogFooter
│   ├── certifications/  certifications_screen.dart
│   ├── languages/       languages_screen.dart
│   ├── awards/          awards_screen.dart
│   ├── volunteer/       volunteer_screen.dart
│   ├── publications/    publications_screen.dart
│   ├── custom_sections/ custom_sections_screen.dart
│   ├── templates/       templates_screen.dart
│   ├── ats_scanner/     ats_scanner_screen.dart
│   ├── export_pdf/      export_pdf_screen.dart
│   ├── settings/        settings_screen.dart
│   └── dashboard/       main_shell.dart
└── shared/
    └── widgets/         form_panel_wrapper.dart, section_header.dart,
                         form_field_widgets.dart, template_preview_panel.dart,
                         ats_score_panel.dart, resume_sidebar.dart
```

---

## Key Design Decisions

### Provider Architecture

| Provider | Type | Purpose |
|----------|------|---------|
| `resumeProvider` | `NotifierProvider<ResumeNotifier, ResumeData>` | Core resume data (package-compatible fields only) |
| `atsTargetingProvider` | `NotifierProvider<AtsTargetingNotifier, AtsTargetingState>` | ATS targeting: `targetJobTitle`, `targetCompany`, `highlightedSkills`, `hiddenSections` |
| `customSectionsProvider` | `NotifierProvider<CustomSectionsNotifier, List<CustomSection>>` | Custom resume sections (app-local model) |
| `atsScoreProvider` | `Provider<AtsScore>` | Computed ATS score from package |
| `navigationProvider` | `NotifierProvider` | Active left-panel section |
| `templateProvider` | `Provider` | Selected PDF template |

### Package Version Compatibility

The app is designed to compile with **both** older and v2 Ultra versions of `flutter_resume_builder`:

- **v2 Ultra fields** (`targetJobTitle`, `highlightedSkills`, `hiddenSections`, `customSections`, `theme`, `meta`, `keywordGap`, `keywordMatchRate`) are accessed via **`dynamic` + `try/catch`** guards — they silently no-op on older package versions
- **`CustomSection` / `CustomSectionItem`** are defined locally in `lib/core/models/custom_section.dart` — no package dependency
- **`AtsTargetingState`** is a pure app-level state class stored in `atsTargetingProvider`

### Dialog Widgets

`DialogHeader` and `DialogFooter` (public) live in `projects_screen.dart` and are imported by:
- `certifications_screen.dart`
- `languages_screen.dart`
- `awards_screen.dart`
- `volunteer_screen.dart`
- `publications_screen.dart`
- `custom_sections_screen.dart`

### Theme Fonts

`app_theme.dart` uses the string literal `'Inter'` (not `GoogleFonts.inter().fontFamily`) in all `const TextStyle` and `ThemeData` declarations. `google_fonts` is kept as a dependency for runtime font loading, but is not called inside `const` contexts.

---

## Resume Sections

| Section | Model Class | Notes |
|---------|------------|-------|
| Personal Info | `ResumeData.fullName`, `.jobTitle`, `.summary` | |
| Contact | `ContactInfo` | Email, phone, location, LinkedIn, GitHub, website, Twitter |
| Experience | `WorkExperience` | Employment types include `remote` |
| Education | `Education` | |
| Skills | `Skill` | Highlight via `atsTargetingProvider` |
| Projects | `Project` | 8 project types |
| Certifications | `Certification` | With credential ID and score |
| Languages | `LanguageEntry` | 6 proficiency levels |
| Awards | `Award` | |
| Volunteer | `Volunteer` | No `location` field in base model |
| Publications | `Publication` | 8 types including `chapter` and `whitepaper` |
| Custom Sections | `CustomSection` (local) | App-local model with items |

---

## PDF Templates

### Classic Templates (10)
| Template | Tier | Description |
|----------|------|-------------|
| ATS Friendly | Free | Clean single-column, maximum parser compatibility |
| Modern | Free | Two-column with accent color |
| Minimal | Free | Ultra-clean whitespace-focused |
| Aurora | Pro | Purple-to-cyan gradient header, luminous cards |
| Blueprint | Pro | Navy engineering-diagram precision grid |
| Elegant | Pro | Gold-accented serif classic, cream background |
| Neon | Pro | Cyberpunk dark with glowing magenta/cyan accents |
| Marble | Pro | Navy header, gold ornamental rules, royal two-column |
| Organic | Pro | Warm terracotta, rounded cards, nature-inspired |
| Obsidian | Pro | Obsidian black header, platinum rules, power look |

### Ultra² Batch 3 — New Premium Showcase Templates (5)
| Template | Tier | Design Style | Best For |
|----------|------|-------------|----------|
| Apple White | Pro | Minimal corporate, Apple HIG-inspired | FAANG, product, UX leads |
| Dark Futuristic | Pro | Deep space + electric neon-blue holographic | AI, cybersecurity, robotics |
| Luxury Gold | Pro | Velvet navy + champagne gold foil executive | C-suite, investment banking |
| Creative Designer | Pro | Bold coral/teal asymmetric editorial split | Creative directors, brand designers |
| Glassmorphism | Pro | Frosted glass cards, aurora gradient, depth layers | UX/UI designers, product designers |

---

## Autosave

The app autosaves resume data to `SharedPreferences` using a 2-second debounce via `AutosaveService` (Riverpod listener). Data persists across app restarts.

---

## Running Tests

```powershell
flutter test
```

The test file at `test/widget_test.dart` includes:
- ATS scorer unit tests with sample resume data
- App smoke test (widget tree mounts without exceptions)

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `flutter_resume_builder` not found | Ensure `../flutter_resume_builder` folder exists relative to this app |
| `flutter pub get` fails | Run `flutter pub get` inside the `flutter_resume_builder` folder first |
| Build fails with "Method invocation is not constant" | Ensure `app_theme.dart` uses `'Inter'` string literals, not `GoogleFonts.inter().fontFamily` |
| `_DialogHeader`/`_DialogFooter` not found | These are now public: `DialogHeader` / `DialogFooter` in `projects_screen.dart` |
| `customSections` named param not found | `resume_provider.dart` no longer passes v2-only fields to `ResumeData()`; custom sections are in `customSectionsProvider` |
| **`MSBuild INSTALL.vcxproj` error (error MSB3073)** | Caused by `sqlite3_flutter_libs >= 0.5.22` cmake INSTALL step failing on VS BuildTools (non-full VS). **Fixed**: pinned to `sqlite3_flutter_libs: ^0.5.20` in pubspec.yaml. Also ensure you have **Visual Studio 2022** (full install) with **"Desktop development with C++"** workload — VS BuildTools alone is insufficient for Flutter Windows builds. |
| `Try flutter pub outdated for more information` | Run `flutter pub upgrade` from the app directory to fetch latest compatible versions. All deps are now pinned to latest stable in pubspec.yaml. |
| cmake error on `App_Flutter_Resume_Builder\build\windows\x64\INSTALL.vcxproj` | Delete the `build\` folder entirely (`Remove-Item -Recurse -Force build\`) then run `flutter build windows --release` again with the updated pubspec. |

---

## License

Private — all rights reserved.

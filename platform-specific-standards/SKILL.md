---
description: "Enforce platform-specific standards when developing for mobile (Expo/React Native), desktop (Tauri), Chrome extensions, or when implementing real-time communication (WebSocket/SSE), internationalization (i18n/l10n), feature flags, or accessibility features. Use this skill whenever the user works on mobile app screens, native modules, EAS builds, Tauri commands, Chrome extension manifests, WebSocket/SSE implementations, translation strings, locale handling, feature flag definitions, progressive rollouts, ARIA attributes, keyboard navigation, or screen reader support — even if they don't explicitly mention the standard."
---

# Platform-Specific Standards

This skill enforces engineering standards for specialized platforms and cross-cutting features. It detects what you are working on and applies the relevant standard automatically.

## Detection Rules

Identify the platform or feature from context clues. Only read the 1-2 standards that apply — never all of them.

| Signal | Platform/Feature | Standards Document |
|--------|------------------|--------------------|
| `app.config.ts`, `eas.json`, `expo-*`, React Native imports, `starter-mobile/` | **Mobile (Expo/React Native)** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/mobile-development.md` |
| `tauri.conf.json`, `src-tauri/`, Rust `#[tauri::command]`, `starter-desktop/` | **Desktop (Tauri)** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/desktop-development.md` |
| `manifest.json` (with `permissions`, `content_scripts`, `background`), Chrome APIs | **Chrome Extension** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/chrome-extensions.md` |
| WebSocket, Socket.IO, SSE, `EventSource`, `@SubscribeMessage`, gateway files | **Real-Time Communication** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/real-time-communication.md` |
| Translation files, `useTranslations`, `Intl.*`, locale directories, `.po`/`.xliff` | **Internationalization (i18n)** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/internationalization.md` |
| `FeatureGate`, `useFeatureFlag`, `FeatureFlagController`, `@FeatureFlag`, flag names | **Feature Flags** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/feature-flags.md` |
| `aria-*`, `role=`, `tabIndex`, keyboard event handlers, screen reader labels, `<label>` | **Accessibility** | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/accessibility-standards.md` |

## Workflow

1. **Detect**: Determine which platform or feature the current task involves using the signals above.
2. **Read**: Use the WebFetch tool to load ONLY the relevant standards document(s) — one or two at most.
3. **Apply**: Enforce the standards from those documents throughout the task. Flag violations, suggest corrections, and follow the documented patterns.
4. **Validate**: Before completing work, verify compliance with the loaded standards.

## Rules

- This skill is a lightweight dispatcher. The referenced standards documents contain all the detail — do not duplicate their content here.
- If multiple platforms apply (e.g., mobile + feature flags), read both relevant documents.
- Never read all seven documents. Be selective based on what the task actually touches.
- If no signals are detected, do not activate. This skill is only for the specific platforms and features listed above.

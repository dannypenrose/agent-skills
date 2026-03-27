---
name: snackbar-enforcement
description: "Enforce consistent snackbar/toast notification patterns across the entire platform. Use this skill whenever the user creates, modifies, or reviews snackbar/toast notifications, notification providers, or feedback components. Triggers when code uses enqueueSnackbar, toast(), notify(), or any notification API — even if the user doesn't explicitly mention 'snackbar standards'."
---

# Snackbar / Toast Notification Enforcement

Ensure all snackbar and toast notifications follow a consistent pattern across the entire platform, regardless of the underlying library. Consistency in user feedback notifications is critical for UX coherence.

## When This Skill Activates

This skill applies whenever work touches:

- Snackbar or toast notification calls (`enqueueSnackbar`, `toast`, `notify`, etc.)
- Notification provider setup or configuration
- Success/error/warning/info feedback to the user after actions
- Notification styling, positioning, or theming
- Adding or changing a notification library dependency

## Step 1: Detect the Project's Notification Library

Check the project's `package.json` for known notification libraries:

| Library | Package Name | Hook/API |
|---------|-------------|----------|
| notistack | `notistack` | `useSnackbar()` / `enqueueSnackbar()` |
| react-hot-toast | `react-hot-toast` | `toast()` |
| react-toastify | `react-toastify` | `toast()` |
| sonner | `sonner` | `toast()` |
| Chakra UI toast | `@chakra-ui/react` | `useToast()` |
| MUI Snackbar | `@mui/material` | `<Snackbar>` component |

If no notification library exists, recommend **notistack** as the default — it provides the best balance of customisability, Material Design integration, and stacking behaviour.

## Step 2: Locate the Existing Pattern

Search the project for the notification setup:

1. **Provider**: Search for `SnackbarProvider`, `ToastContainer`, `Toaster`, or equivalent wrapper
2. **Styled components**: Search for custom notification styles (e.g., `StyledNotistack`, custom toast components)
3. **Usage pattern**: Search for `enqueueSnackbar`, `toast(`, `useToast`, `useSnackbar` to find how notifications are invoked

Read these files to understand the established pattern before writing any notification code.

## Step 3: Apply Consistency Rules

### Provider Configuration (enforce these defaults)

| Setting | Required Value | Rationale |
|---------|---------------|-----------|
| Max visible | 5 | Prevents notification overload |
| Auto-hide duration | 3000ms (3 seconds) | Long enough to read, short enough not to annoy |
| Prevent duplicates | `true` | Identical messages should not stack |
| Default variant | `success` | Most actions succeed; errors are explicit |
| Position | Top-right | Non-intrusive, consistent with platform conventions |
| Close button | Always present | User must always be able to dismiss manually |

### Variant Usage (mandatory)

| Variant | When to Use | Icon Style |
|---------|------------|------------|
| `success` | CRUD operations completed, settings saved, actions confirmed | Checkmark circle |
| `error` | Operation failed, server error, validation failure | Danger/exclamation |
| `warning` | Destructive action confirmation, degraded state, rate limits | Alert triangle |
| `info` | Neutral information, status updates, tips | Info circle |

### Message Format

- **Short and specific**: `"Client updated successfully"` not `"The client record has been successfully updated in the database"`
- **Action + Result pattern**: `"[Thing] [action]ed successfully"` or `"Failed to [action] [thing]"`
- **No technical details**: Never expose error codes, stack traces, or internal state to the user
- **Sentence case**: Capitalise first word only (unless proper noun)
- **No trailing period**: Toast messages are brief labels, not sentences

#### Good Examples
```
Client updated successfully
Failed to delete contact
Form submitted
Session expired — please log in again
File uploaded (3 of 5)
```

#### Bad Examples
```
Success!                          // Too vague
Error: 500 Internal Server Error  // Technical leak
The operation completed.          // Unnecessary period, vague
CONTACT DELETED                   // Shouting
```

### Invocation Pattern

All notification calls must follow this structure, regardless of library:

```typescript
// notistack pattern (default recommendation)
const { enqueueSnackbar } = useSnackbar();

// Success
enqueueSnackbar('Client created successfully', { variant: 'success' });

// Error
enqueueSnackbar('Failed to create client', { variant: 'error' });

// Warning
enqueueSnackbar('This action cannot be undone', { variant: 'warning' });

// Info
enqueueSnackbar('Changes saved as draft', { variant: 'info' });
```

### Styling Requirements

Regardless of library, notification styling must:

- Use the project's theme typography (not hardcoded fonts/sizes)
- Use theme palette colours for variant backgrounds/icons
- Support light and dark mode
- Include custom icons per variant (not plain text)
- Use theme border radius and shadow tokens
- Have consistent padding: icon area + message + close button

### Architecture Requirements

- **Single provider**: One notification provider wrapping the entire app (inside `ThemeProvider`)
- **Re-export pattern**: Export the notification hook from a local `components/snackbar/index` (or equivalent) so the library can be swapped without touching every consumer
- **No direct library imports in feature code**: Features import from the project's wrapper, not directly from `notistack`/`react-hot-toast`/etc.

```typescript
// components/snackbar/index.ts (or equivalent)
export * from 'notistack'; // re-export everything
export { default as SnackbarProvider } from './snackbar-provider';
```

This pattern means swapping the underlying library only requires changing the wrapper — not every file that shows a notification.

## Step 4: Flag Violations

When violations are found:

1. **State the violation** with the specific rule
2. **Show the current code** that violates the pattern
3. **Show the fix** matching the project's established pattern

Example:
```
SNACKBAR: Inconsistent message format
Rule: Messages must follow "Action + Result" pattern (Snackbar Standards > Message Format)

Found: enqueueSnackbar('Success!', { variant: 'success' })
Fix:   enqueueSnackbar('Invoice created successfully', { variant: 'success' })
```

## Step 5: Proactive Enforcement

When writing code alongside the user, proactively:

- Add appropriate snackbar calls after CRUD operations (create, update, delete)
- Add error notifications in catch blocks for user-facing operations
- Use the correct variant for the context (don't default everything to `success`)
- Follow the project's existing import path for the notification hook
- Ensure new notification messages match the tone and format of existing ones

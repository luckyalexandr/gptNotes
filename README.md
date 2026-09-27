# Notes App — Project Documentation

**Version:** 1.0  
**Status:** Design Phase COMPLETE  
**Next phase:** Technical Design → Implementation

---

## 1. Purpose

Notes App is a simple cross-platform notes application focused on one thing: making the user's thoughts available independently of the device currently being used.

> **“My thoughts should not depend on what device is currently in my hands.”**

The application should feel like a normal notes app, not a document-management system or CMS.

The internal architecture may be sophisticated, but that complexity must not become unnecessary complexity for the user.

---

## 2. Product Principles

### 2.1 Core principle

> **The complexity of the product's internal architecture must never become unnecessary complexity for the user.**

### 2.2 Supporting principles

- First increase usefulness, then decorate.
- User convenience has priority over developer convenience.
- Standard platform behavior should be used instead of reinventing it.
- Do not add complexity without a clear user benefit.
- The application should have “soul”.
- A settled product decision should not be reopened without a real reason.
- The application is a notes app, not a CMS.

---

## 3. Platforms

The application is intended for:

- Android
- iOS
- macOS
- Windows
- Linux

**Implementation priority:** Android.

The product model and UX should remain cross-platform even when implementation begins on Android.

---

# 4. MVP Scope

## 4.1 Included in MVP

- Create and edit notes
- Autosave
- Offline work
- Automatic synchronization
- Search
- Text formatting
- Headings
- Bulleted lists
- Numbered lists
- Multilevel lists
- Checklists
- Links
- Underline
- Attachments
- Favorites
- Pinned notes
- Sharing
- Mobile reminders and notifications
- Google account as permanent storage
- Trash
- Basic appearance settings
- Localization
- Accessibility support
- Error handling and diagnostics

## 4.2 Explicitly excluded from MVP

- Folders
- Labels/tags
- Markdown
- Recurring reminders
- Reader/read-only mode
- Font selection inside the editor
- Manual font size inside the editor
- Text alignment
- Quotes
- Tables
- Code blocks
- Text drag & drop on mobile
- Guest/local-only mode
- Multiple accounts
- Collaboration
- Goals
- Export
- Advanced appearance customization
- External import
- Custom sharing system
- Manual synchronization ritual

The absence of a feature from MVP is intentional and should not automatically be treated as a missing requirement.

---

# 5. Note Model

The user sees a normal document.

The internal model is structured:

`Note → Blocks → Content`

A note consists of:

- optional title
- one or more content blocks
- optional attachments
- metadata such as favorite, pinned and reminder state

## 5.1 Block types

- Text
- Heading 1
- Heading 2
- Heading 3
- Bulleted list
- Numbered list
- Checklist

Multilevel lists are implemented through nesting/level information, not as a separate visual block type.

## 5.2 Inline formatting

Conceptually:

`TextBlock → content → Run → text + formatting`

Supported inline formatting:

- Bold
- Italic
- Underline
- Strikethrough
- Link

Runs are an internal implementation detail and are invisible to the user.

Adjacent runs with identical formatting should be merged where technically appropriate.

## 5.3 Empty note

A note is empty only when:

- it has no title, and
- it has no content.

An empty note is not saved.

No additional empty-state logic is required.

---

# 6. Editor

## 6.1 Creating a note

The Note List contains the main `+` action.

When the user creates a note:

- the cursor opens directly in the content area;
- the title is visible but optional;
- there is no `+` button inside the editor;
- there is no separate Save button;
- changes are autosaved.

`✓` finishes editing and closes the note.

There is no read-only mode.

## 6.2 Editor controls

Left:

- Back

Right:

- Undo
- Redo
- Delete
- Done/Save
- Formatting

The exact platform presentation may adapt to screen size and platform conventions.

## 6.3 Title

The title is optional.

If no title exists, the beginning of the content is used as the note preview.

---

# 7. Formatting

## 7.1 Inline

Supported:

- Bold
- Italic
- Underline
- Strikethrough
- Link

Formatting may be combined.

## 7.2 Selection behavior

Partial selection is supported, including selections spanning multiple lines/blocks.

Without a selection, formatting affects text entered at the current cursor position.

If the entire selection already has a formatting property, applying that property removes it.

If any part of the selection lacks the property, applying it applies the property to the whole selection.

Mixed formatting produces a neutral/mixed toolbar state.

## 7.3 Enter

Pressing Enter creates the next block without inheriting active inline formatting.

This prevents accidental continuation of formatting.

## 7.4 Undo

One formatting action is one logical undo operation.

Formatting has its own undo behavior but uses the same editor undo system.

## 7.5 Clear formatting

Clear formatting removes inline formatting.

It does not remove block structure.

## 7.6 Link

Links combine with other inline formatting.

The link dialog contains:

- selected text and/or text input
- URL

If no text is selected, the user can enter the text and URL.

## 7.7 Formatting menu

### INLINE
- Bold
- Italic
- Underline
- Strikethrough

### STRUCTURE
- Normal
- H1
- H2
- H3

### LISTS
- Bulleted
- Numbered
- Checklist
- Multilevel

### OTHER
- Link
- Clear formatting

Not included:

- font selection
- manual font size
- alignment
- quotes
- tables
- code blocks

---

# 8. Lists and Checklists

## 8.1 Lists

- Enter creates the next item at the same level.
- Enter on an empty item ends the current list level.
- Tab / Shift+Tab controls nesting on physical keyboards.
- Mobile-specific level controls may be added if needed during implementation.
- Applying a list to multiple ordinary blocks turns each into a list item.
- Switching between bulleted and numbered lists changes list type, not nesting.
- Numbering updates automatically.
- Backspace at the beginning of a nested item reduces its level.
- Backspace at the beginning of a top-level item converts it to normal text.
- Neighboring blocks may merge through normal editing behavior while preserving formatting.

## 8.2 Checklists

A checklist item has:

- checkbox
- editable text
- completion state

When checked:

- the text remains editable;
- strikethrough is applied automatically.

When unchecked:

- only the automatically applied completion strikethrough is removed.

Manual strikethrough remains independent.

A new item after a completed item starts unchecked.

The Note List preview shows the actual checklist content, not a progress counter such as `1/6 complete`.

---

# 9. Copy, Paste and Cut

The editor supports structured clipboard operations.

Copy should preserve supported:

- text
- formatting
- blocks
- lists
- nesting
- checklist state

The clipboard conceptually contains structured/rich data plus a plain-text fallback.

When pasting into Notes:

- supported structure and formatting are restored;
- unsupported external formatting is converted or discarded;
- plain-text paste contains no formatting.

Pasting over a selection replaces the selection.

Undoing a paste undoes the complete paste operation.

Undoing a cut restores the complete cut content and supported formatting.

Mobile MVP does not require text drag & drop.

Cursor position does not need to be restored across application sessions.

---

# 10. Note List

## 10.1 Ordering

Order:

1. Pinned notes
2. Other notes

Within each group, notes are ordered by their last meaningful modification.

Opening a note without editing it does not move it.

Changes consisting only of spaces or newlines do not move the note.

Real title/content changes move the note upward.

Pinned notes remain in the pinned section.

---

## 10.2 Cards

The visual concept is adaptive and inspired by the convenience of Google Keep without copying its interface.

Cards use:

- small but visible padding
- rounded corners
- slightly differentiated background
- light shadow
- subtle transparency/glass feel
- adaptive geometry

The layout must avoid visual chaos.

Typical layouts:

- portrait mobile: two columns
- landscape/tablet: adaptive number of columns

No fixed visual dimensions should be used where responsive behavior is more appropriate.

There is no lower action/info bar.

---

## 10.3 Card content

- Title: approximately one line
- Content: approximately 3–5 lines
- No title: beginning of content is shown
- Text: text preview + ellipsis
- Image-heavy note: images may dominate the preview
- File-heavy note: files may dominate the preview
- Maximum three image thumbnails
- Maximum two or three compact file items
- Remaining items: `…`

Content has priority over statistics.

Compact state indicators:

- favorite
- pinned
- reminder

---

# 11. Note Actions

## 11.1 Tap

Opens the note for editing.

The cursor opens at the end of the relevant content.

## 11.2 Swipe

Swipe left:

- Delete

Swipe right:

- Toggle favorite
- Show Snackbar with Undo

## 11.3 Long press

Long press enters multi-select mode.

Behavior:

- checkboxes appear;
- the long-pressed note becomes selected;
- the top action bar appears;
- selected notes can be pinned, favorited or deleted;
- Delete is placed at the far right;
- selection mode has a subtle active/shake visual state;
- selection mode ends after the operation.

There is no Move action because folders and labels are not part of MVP.

---

# 12. Deletion

Deleting a note is immediate from the user's perspective.

The note disappears immediately.

A Snackbar remains for approximately 10 seconds with Undo.

If Undo is not used, the note moves to Trash.

No confirmation dialog is required for normal note deletion.

---

# 13. Empty State

When there are no notes:

- show the donkey icon;
- show a short invitation;
- use a small but noticeable secondary message;
- localize the text;
- keep the presentation friendly but not childish.

The donkey is a quiet brand hero, not a children's mascot.

The FAB `+` remains available.

---

# 14. Navigation

## 14.1 Note List top bar

Left:

- hamburger menu `☰`
- becomes `×` while the menu is open

Center/brand area:

- donkey icon
- application name

Right:

- search

There is no back arrow on the Note List.

The editor has a back arrow.

## 14.2 Main menu

The menu:

- slides in from the left;
- occupies the full height;
- does not occupy the full width;
- dims the remaining screen.

MVP menu:

- All notes
- Favorites
- Pinned
- Settings
- Account
- About app
- Delete my data

No folders or labels.

---

# 15. Search

## 15.1 Global search

Search is opened from the Note List.

The search field/window appears near the top.

Animated appearance/disappearance is preferred where appropriate.

Search covers:

- titles
- note text
- checklist text
- file names

Search operates while the user types.

If there are no results, show a localized equivalent of:

> “По вашему запросу ничего не найдено”

Opening a result takes the user into the editor with the cursor at the end of the relevant match.

## 15.2 Search inside editor

Editor search is limited to the current note.

It searches:

- main note content
- textual checklist content

It does not search:

- the title
- other notes

The matching text is highlighted.

---

# 16. Favorites and Pinning

Favorite and pinned are independent states.

A note may be:

- neither
- favorite only
- pinned only
- both

They use different icons and different menu sections.

Pinned notes always remain at the top.

Both states use the same selection and list behavior.

Swipe right toggles favorite and provides Undo.

---

# 17. Reminders

Reminders are supported on mobile devices only.

They are not implemented on desktop platforms in MVP.

## 17.1 Reminder data

A reminder contains:

- date
- time
- sound/melody

It can be edited or deleted.

The note card displays a reminder indicator.

## 17.2 Notification

At reminder time:

- the system notification appears;
- the selected sound is used;
- the notification provides Done;
- the notification provides Snooze.

Snooze options:

- 5 minutes
- 10 minutes
- 15 minutes
- 30 minutes
- More → choose date/time

There are no recurring reminders.

A reminder is not an alarm clock.

Editing the note does not remove its reminder.

Deleting the note deletes its reminder.

Snooze changes only that reminder.

---

# 18. Trash

Deleted notes remain in Trash for **11 days**.

After 11 days they are permanently deleted.

Trash provides:

- list of deleted notes
- checkbox for each note
- Select all
- Restore
- Empty trash

Restore remains inactive until at least one note is selected.

Restoring a note preserves its previous state, including:

- pin
- favorite
- reminder
- other note metadata

If a restored reminder is already overdue, the reminder setup is shown again so the user can choose a new date/time/sound.

---

# 19. Attachments

Any file type can be attached.

## 19.1 Presentation

Photos:

- thumbnail preview

Other files:

- OS-like file item
- extension/type icon
- file name

Long names are truncated while keeping the extension visible.

Attachments appear at the position where they were inserted.

Attachments can be:

- deleted
- moved
- undone/redone

A quick tap opens the appropriate context menu.

## 19.2 Offline behavior

Attachments belonging to a locally available note workspace can be used while offline.

## 19.3 Deleting a note

Deleting a note deletes the copies of its attachments stored with the note.

It does not delete the original files/photos from the user's device.

---

# 20. Sharing

Sharing uses the standard operating-system Share mechanism.

No custom application list is required.

The shared content should provide:

1. Rich Text where supported
2. Plain Text fallback

Shared content includes:

- title
- text
- formatting
- lists
- checklists
- links
- files/images

Service states are not shared:

- favorite
- pinned
- reminder

External import is a future feature.

Markdown is not used.

---

# 21. Account and Storage

Google account is the permanent storage identity.

The exact Google storage technology is a technical decision and is intentionally not fixed in this document.

The device contains a temporary local offline workspace/cache.

There is no user-facing local-only mode.

## 21.1 First launch

Flow:

`Welcome → Войти через Google → Note List`

The user is informed:

> “Все данные будут храниться только в вашей учётной записи.”

After successful authentication, the Note List opens.

## 21.2 Normal launch

`Launch → authentication check → Note List`

## 21.3 Logout

Logout returns to the Google authorization screen.

Each account has its own local directory/workspace.

Logging out removes that account's local directory.

When another account is logged in, its data is downloaded/synchronized separately.

This structure also leaves room for future multiple-account support without making multiple accounts an MVP feature.

## 21.4 Uninstall

Normal OS uninstall removes local application data.

Cloud data remains.

After reinstalling and logging in again, synchronization restores the account's notes.

---

# 22. Delete My Data

The main menu contains **Delete my data**.

This is separate from Logout.

## 22.1 First warning

The user is told that the operation is irreversible.

The action asks:

> “Удалить все ваши заметки и связанные с ними данные из аккаунта Google?”

## 22.2 Second confirmation

A second confirmation asks:

> “Вы уверены, что хотите полностью удалить все заметки?”

Buttons:

- Отменить
- Подтвердить

The confirmation button is disabled for 10 seconds and displays a countdown.

After confirmation:

- all notes and related data are permanently deleted from the Google account;
- the user remains logged in;
- the note list becomes empty.

Delete my data does not log the user out.

---

# 23. Offline and Synchronization

The application supports offline work.

While offline, the user can:

- read locally available notes;
- create notes;
- edit notes;
- work with locally available attachments.

Changes are stored in the account-specific local workspace.

When connectivity returns, synchronization occurs automatically.

The user should not need to perform a manual sync ritual.

## 23.1 Sync network setting

The user can choose:

- Wi-Fi only
- Any network

A subtle synchronization indicator may be shown.

The application should not force the user into an offline screen merely because the network is unavailable.

---

# 24. Conflict Resolution

If the same note is modified independently on two devices before synchronization, the application does not guess which version is correct and does not silently merge them.

A dedicated conflict page is shown.

For each version:

- device name
- modification time
- content

The point where divergence begins is explicitly indicated.

Both versions are editable.

The page can scroll as needed.

Bottom actions remain visible:

- Save 1
- Save 2
- Save both variants

### Save 1 / Save 2

The selected version becomes the synchronized note.

### Save both variants

Two separate notes are created.

Their titles receive:

- device name
- modification time

No automatic conflict guessing is required.

---

# 25. Settings

Settings remain minimal and use familiar platform conventions.

## 25.1 Appearance

- Light
- Dark
- System
- Color palette
- Text color when supported by the selected palette
- Text size
- Font selection from fonts available on the device

Advanced appearance customization is deferred to a future paid tier.

## 25.2 Language

Language selection uses:

- dropdown
- Apply button

The language changes only after Apply.

Built-in languages:

- English
- German
- Spanish
- Portuguese
- Russian
- Ukrainian
- Arabic

First launch:

- use the system language if it is supported;
- otherwise use English.

Translations are embedded in the application.

UI text uses translation keys rather than hard-coded strings.

User note content is never translated automatically as part of localization.

## 25.3 RTL

> “Интерфейс поддерживает RTL автоматически средствами UI-фреймворка. Элементы, зависящие от направления интерфейса, зеркально перестраиваются; элементы, не зависящие от направления, не зеркалятся.”

## 25.4 Account

Account settings include:

- Google login/account information
- logout
- sync: Wi-Fi only / any network

## 25.5 Other

- Trash
- About app

There are no separate Notifications, Editor, or Data & Sync settings sections in MVP.

---

# 26. Accessibility

The application relies on the standard accessibility mechanisms of each platform.

General requirement:

> “Приложение использует стандартные средства доступности каждой платформы. Все собственные элементы интерфейса должны корректно работать с ними и предоставлять необходимую семантическую информацию.”

Requirements include:

- adaptive interface;
- increased system text;
- narrow screens;
- tablets;
- landscape;
- RTL;
- meaningful accessibility labels/semantics;
- icons must not be the only source of meaning;
- system should announce important state changes;
- checklist items expose name, type, state and ability to change state;
- editor uses platform accessibility mechanisms;
- no custom accessibility framework unless necessary;
- attachments have meaningful descriptions;
- files expose type and name;
- keyboard and alternative input methods work where appropriate for the platform.

## 26.1 Color and contrast

> “Цвет интерфейса адаптируется к выбранной пользователем палитре, при этом текст, иконки, системные состояния и сообщения должны сохранять достаточную визуальную различимость с фоном. Информация не должна передаваться только цветом — для состояний используются также иконки, текст или другие визуальные признаки.”

---

# 27. Permissions

The application uses standard OS permission mechanisms.

Permissions are requested only when the user first uses the feature that requires them.

Relevant permissions include:

- notifications
- camera
- microphone
- photos/files according to platform rules

If permission is denied:

- if the OS permits another request, use the standard request;
- if the OS no longer permits direct re-request, show a short explanation and provide a button to the application's system settings;
- where the exact permission page is unavailable, use the nearest appropriate application settings page.

No fake custom system permission dialogs are used.

Camera and microphone are planned application functionality; their detailed feature behavior is specified during implementation of those functions.

---

# 28. Error Handling and Diagnostics

Technical error handling and diagnostics are centralized.

## 28.1 User-facing errors

User-facing messages should be:

- short;
- understandable;
- free of unnecessary technical details;
- actionable where recovery is possible.

Where possible, provide:

- Retry
- Recovery
- Alternative action

## 28.2 Developer-facing diagnostics

Relevant diagnostic information may include:

- application version
- platform
- OS version
- time
- relevant technical context
- error type

Diagnostics must not automatically include note content or unnecessary personal data.

The concrete crash/error reporting service is a technical decision.

For errors that cannot be resolved by the user:

- report the problem clearly;
- optionally allow the user to add a short description.

---

# 29. Application Lifecycle

The application follows normal OS lifecycle behavior.

## 29.1 Background/minimize

Standard platform behavior applies.

Unused resources may be unloaded.

## 29.2 Local saving

Changes are continuously written to the local working copy.

Synchronization occurs automatically when possible.

## 29.3 Normal close

Where possible:

- available operations are completed;
- pending synchronization is continued;
- the application closes according to the platform.

Reminders must not depend on the application UI remaining open.

## 29.4 OS memory eviction

Memory eviction is normal OS behavior, not an application error.

Already locally saved changes remain.

After relaunch:

- local state is restored;
- synchronization resumes.

Force termination follows OS rules.

---

# 30. Organization

Folders are not part of MVP.

Labels/tags are not part of MVP.

Current organization is provided through:

- search
- pinned notes
- favorites
- colors
- reminders

The principle is:

> **“У нас заметки, а не CMS.”**

Labels/tags can be reconsidered later based on real usage.

---

# 31. Brand and Visual Character

The donkey is the quiet visual hero of the application.

It should communicate personality without making the application childish.

The donkey is particularly appropriate for:

- empty states
- application identity
- subtle brand elements

It should not turn the interface into a cartoon UI.

The overall visual direction is:

- calm
- useful
- adaptive
- slightly warm
- uncluttered

---

# 32. Future Features

The following are possible future directions:

- collaboration/shared editing
- Goals
- labels/tags
- multiple Google accounts
- export
- advanced appearance customization
- additional languages
- external import
- other useful features discovered through real usage

For example, additional languages may later be added through application resources/default language files.

Future features must not complicate the MVP without a clear user benefit.

---

# 33. Status Convention

The project uses four status categories.

### FIXED

A product decision has been made.

It should not be reopened without a concrete reason.

### DEFERRED

The feature or detail is intentionally postponed.

It may be addressed later without being treated as unfinished MVP work.

### TECHNICAL

The user-facing behavior is fixed, but the implementation detail is still to be selected.

### FUTURE

A possible post-MVP direction.

It is not part of the current implementation commitment.

---

# 34. Remaining Technical Decisions

The Design Phase is complete.

The following decisions remain for Technical Design:

- exact Google storage/synchronization technology;
- attachment storage mechanism;
- synchronization protocol and data model;
- concrete error/crash reporting service;
- platform-specific implementation details;
- exact technology stack;
- implementation architecture;
- local database/cache implementation;
- secure handling of authentication and stored data;
- background synchronization behavior within each platform's restrictions.

These are implementation questions, not reasons to reopen settled UX decisions.

---

# 35. Design Audit

The final design audit found no critical contradictions.

One cleanup was made:

Because folders and labels are not part of MVP, **Move** was removed from multi-select actions.

Current multi-select actions are:

- Pin
- Favorite
- Delete

The design is internally consistent enough to proceed to technical design.

---

# 36. Development Sequence

The project proceeds in this order:

**1. Design Phase**  
Completed.

**2. Project Documentation**  
This document.

**3. Technical Design**  
Define architecture and implementation technologies without changing settled UX decisions unnecessarily.

**4. Implementation**  
Begin with Android while keeping the product model cross-platform.

**5. Testing and refinement**  
Validate behavior against this documentation and real device usage.

**6. Release preparation**

---

# 37. Final Product Definition

Notes App is a cross-platform notes application built around a simple idea:

> **The user's thoughts should remain available regardless of the device in the user's hands.**

The application provides:

- simple notes;
- structured text;
- lists and checklists;
- attachments;
- search;
- favorites and pinning;
- reminders on mobile;
- offline work;
- automatic synchronization;
- Google account-backed permanent storage;
- standard platform sharing;
- accessibility and localization.

It deliberately avoids unnecessary organizational and editing complexity.

The internal architecture may grow as the product grows.

The user's experience should not have to.

---

## Design Status

**DESIGN PHASE: COMPLETE**

**CURRENT PHASE: PROJECT DOCUMENTATION**

**NEXT PHASE: TECHNICAL DESIGN**

**MVP PRODUCT DECISIONS: FIXED**

**UNRESOLVED ITEMS: TECHNICAL ONLY**

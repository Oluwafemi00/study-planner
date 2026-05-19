# Study Planner Pro – PWA Productivity App

**A fully offline, installable study planner that helps students build consistent habits and prioritize effectively.**

Built without frameworks—just vanilla JavaScript, modern CSS, and production-grade PWA patterns.

![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/css3-%231572B6.svg?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)
![PWA](https://img.shields.io/badge/PWA-5A0FC8.svg?style=for-the-badge&logo=pwa&logoColor=white)

**[🌍 Live Demo](https://oluwafemi00.github.io/study-planner-pro/)** | **[📱 Install as App](#installation)**

---

## The Problem It Solves

Students juggle multiple subjects, deadlines, and study sessions—but most task apps are either too complex (Notion, Todoist) or too basic (Apple Reminders).

**Study Planner Pro** is the middle ground:

- **Offline-first** → Works in libraries, trains, anywhere without WiFi
- **Priority-aware** → High-priority and overdue tasks automatically surface
- **Focus-built-in** → Pomodoro timer keeps sessions structured
- **Zero friction** → Installs like a native app, no account required

I built this because I needed it—and now it's the tool I actually use during exam prep.

---

## What Makes It Production-Ready

### 📴 True Offline-First PWA

Not just "works offline"—**designed** for offline:

- Service Worker caches all assets on first visit
- IndexedDB fallback for older browsers
- Install prompt on supported devices
- Works on iOS, Android, Windows, macOS

### 🎯 Smart Task Prioritization

Tasks aren't just lists—they're **intelligently sorted**:

- High-priority items float to the top automatically
- Overdue tasks get visual urgency indicators
- Completed tasks collapse out of the way

### 🔄 Drag-and-Drop Reordering

Built with the **HTML5 Drag-and-Drop API**:

- Smooth visual feedback during drag
- Persistent order (survives page reloads)
- Works on touch devices

### 🍅 Integrated Pomodoro Timer

No need to switch apps:

- 25-minute focus sessions
- Audio notification when time's up
- Tracks how many sessions you complete

### 💾 Persistent Data Without a Backend

Everything stored locally:

- Tasks saved in `localStorage`
- Theme preference remembered
- Settings persist across sessions
- No privacy concerns—data never leaves your device

### 🖨️ Print-Optimized Study Schedules

Generate clean PDFs:

- Custom CSS print rules
- No UI clutter in printouts
- Perfect for weekly study plans

---

## Screenshots

[Add screenshots: main task view, Pomodoro timer active, drag-and-drop demo, print preview, PWA install prompt]

---

## Tech Stack & Architecture

**Built Without Frameworks—Intentionally**

I wanted to master the fundamentals before reaching for React/Vue, so this uses:

- **Vanilla JavaScript (ES6+)**: Classes, modules, async/await
- **HTML5 APIs**: Drag-and-Drop, LocalStorage, Notifications
- **Modern CSS**: Grid, Flexbox, CSS Variables for theming
- **PWA APIs**: Service Workers, Web App Manifest, Cache API

---

## Architecture Decisions

### Single Source of Truth Pattern

```javascript
let tasks = JSON.parse(localStorage.getItem("tasks")) || [];

function updateTask(id, updates) {
  tasks = tasks.map((task) =>
    task.id === id ? { ...task, ...updates } : task,
  );
  saveAndRender();
}

function saveAndRender() {
  localStorage.setItem("tasks", JSON.stringify(tasks));
  renderTasks();
}
```

Every state change flows through one function—no accidental stale data.

### Event Delegation for Performance

```javascript
// Instead of adding listeners to each task button:
taskList.addEventListener("click", (e) => {
  if (e.target.classList.contains("delete-btn")) {
    e.stopPropagation();
    deleteTask(e.target.dataset.id);
  }
});
```

Scales to hundreds of tasks without memory leaks.

### CSS Variable Theming

```css
:root {
  --bg-primary: #ffffff;
  --text-primary: #1a1a1a;
}

body.dark-mode {
  --bg-primary: #1a1a1a;
  --text-primary: #ffffff;
}
```

One class toggle switches the entire theme—no inline styles.

---

## The Technical Challenges

### 1. Service Worker Cache Invalidation

**Problem:** Updates to the app wouldn't show because old files were cached.  
**Solution:** Implemented versioned cache keys and cache-busting on activation:

```javascript
const CACHE_VERSION = "v1.2.0";
self.addEventListener("activate", (event) => {
  event.waitUntil(
    caches
      .keys()
      .then((keys) =>
        Promise.all(
          keys
            .filter((key) => key !== CACHE_VERSION)
            .map((key) => caches.delete(key)),
        ),
      ),
  );
});
```

### 2. Drag-and-Drop on Touch Devices

**Problem:** HTML5 Drag-and-Drop API doesn't work on mobile by default.  
**Solution:** Added touch event polyfill for mobile compatibility.

### 3. Priority Sorting Logic

**Problem:** Need to sort by priority AND due date without breaking manual order.  
**Solution:** Multi-criteria sort with user-defined order as tiebreaker:

```javascript
tasks.sort((a, b) => {
  if (a.isOverdue !== b.isOverdue) return a.isOverdue ? -1 : 1;
  if (a.priority !== b.priority) return b.priority - a.priority;
  return a.order - b.order; // Preserve manual arrangement
});
```

---

## What I Learned Building This

### PWA Development

- Service Worker lifecycle (install → activate → fetch)
- Cache strategies (Cache First vs Network First)
- Web App Manifest configuration for installability
- iOS Safari PWA quirks and limitations

### State Management in Vanilla JS

- Building a reactive system without frameworks
- Immutable update patterns for data integrity
- Performance optimization (debouncing, event delegation)

### Accessibility

- Keyboard navigation for drag-and-drop
- ARIA labels for screen readers
- Focus management in modals

### UX Patterns

- Optimistic UI updates (instant feedback before save)
- Loading states for async operations
- Empty states that guide users

---

## Real-World Impact

This isn't a toy project—I built it to solve my own problem during university:

**Before Study Planner Pro:**

- Scattered tasks across Notes app, Google Keep, paper
- Forgot about assignments until the night before
- No structure to study sessions

**After:**

- All tasks in one place, prioritized automatically
- Pomodoro timer keeps me focused
- Works offline in the library (crucial!)
- Installed on my phone and laptop—always accessible

**Now:** I use it daily. Friends started using it. That's the metric that matters.

---

## Installation

### As a Web App (Recommended)

1. Visit [https://oluwafemi00.github.io/study-planner-pro/](https://oluwafemi00.github.io/study-planner-pro/)
2. Look for the install prompt (browser-dependent):
   - **Chrome/Edge:** Address bar → Install icon
   - **Safari iOS:** Share → Add to Home Screen
   - **Android:** Banner appears automatically

### Local Development

```bash
# Clone the repository
git clone https://github.com/oluwafemi00/study-planner-pro.git

# Navigate to directory
cd study-planner-pro

# Open in browser
open index.html

# OR serve with a local server (for Service Worker testing)
npx serve .
```

**No build step. No npm install. Just open and run.**

---

## Project Evolution

**v1.0** → Basic task list with localStorage  
**v1.5** → Added priority sorting and search  
**v2.0** → PWA conversion (Service Worker + Manifest)  
**v2.5** → Drag-and-drop reordering  
**v3.0** → Pomodoro timer integration  
**Current** → Print optimization + accessibility improvements

Each version solved a real pain point I experienced using the app.

---

## Future Enhancements

- [ ] Multi-device sync (PouchDB + CouchDB)
- [ ] Study session analytics (time spent per subject)
- [ ] Recurring tasks (daily/weekly assignments)
- [ ] Export tasks as JSON/CSV
- [ ] Collaboration mode (shared study plans)
- [ ] Mobile app versions (React Native wrapper)

---

## Browser Support

| Feature        | Chrome | Firefox | Safari      | Edge |
| -------------- | ------ | ------- | ----------- | ---- |
| Core App       | ✅     | ✅      | ✅          | ✅   |
| PWA Install    | ✅     | ✅      | ⚠️ iOS only | ✅   |
| Service Worker | ✅     | ✅      | ✅          | ✅   |
| Drag-and-Drop  | ✅     | ✅      | ✅          | ✅   |

⚠️ _Safari iOS requires "Add to Home Screen" instead of native install prompt_

---

## Why Vanilla JavaScript?

**"Why didn't you use React?"**

This was a deliberate learning choice:

- **Master the fundamentals** before abstractions
- **Understand what frameworks solve** by solving it manually
- **No build complexity** → faster iteration
- **Smaller bundle size** → better performance
- **Full control** → every line is mine to debug

I'm building React apps now (check my other repos), but this project taught me _why_ frameworks exist.

---

## Author

**Femi Sodiq**  
Frontend Engineer | Building tools that solve real problems, not just portfolio pieces  
[LinkedIn](#) | [Portfolio](#) | [More Projects](https://github.com/oluwafemi00)

---

## License

MIT License - Fork it, use it, learn from it.

---

**⭐ If this app helped you stay organized or taught you PWA patterns, star the repo!**

**💼 Hiring managers:** This project demonstrates PWA development, vanilla JS mastery, state management, offline-first architecture, and shipping real products that solve user problems.

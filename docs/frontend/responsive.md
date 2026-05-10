# Responsive Design#

**Mobile-First Approach** — 320px → 4K display support.

## Overview#

CosmicSec frontend adapts to all screen sizes with responsive design.

## Breakpoints#

- **Mobile**: 320px — 767px#
- **Tablet**: 768px — 1023px#
- **Desktop**: 1024px — 1919px#
- **Wide**: 1920px+#
- **4K**: 3840px+#

## Responsive Features#

- **Mobile navigation** — bottom nav bar#
- **Adaptive layouts** — reflow for screen size#
- **Touch-friendly** — larger touch targets on mobile#
- **Progressive disclosure** — show/hide based on screen size#
- **Responsive tables** — horizontal scroll on small screens#

## PWA Support#

- **Installable** — add to home screen#
- **Offline support** — service worker caching#
- **Push notifications** — even when app is closed#
- **Background sync** — sync when connection returns#

## Testing Responsiveness#

```bash#
# Start dev server#
npm run dev#

# Test with Chrome DevTools#
# Toggle device toolbar: Ctrl+Shift+M#
# Test various screen sizes#
```

## Mobile-Specific Features#

- **Swipe actions** — swipe to reveal actions#
- **Pull to refresh** — refresh content#
- **Bottom sheet** — mobile-friendly modals#
- **Safe area** — respect notch/island areas#

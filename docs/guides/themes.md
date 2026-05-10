# Theme Customization Guide#

**Customizing Glassmorphism UI.**

## Overview#

Learn how to customize CosmicSec's glassmorphism design system.

## Available Themes#

- **Cosmic Dark** — default dark theme#
- **Midnight Blue** — blue-tinted dark#
- **Neon Nights** — vibrant neon accents#
- **Emerald Forest** — green-tinted dark#

## Using Theme Builder#

Access the Theme Builder at `/theme-builder` to:
- Adjust blur intensity (10px-20px)#
- Change backdrop opacity#
- Customize gradient colors#
- Preview changes in real-time#

## Creating Custom Themes#

```typescript#
import { ThemeProvider, ThemeBuilder } from '@cosmicsec/ui';#

const myTheme = ThemeBuilder()
  .setPrimary('#6366f1')
  .setBlur(15)
  .build();
```

## WCAG 2.1 AA Compliance#

All themes meet accessibility standards:
- Contrast ratio ≥ 4.5:1 for normal text#
- Contrast ratio ≥ 3:1 for large text#
- Keyboard navigation support#
- Screen reader compatibility#

# WMO Weather Accent Palette

Session-derived palette used in `WeatherForecastDrawer.tsx` for city summary/header accents.

## Mapping

| Condition group | WMO codes / match | Accent |
|---|---|---|
| Thunder | `95, 96, 99` or `desc.includes('thunder')` | `#a855f7` |
| Snow / sleet | `71, 73, 75, 77, 85, 86` or `snow` / `sleet` | `#38bdf8` |
| Drizzle | `51, 53, 55, 56, 57` or `drizzle` | `#93c5fd` |
| Rain / shower | `61, 63, 65, 66, 67, 80, 81, 82` or `rain` / `shower` | `#60a5fa` |
| Fog / haze | `45, 48` or `fog` / `mist` / `haze` | `#94a3b8` |
| Overcast / cloud | `3` or `cloud` / `overcast` | `#64748b` |
| Partly cloudy | `1, 2` | `#fb923c` |
| Clear fallback | everything else | `#fbbf24` |

## Usage notes

- City name + temperature can use accent directly.
- Weather description should mix accent with white for readability:

```tsx
style={{ color: `color-mix(in srgb, ${accentColor} 70%, white)` }}
```

- Liquid/stat card borders can use alpha-suffixed accent:

```tsx
style={{ borderColor: `${accentColor}2E` }}
```

`2E` hex alpha ~= 18% opacity.

## Trigger

Use this palette when user asks for:
- more meaning in weather UI
- condition-aware color for city summary
- weather-specific accent instead of generic temperature tint

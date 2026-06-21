---
name: weather
description: Weather UI patterns for AIOZ World Deck — WMO accent palettes, forecast display, liquid drawer integration, weather-condition-based color mapping.
---

# Weather UI

Patterns for weather-condition-aware UI in the AIOZ World Deck project. Used by `WeatherCityMarkersLayer`, `WeatherForecastDrawer`, `WeatherForecastPopup`, and any future weather widgets.

## WMO Weather-Code Accent Palette

File: `references/wmo-accent-palette.md`

Maps WMO weather codes (0–99) to accent hex colors for weather-condition-based theming. Reuse `getWeatherAccentColor(weatherCode, weatherDesc)` whenever a weather widget needs dynamic accent color.

```
clear → amber:      #fbbf24
partly cloudy → orange: #fb923c
overcast → slate:   #64748b
fog → gray:         #94a3b8
drizzle → soft blue:   #93c5fd
rain → blue:        #60a5fa
snow → ice blue:    #38bdf8
thunder → purple:   #a855f7
```

## Applying Weather Accent in LiquidGlass Components

This project uses `LiquidContainer` and `LiquidGlass` for glass-effect cards. Apply weather accent via inline `borderColor`:

```tsx
style={{ borderColor: `${accentColor}2E` }}  // 18% alpha
```

For text legibility over mixed backgrounds, use `color-mix`:

```tsx
style={{ color: `color-mix(in srgb, ${accentColor} 70%, white)` }}
```

## Available Data Fields

`WeatherCity` type fields commonly used in header stats:
- `feelsLike`, `humidity`, `visibility`, `windSpeed`, `windDirection`
- `pressure`, `uvIndex`
- `weatherCode`, `weatherDesc` – used for accent color and emoji

`WeatherForecastDay` fields for daily rows:
- `precipitationProbabilityMax`, `precipitationSum`
- `sunshineDuration`, `uvIndexMax`
- `windSpeedMax`, `windDirectionDominant`
- `temperatureMax`, `temperatureMin`
- `weatherCode`, `weatherDesc`

## Dependency Array Pattern for Weather Memos

Group weather-dependent `useMemo` dependencies by `city?.weatherCode` + `city?.weatherDesc`:

```tsx
const weatherAccent = useMemo(
  () => getWeatherAccentColor(city?.weatherCode ?? 0, city?.weatherDesc ?? ''),
  [city?.weatherCode, city?.weatherDesc],
);
```

## Stat Chips Layout

6-item grid in `grid-cols-3` gives 2 rows of 3. Each chip: icon + label + value. Use `backdrop-blur-md` + `bg-slate-950/28` for glass base, weather accent for border.

## Summary Insight Cards

3-card row from forecast extremes:
- Rain risk (highest precip probability)
- Longest sun (max sunshine duration)
- Peak wind (max wind speed)
- Each shows value + weekday detail.

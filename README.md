# @bobberz/bottom-sheet

[![npm](https://img.shields.io/npm/v/@bobberz/bottom-sheet)](https://www.npmjs.com/package/@bobberz/bottom-sheet)

`@bobberz/bottom-sheet` is a React Native component for performant bottom sheets where:

- snap points accept pixels, percentages, `"content"` sizing, and measured anchors
- detached sheets can morph into fullscreen with live corner-radius interpolation
- dismissal rubber-bands at the floor instead of freezing when `dismissible={false}`
- `collapsedHeight` adds a persistent peek state for maps-style attached sheets
- scrollable content hands off between sheet drag and inner scroll automatically
- the backdrop can be configured to pass through touches or close on tap

If you want a shorter machine-oriented summary, see [`llms.txt`](./llms.txt).

## What It Does

`BottomSheet` is a flexible sheet container that slides up from the bottom of the screen.

The component handles:

- spring-based snap animation
- multi-stop snap points
- horizontal gesture rejection
- backdrop opacity interpolation
- rubber-band overscroll at top and bottom edges
- detached floating card mode
- fullscreen expansion with corner morph
- scroll-to-drag handoff for inner scrollable content
- anchor-based snap points measured from child layout

## Core Behavior

These are the important behavior rules:

1. The sheet snaps to the nearest snap point on release, projected by velocity.
2. Swiping down past the floor dismisses when `dismissible` is true.
3. When `dismissible={false}`, swiping below the floor rubber-bands instead of dismissing.
4. Backdrop opacity scales between floor and ceiling heights. Below the floor it fades toward zero.
5. The backdrop blocks touches when `backdropPressBehavior="close"` and passes them through with `"none"`.
6. Anchor snap points are measured from child layout using `BottomSheetAnchor` marker views.
7. `BottomSheetScrollView` enables scroll-to-drag handoff: the sheet drags until it reaches the ceiling, then inner scrolling takes over.
8. Detached sheets float above the bottom edge with configurable padding and can morph into fullscreen when `allowFullScreen` is set.

## Installation

Install the package and make sure your app already has the required gesture/animation setup.

```bash
npm install @bobberz/bottom-sheet react-native-gesture-handler react-native-reanimated react-native-safe-area-context
```

Your app must have:

- `react-native-gesture-handler`
- `react-native-reanimated`
- `react-native-safe-area-context`
- the normal configuration required by those libraries for your React Native or Expo setup

## Quick Start

```tsx
import { BottomSheet } from "@bobberz/bottom-sheet";

export function Example() {
  return (
    <BottomSheet
      snapPoints={["content", "72%"]}
      allowFullScreen
      initialSnapIndex={0}
    >
      <YourContent />
    </BottomSheet>
  );
}
```

## Snap Point Types

Snap points define the heights the sheet can rest at. Pass them as `snapPoints`.

### Pixel values

A number is treated as an absolute height in points.

```tsx
snapPoints={[220, 440]}
```

### Percentage values

A string like `"56%"` is a percentage of the available height (viewport minus top inset).

```tsx
snapPoints={["32%", "56%", "84%"]}
```

### Content sizing

The string `"content"` measures the sheet content's natural height and uses it as a snap point.

```tsx
snapPoints={["content", "72%"]}
```

### Anchor snap points

Anchor snap points are measured from `BottomSheetAnchor` marker views placed inside the sheet content. Each anchor produces a snap height equal to the anchor's bottom edge plus an optional offset.

```tsx
import { createBottomSheetAnchor, BottomSheetAnchor } from "@bobberz/bottom-sheet";

const summaryAnchor = createBottomSheetAnchor("summary", { offset: 18 });
const detailAnchor = createBottomSheetAnchor("detail", { offset: 18 });

<BottomSheet snapPoints={[summaryAnchor, detailAnchor]} allowFullScreen>
  <BottomSheetAnchor name="summary">
    <SummarySection />
  </BottomSheetAnchor>
  <BottomSheetAnchor name="detail">
    <DetailSection />
  </BottomSheetAnchor>
</BottomSheet>
```

The `name` on `BottomSheetAnchor` must match the `key` passed to `createBottomSheetAnchor`.

### Fullscreen ceiling

When `allowFullScreen` is true, the sheet adds the full available height as an extra snap point above all configured stops.

## Props Reference

### `snapPoints`

Array of snap point definitions. Accepts pixels, percentages, `"content"`, and anchor points. Default: `["content"]`.

### `initialSnapIndex`

Default: `0`

Which snap point to open at when the sheet first presents.

### `open`

Controlled open state. When provided, the sheet is controlled and you must update this value in response to `onOpenChange`.

### `defaultOpen`

Default: `true`

Initial open state for uncontrolled usage.

### `allowFullScreen`

Default: `false`

Adds the full available height as an additional snap point above the highest configured stop.

### `dismissible`

Default: `true`

When true, swiping below the floor or tapping the backdrop dismisses the sheet. When false, the sheet rubber-bands at its lowest snap point.

### `collapsedHeight`

A snap point definition that becomes the sheet's persistent floor. The sheet cannot be dismissed below this height. Useful for maps-style peek states.

### `detached`

Default: `false`

Floats the sheet above the bottom edge with padding. Renders rounded corners on all four sides.

### `detachedPadding`

Padding around a detached sheet. Accepts a number (uniform) or an object with `bottom`, `horizontal`, `left`, `right`, `top`, and `vertical` fields.

### `cornerRadius`

Default: `28`

Border radius for the sheet's top corners (all four corners when detached).

### `fullScreenCornerRadius`

Corner radius when the sheet is at fullscreen height. Defaults to `cornerRadius` for attached sheets and `0` for detached sheets. The radius interpolates live during drag.

### `dragRegion`

Default: `"sheet"`

- `"sheet"`: the entire sheet surface is draggable.
- `"handle"`: only the handle area responds to drag. Use with `BottomSheetScrollView` for scroll-to-drag handoff.

### `backdropOpacity`

Default: `0.34`

Maximum backdrop opacity at the highest snap point. Set to `0` to hide the backdrop.

### `backdropPressBehavior`

Default: `"close"`

- `"close"`: tapping the backdrop dismisses the sheet.
- `"none"`: the backdrop is visible but passes touches through to the content behind it.

### `backdropColor`

Default: `"#000000"`

### `backdropStyle`

Additional style applied to the backdrop pressable.

### `handleVisible`

Default: `true`

Shows or hides the drag handle indicator.

### `handleColor`

Default: `"rgba(255, 255, 255, 0.42)"`

### `handleStyle`

Additional style applied to the handle area.

### `sheetStyle`

Style applied to the sheet container. Use for background color.

### `contentContainerStyle`

Style applied to the content wrapper inside the sheet.

### `applyContentInset`

Default: `true`

When true, adds bottom safe-area padding to the content container.

### `contentBottomInset`

Default: `0`

Extra bottom inset added to the content container, on top of the safe-area inset.

### `topInset`

Default: `0`

Top inset that limits the sheet's maximum height. When `allowFullScreen` is true, the larger of `topInset` and the safe-area top is used.

### `style`

Style applied to the root overlay container.

### `onOpenChange`

`(open: boolean) => void`

Called when the sheet's open state changes. Required for controlled usage.

### `onDismiss`

`() => void`

Called when the sheet finishes dismissing.

### `onSnapChange`

`(index: number, height: number) => void`

Called when the sheet settles at a snap point. Receives the snap index and the resolved height in points.

## Ref Methods

Access imperative methods via a ref.

```tsx
const sheetRef = useRef<BottomSheetRef>(null);

<BottomSheet ref={sheetRef} ...>

sheetRef.current?.present();
sheetRef.current?.dismiss();
sheetRef.current?.expand();
sheetRef.current?.snapToIndex(1);
```

### `present()`

Opens the sheet at `initialSnapIndex`.

### `dismiss()`

Closes the sheet with a spring animation.

### `expand()`

Snaps to the highest configured snap point.

### `snapToIndex(index)`

Snaps to the snap point at the given index.

## Scrollable Content

Use `BottomSheetScrollView` for scrollable content inside the sheet.

When `dragRegion="sheet"`, the scroll view coordinates with the sheet gesture:

- While the sheet is below its ceiling, dragging moves the sheet.
- Once the sheet reaches its ceiling, inner scrolling activates.
- Pulling down from scroll offset zero hands the gesture back to the sheet.

```tsx
import { BottomSheet, BottomSheetScrollView, useBottomSheetInsets } from "@bobberz/bottom-sheet";

function SheetContent() {
  const insets = useBottomSheetInsets();

  return (
    <BottomSheetScrollView
      contentContainerStyle={{ paddingBottom: insets.bottom + 20 }}
    >
      <LongContent />
    </BottomSheetScrollView>
  );
}

<BottomSheet
  snapPoints={["48%", "82%"]}
  allowFullScreen
  dragRegion="sheet"
  applyContentInset={false}
>
  <SheetContent />
</BottomSheet>
```

### `useBottomSheetInsets()`

Returns `{ bottom: number }` representing the content bottom inset (safe area + `contentBottomInset`). Use this to add padding inside `BottomSheetScrollView`.

## Detached Mode

Detached sheets float above the bottom edge, rounded on all four corners.

```tsx
<BottomSheet
  detached
  detachedPadding={{ bottom: 16, horizontal: 16 }}
  snapPoints={["46%"]}
>
  <CardContent />
</BottomSheet>
```

When combined with `allowFullScreen`, the sheet morphs from a floating card to fullscreen. The corner radius, margins, and shadow interpolate smoothly during the transition.

## Non-Dismissible Sheets

```tsx
<BottomSheet
  dismissible={false}
  snapPoints={[220, "56%"]}
>
  <Content />
</BottomSheet>
```

The sheet rubber-bands at its lowest snap point instead of dismissing. Backdrop tap is ignored.

## Collapsed Peek

```tsx
<BottomSheet
  collapsedHeight={136}
  dismissible={false}
  backdropOpacity={0}
  backdropPressBehavior="none"
  snapPoints={["48%", "84%"]}
>
  <PeekContent />
</BottomSheet>
```

`collapsedHeight` is prepended to the snap points as the floor. Combined with `dismissible={false}`, this creates a persistent peek that the user can swipe up to expand.

## Public Exports

The package exports:

- `BottomSheet`
- `BottomSheetAnchor`
- `BottomSheetScrollView`
- `createBottomSheetAnchor`
- `useBottomSheetInsets`
- `BottomSheetAnchorPoint`
- `BottomSheetAnchorProps`
- `BottomSheetDetachedPadding`
- `BottomSheetInsets`
- `BottomSheetProps`
- `BottomSheetRef`
- `BottomSheetSnapPoint`
- `BottomSheetScrollViewProps`

## Performance Notes

The library is designed around performance first.

It does the following:

- keeps gesture math on the UI thread with Reanimated worklets
- uses spring animations with velocity projection for natural snapping
- measures anchor positions in an off-screen container to avoid layout thrashing
- debounces anchor registration to batch rapid layout changes

For best results:

- keep children stable across renders
- use `sheetStyle` for background color instead of wrapping in extra views
- prefer pixel or percentage snap points over anchors when the heights are known ahead of time
- set `applyContentInset={false}` when using `BottomSheetScrollView` and handle padding manually via `useBottomSheetInsets`

## Local Demo

The repo includes a working Expo demo app in `example/`.

It covers ten scenarios: dynamic content, fixed height, percentage snaps, anchor snaps, detached cards, detached-to-fullscreen morphing, non-dismissible sheets, collapsible peek, multi-stop workflow snaps, and scrollable fullscreen.

From this package directory:

```bash
npm install
npm run example:install
npm run example:start
```

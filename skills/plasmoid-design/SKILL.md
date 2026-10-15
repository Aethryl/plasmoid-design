---
name: plasmoid-design
description: Use when building, restyling or reviewing a KDE Plasma 6 widget (plasmoid) or a Kirigami/QML app window, or when its sizing, scaling, spacing or layout looks off.
---

# Plasmoid design

Rules for Plasma 6 panel widgets, their popups, and Kirigami/QML windows, drawn from the KDE Human Interface Guidelines, the Kirigami and Plasma widget docs and Qt Quick Layouts. Worked before/after pairs are in `references/examples.md`; every rule's source is in `references/sources.md`. For generic QML correctness, also apply Qt's `qt-qml` skill when it is installed.

## Work in this order

1. Decide the shape first: compact icon or text in the panel, a popup for quick actions, a real window for anything with lists, grids, forms or editing.
2. Lay out with layouts and implicit sizes, then set spacing from units, then colour from the theme. Never start from pixel positions.
3. Look at the result. Take a screenshot at the narrowest and widest size the window allows, in Breeze Light and Dark, before calling it done.

## Units and scaling

- Size everything in `Kirigami.Units.gridUnit` multiples. It follows the user's font size, so the UI scales with it. A literal pixel width is a bug unless it is a 1px hairline.
- Spacing: `smallSpacing` between items that belong together, `largeSpacing` between groups and between a window edge and framed content, `mediumSpacing` between toolbar items. A frameless scroll view or list touches the window edge with zero margin.
- Icons come from `Kirigami.Units.iconSizes`: `small` in menus and raised buttons, `smallMedium` in toolbars and single-line list items, `medium` for list items with a subtitle, `sizeForLabels` to match text height.
- Radii use `Kirigami.Units.cornerRadius`; durations use `shortDuration` and `longDuration`, so a user who disables animations gets none. Treat a duration of 0 as "no motion" and show a static state instead.
- Qt already applies display scaling, including fractional factors. Never multiply by `devicePixelRatio` or `Screen.devicePixelRatio`, and never compensate for 125% or 150% by hand.
- Images get a `sourceSize` near their displayed size and `asynchronous: true`; icons are theme icon names or SVG, not bitmaps.
- `PlasmaCore.Units` no longer exists in Plasma 6. Import `org.kde.kirigami as Kirigami` and use `Kirigami.Units`.

## Layouts

- A child of `RowLayout`, `ColumnLayout` or `GridLayout` is sized only with `Layout.*`: `fillWidth`, `preferredWidth`, `minimumWidth`, `maximumWidth`, and the height equivalents. Setting `width` or `height` on it is ignored or fights the layout.
- Never put `anchors` and `Layout.*` on the same item. Only the outermost layout may use `anchors.fill: parent`.
- Every custom item reports `implicitWidth` and `implicitHeight`, usually from its inner layout. A zero implicit size makes the layout guess.
- Never bind an implicit or preferred size to the layout's own width or height; that is a binding loop.
- Set `spacing` explicitly on every layout; the Qt default of 5px ignores the user's scale.
- Use `RowLayout` and `ColumnLayout` for anything that resizes. `Row` and `Column` are for fixed arrangements only.
- Long text either wraps (`wrapMode`) or elides (`elide: Text.ElideRight`) with `Layout.fillWidth: true`. Text that can push its container wider is a bug.
- A grid of thumbnails computes its column count from the available width and a target cell width in grid units, then stretches cells to fill the row. Do not hardcode a column count or a cell width in pixels.

## Windows

- A window must work narrow, tiled to half a screen, and maximised. Switch structure at a width breakpoint (for example a sidebar collapses into a drawer or a dropdown) instead of shrinking everything.
- Navigation: up to five destinations use tabs or a sidebar list; more go in a `Kirigami.GlobalDrawer`. Disable actions that do not apply; do not hide them.
- Lists for text-heavy content, grids for visual content, tables only for three or more data columns.
- `Kirigami.OverlaySheet` is for read-only content. Anything that takes input uses `Kirigami.Dialog` or an inline panel.
- Give the window a sensible minimum size in grid units so its layout can never collapse into overlapping controls.
- Toolbars: put the primary action first, group related actions, and let `Kirigami.ActionToolBar` move overflow into a menu rather than squeezing buttons.
- Settings and forms use `Kirigami.FormLayout`.

## Plasma widgets

- The root is `PlasmoidItem`. `compactRepresentation`, `fullRepresentation`, `preferredRepresentation`, `switchWidth` and `switchHeight` are properties of the root.
- The popup's size comes from `Layout.preferredWidth`, `Layout.preferredHeight` and the minimums on the full representation, in grid units.
- The compact representation sizes from the panel thickness and `Kirigami.Units.iconSizes`, never from fixed numbers.
- For a widget that needs a real application window, run it with `plasmawindowed <plugin-id>`. That instance has `Plasmoid.formFactor === PlasmaCore.Types.Planar`. Keep timers and side effects in the panel instance only, so two instances never both act.
- Only use properties that exist. Kirigami.Theme has `textColor`, `disabledTextColor`, `highlightColor`, `highlightedTextColor`, `backgroundColor`, `alternateBackgroundColor`, `linkColor`, `visitedLinkColor`, `activeTextColor`, `positiveTextColor`, `neutralTextColor`, `negativeTextColor`, and the matching `*BackgroundColor` variants. An unknown property silently becomes `undefined`.

## Colour and text

- Colours come only from `Kirigami.Theme`, picked with the right `colorSet` (`View` for content, `Window` for chrome, `Header` for headers). No hex literals, so light and dark both work.
- Headings use `Kirigami.Heading` with a `level`; body text uses `QQC2.Label` or `PlasmaComponents.Label`.
- Help text sits under its control, visible, not only in a tooltip. Controls are always visible, not only on hover.

## Review checklist

Before reporting a Plasma or Kirigami UI as finished, check each point and fix what fails:

- No pixel literals except 1px lines; all sizes in grid units, all gaps in spacing units.
- No `anchors` on layout children, no `width`/`height` on layout children, every custom item has an implicit size.
- Every layout sets `spacing`; long text wraps or elides.
- The window has a minimum size and still works at it; grids reflow by width.
- No hex colours; every theme property used exists.
- Screenshots taken at minimum and large size, light and dark, and looked at.

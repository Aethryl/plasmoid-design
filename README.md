# plasmoid-design

A Claude Code skill for building and reviewing KDE Plasma 6 widgets (plasmoids) and Kirigami/QML app windows. It covers sizing in grid units, spacing, icon sizes, Qt Quick Layouts rules, responsive windows, popup sizing, theme colours, and a review checklist, all based on the KDE HIG, the Kirigami and Plasma docs, and Qt's layout documentation.

It complements Qt's own [`qt-qml` skill](https://github.com/TheQtCompanyRnD/agent-skills/tree/main/skills/qt-qml), which covers generic QML correctness.

## Install

```sh
claude plugin marketplace add Aethryl/plasmoid-design
claude plugin install plasmoid-design@plasmoid-design
```

## Contents

- `skills/plasmoid-design/SKILL.md`: the rules and the review checklist
- `skills/plasmoid-design/references/examples.md`: before/after QML pairs
- `skills/plasmoid-design/references/sources.md`: where each rule comes from

## Licence

MIT

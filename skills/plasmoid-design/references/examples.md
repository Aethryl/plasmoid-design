# Before/after examples

## Pixel sizes → grid units

```qml
// before
Rectangle { width: 320; height: 40; radius: 6 }

// after
Rectangle {
    implicitWidth: Kirigami.Units.gridUnit * 18
    implicitHeight: Kirigami.Units.gridUnit * 2
    radius: Kirigami.Units.cornerRadius
}
```

## Width on a layout child → Layout properties

```qml
// before
RowLayout {
    TextField { width: 200 }
    Button { anchors.right: parent.right; text: "Apply" }
}

// after
RowLayout {
    spacing: Kirigami.Units.smallSpacing
    QQC2.TextField { Layout.fillWidth: true; Layout.minimumWidth: Kirigami.Units.gridUnit * 8 }
    QQC2.Button { text: i18n("Apply") }
}
```

## Custom item without implicit size → size from its content

```qml
// before
Item {
    Image { anchors.fill: parent }
}

// after
Item {
    implicitWidth: Kirigami.Units.gridUnit * 12
    implicitHeight: Math.round(implicitWidth * 9 / 21)
    Image {
        anchors.fill: parent
        fillMode: Image.PreserveAspectCrop
        sourceSize.width: width
        asynchronous: true
    }
}
```

## Fixed column grid → reflowing grid

```qml
// before
GridView { cellWidth: 300; cellHeight: 160 }

// after
GridView {
    id: grid
    readonly property real target: Kirigami.Units.gridUnit * 14
    readonly property int columns: Math.max(1, Math.floor(width / target))
    cellWidth: Math.floor(width / columns)
    cellHeight: Math.round(cellWidth * 9 / 21) + Kirigami.Units.gridUnit * 2
}
```

## Sidebar that collapses on narrow windows

```qml
Kirigami.ApplicationWindow {
    id: window
    minimumWidth: Kirigami.Units.gridUnit * 24
    minimumHeight: Kirigami.Units.gridUnit * 20
    readonly property bool narrow: width < Kirigami.Units.gridUnit * 40

    RowLayout {
        anchors.fill: parent
        spacing: 0
        Sidebar { visible: !window.narrow; Layout.preferredWidth: Kirigami.Units.gridUnit * 12; Layout.fillHeight: true }
        Kirigami.Separator { visible: !window.narrow; Layout.fillHeight: true }
        ColumnLayout {
            Layout.fillWidth: true
            Layout.fillHeight: true
            spacing: 0
            QQC2.ComboBox { visible: window.narrow; Layout.fillWidth: true; Layout.margins: Kirigami.Units.largeSpacing }
            Content { Layout.fillWidth: true; Layout.fillHeight: true }
        }
    }
}
```

## Squeezed toolbar → ActionToolBar with overflow

```qml
// before
RowLayout {
    Button { text: "Exclude" }
    Button { text: "Include" }
    Button { text: "Move to…" }
    Button { text: "Delete" }
}

// after
Kirigami.ActionToolBar {
    Layout.fillWidth: true
    actions: [
        Kirigami.Action { text: i18n("Exclude"); icon.name: "view-hidden" },
        Kirigami.Action { text: i18n("Include"); icon.name: "view-visible" },
        Kirigami.Action { text: i18n("Move to…"); icon.name: "folder-move" },
        Kirigami.Action { text: i18n("Delete"); icon.name: "edit-delete" }
    ]
}
```

## Popup size of a plasmoid

```qml
PlasmoidItem {
    fullRepresentation: ColumnLayout {
        Layout.preferredWidth: Kirigami.Units.gridUnit * 24
        Layout.preferredHeight: Kirigami.Units.gridUnit * 18
        Layout.minimumWidth: Kirigami.Units.gridUnit * 16
        Layout.minimumHeight: Kirigami.Units.gridUnit * 12
        spacing: Kirigami.Units.smallSpacing
    }
}
```

## Panel and window instance of the same widget

```qml
PlasmoidItem {
    readonly property bool inPanel: Plasmoid.formFactor === PlasmaCore.Types.Horizontal
        || Plasmoid.formFactor === PlasmaCore.Types.Vertical
    preferredRepresentation: inPanel ? compactRepresentation : fullRepresentation
    Timer { running: inPanel; repeat: true; interval: 60000; onTriggered: work() }
}
```

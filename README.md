
# Multi Select Flutter

[![Pub Version](https://img.shields.io/pub/v/multi_select_flutter.svg)](https://pub.dev/packages/multi_select_flutter)

Multi Select Flutter is a package for creating multi-select widgets in a variety of ways.

| <img src="https://i.imgur.com/12ba2NO.gif" width="250"/><br /><sub><b>Dialog</b></sub> | <img src="https://i.imgur.com/NqldWcV.gif" width="250"/><br /><sub><b>BottomSheet</b></sub> | <img src="https://i.imgur.com/m7FzBIW.gif" width="250"/><br /><sub><b>ChoiceChip</b></sub> |
| :---: | :---: | :---: |

## Features
- Supports FormField features like validator.
- Neutral default design that can be altered to your heart's content.
- Choose between Dialog, BottomSheet, or ChoiceChip style widgets.
- Make your multi select `searchable` for larger lists.

## Install
Add this to your pubspec.yaml file:
```yaml
dependencies:
  multi_select_flutter: ^3.1.1
```

## Usage

### MultiSelectDialogField / MultiSelectBottomSheetField
<img src="https://i.imgur.com/JoTYWce.png" /><img src="https://i.imgur.com/Co8fhrD.png" />

These widgets provide an InkWell button which open the dialog or bottom sheet and are equipped with FormField features. You can customize it to your liking using the provided parameters.

To store the selected values, you can use the `onConfirm` parameter. You could also use `onSelectionChanged` for this.

By default these widgets render a MultiSelectChipDisplay below the field. This can be overridden with the `chipDisplay` parameter or removed completely by using `chipDisplay: MultiSelectChipDisplay(items: [])`.

```dart
MultiSelectDialogField(
  items: _animals.map((e) => MultiSelectItem(e, e.name)).toList(),
  listType: MultiSelectListType.CHIP,
  onConfirm: (values) {
    _selectedAnimals = values;
  },
),
```

### MultiSelectDialog / MultiSelectBottomSheet
<img src="https://i.imgur.com/XeuzNtQ.png" height="250" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://i.imgur.com/AwVOr54.png" height="200" />

If you prefer to create your own button for opening the dialog or bottom sheet, you may do so and then make a call to a function like this:

`MultiSelectDialog` can be used in the builder of `showDialog()`.
```dart
void _showMultiSelect(BuildContext context) async {
  await showDialog(
    context: context,
    builder: (ctx) {
      return  MultiSelectDialog(
        items: _items,
        initialValue: _selectedAnimals,
        onConfirm: (values) {...},
      );
    },
  );
}
```

`MultiSelectBottomSheet` can be used in the builder of `showModalBottomSheet()`.
```dart
void _showMultiSelect(BuildContext context) async {
  await showModalBottomSheet(
    isScrollControlled: true, // required for min/max child size
    context: context,
    builder: (ctx) {
      return  MultiSelectBottomSheet(
        items: _items,
        initialValue: _selectedAnimals,
        onConfirm: (values) {...},
        maxChildSize: 0.8,
      );
    },
  );
}
```

### MultiSelectChipDisplay
<img src="https://i.imgur.com/hMbuSxk.png" width="300" />

To display the selected items, this widget can be used alongside your own button or it can be specified as a `chipDisplay` parameter of widgets like `MultiSelectDialogField`.

You can also remove items from the source list in the onTap function.

```dart
MultiSelectChipDisplay(
  items: _selectedAnimals.map((e) => MultiSelectItem(e, e)).toList(),
  onTap: (value) {
    setState(() {
      _selectedAnimals.remove(value);
    });
  },
),
```
A MultiSelectChipDisplay that is part of a MultiSelectDialogField still renders outside the BoxDecoration of the MultiSelectDialogField as seen here:

<img  src="https://imgur.com/RuluMpS.png"  alt="chipDisplay"/>

If you want to encapsulate the MultiSelectChipDisplay, wrap the MultiSelectDialogField in a Container and apply the decoration to that instead:

```dart
Container(
  decoration: BoxDecoration(...),
  child: MultiSelectDialogField(
    items: _items,
    chipDisplay: MultiSelectChipDisplay(...),
  ),
),
```
<img  src="https://imgur.com/EcCyly4.png"  alt="chipDisplay"/>

### MultiSelectChipField
<img src="https://i.imgur.com/KDmtpmV.png" alt="chipField"/> 

This widget is similar to MultiSelectChipDisplay, except these chips are the primary interface for selecting items.
```dart
MultiSelectChipField<Animal>(
  items: _items,
  icon: Icon(Icons.check),
  onTap: (values) {
    _selectedAnimals = values;
  },
),
```
#### Using `itemBuilder` to create custom items:
```dart
MultiSelectChipField<Animal>(
  items: _items,
  key: _multiSelectKey,
  validator: (values) {...}
  itemBuilder: (item, state) {
    // return your custom widget here
    return InkWell(
      onTap: () {
        _selectedAnimals.contains(item.value)
			      ? _selectedAnimals.remove(item.value)
			      : _selectedAnimals.add(item.value);
	      state.didChange(_selectedAnimals);
	      _multiSelectKey.currentState.validate();
	    },
	    child: Text(item.value.name),
    );
  },
),
```
The `itemBuilder` param takes a function that will create a widget for each of the provided `items`. 

In order to use validator and other FormField features with custom widgets, you must call `state.didChange(_updatedList)` any time the list of selected items is updated.

#### Using `scrollControl` to auto scroll:
```dart
MultiSelectChipField(
  items: _items,
  scrollControl: (controller) {
    _startAnimation(controller);
  },
)

// waits 5 seconds, scrolls to end slow, then back fast
void _startAnimation(ScrollController controller) {
  // when using more than one animation, use async/await
  Future.delayed(const Duration(milliseconds: 5000), () async {
    await controller.animateTo(
      controller.position.maxScrollExtent,
      duration: Duration(milliseconds: 8000), 
      curve: Curves.linear);
      
    await controller.animateTo(
      controller.position.minScrollExtent,
      duration: Duration(milliseconds: 1250),
      curve: Curves.fastLinearToSlowEaseIn);
  });
}
```

## Constructors

### MultiSelectDialog
<img src="https://i.imgur.com/oIxOHLS.png" height="120" /><img src="https://i.imgur.com/4aPWPw2.png" height="120" /><img src="https://i.imgur.com/VDUHCMy.png" height="120" />

| Parameter | Type | Default| Description |
|---|---|---|---
| `backgroundColor` | Color | `null` | Set the background color of the dialog. |
| `cancelText` | Text | `Text("CANCEL")` | Specifies the cancel button text. |
| `checkColor` | Color | `null` | Set the color of the check in the checkbox. |
| `confirmText` | Text | `Text("OK")` | Specifies the confirm button text. |
| `closeSearchIcon` | Icon | `Icon(Icons.close)` | The icon button that hides the search field . |
| `colorator` | Color Function(V) | `null` | Set the selected color of individual items based on their value. Applies to both chips and checkboxes. |
| `height` | double | `null` | Give the dialog a fixed height. |
| `initialValue` | List\<V> | `null` | List of selected values. Required to retain values when re-opening the dialog. |
| `items` | List\<MultiSelectItem\<V>> | `null` | The source list of options. |
| `itemsTextStyle` | TextStyle | `null` | Specifies the style of text on chips or list tiles. |
| `listType` | MultiSelectListType | `MultiSelectListType.LIST` | Change the listType. Can be either &nbsp;`MultiSelectListType.LIST` or `MultiSelectListType.CHIP` |
| `onSelectionChanged` | Function(List\<V>) | `null` | Fires when an item is selected or unselected. |
| `onConfirm` | Function(List<V>) | `null` | Fires when the confirm button is pressed. |
| `searchable` | bool | `false` | Toggle search functionality within the dialog. |
| `searchHintStyle` | TextStyle | `null` | Style the text of the search hint. |
| `searchIcon` | Icon | `Icon(Icons.search)` | The icon button that shows the search field. |
| `searchHint` | String | `"Search"` | Set the placeholder text of the search field. |
| `searchTextStyle` | TextStyle | `null` | Style the search text. |
| `selectedColor` | Color | `null` | Set the color of the checkbox or chip items that are selected. |
| `title` | Text | `Text("Select")` | The title that is displayed at the top of the dialog. |
| `unselectedColor` | Color | `primaryColor` | Set the color of the chip body or checkbox border while not selected. | 

### MultiSelectDialogField
<img src="https://i.imgur.com/Zivo3FS.png" height="150" /><img src="https://i.imgur.com/XKBq6eG.png" height="150" /><img src="https://i.imgur.com/fBzleJj.png" height="150" />

MultiSelectDialogField has all the parameters of MultiSelectDialog plus these extra parameters:

| Parameter | Type | Default | Description |
|---|---|---|---
| `autovalidateMode` | AutovalidateMode | `AutovalidateMode.disabled` | If enabled, form fields will validate and update their error text immediately after every change. Default is disabled. |
| `barrierColor` | Color | `null` | Set the color of the space outside the dialog. |
| `buttonText` | Text | `"Select"` | Set text that is displayed on the button. |
| `buttonIcon` | Icon | `Icons.arrow_downward` | Specify the button icon. |
| `chipDisplay` | MultiSelectChipDisplay | `MultiSelectChipDisplay(...)` | Override the default MultiSelectChipDisplay that belongs to this field. |
| `decoration` | BoxDecoration | `null` | Style the Container that makes up the field. |
| `key` | GlobalKey\<FormFieldState> | `null` | Can be used to call methods like `_multiSelectKey.currentState.validate()`. |
| `onSaved` | List\<MultiSelectItem> | `null` | A callback that is called whenever we submit the field (usually by calling the `save` method on a form. |
| `validator` | FormFieldValidator\<List> | `null` | Validation. See [Flutter's documentation](https://flutter.dev/docs/cookbook/forms/validation). |

### MultiSelectBottomSheet
<img src="https://i.imgur.com/pfGYQTk.png" height="120" /><img src="https://i.imgur.com/s70LWyB.png" height="120" /><img src="https://i.imgur.com/iiqTTIH.png" height="120" />

| Parameter | Type | Default | Description |
|---|---|---|---
| `cancelText` | Text | `Text("CANCEL")` | Specifies the cancel button text. |
| `checkColor` | Color | `null` | Set the color of the check in the checkbox. |
| `confirmText` | Text | `Text("OK")` | Specifies the confirm button text. |
| `closeSearchIcon` | Icon | `Icon(Icons.close)` | The icon button that hides the search field . |
| `colorator` | Color Function(V) | `null` | Set the selected color of individual items based on their value. Applies to both chips and checkboxes. |
| `initialChildSize` | double | `0.3` | The initial height of the BottomSheet. |
| `initialValue` | List\<V> | `null` | List of selected values. Required to retain values when re-opening the BottomSheet. |
| `items` | List\<MultiSelectItem\<V>> | `null` | The source list of options. |
| `itemsTextStyle` | TextStyle | `null` | Specifies the style of text on chips or list tiles. |
| `listType` | MultiSelectListType | `MultiSelectListType.LIST` | Change the listType. Can be either &nbsp;`MultiSelectListType.LIST` or `MultiSelectListType.CHIP` |
| `maxChildSize` | double | `0.6` | Set the maximum height threshold of the BottomSheet. |
| `minChildSize` | double | `0.3` | Set the minimum height threshold of the BottomSheet before it closes. |
| `onSelectionChanged` | Function(List\<V>) | `null` | Fires when an item is selected or unselected. |
| `onConfirm` | Function(List<V>) | `null` | Fires when the confirm button is pressed. |
| `searchable` | bool | `false` | Toggle search functionality within the BottomSheet. |
| `searchHint` | String | `"Search"` | Set the placeholder text of the search field. |
| `searchHintStyle` | TextStyle | `null` | Style the text of the search hint. |
| `searchIcon` | Icon | `Icon(Icons.search)` | The icon button that shows the search field. |
| `searchTextStyle` | TextStyle | `null` | Style the search text. |
| `selectedColor` | Color | `null` | Set the color of the checkbox or chip items that are selected. |
| `title` | Text | `Text("Select")` | The title that is displayed at the top of the BottomSheet. |
| `unselectedColor` | Color | `primaryColor` | Set the color of the chip body or checkbox border while not selected. | 

### MultiSelectBottomSheetField
<img src="https://i.imgur.com/i17CAgc.png" height="160" /><img src="https://i.imgur.com/XFivjfC.png" height="160" /><img src="https://i.imgur.com/lXUBkWs.png" height="160" />

MultiSelectBottomSheetField has all the parameters of MultiSelectBottomSheet plus these extra parameters:

| Parameter | Type | Default | Usage |
|---|---|---|---
| `autovalidateMode` | AutovalidateMode | `AutovalidateMode.disabled` | If enabled, form fields will validate and update their error text immediately after every change. Default is disabled. |
| `backgroundColor` | Color | `null` | Set the background color of the BottomSheet. |
| `barrierColor` | Color | `null` | Set the color of the space outside the BottomSheet. |
| `buttonIcon` | Icon | `Icons.arrow_downward` | Specify the button icon. |
| `buttonText` | Text | `"Select"` | Set text that is displayed on the button. |
| `chipDisplay` | MultiSelectChipDisplay | `MultiSelectChipDisplay(...)` | Override the default MultiSelectChipDisplay that belongs to this field. |
| `decoration` | BoxDecoration | `null` | Style the Container that makes up the field. |
| `key` | GlobalKey\<FormFieldState> | `null` | Can be used to call methods like `_multiSelectKey.currentState.validate()`. |
| `onSaved` | List\<MultiSelectItem> | `null` | A callback that is called whenever we submit the field (usually by calling the `save` method on a form. |
| `shape` | ShapeBorder | `RoundedRectangleBorder(borderRadius: BorderRadius.vertical(top: Radius.circular(15.0)))` | Apply a ShapeBorder to alter the edges of the BottomSheet. |
| `validator` | FormFieldValidator\<List> | `null` | Validation. See [Flutter's documentation](https://flutter.dev/docs/cookbook/forms/validation). |

### MultiSelectChipField
<img src="https://i.imgur.com/BmoqrH4.png" height="160" /><img src="https://i.imgur.com/2uGWQkc.png" height="160" /><img src="https://i.imgur.com/0I7wcVz.png" height="160" />

| Parameter | Type | Default | Description |
|---|---|---|---
| `autovalidateMode` | AutovalidateMode | `AutovalidateMode.disabled` | If enabled, form fields will validate and update their error text immediately after every change. Default is disabled. |
| `chipColor` | Color | `primaryColor` | Set the chip color. | 
| `chipShape` | ShapeBorder | `null` | Define a ShapeBorder for the chips. |
| `closeSearchIcon` | Icon | `Icon(Icons.close)` | The icon button that hides the search field . |
| `colorator` | Color Function(V) | `null` | Set the selected chip color of individual items based on their value. |
| `decoration` | BoxDecoration | `null` | Style the surrounding Container. |
| `headerColor` | Color | `primaryColor` | Set the header color. |
| `height` | double | `null` | Set the height of the selectable area. |
| `icon` | Icon | `null` | The icon to display prior to the chip label. |
| `initialValue` | List\<V> | `null` | List of selected values before any interaction. |
| `itemBuilder` | Function(MultiSelectItem\<V>, FormFieldState\<List\<V>>) | `null` | Build a custom widget that gets created dynamically for each item. |
| `items` | List\<MultiSelectItem\<V>> | `null` | The source list of options. |
| `key` | GlobalKey\<FormFieldState> | `null` | Can be used to call methods like `_multiSelectKey.currentState.validate()`. |
| `onSaved` | List\<MultiSelectItem> | `null` | A callback that is called whenever the field is submitted (usually by calling the `save` method on a form. |
| `onTap` | Function(V) | `null` | Fires when a chip is tapped.
| `scroll` | bool | `true` | Enables horizontal scrolling. |
| `scrollBar` | HorizontalScrollBar | `null` | Define a scroll bar. |
| `scrollControl` | Function(ScrollController) | `null` | Make use of the ScrollController to automatically scroll through the list. |
| `searchable` | bool | `false` | Toggle search functionality. |
| `searchHint` | String | `"Search"` | Set the placeholder text of the search field. |
| `searchHintStyle` | TextStyle | `null` | Style the text of the search hint. |
| `searchIcon` | Icon | `Icon(Icons.search)` | The icon button that shows the search field. |
| `searchTextStyle` | TextStyle | `null` | Style the search text. |
| `selectedChipColor` | Color | `null` | Set the color of the chip items that are selected. |
| `selectedTextStyle` | TextStyle | `null` | Set the TextStyle on selected chips. |
| `showHeader` | bool | `true` | Determines whether to show the header. |
| `textStyle` | TextStyle | `null` | Style the text on the chips. |
| `title` | Text | `Text("Select")` | The title that is displayed in the header. |
| `validator` | FormFieldValidator\<List> | `null` | Validation. See [Flutter's documentation](https://flutter.dev/docs/cookbook/forms/validation). |

### MultiSelectChipDisplay
<img src="https://i.imgur.com/HIi3alZ.png" height="165" />

| Parameter | Type | Default | Description |
|---|---|---|---
| `alignment` | Alignment | `Alignment.centerLeft` | Change the alignment of the chips. |
| `chipColor` | Color | `primaryColor` | Set the chip color. | 
| `colorator` | Color Function(V) | `null` | Set the chip color of individual items based on their value. |
| `decoration` | BoxDecoration | `null` | Style the Container that makes up the chip display. |
| `icon` | Icon | `null` | The icon to display prior to the chip label. |
| `items` | List\<MultiSelectItem> | `null` | The source list of selected items. | 
| `onTap` | Function(V) | `null` | Fires when a chip is tapped.
| `shape` | ShapeBorder | `null` | Define a ShapeBorder for the chips. |
| `textStyle` | TextStyle | `null` | Style the text on the chips. |

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
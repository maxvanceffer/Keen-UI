## v0.8.9 (Release date here)

### New
* Add `autofocus` prop for UiTextbox and UiAutocomplete
* Add `buttonType` prop to UiButton and UiIconButton

### Fixes
* UiTextbox and UiAutocomplete: Fix `debounce` prop handling
* UiFab and UiIconButton: Set icon width to `100%` to fix horizontal centering in Safari on macOS
* UiButton and UiIconButton: Fix `dropdownPosition` prop not being passed to UiPopover
* UiAlert: Fix reactivity of `icon` prop

### Breaking
* UiTextbox: `autocomplete` prop type changed to String, to allow for configuring browser autocomplete


## v0.8.8 (May 21, 2016)

### New
* Add UiSelect component
* Add UiSlider component

### Fixes
* Add pointer cursor to all relevant interactive components (UiButton, UiIconButton, UiFab, UiMenuItem, UiCheckbox, UiRadio, UiSwitch)
* Fix z-index issue with tooltips and dropdowns in a modal
* UiIcon: Vertically align icons to middle for use inline with text
* UiMenu: Show scrollbar when menu height exceeds viewport height
* UiAutocomplete, UiRadioGroup, UiRating and UiTextbox: Keep help text visible when disabled
* UiIconButton and UiFab: Fix horizontal centering of icon in Firefox
* UiIconButton: Change `display` to `inline-flex`
* UiCollapsible: Fix header vertical centering in IE

### Breaking
* UiMenu and UiMenuItem: rename `keyboardShortcut` and `showKeyboardShortcuts` props to `secondaryText` and `showSecondaryText`, menu options should use the `secondaryText` property for showing secondary text like keyboard shortcuts and badges

* UiButton and UiIconButton: rename `showMenuKeyboardShortcuts` prop to `showMenuSecondaryText`, menu options should use the `secondaryText` property for showing secondary text like keyboard shortcuts and badges
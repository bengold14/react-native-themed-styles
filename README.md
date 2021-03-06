![](https://github.com/wvteijlingen/react-native-themed-styles/workflows/Node%20CI/badge.svg)

# react-native-themed-styles

A small package that allows you to create custom UI themes and use them throughout your app with a useTheme hook.

It does not impose any structure on your theme, which means you can use it not only for light/dark mode, but also for spacing, fonts or whatever you dream up.

- No dependencies
- Simple and clear API
- Fully typed
- No new concepts to learn, it builds on StyleSheets and hooks

## Installation

**Using Yarn**

```
yarn add -D react-native-themed-styles
```

**Using NPM**

```
npm install --save-dev react-native-themed-styles
```

**Using copy/paste**

If you want to keep your dependencies low and don't care about upstream updates, you can also just
copy the index file into your own repository.

## Usage

Define your themes:

```ts
// themes.ts

import { registerThemes } from "react-native-themed-styles"

const light = { backgroundColor: "white", textColor: "black" }
const dark = { backgroundColor: "black", textColor: "white" }

const styleSheetFactory = registerThemes(
  { light, dark }, // All themes you want to use.
  () => "light" // A function that returns the name of the default theme.
)

export { styleSheetFactory }
```

Use your themes:

```tsx
// my-component.tsx

import { useTheme } from "react-native-themed-styles"
import { styleSheetFactory } from "./themes"

const themedStyles = styleSheetFactory(theme => ({
  container: {
    backgroundColor: theme.backgroundColor,
    flex: 1
  },
  text: {
    color: theme.textColor
  }
}))

const MyComponent = () => {
  const [styles] = useTheme(themedStyles)

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Hello there</Text>
    </View>
  )
}
```

## Mirroring the OS theme

You most likely want your app to automatically switch themes based on the OS theme, i.e. dark or light mode.
You can easily implement this with the `react-native-appearance` package, by using its `useColorScheme` hook in the second argument of `registerThemes`:

```ts
import { useColorScheme } from "react-native-appearance"
import { registerThemes } from "react-native-themed-styles"

const styleSheetFactory = registerThemes({ light, dark }, () => {
  const colorScheme = useColorScheme()
  return ["light", "dark"].includes(colorScheme) ? colorScheme : "light"
})
```

## API

### Function: `registerThemes(themes, appearanceProvider)`

Use this function to register your themes. This will return a factory function that you can use to create a themed StyleSheet.

**Parameters**

- `themes`: An object containing all your themes, keyed by name. All themes must have the same data structure.
- `appearanceProvider`: A function that returns the name of the default theme.

**Returns**

```
ThemedStyleSheetCreator
```

---

### Function: `ThemedStyleSheetCreator`

A function that you can use to create a themed StyleSheet.

**Parameters**

- `callback`: A callback from which you must return an object of styles, as you would when using `StyleSheet.create`. You can access the `theme`
  argument to access your theme data.

**Returns**

```
ThemedStyleSheet
```

---

### Function: `useTheme(themedStyleSheet[, themeName])`

Use this function to apply a theme and retrieve computed component styles.

**Parameters**

- `themedStyleSheet`: A `ThemedStyleSheet` as returned from the `createStyles` function.
- `themeName`: Optional string defining which theme to apply. If not passed, it applies the theme returned by the `appearanceProvider` that you passed to the `registerThemes` function.

**Returns**

```
[styles, theme, themeName]
```

A tuple containing the following entries:

- `styles`: The styles with the theme applied
- `theme`: The raw theme that was applied.
- `themeName`: The name of the applied theme.

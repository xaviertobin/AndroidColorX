AndroidColorX [![CircleCI](https://circleci.com/gh/JorgeCastilloPrz/AndroidColorX/tree/master.svg?style=svg&circle-token=5cd5f9a1d941936290fde62a8321f9bf9d60f2c5)](https://circleci.com/gh/JorgeCastilloPrz/AndroidColorX/tree/master)
======

AndroidColorX (i.e: Android Color Extensions) is an Android library written in Kotlin that provides color utilities as [Kotlin extension functions](https://kotlinlang.org/docs/tutorials/kotlin-for-py/extension-functionsproperties.html). The library relies on AndroidX [`ColorUtils`](https://developer.android.com/reference/kotlin/androidx/core/graphics/ColorUtils) for some of its calculations.

### How to use

Add the following dependency to your `build.gradle`. It's available in **Maven Central**.

```groovy
dependencies {
    implementation 'me.jorgecastillo:androidcolorx:0.2.0'
}
```

### Color conversion

This library provides seamless conversion between the following color types:

* `android.graphics.Color (ColorInt)`
* `RGBColor`
* `ARGBColor`
* `HEXColor`
* `HSLColor`
* `HSLAColor`
* `HSVColor`
* `CMYKColor`

To convert a color type to any of the other types, you can use the extensions provided for it.

```kotlin
val color = Color.parseColor("#e91e63")

val rgb = color.asRgb()
val argb = color.asArgb()
val hex = color.asHex()
val hsl = color.asHsl()
val hsla = color.asHsla()
val hsv = color.asHsv()
val cmyk = color.asCmyk()

val colorHsl = HSLColor(hue = 210f, saturation = 0.5f, lightness = 0.5f)

val colorInt = colorHsl.asColorInt()
val rgb = colorHsl.asRgb()
val argb = colorHsl.asArgb()
val hex = colorHsl.asHex()
val cmyk = colorHsl.asCmyk()
val hsla = colorHsl.asHsla()
val hsv = colorHsl.asHsv()
```

The same extensions **are available for all the mentioned color types**. Note that when converting from a color with an alpha component (i.e: `ARGB` or `HSLA`) to one without it (i.e: `RGB` or `HSL`) it will be a lossy conversion. If it's the other way around, conversions will assume full alpha (255 or 1f).

Also note that conversions from colors that store their components as `Float` (i.e: cmyk, hsl, hsla) to colors that store them as `Int` (i.e: RGBColor, ColorInt) and back will imply precision loss.

### Shades and tints

If you're aware of webs like [Color-Hex](https://www.color-hex.com/color/6dc066) you've probably seen those shades and tints palettes. AndroidColorX provides extensions for calculating those:

<img src="https://drive.google.com/uc?id=1JiQLN_AlTGCUiEUXXEh8pwTAStONMGH5" width="300px"/>

Here's the code for all the color types available:

```kotlin

val color = Color.parseColor("#e91e63")

// shades
val shades: List<Int> = color.shades()
val shadesHsl: List<HSLColor> = color.asHsl().shades()
val shadesHsla: List<HSLAColor> = color.asHsla().shades()
val shadesHsv: List<HSVColor> = color.asHsv().shades()
val shadesCmyk: List<CMYKColor> = color.asCmyk().shades()
val shadesHex: List<HEXColor> = color.asHex().shades()
val shadesRgb: List<RGBColor> = color.asRgb().shades()
val shadesArgb: List<ARGBColor> = color.asArgb().shades()

// tints
val tints: List<Int> = color.tints()
val tintsHsl: List<HSLColor> = color.asHsl().tints()
val tintsHsla: List<HSLAColor> = color.asHsla().tints()
val tintsHsv: List<HSVColor> = color.asHsv().tints()
val tintsCmyk: List<CMYKColor> = color.asCmyk().tints()
val tintsHex: List<HEXColor> = color.asHex().tints()
val tintsRgb: List<RGBColor> = color.asRgb().tints()
val tintsArgb: List<ARGBColor> = color.asArgb().tints()
```

These functions calculate `10` shades or tints by default that will be returned **along with the original color**, but you can also generate an arbitrary amount if you need to:

```kotlin
val color = Color.parseColor("#e91e63")

// shades
val shades: List<Int> = color.shades(count = 3)
val shadesHsl: List<HSLColor> = color.asHsl().shades(count = 3)
...

// tints
val tints: List<Int> = color.tints(count = 15)
val tintsHsl: List<HSLColor> = color.asHsl().tints(count = 15)
...
```

Note that the colors will always range from 0 to 1 luminance for tinting or shading regardless of the amount of colors to generate, so the more you generate the more gradual the contrast will be between each consecutive pair of colors.

These extensions **are available for all the mentioned color types**.

### Complimentary, triadic, tetradic and analogous colors

The library provides extensions to calculate all those. There is a brief but very useful [post to understand all those here](https://www.tigercolor.com/color-lab/color-theory/color-harmonies.htm).

<img src="https://drive.google.com/uc?id=1QXswFgCHjS2ar_w7-44e9iNanHHl0n8v" width="300px"/>

```kotlin
val color = Color.parseColor("#e91e63")

// Returns the complimentary color to the given one.
val complimentary: Int = color.complimentary()

// Returns the other 2 colors to complete the triadic color scheme.
val triadic: Pair<Int, Int> = color.triadic()

// Returns the other 3 colors to complete the tetradic color scheme.
val tetradic: Triple<Int, Int, Int> = color.tetradic()

// Returns the other 2 colors to complete the analogous color scheme.
val analogous: Pair<Int, Int> = color.analogous()
```

Once again, you've got those extensions available for all the color types in the library.

### Darken / Lighten a color

You can darken or lighten a color by an amount:

```kotlin
val color = Color.parseColor("#e91e63")

// Int in the 0...100 range. Anything else is clamped to 0...100
val darkerColor = color.darken(50)
val lighterColor = color.lighten(50)

// Float in the 0...1 range. Anything else is clamped to 0...1
val darkerColor = color.darken(0.5)
val lighterColor = color.lighten(0.5)
```

You can use this extensions over any color type available.

### Other interesting extensions

#### isDark

You can use this one to detect whether a color is considered "dark" or not. It delegates in the following AndroidX `ColorUtils` code `ColorUtils.calculateLuminance(this) < 0.5`. This is frequently used for inferring which color you should tint an icon or a text with, so it can have a good contrast on top of the underlying background.

```kotlin
icon.setImageDrawable(
    ContextCompat.getDrawable(
        context,
        if (bgColor.isDark()) {
            R.drawable.ic_fav_light
        } else {
            R.drawable.ic_fav_dark
        })
)
```

#### contrasting

The library also provides a handy extension to get a nicely contrasting color for a given color. You can use it for the same purpose and to remove a bit of boilerplate.

```kotlin
val color = HEXColor("#e91e63")

// Defaults to black or white, the one with better contrast
color.contrasting()

// You can pass custom light and dark colors
color.contrasting(lightColor = HEXColor("#ffffff"), darkColor = HEXColor("#000000"))
```

These extensions can be used over any color types provided by the library.

License
-------

    Copyright 2019 Jorge Castillo Pérez

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.



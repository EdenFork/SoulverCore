# SoulverCore

SoulverCore is a Swift framework that lets you easily evaluate mathematical expressions. It powers the popular Mac notepad-calculator app [Soulver](https://soulver.app). It has a very simple API with sensible defaults, and is also quite customizable ("simple things are easy, complex things are possible"). It has no dependencies & requires no special entitlements.

## Requirements

- Xcode 11+
- Swift 5+

## Supported Platforms

SoulverCore is distributed as a Binary Framework (.xcframework) and supports macOS & iOS. 

- macOS 10.14.4+
- iOS 12.2+

## Installation


Drag `SoulverCore.xcframework` into the `Frameworks, Libraries, and Embedded Content` section of the General settings for your Mac or iOS target.


## Getting Started

To calculate the result of a single expression, use a `Calculator`:

```swift
import SoulverCore

let calculator = Calculator(customization: .standard)
let result = calculator.evaluate("123 + 456")
print("The answer is \(result.stringValue)") // prints 579
```

SoulverCore includes unit calculations, calendar calculations, percentage phrases, and ignores  "meaningless" words:

```swift
calculator.evaluate("$10 for lunch + 15% tip") // $11.50
calculator.evaluate("65 kg in pounds") // 154.32 lb
calculator.evaluate("40 as % of 90") // 25%
calculator.evaluate("January 30 2020 + 3 months 2 weeks 5 ") // 25%
```

## Variables

Use a `VariableList` to customize variables in your expression:

```swift
let variableList = VariableList(variables:
    [
        Variable(name: "a", value: "123"),
        Variable(name: "b", value: "456"),
    ]
)
calculator.evaluate("a + b", with: variableList) // 579        
```
## Output Formatting

Use a `FormattingPreferences` to customize the formatting of your result.

```swift
var formattingPreferences = FormattingPreferences()
formattingPreferences.dp = 2 // decimal places
calculator.formattingPreferences = formattingPreferences

calculator.calculate("π") // 3.14
```

## Custom Units

You can add custom units to an `EngineCustomization`.

```swift

var customization: EngineCustomization = .standard

customization.customUnits = [
    CustomUnit(name: "parrots", definition: 15, equivalentUnit: .centimeters),
    CustomUnit(name: "python", definition: 570, equivalentUnit: .centimeters)
]

let calculator = Calculator(customization: customization)
calculator.calculate("1 python in parrots") // 38 parrots
```

## Locale settings

SoulverCore respects the decimal separator and thousands separator of the current locale. Alternatively, convert the standard EngineCustomization to use another locale:

```swift

let europeanLocale = Locale(identifier: "en_DE") // In Germany a comma is used as the decimal separator
let customization = EngineCustomization.standard.convertTo(locale: europeanLocale)

let calculator = Calculator(customization: customization)
calculator.calculate("1,2 + 3,4") // 4,6
```

## Multi-line calculations

Use a `LineCollection` to represent a list of lines to be evaluated. The `LineCollection` creates its own calculator, though you can still pass in your own customization.

```swift

let multiLineText =
"""
a = 10
b = 20
c = a + b
"""
        
let lineCollection = LineCollection(multiLineText:
    multiLineText, customization: .standard)

// Calculate the result of each line (synchronously)
lineCollection.evaluateAll()

// Use subscripts to get access to particular lines' results
let result = lineCollection[2].result // 30
````

## Line References

A line collection manages dependencies between lines. You can also get a "reference" to a line. A reference is a single unicode character, which will be substituted with the line's actual answer during evaluation.

```swift

let multiLineText =
"""
10
20
"""

let lineCollection = LineCollection(multiLineText:
    multiLineText, customization: .standard)

let referenceToFirstLine = lineCollection.makeReferenceForLineAt(lineIndex: 0)
let referenceToSecondLine = lineCollection.makeReferenceForLineAt(lineIndex: 1)

lineCollection.addLine("\(referenceToFirstLine) + \(referenceToSecondLine)")

// Calculate the result of each line
lineCollection.evaluateAll()

print(lineCollection[2].result!.stringValue) // 30
````

## More information

SoulverCore has all the math features of [Soulver](https://soulver.app). Check out [Soulver's documentation](https://documentation.soulver.app) to see lists of built-in units and functions. Let me know if there's a feature we don't have that you need and I'll see what I can do.

## Authors

* **Zac Cohan** (zac@soulver.app)

## License

Please [email us](mailto:contact@soulver.app) if you wish to use SoulverCore in a public or commerical project.
# Options

![Made for Swift](https://img.shields.io/badge/MADE%20FOR-SWIFT-orange?style=for-the-badge&logo=swift)

![Swift Package Manager supported](https://img.shields.io/badge/SWIFT%20PACKAGE%20MANAGER-SUPPORTED-green?style=for-the-badge&logo=SWIFT)


[![Build and test](https://github.com/allexks/Options/actions/workflows/swift-build-test.yml/badge.svg)](https://github.com/allexks/Options/actions/workflows/swift-build-test.yml)
![GitHub](https://img.shields.io/github/license/allexks/Options)

The swiftier `OptionSet`. With the help of enums.

## The Problem

Let's say that you have some model in your code, for example named `Card`. This model has a property `color` which aims to represent its color. Let's also say that all possible card colors are *white*, *blue*, *black*, *red*, *green* and **all possible combinations of those 5, including none of them**.

The standard way this can be handled now in Swift is to create an `OptionSet` that has flags assigning all colors the numbers 1, 2, 4, 8, 16, ... (so that they can be easily combined with the bitwise operator `|`), like so:

```swift
struct CardColor: OptionSet {
    let rawValue: Int

    init(rawValue: Int) {
        self.rawValue = rawValue
    }

    static let white = CardColor(rawValue: 1 << 0)
    static let blue  = CardColor(rawValue: 1 << 1)
    static let black = CardColor(rawValue: 1 << 2)
    static let red   = CardColor(rawValue: 1 << 3)
    static let green = CardColor(rawValue: 1 << 4)
}
```

The code snippet above shows the bare minimum for creating an `OptionSet` that would suit our needs. Although rather simple and straightforward, it still contains quite some boilerplate for just 5 flags: a stored property, an initializer, as well as all literal raw values for all flags manually written.

Our "swifty" intuition tells us that we can do better, can't we?

## The Solution

Create a regular enum with all options. They don't need to have a raw value, the only condition is that the enum should conform to `CaseIterable` (because the way they are arranged in the `.allCases` array will determine their raw values).

```swift
enum CardColor: CaseIterable {
    case white
    case blue
    case black
    case red
    case green
}
```

Now in order to use the enum like an `OptionSet` we just need to use the `Options` struct provided in this library:

```swift
let colorless: Options<CardColor> = []
let onlyWhite: Options<CardColor> = [.white]
let bluAndRed: Options<CardColor> = [.blue, .red]
```

No need for boilerplates and supplying the raw values of all flags.

## Extras

The following convenience properties and methods exist:

```swift
let allCombined: Options<CardColor> = .all // Equivalent to Options<Topping>([.white, .blue, .black, .red, .green])

let allPossibleBlueColorCombinations = Options<CardColor>.allContaining(.blue) // returns a list of all elements which contain `[.blue]`, sorted ascendingly by their raw values

let favoriteColorCombo: Options<CardColor> = [.white, .blue, .red]
let allPossibleFavoriteColorCombinations = Options<CardColor>.allContaining(favoriteColorCombo) // returns a list of all elements which contain `[.white, .blue, .red]`, sorted ascendingly by their raw values
let sameAsAbove = favoriteColorCombo.allCombinationsContainingSelf

let favoriteComboArray = favoriteColorCombo.decomposed // this is a [CardColor] array containing [.white, .blue, .red]
```

## Limitations

1. The square brackets should be present even if creating an instance just by using one option, for example:

```swift
Options<CardColor>.all.contains(.white)    // this will not compile
Options<CardColor>.all.contains([.white]) //  this will solve it
```

2. The supplied `enum` needs to have a maximum of 64 cases.

## License

[MIT](LICENSE)

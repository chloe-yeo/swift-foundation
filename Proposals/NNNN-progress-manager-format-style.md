# `ProgressManager` Format Styles

* Proposal: [SF-NNNN](NNNN-progress-manager-format-style.md)
* Authors: [Chloe Yeo](https://github.com/chloe-yeo)
* Review Manager: TBD
* Status: **Pitch**

## Revision history

* **v1** Initial version

## Introduction 

With the introduction of Swift-native API for progress reporting in [SF-0023](https://github.com/chloe-yeo/swift-foundation/blob/6342785b7d09db02de145a38c161a8872179daac/Proposals/0023-progress-manager.md), we would like to add `ProgressManager.FormatStyle` and `ProgressReporter.FormatStyle` to format the `fractionCompleted`, `completedCount` and `totalCount` properties of `ProgressManager` and `ProgressReporter` respectively. 

## Motivation

The addition of format styles for `ProgressManager` and `ProgressReporter` is important to enable adoption of `ProgressManager` and `ProgressReporter` in applications and UI components, such as SwiftUI's `ProgressView`. 

In its current state, without the addition of `ProgressManager.FormatStyle`, you would have to construct your own String-based description of a `ProgressManager` instance as follows: 

```swift
let progressManager = ProgressManager(totalCount: 100)
progressManager.complete(count: 25)

let percentage = progressManager.fractionCompleted.formatted(.percent) 
let percentageDescription = "\(percentage) completed"
print(percentageDescription) // "25% completed"
```

## Proposed solution and example

With the introduction of `ProgressManager.FormatStyle`, `ProgressManager` instance can now be formatted as: 

```swift 
let progressManager = ProgressManager(totalCount: 100)
progressManager.complete(count: 25)

let percentCompleted = progressManager.formatted(.fractionCompleted())
print(percentCompleted) // "25% completed"
```

## Detailed design

### `ProgressManager.FormatStyle`

`ProgressManager.FormatStyle` conforms to `FormatStyle` protocol. 

```swift
@available(FoundationPreview 6.4, *)
extension ProgressManager {

    public struct FormatStyle : Sendable, Codable, Equatable, Hashable {
        
        public enum Style: Sendable, Codable, Equatable, Hashable {
            case fractionCompleted(FloatingPointFormatStyle<Double>.Percent)
            case count(IntegerFormatStyle<Int>)
        }
        
        public var style: Style
        public var locale: Locale
        
        public init(style: Style = .fractionCompleted(FloatingPointFormatStyle<Double>.Percent()), locale: Locale = .autoupdatingCurrent)
    }
}

@available(FoundationPreview 6.4, *)
extension ProgressManager.FormatStyle: FormatStyle {

    public func format(_ manager: ProgressManager) -> String
}

@available(FoundationPreview 6.4, *)
extension FormatStyle where Self == ProgressManager.FormatStyle {
    public static func fractionCompleted(format: FloatingPointFormatStyle<Double>.Percent = FloatingPointFormatStyle<Double>.Percent()) -> Self

    public static func count(format: IntegerFormatStyle<Int> = IntegerFormatStyle<Int>()) -> Self
}

@available(FoundationPreview 6.4, *)
extension ProgressManager {
    public func formatted<F>(_ style: F) -> F.FormatOutput

    public func formatted() -> String
}
```

### `ProgressReporter.FormatStyle`

`ProgressReporter.FormatStyle` conforms to `FormatStyle` protocol. 

```swift 
@available(FoundationPreview 6.4, *)
extension ProgressReporter {
    public struct FormatStyle : Sendable, Codable, Equatable, Hashable {

        public enum Style: Sendable, Codable, Equatable, Hashable {
            case fractionCompleted(FloatingPointFormatStyle<Double>.Percent)
            case count(IntegerFormatStyle<Int>)
        }
        
        public var style: Style
        public var locale: Locale
        
        public init(style: Style = .fractionCompleted(FloatingPointFormatStyle<Double>.Percent()), locale: Locale = .autoupdatingCurrent)
    }
}

@available(FoundationPreview 6.4, *)
extension ProgressReporter.FormatStyle: FormatStyle {

    public func format(_ reporter: ProgressReporter) -> String
}

@available(FoundationPreview 6.4, *)
extension FormatStyle where Self == ProgressReporter.FormatStyle {
    public static func fractionCompleted(format: FloatingPointFormatStyle<Double>.Percent = FloatingPointFormatStyle<Double>.Percent()) -> Self

    public static func count(format: IntegerFormatStyle<Int> = IntegerFormatStyle<Int>()) -> Self
}

@available(FoundationPreview 6.4, *)
extension ProgressReporter {
    public func formatted<F>(_ style: F) -> F.FormatOutput

    public func formatted() -> String
}
```

## Impact on existing code

No impact. This is a purely additive change. 

## Alternatives considered

None. 

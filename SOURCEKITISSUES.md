# Current known SourceKit issues that prevent SwiftShield from obfuscating *everything*

# SourceKit Bugs

Bugs that are marked as merged are fixed in the Swift repo, but still bugged in the current Xcode version. They are fixed in SwiftShield when the new Xcode containing these fixes come out.

- **IN-REVIEW**: [(SR-8616)](https://bugs.swift.org/browse/SR-8616) `is` pattern: Matched type won't index if the left element is an optional (`if [].first is Foo`). For now, you can overcome this by not using the optionals directly.
- **MERGED**: [(SR-8617)](https://bugs.swift.org/browse/SR-8617) Enum names: Explicitly using an enum type in pattern matching prevents it from getting indexed (`if case MyClass.MyEnum.myCase {}` - `myCase` will be indexed, but `MyClass` won't.)
- **IN-REVIEW**: [(SR-9020)](https://bugs.swift.org/browse/SR-9020) Legacy KeyPaths that include types, such as `#keyPath(Foo.bar)` will not get indexed.
- **MERGED**: [(SR-9039)](https://bugs.swift.org/browse/SR-9039) Explicit Swift KeyPaths such as `\Foo.bar` will not index the type portion.
- Emoji Strings: Although SourceKit has no real bugs regarding emojis, it does treat them differently: While SourceKit treats emojis as several characters, Swift treats them as a single one - which will prevent SwiftShield from knowing the correct position of the references after said emoji. While a solution isn't found, you can avoid this by not using emojis.
- **IN-REVIEW**: Expressions marked as `@available` fail to be indexed.
- `@objc optional` protocol methods don't have their references indexed.

# Types that won't be obfuscated

The following types and cases might be working correctly in SourceKit, but are currently disabled for other reasons.

- Typealiases and Associated Types: Not always indexed (`typealias Foo = UIImage | extension Foo {}` - Foo is ignored and indexed as UIImage). Note that these can't be reverse-engineered as they are purely an editor thing, so no action is required!
- Enum cases and names ending with `CodingKeys`: We avoid obfuscating Codable enums (otherwise your app wouldn't work), but this is detected by a name having the suffix `CodingKeys`.
- **MERGED**: Methods with names under four characters: Operators only get indexed as such if they are declared in a global scope. Since most people use `public static func`, they get indexed as regular methods. To prevent operators from being obfuscated, methods with names shorter than four characters won't get obfuscated.
- Properties: Properties are on hold for a while because they break derived `Codable` types. Although the obfuscation works correctly, if you build `Codable` types to work on top of a backend's json, parsing will fail because of the different property name.
- Module names: Not implemented yet!

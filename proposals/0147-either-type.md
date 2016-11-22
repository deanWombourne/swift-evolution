# Include a generic `Either` sum type in Swift

* Proposal: [??](0147-either-type.md)
* Authors: [Sam Dean](https://github.com/deanWombourne)
* Review Manager: 
* Status: 
* Decision Notes:

## Introduction

Provide users of Swift with an enumeration to return from old-style (i.e. `(result, error?)`) callbacks.

## Motivation

I see this being reimplemented everywhere and it seems a very small thing to include as standard.

Including this in Swift itself would remove the duplication created by multiple implementations, and would also encourage
users to follow this pattern, instead of the objective-c style of passing both a nullable result and error in callbacks.

### Known issues:

There are probably arguments against this, but I don't know enough about building a language to know what they might be

## Proposed solution

```
public enum Either<T> {
    case left(Error)
    case right(T)
}
```

This could also include helpful methods i.e. `map`
```
public extension Either {
    
    public func map<U>(_ transform: (T) throws -> U) rethrows -> Either<U> {
        switch self {
            case .left(let error): return .left(error)
            case .right(let value): return .right(try transform(value))
    }
}
```

And maybe even `flatMap`, but I might be reaching here?
```
public extension Either {
    
    public func flatMap(_ transform: (T) throws -> U) -> Either<U> {
        switch self {
            case .left(let error): return .left(error)
            case .right(let value):
                do {
                    return .right(try transform(value)
                } catch let e {
                    return .left(e)
                }
        }
    }
}
```

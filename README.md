These guidelines build on Apple's existing [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html).
Unless explicitly contradicted below, assume that all of Apple's guidelines apply as well.

##Table of Contents
* [Whitespace](#whitespace)
* [Documentation and Organization](#documentation-and-organization)
* [Declarations](#declarations)
* [Expressions](#expressions)
* [Control Structures](#control-structures)
* [Exceptions and Error Handling](#exceptions-and-error-handling)
* [Blocks](#blocks)
* [Literals](#literals)
* [Categories](#categories)
* [Legacy Specific Stuff](#legacy-specific-stuff)
* [Notifications VS. KVO](#notifications-vs-kvo)


## Whitespace

 * Tabs, not spaces.
 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.

## Documentation and Organization

 * All method declarations should be documented.
 * Comments should be hard-wrapped at 80 characters.
 * Comments should be [Appledoc](http://gentlebytes.com/appledoc/)-style.
 * Document whether object parameters allow `nil` as a value.
 * Use `#pragma mark -`s to categorize methods into functional groupings and protocol implementations, following this general structure:
 * Use `#pragma mark`s to subcategorize methods when necessary:

```objc
#pragma mark - Properties

@dynamic someProperty;

- (void)setCustomProperty:(id)value {}

#pragma mark Special Properties

@dynamic someOtherProperty;


#pragma mark - Lifecycle

+ (id)objectWithThing:(id)thing {}
- (id)init {}

#pragma mark - Drawing

- (void)drawRect:(CGRect) {}

#pragma mark - Another functional grouping

#pragma mark - GHSuperclass

- (void)someOverriddenMethod {}

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## Declarations

 * <del>Never declare an ivar unless you need to change its type from its declared property.</del> *(Let's discuss -Luke)*
 * Don’t use line breaks in method declarations.
 * Prefer exposing an immutable type for a property if it being mutable is an implementation detail. This is a valid reason to declare an ivar for a property.
 * Always declare memory-management semantics even on `readonly` properties.
 * Declare properties `readonly` if they are only set once in `-init`.
 * Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
 * Declare properties `copy` if they return immutable objects and aren't ever mutated in the implementation. `strong` should only be used when exposing a mutable object, or an object that does not conform to `<NSCopying>`.
 * Instance variables should be prefixed with an underscore (just like when implicitly synthesized).
 * Don't put a space between an object type and the protocol it conforms to.
 
```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;
```
 
 * C function declarations should have no space before the opening parenthesis, and should be namespaced just like a class.

```objc
void GHAwesomeFunction(BOOL hasSomeArgs);
```

 * Constructors should generally return [`instancetype`](http://clang.llvm.org/docs/LanguageExtensions.html#related-result-types) rather than `id`.
 * Prefer C99 struct initialiser syntax to helper functions (such as `CGRectMake()`).

```objc
  CGRect rect = { .origin.x = 3.0, .origin.y = 12.0, .size.width = 15.0, .size.height = 80.0 };
   ```

## Expressions

 * <del>Don't access an ivar unless you're in `-init`, `-dealloc` or a custom accessor.</del>
 * Use dot-syntax when invoking idempotent methods, including setters and class methods (like `NSFileManager.defaultManager`).
 * Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.
 * Comparisons should be explicit for everything except `BOOL`s.
 * Prefer positive comparisons to negative.
 * Long form ternary operators should be wrapped in parentheses and only used for assignment and arguments.

```objc
Blah *a = (stuff == thing ? foo : bar);
```

* <del>Short form, `nil` coalescing ternary operators should avoid parentheses.</del>

```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

 * Separate binary operands with a single space, but unary operands and casts with none:

```c
void *ptr = &value + 10 * 3;
NewType a = (NewType)b;

for (int i = 0; i < 10; i++) {
    doCoolThings();
}
```

## Control Structures

 * Always surround `if` bodies with curly braces if there is an `else`. Single-line `if` bodies without an `else` should be on the same line as the `if`. 
 * All curly braces should begin on the same line as their associated statement. They should end on a new line.
 * Put a single space after keywords and before their parentheses.
 * Return and break early.
 * No spaces between parentheses and their contents.

```objc
if (shitIsBad) return;

if (something == nil) {
	// do stuff
} else {
	// do other stuff
}
```

## Exceptions and Error Handling

 * Don't use exceptions for flow control.
 * Use exceptions only to indicate programmer error.
 * To indicate errors, use an `NSError **` argument or send an error on a [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) signal.

## Blocks

 * Blocks should have a space between their return type and name.
 * Block definitions should omit their return type when possible.
 * Block definitions should omit their arguments if they are `void`.
 * Parameters in block types should be named unless the block is initialized immediately.

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## Literals

 * Avoid making numbers a specific type unless necessary (for example, prefer `5` to `5.0`, and `5.3` to `5.3f`).
 * The contents of array and dictionary literals should have a space on both sides.
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
NSArray *theShit = @[ @1, @2, @3 ];

NSDictionary *keyedShit = @{ GHDidCreateStyleGuide: @YES };
```

 * Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

``` objc
NSArray *theShit = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];

NSDictionary *keyedShit = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff",
};
```

## Categories

 * <del>Categories should be named for the sort of functionality they provide. Don't create umbrella categories.</del>
 * <del>Category methods should always be prefixed.</del>
 * <del>If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.</del>
*(Let's discuss- I think categories are great, and can fill in a lot of missing functionality in the core libraries, but maybe we need to think through them a little bit more and have a good gameplan. -Luke)*

##Legacy Specific Stuff

* Don't ever do this:

`@interface SomeViewController : UIViewController<UITableViewDelegate, UITableViewDataSource>`

* When what you actually want to do is this:

`@interface SomeViewController : UITableViewController`

* When working with SQLite, always use parameter binding for SQL sanitization. Example when using FMDB:

`FMResultSet *siteQuery = [db executeQuery:@"select stuff where id = ?" withArgumentsInArray:@[siteID]];`

* Don't ever do this (or use classes that do):

`NSString *badQuery = [NSString stringWithFormat:@"select stuff where id = %@", siteID];`

* Declare iVars in the implementation (.m) not the (.h)

##Notifications VS. KVO

* Prefer Key-Value-Observing when the event is occuring within a class that is a property of of the observing class
* Prefer NSNotifications when no such relationship exists between the classes.

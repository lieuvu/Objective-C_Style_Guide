# The Objective-C style guide.

This style guide outlines the coding conventions for Lieu Vu.


# Introduction

The reason I made this style guide was so that I could keep the code in my projects consistent.


# Credits

The creation of this style is inspired by [raywenderlich.com](https://github.com/raywenderlich/objective-c-style-guide) and [Robots & Pencils'](https://github.com/RobotsAndPencils/objective-c-style-guide)


# Background

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)


# Table of Contents

* [Language](#language)
* [Code Organization](#code-organization)
* [Header Files](#header-files)
	* [Import Order](#import-order)
* [Spacing](#spacing)
* [Naming](#naming)
    * [Synthesize](#synthesize)
    * [Underscores](#underscores)
* [Variables](#variables)
    * [Variable Qualifiers](#variable-qualifiers)
    * [Property Attributes](#property-attributes)
	* [Private Properties](#private-properties)
    * [Platform Independent](#platform-independent)
* [Constants](#constants)
* [Methods](#methods)
    * [Init Methods](#init-methods)
    * [Class Constructor Methods](#class-constructor-methods)
* [Nullability Annotations](#nullability-annotations)
* [Overriding Swift Names for Objective-C Interfaces](#overriding-swift-names-for-objective-c-interfaces)
* [Availability Checking](#availability-checking)
* [Lightweight Generics](#lightweight-generics)
* [Dot-Notation Syntax](#dot-notation-syntax)
* [Literals](#literals)
* [Enumerated Types](#enumerated-types)
* [Booleans](#booleans)
* [Conditionals](#conditionals)
    * [Ternary Operator](#ternary-operator)
    * [Switch-Statement](#switch-statement)
* [CGRect Functions](#cgrect-functions)
* [Golden Path](#golden-path)
* [Error handling](#error-handling)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)
* [Comments](#comments)
    * [Documentation Comments](#documentation-comments)
* [Tips and Tricks](#tips-and-tricks)
    * [String Constants](#string-constants)
    * [Modify Readonly Properties in Categories](#modify-readonly-properties-in-categories)
    * [Private Methods in Categories](#private-methods-in-categories)
* [Other Objective-C Style Guide](#other-objective-c-style-guides)


# Language

US English should be used.

**Preferred:**
```objc
UIColor *myColor = [UIColor whiteColor];
```

**Not Preferred:**
```objc
UIColor *myColour = [UIColor whiteColor];
```


# Code Organization

Use `#pragma mark -` and to categorize methods in functional groupings and protocol/delegate implementations following this general structure.

Put 77 character `/` before `#pragma mark -` to highlight a section in code. Leave two blank lines before this line.

Leave one blank line under `#pragma mark -`

```objc
#pragma mark - Lifecycle

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}


////////////////////////////////////////////////////////////////////////////
#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}


////////////////////////////////////////////////////////////////////////////
#pragma mark - IBActions

- (IBAction)submitData:(id)sender {}


////////////////////////////////////////////////////////////////////////////
#pragma mark - Public

- (void)publicMethod {}


////////////////////////////////////////////////////////////////////////////
#pragma mark - Private

- (void)privateMethod {}


////////////////////////////////////////////////////////////////////////////
#pragma mark - Protocol conformance


////////////////////////////////////////////////////////////////////////////
#pragma mark - UITextFieldDelegate


////////////////////////////////////////////////////////////////////////////
#pragma mark - UITableViewDataSource


////////////////////////////////////////////////////////////////////////////
#pragma mark - UITableViewDelegate


////////////////////////////////////////////////////////////////////////////
#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}


////////////////////////////////////////////////////////////////////////////
#pragma mark - NSObject

- (NSString *)description {}
```


# Header Files


Use `@class` directive extensively where appropriate to avoid redundant header files import

**Preferred:**
```objc
@class Song

@interface PlayList : NSObject
@property (nonull, readonly, strong, nonatomic) NSArray<Song *> songs;
@end
```

**Not Preferred:**
```objc
#import "Song.h"

@interface PlayList : NSObject
@property (nonull, readonly, strong, nonatomic) NSArray<Song *> songs;
@end
```


## Import Order

The necessary system frameworks are imported in the file `<headerfile>.h` for `<headerfile>` object.

Categories should be put in separate files for better code management and code maitenance. Category header files are named as `<headerfile>+Category.h`.

Un-categories header files should be name as `<headerfile>_Foo.h`, where `Foo` should convey the meaning of the file.

Below is the file hierarchy - starting from a `.m` file at the bottom, each file imports the one above it.

* MyClass:
    * MyClas.h - Public API (Application Programming Interface)
    * MyClas_Exclusive.h - Company-internal SPI (System Programming Interface)
    * MyClass_Internal.h - Project-internal IPI (Internal Programming Interface)
    * MyClass.m - Implementation, generally of all API/SPI/IPI declarations
* MyClass+Category:
    * MyClass.h - Public API
    * MyClass+Category.h - Category API
    * MyClass+Category.m - Category implementation

Class extension (Annonymous category) `@interface MyClass ()` should always be put in the implementation file `MyClass.m`.

In case the class extension interface needed to be exposed to be used for example the category implementation or testing, the file naming convention is `MyClass_Private.h`. The hierarchy of header files - starting from a `.m` file at the bottom, each file imports the one above it, is as follow:

* MyClass:
    * MyClas.h - Public API
    * MyClass_Private.h - Class extension
    * MyClas_Exclusive.h - Company-internal SPI
    * MyClass_Internal.h - Project-internal IPI
    * MyClass.m - Implementation, generally of all API/SPI/IPI declarations

For `MyClass+Category`, `MyClass_Private.h` import `MyClass.h` but does not import the file `MyClass+Category.h`.

* MyClass+Category:
    * MyClass.h - Public API
    * MyClass+Category.h - Category API
    * MyClass_Private.h - Class extension (not import `MyClass+Category.h`)
    * MyClass+Category.m - Category implementation

It is easily to see that that `MyClass_Private.h` and `MyClass+Category.h` both import `MyClass.h`. It is impossible to avoid duplicate import here because category needs public API to work. Since directive `#import` will ignore the files that are already imported, duplicate import is not an issue. We follow the convention to eliminate by ourselves duplicate imports as much as possible not depending on the power of `#import`. Nonetheless, when it is unavoidable, the directive will come to secure.

There are other header files that should be imported in `MyClass.m`:

* Project Files: Controllers
* Project Files: View Controllers
* Project Files: Models
* Project Files: View Objects
* Project Files: Other

Application Programming Interface - API is for everyone to use and is publicly supported (usually in Foo.framework/Headers).

System Programming Interface - SPI exposes additional functionality for internal clients of the code, with the understanding that support may be limited and the interface is subject to change (usually in Foo.framework/PrivateHeaders).

Internal Programming Interface consists of details that should never be used outside the project itself and these headers are not included in the framework at all.


# Spacing

Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.

Braces except methods (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**Preferred:**
```objc
if (user.isHappy) {
  // Do something
} else {
  // Do something else
}
```

**Not Preferred:**
```objc
if (user.isHappy)
{
    // Do something
}
else {
    // Do something else
}
```

There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.

Prefer using auto-synthesis. But if necessary, `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.


# Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Preferred:**
```objc
UIButton *settingsButton;
```

**Not Preferred:**
```objc
UIButton *setBut;
```

A three letter prefix should always be used for class names and constants, however may be omitted for Core Data entity names.

Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**Preferred:**
```objc
static NSTimeInterval const RWTTutorialViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not Preferred:**
```objc
static NSTimeInterval const fadetime = 1.7;
```

Properties should be camel-case with the leading word being lowercase.

**Preferred:**
```objc
@property (strong, nonatomic) NSString *descriptiveVariableName;
```

**Not Preferred:**
```objc
id varnm;
```

## Synthesize

Use auto-synthesis for properties rather than manual `@synthesize` statements unless you have good reason.

When you intends to use backing instance variable (i.e. _variableName) you need to explicitly decare it using `@synthesize` statements even though it is often implicitly generted by `Clang` compiler. This helps other developers in tracking your code.

**Preferred:**
```objc
@implementation

@synthesize name = _name;

- (instancetype)initWithName:(NSString *)name
{
    _name = name;
}

@end
```

**Not Preferred:**
```objc
@implementation

/* no @synthesize statments */
- (instancetype)initWithName:(NSString *)name
{
    _name = name;
}

@end
```

## Underscores

When using properties in the `Class` implementation, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`.

An exception to this: inside initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters, the backing instance variable (i.e. _variableName) could be used directly to avoid any potential side effects of the getters/setters.

Local variables should not contain underscores.

# Nullability Annotations

For better interoperability with `Swift` (to avoid implicitly unwrapped optional`!`):

* using nullability annotations `nonnull`, `nullable`, `null_unsepcified` and `null_resettable` for property attribute.
* using nullability annotations `_Nonnull`, `_Nullable` and `_Null_unspecified` in the following situations:
    * for pointer types after asterisk (`*`) in method arguments
    * block pointers after the caret (`^`)
    * before `id` type

**Preferred:**
```objc
@interface MyClass : NSObject

@property (readonly, strong, nonatomic, nonnull) NSArray *locations;
@property (readonly, strong, nonatomic, nullable) Location *latestLocation;

- (NSString *_Nullable)resultWithString:(NSString *_Nullable)string;
- (NSString *_Nullable)findByName:(NSString *_Nonnull)name;
- (void)compute:(NSError *_Nullable *_Nullable)error;
- (void)doSomething:(NSError *_Nullable *_Nonnull)error;
- (void)convertObject:(_Nonnull id (^_Nullable)(void))handler;
- (void)handleCompletion:(_Nonnull id (^_Nonnull)(void))handler;

@end
```

**Not Preferred:**
```objc
@interface MyClass : NSObject

@property (readonly, strong, nonatomic) NSArray *locations;
@property (readonly, strong, nonatomic) Location *latestLocation;

- (NSString *)resultWithString:(NSString *)string;
- (NSString *)findByName:(NSString *)name;
- (void)compute:(NSError **)error;
- (void)doSomething:(NSError **)error;
- (void)convertObject:(id (^)(void))handler;
- (void)handleCompletion:(id (^)(void))handler;

@end
```

Use macro to help streamline the process of annotating existing classes. Mark the beginning and end of a section of your header file `*.h` with `NS_ASSUME_NONNULL_BEGIN` and `NS_ASSUME_NONNULL_END`, then mark the exceptions.

Complex pointers MUST be annotated even with the use of macro for clarity.

```objc
// Previous example using marcro NS_ASSUME_NONNUL_BEGIN/END

NS_ASSUME_NONNULL_BEGIN

@interface MyClass : NSObject

@property (readonly, nonatomic) NSArray *locations;
@property (nullable, readonly, nonatomic) Location *latestLocation;

- (NSString *_Nullable)resultWithString:(NSString * _Nullable)string;
- (NSString *_Nullable)findByName:(NSString *)name;
- (void)compute:(NSError *_Nullable * _Nullable)error;
- (void)doSomething:(NSError *_Nullable *_Nonnull)error;
- (void)convertObject:(_Nonnull id (^_Nullable)(void))handler;
- (void)handleCompletion:(_Nonnull id (^)(void))handler;

@end

NS_ASSUME_NONNULL_END
```

For more information about nullability annotations see [Nullability and Objective-C](https://developer.apple.com/swift/blog/?id=25) and [NSHipster Swift 1.2](http://nshipster.com/swift-1.2/).


# Overriding Swift Names for Objective-C Interfaces

* The Swift compiler imiport Objective-C class factory methods as Swift  initializers, and Objective-C enumeration cases truncated names. Therefore, use
`NS_SWIFT_NAME` macro to customize how a declaration is imported. Additionally,
the macro also helps to rename the class more Swifty.

```objc
NS_SWIFT_NAME(DatabseReference)
@interface FIRDatabaseReference : FIRDatabseQuery
@end

+ (instancetype)recordWithRPM:(NSUInteger)RPM NS_SWIFT_NAME(init(rpm:));
+ (id)recordWithQuality:(double)quality NS_SWIFT_NAME(record(quality:));

typedef NS_ENUM(NSInteger, ABCRecordSide) {
    ABCRecordSideA,
    ABCRecordSideB NS_SWIFT_NAME(flipSide)
}
```



# Availability Checking

The new objective-c expression `@available` should be used to perfrom system versio checking at runtime. An if statement with an @available condition can be used to guard uses of newer APIs.

```objc
if (@available(iOS 11, macOS 10.13, *)) {
    // new APIs from iOS 11 and macOS 10.13 here
}

// calling this method requires availability checking
@available (iOS 11)
- (NSString *)newMethod:(NSString *)str
{

}

```

For more information about availability checking see [What's New in LLVM - 2017](https://developer.apple.com/videos/play/wwdc2017/411/).


# Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, except in the case of constants.

**Preferred:**
```objc
NSString *text;
NSString *fileName;
```

**Not Preferred:**
```objc
NSString* text;
NSString * fileName;
```

[Private properties](#private-properties) should be used in place of instance variables whenever possible. Although using instance variables is a valid way of doing things, by agreeing to prefer properties our code will be more consistent.

Direct access to instance variables that 'back' properties should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

## Variable Qualifiers

The variable qualifiers [introduced with ARC](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifier (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`, `__block`) should be placed after the asterisk of the pointer pointing to the object.

**Preferred:**
```objc
Car * __weak weakCar;                   // weakCar is a weak reference to Car object
NSString * __block text;                // text has block scope
Fraction * __autoreleasing *fractions;  // fractions is a pointer to an autoreleasing reference to Fraction object
```

**Not Preferred:**
```objc
__weak Car *weakCar;
__block NSString *text;
__autoreleasing Fraction **fractions;
```

## Property Attributes

Property attributes should be explicitly listed, and will help new programmers when reading the code. The order of properties should be accessibility, storage, atomicity then nullability annotations which is consistent with automatically generated code when connecting UI elements from Interface Builder.

**Preferred:**
```objc
@property (readwrite, weak, nonatomic, nonnull) IBOutlet UIView *containerView;
@property (readwrite, strong, nonatomic, nonnull) NSString *tutorialName;
```

**Not Preferred:**
```objc
@property (nonnull, nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic, strong, nonnull) NSString *tutorialName;
```

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong` if accompaning with the attribute `readwrite`. Why? Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.

**Preferred:**
```objc
@property (readwrite, copy, nonatomic) NSString *tutorialName;
```

**Not Preferred:**
```objc
@property (readwrite, strong, nonatomic) NSString *tutorialName;
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories should never be used unless extending another class. The anonymous category can be shared/exposed for testing using the `<headerfile>_Private.h` file naming convention see [Header Files](#header-files).

**For Example:**
```objc
@interface RWTDetailViewController ()

@property (readwrite, strong, nonatomic) GADBannerView *googleAdView;
@property (readwrite, strong, nonatomic) ADBannerView *iAdView;
@property (readwrite, strong, nonatomic) UIWebView *adXWebView;

@end
```

## Platform Independent

Using platform independent type such as `NSInteger`, `NSUInteger`, `CGFloat` instead of int, unsigned int and float.

**Preferred:**
```objc
NSInteger count;
NSUInteger age;
CGFloat circumference;
```

**Not Preferred:**
```objc
int count;
unsigned int age;
float circumference;
```


# Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro or as helpers of `static` constants.

**Preferred:**
```objc
static NSString * const RWTAboutViewControllerCompanyName = @"RayWenderlich.com";

static const CGFloat RWTImageThumbnailHeight = 50.0;
```

**Not Preferred:**
```objc
#define CompanyName @"RayWenderlich.com"

#define thumbnailHeight 2
```
Constants accessible outside an implementation file should be declared using `FOUNDATION_EXPORT`

**Example**
```objc
// in header file
FOUNDATION_EXPORT NSString * const XXXBaseURL;

// in implementation file
NSString * const XXXBaseURL = @"http://someapp-staging.herokuapp.com/api/";
```

`FOUNDATION_EXPORT` is used instead of `extern` because `FOUNDATION_EXPORT` will compile to a language specific designation (`extern` in C), making it more compatible and safer to use.

If you put `static` in a header file, you are probably doing it wrong.


# Methods

In method signatures, there should be a space after the method type (-/+ symbol). There should be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Preferred:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**
```objc
-(void)setT:(NSString *)text i:(UIImage *)image;                        // Method not descriptive and no space between - and (void)
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;            // Method not descriptive
- (id)taggedView:(NSInteger)tag;                                        // Method not descriptive
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height; // Method contains word 'and'
- (instancetype)initWith:(int)width and:(int)height;                    // Method not descriptive and contains word 'and'
```

Method braces always open on the new line after the statement.

**Preferred:**
```objc
- (instancetype) initWithFirstName:(NSString *)firstName lastName:(NSString *)lastName
{
    // Do something
}
```

**Not Preferred:**
```objc
- (instancetype) initWithFirstName:(NSString *)firstName lastName:(NSString *)lastName {
    // Do something
}
```

Colon-aligning method invocation should often be avoided. There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable.

**Preferred:**
```objc
NSDecimalNumberHandler *roundPlain;

roundPlain = [NSDecimalNumber decimalNumberHandlerWithRoundingMode:NSRoundPlain
                                                             scale:1
                                                  raiseOnExactness:NO
                                                   raiseOnOverflow:NO
                                                  raiseONUnderflow:NO
                                               raiseOnDivideByZero:YES];
```

**Not Preferred:**
```objc
roundPlain = [NSDecimalNumber decimalNumberHandlerWithRoundingMode:NSRoundPlain scale:1 raiseOnExactness:NO raiseOnOverflow:NO raiseONUnderflow:NO raiseOnDivideByZero:YES];
```

Colon-aligning **MUST NOT** however be used for methods containing blocks because Xcode's indenting makes it illegible.

**Preferred:**
```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^ {
  // something
} completion:^(BOOL finished) {
  // something
}];
```

**Not Preferred:**
```objc
// colon-aligning makes the block indentation hard to read
[UIView animateWithDuration:1.0
                 animations:^ {
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```

## Init Methods

Init methods should follow the convention to make sure the class return nil if the parent class can not be initialized.  A return type of 'instancetype' should also be used instead of 'id'.

```objc
- (instancetype)init
  {
    self = [super init];
    if (self) {
        // ...
    }
    return self;
  }
```

> See [Class Constructor Methods](#class-constructor-methods) for link to article on instancetype.

## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type.

```objc
@interface Airplane
+ (instancetype)airplaneWithType:(RWTAirplaneType)type;
@end
```

> More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).


# Lightweight Generics

Lightweight generics are encouraged to use extensively to make the program more robust and less error prones.

```objc
NSArray<Song *> *songs;                 // songs accept only song object
NSArray<__kindof Animal *> *animals;    // animals accept only object of type Animal or Animal's subclass
```


# Dot-Notation Syntax

Dot syntax is purely a convenient wrapper around accessor method calls. When you use dot syntax, the property is still accessed or changed using getter and setter methods.  Read more [here](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html)

Since dot-notation can be used to chain with structure, it is recommended to use.

Dot-notation should be used inside the `@implementation` part for `self`.

**Preferred:**
```objc
NSUInteger arrayCount = self.array.count;
NSUInteger length = self.subStringRange.length;

for (Song *song in self.songs) {
    if ([song.title containsString:searchString]) { // song is likely an object of type Song
        ...
    }
}

view.backgroundColor = [UIColor orangeColor];
[[UIApplication sharedApplication] delegate];
myView.subview[0].bacgroundColor = [UIColor orangeColor] // subview is an array

-(id) copyWithZone:(NSZone *)zone
{
    AddressBook *newBook = [[AddressBook allocWithZone:zone] init];

    newBook.bookName = self.bookName;
    newBook.book = self.book;

    return newBook;
}
```

**Not Preferred:**
```objc
NSInteger arrayCount = [self.array count];              // count is a property
NSUInteger length = [self subStringRange].length        // looks inconsistent

for (Song *song in self.songs) {                        // song is likely an object of type Song
    if ([[song title] containsString:searchString]) {   // -> use dot to reduce braces
        ...
    }
}

[view setBackgroundColor:[UIColor orangeColor]];    // many braces
[UIApplication sharedApplication].delegate;         // looks inconsistent
[[[myView subview] objectAtIndex:0] setBacgroundColor:[UIColor orangeColor]]    // many braces

-(id) copyWithZone:(NSZone *)zone
{
    AddressBook *newBook = [[AddressBook allocWithZone:zone] init];

    newBook->_bookName = _bookName;
    newBook->_book = _book;

    return newBook;
}
```

Be aware in some special situation, the dot-notation will not work as expected.

```objc
NSURL *btnSoundURL = [[NSURL alloc] initWIthFileURLWithPath:[[NSBundle mainBundle] pathForResource:@"Tock" ofType:@"aif"]];
// the following line will not work - error: address of property expression requested
AudioServicesCreateSystemSoundId((__bridge CFURLRef))btnSoundRUL, &self.btnSound)
// the following line will work
AudioServicesCreateSystemSoundId((__bridge CFURLRef))btnSoundRUL, &self->_btnSound)

```


# Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values can not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Preferred:**
```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
```

**Not Preferred:**
```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
```


# Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`

**For Example:**
```objc
typedef NS_ENUM(NSInteger, RWTLeftMenuTopItemType) {
  RWTLeftMenuTopItemMain,
  RWTLeftMenuTopItemShows,
  RWTLeftMenuTopItemSchedule
};
```

You can also make explicit value assignments (showing older k-style constant definition):

```objc
typedef NS_ENUM(NSInteger, RWTGlobalConstants) {
  RWTPinSizeMin = 1,
  RWTPinSizeMax = 5,
  RWTPinCountMin = 100,
  RWTPinCountMax = 500,
};
```

Older k-style constant definitions should be **avoided** unless writing CoreFoundation C code (unlikely).

**Not Preferred:**
```objc
enum GlobalConstants {
  kMaxPinSize = 5,
  kMaxPinCount = 500,
};
```


# Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should only be used for CoreFoundation, C or C++ code.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Preferred:**
```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Not Preferred:**
```objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).


# Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**Preferred:**
```objc
if (!error) {
  return success;
}
```

**Not Preferred:**
```objc
if (!error)
  return success;
```
or
```objc
if (!error) return success;
```

## Ternary Operator

The Ternary operator, `?:` , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an `if` statement, or refactored into instance variables. In general, the best use of the ternary operator is during assignment of a variable and deciding which value to use.

Non-boolean variables should be compared against something, and parentheses are added for improved readability.  If the variable being compared is a boolean type, then no parentheses are needed.

**Preferred:**
```objc
NSInteger value = 5;
result = (value != 0) ? x : y;

BOOL isHorizontal = YES;
result = isHorizontal ? x : y;
```

**Not Preferred:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Switch Statement

Braces are not required for switch statements, unless enforced by the compiler. When a case contains more than one line, braces should be added.

```objc
switch (condition) {
  case 1:
    // ...
    break;
  case 2: {
    // ...
    // Multi-line example using braces
    break;
  }
  case 3:
    // ...
    break;
  default:
    // ...
    break;
}
```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

```objc
switch (condition) {
  case 1:
    // ** fall-through! **
  case 2:
    // code executed for values 1 and 2
    break;
  default:
    // ...
    break;
}

```

When using an enumerated type for a switch, 'default' is not needed.   For example:

```objc
RWTLeftMenuTopItemType menuType = RWTLeftMenuTopItemMain;

switch (menuType) {
  case RWTLeftMenuTopItemMain:
    // ...
    break;
  case RWTLeftMenuTopItemShows:
    // ...
    break;
  case RWTLeftMenuTopItemSchedule:
    // ...
    break;
}
```


# CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [CGGeometry functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

* All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Preferred:**
```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**Not Preferred:**
```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```


# Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Preferred:**
```objc
- (void)someMethod {
  if (![someOther boolValue]) {
    return;
  }

  //Do something important
}
```

**Not Preferred:**
```objc
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```


# Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Preferred:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
```

**Not Preferred:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).


# Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.

```objc
+ (instancetype)sharedInstance {
  static id sharedInstance = nil;

  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    sharedInstance = [[self alloc] init];
  });

  return sharedInstance;
}
```

> This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).


# Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).


# Comments (need to add more annotations)

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. *Exception: This does not apply to those comments used to generate documentation.*

## Documentation Comments

The comments for documentation is mostly produced in the header file `*.h`.

The comments must be end with **period** except for `@name`.

The documentation for **enumeration** should be as follows:

```objc
/** These options apply to the various print fraction options */
 typedef NS_OPTIONS(NSInteger, FractionPrintOptions) {
    /** Print fraction in reducible form */
    FractionInReducibleForm = 1,
    /** Print fraction in mixed form */
    FractionInMixedForm = 2
 };
```

The documentation for **property** should be as follows:

```objc
// short property comment
/** The numerator of a fraction. */
 @property (assign, readonly, nonatomic) NSInterger numerator;

 // long property comment
 /**
  The security used by create session to evaluate server trust for secure connection. It uses the default session to set up this.
  */
@property (strong, nonatomic) Security *security
```

The documentation for **mehthod** should be as follows:

```objc
/**
 Add a fraction.

 @param otherFraction The other fraction to be added.

 @return A fraction after substracting.
 */
 - (instancetype)add:(Fraction *)otherFraction;
```

For apple doc, three foward slashes`///` should be used with @name to categorize group of methods or emphasize property. **Remember to keep it consitent with `#pragma mark -` in the implementation section.**

```objc

/// ---------------------------
/// @name Arithmetic Operations
/// ---------------------------

/**
 Add a fraction.

 @param otherFraction The other fraction to be added.

 @return A fraction after adding.
 */
 - (instancetype)add:(Fraction *)otherFraction;

/**
 Substract a fraction.

 @param otherFraction The other fraction to be substracted.

 @return A fraction after substracting.
 */
 - (instancetype)substract:(Fraction *)otherFraction;

 // other codes

/// ------------------------
/// @name Utility Operations
/// ------------------------

// other codes
```

Some noticeble commenting directive: `@warning` `@availability`...


# Tips and Tricks

## String Constants

C99 compiliers, including Objective-C compilers, combine a run of adjacent string literals into a single string literal. This can be done by using `#define` directive to reduce repetition in sets of string constants, such as API paths.

**Consider:**
```objc
static NSString * const APIPathCurrentUserFetch = @"v1/user/";
static NSString * const APIPathFacebookLoginRegister = @"v1/user/facebook";
static NSString * const APIPathSignOut = @"v1/user/logout";
static NSString * const APIPathTerms = @"v1/text/terms";
static NSString * const APIPathPrivacy = @"v1/text/privacy";
static NSString * const APIPathNotificationRegister = @"v1/notifications/token";
static NSString * const APIPathNotificationsPreferences = @"v1/notifications/preferences";
```

If the `v1` at the beginning of those paths ever needed to change, it would have to be changed in every single line

Using `#define` to declare the building blocks, then put them together to make the `static NSString *constant` value

**Better Approach:**
```objc
#define APIPathComponentBase @"v1/"
#define APIPathComponentUser APIPathComponentBase @"user/"

static NSString * const APIPathCurrentUserFetch = APIPathComponentUser;
static NSString * const APIPathFacebookLoginRegister = APIPathComponentUser @"facebook";
static NSString * const APIPathSignOut = APIPathComponentUser @"logout";

#define APIPathComponentText APIPathComponentBase @"text/"

static NSString * const APIPathTerms = APIPathComponentText @"terms";
static NSString * const APIPathPrivacy = APIPathComponentText @"privacy";

#define APIPathComponentNotifications APIPathComponentBase @"notifications/"

static NSString * const APIPathNotificationRegister = APIPathComponentNotifications @"token";
static NSString * const APIPathNotificationsPreferences = APIPathComponentNotifications @"preferences";preferences";
```

Now, `v1` is only in one place. Changing it there changes it for every API path.

When the preprocessor runs, the `#define` constants are replaced with their string vlaues so that, for example, `APIPathNotificationPreferences` becomes:

```objc
static NSString * const APIPathNotificationPreferences = @"v1/" @"notifications/" @"preferences";
```

The compiler then combines the adjacent string literal so that `APIPathNotificationPreferences` is equal to `@"v1/notifications/preferences"`

## Modify Readonly Properties in Categories


Sometimes, a category needs to modify the properties with attribute `readonly` of a class. It is advisable to create class extension in the category and change the attribute of the property to `readwrite`.

```objc
// In MyClass.h

@interface MyClass

@property (readonly, strong, nonatomic) UIButton *button;
@property (readonly, assign, nonatomic) BOOL isSelected;

@end

// In MyClass+Helpers.m

@interface MyClass ()

@property (readwrite, strong, nonatomic) UIButton *button;
@property (readwrite, assign, nonatomic) BOOL isSelected;

@end

```

## Private Methods in Categories

Somtimes, a category needs to have its own private methods. It can be achieved by creating another category in the `.m` file.

```objc
// In MyClass+Helpers.m

@interace MyClass (Helpers_Private)

// code

@end


////////////////////////////////////////////////////////////////////////////
#pragma mark -

@implementation MyClass (Helpers)

// code

@end


////////////////////////////////////////////////////////////////////////////
#pragma mark -

@implementation MyClass (Helpers_Private)

// code

@end

```


# Other Objective-C Style Guides

If mine doesn't fit your tastes, have a look at some other style guides:

* [Robots & Pencils](https://github.com/RobotsAndPencils/objective-c-style-guide)
* [New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
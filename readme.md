C++ style guide
---------------

This is the style guide for (modern) C++ programming that I have found to be
most useful and idiomatic. It is an organic document and will, in all
likelihood, continue to grow with time. I appreciate and welcome any
contributions you may have insofar as they provide relevant new material that
broadly aligns with the goals of this document.

## contents

* [goals and non-goals](#goals)
* [a broad overview of this document's philosophy](#philosophy)
* [non-code-specific rules](#non-code-specific)
    - [compilation](#compilation)
    - [portability](#portability)
    - [undefined behavior](#undefined-behavior)
* [style-ish concerns](#style-ish-concerns)
* [inline functions, macros, and constexpr](#inline-macros-constexpr)
* [namespaces]()

## <a name="goals"></a>goals and non-goals

Firstly, this document is not meant to be a competitor to other style guides
such as the [C++ Core Guidelines][] or the [Google C++ Style Guide][] Instead,
it aims to document the rules and guidelines that I have found to be most useful
in my own work. Moreover, those documents (especially the former) have a much
wider target audience than does this document. One the one hand this is good
because it allows me to be more opinionated on certain matters that would
otherwise go unmentioned. On the other hand, I cannot help but wish that
everyone would write code exactly as I do; such is life, I suppose.

That said, there are parts of this document which are written in-part as
reactions to other style-guides. If you're familiar with any of the above they
may stick-out at you.

Second, this document partly serves as a means to further educate myself about
the C++ language. I hope that you too can find some usefulness in it.

Last, this document has little to do with naming and spacing conventions beyond
a brief explanation of what I adhere to in my own code.

[C++ Core Guidelines]: https://github.com/isocpp/CppCoreGuidelines
[Google C++ Style Guide]: https://google.github.io/styleguide/cppguide.html

## <a name="philosphy"></a>a broad overview of this document's philosophy

In general you will find that this document aims to improve code-correctness
through the use of appropriate C++ language features -- this is my main concern
when writing software. It is a sort-of mathematical formalism that underpins
many of the rules herein, and may be summarized as a method of writing software
by careful accumulation of provably correct abstractions (where "provably" does
not necessarily mean formal proof, but instead mainly refers to provability to
ourselves and those who read our code). As such, a deep understanding of the
semantics of C++ language features is needed and explanations with reference to
the standards documents will be provided where appropriate.

When used correctly many C++ language features allow us to exclude invalid
program states with relative ease. This not only benefits ourselves when writing
new code, but also benefits our users and those who later read our code
(ourselves, for instance). Most of the time these benefits derive from
leveraging the type system as much as possible. As such, many of the rules
presented herein involve the type system in one way or another.

You will also find a common theme of trusting the standard and trusting the
compiler. However, this does not mean _naively_ trusting. You should, if you are
ever unsure, consult either the standard or you compiler's documentation for
clarification. Furthermore, when in doubt, TEST! First-hand evidence is not only
more likely to convince you of something, but is also less-likely to be wrong
than a poorly written explanation of a feature or bug on an online forum
(provided whatever you are testing does not rely on undefined behavior or
non-portable code -- see [portability](#portability) and
[undefined behavior](#undefined-behavior)).

And, finally, just like the Zen of Python, in C++ we should:

- Value code's aesthetic quality.
- Value explicit constructs over implicit tricks.
- Value simplicity over complexity.
- Value complexity over complication.
- Value readability.

With modern C++ all of these are possible, and we should strive to meet each of
those goals.

## <a name="non-code-specific"></a>non-code-specific rules

These are rules not directly tied to code.

### <a name="compilation"></a>compilation

#### rule: compile against the ISO Standard

Projects should be built against `c++11`, `c++14`, or (soon) `c++17`. On Clang
and GCC this is done with the `-std=c++XX` flag. Avoid using non-standard
dialects and language extensions whenever possible (unless you will always be
using a particular toolchain to target a particular platform where availability
of the dialect and/or extension is guaranteed).

#### rule: compile with warnings

For new projects code should always be built with as many warnings enabled as
possible. Of course this is more difficult to begin doing with legacy code
bases, but is to be encouraged if at all possible. Likewise, for some embedded
systems development it is difficult, if not impossible, to avoid certain
warning-inducing constructs (i.e., int-to-pointer casts and other "unsafe"
conversions).

At a minimum you should use `-Wall` and `-Wextra`.

Use of `-Werror` is also encouraged. Warnings exist for a reason, and this will
force you to address them immediately. Similarly, use of `-Wpendantic`,
`-pedantic`, and `-pendantic-errors` is also encouraged.

Here are lists of additional warning flags for GCC and Clang that I tend to use
on my projects in addition to `-Wall` and `-Werror`. Availability depends on
which toolchain (and version) you are using. The following are from the GCC
documentation. Most, but not all will be available with Clang.

```
-Wall (which turns on:)
    -Waddress, -Warray-bounds=1, -Wbool-compare, -Wc++11-compat,-Wc++14-compat
    -Wchar-subscripts, -Wcomment, -Wenum-compare, -Wformat,
    -Wint-in-bool-context, -Winit-self, -Wlogical-not-parentheses,
    -Wmaybe-uninitialized, -Wmemset-elt-size, -Wmemset-transposed-args,
    -Wmisleading-indentation, -Wnarrowing, -Wnonnull, -Wnonnull-compare,
    -Wopenmp-simd, -Wparentheses, -Wpointer-sign, -Wreorder, -Wreturn-type,
    -Wsequence-point, -Wsign-compare -Wsizeof-pointer-memaccess,
    -Wstrict-aliasing, -Wstrict-overflow=1, -Wswitch, -Wtautological-compare,
    -Wtrigraphs, -Wuninitialized, -Wunkown-pragmas, -Wunused-function,
    -Wunused-label, -Wunused-value, -Wunused-variable, -Wvolatile-register-var

-Wextra (which turns on:)
    -Wclobbered, -Wempty-body, -Wignored-qualifiers,
    -Wmissing-field-initializers, -Woverride-init, -Wtype-limits,
    -Wshift-negative-value, -Wunused-parameter, -Wunused-but-set-parameter

-Wnull-dereference (requires -fdelete-null-pointer-checks)
-Wignored-attributes
-Wmissing-braces
-Wmissing-include-dirs
-Wsequence-point
-Wswitch-default
-Wswitch-bool
-Wswitch-unreachable
-Wunused-local-typedefs
-Wunused-result
-Wsuggest-final-types
-Wsuggest-final-methods
-Wsuggest-override
-Wduplicate-cond
-Wtrampolines
-Wfloat-equal
-Wshadow
-Wunsafe-loop-optimizations
-Waligned-new
-Wplacement-new={1 or 2}
-Wpointer-arith
-Wcast-qual
-Wcast-align
-Wwrite-strings
-Wconversion
-Wsign-conversion
-Wzero-as-null-pointer-constant
-Wuseless-cast
-Wlogical-op
-Wpacked
-Wpadded
-Wredundant-decls
-Winline
-Wvla
-Wdissabled-optimization
```

The warnings concerning undefined behavior and disabled optimizations are
especially useful.

#### rule: (if available) run debug builds with `-fsanitize=undefined`

If you are using GCC or Clang, depending on the version, you may have access to
the `-fsanitize=undefined` instrumentation facilities. This flag (along with
other specific instrumentation flags) will link instrumentation binaries into
your executable that will output diagnostics as your program runs if U.B. is
detected.

#### rule: (if available) run debug builds with `-fsanitize=address`

If you are using GCC or Clang, depending on the version, you may have access to
the `-fsanitize=address` instrumentation facilities. This flag (along with
other specific instrumentation flags) will link instrumentation binaries into
your executable that will output diagnostics as your program runs when address
space violations are encountered.

### <a name="portability"></a>portability

The build against the standard rule above applies equally well here. Using the
standard dialects and avoiding language extensions will ensure that your code
remains portable across platforms.

Besides this, you should strive to write you code in a platform independent
manner. This means avoiding inline assembly, system specific headers, and
other constructs that are not explicitly part of the language whenever possible.
Fortunately, the modern C++ standards provide many useful features for doing so.

#### rule: use `<thread>`, `<atomic>` and friends over non-portable threading facilities

This applies to all the types and methods in the `<thread>`, `<future>`,
`<mutex>`, and `<condition_variable>` headers (i.e., the thread support library)
and the `<atomic>` header (i.e., the atomic operations library).

#### rule: (as of C++17) use `<filesystem>`, `std::fstream` and friends over system-specific APIs

The upcoming filesystem library provides a platform-independent alternative for
manipulating hierarchical filesystem objects. In conjunction with the
`std::{i,o}fstream` objects in the input/output library we have a fully-fledged,
portable method for working with the filesystem.

### <a name="undefined behavior"></a>undefined behavior

While many people lament the fact that undefined behavior exists at all, it is a
part of the language that we must deal with, for better or for worse. U.B. is
intimately tied-up with portability issues (across compilers, especially), and
should be avoided at all costs. This is why use of warnings and diagnostics for
undefined behavior is encouraged.

## <a name="style-ish-concerns"></a>style-ish concerns

This is the only place where I will discuss naming and spacing conventions.
The general rule-of thumb is: do as the standard does. Also, a disclaimer: here
is where I'm allowing myself to be very opinionated.

#### rule: use `lower_snake_case` everywhere, except for template parameters and macros

Namespaces, functions, types, enum values, etc. should all be written in
`lower_snake_case`. Don't use `mixedCase` or `CamelCase` (frankly, I find both
unreadable).

The C++ Standard Library uses `lower_snake_case`, and so should you for
consistency.

```c++
// good: this is a C++ type
class important_resource;

// good: this is a C++ enumeration
enum class important_resource_state
{
    open,
    closed,
    unavailable
};

// good: this is a C++ function
important_resource make_resource (void);
```

Don't use `mixedCase` or `CamelCase`.

```c++
// bad: we're not writing Java, so don't do this
class WidgetFactoryFactory;

// or this
enum WidgetFactoryFactoryState
{
    OKAY,
    ERROR
};

// or this
WidgetFactoryFactory MakeWidgetFactoryFactory (void);
```

```c++
// bad: we're also not writing Haskell, so don't do this either
class Shape;

// or this
enum ShapeType
{
    Circle,
    Triangle,
    Rectangle
};

// or this
Shape makeShape (enum ShapeType);
```

#### rule: macros should be `UPPER_SNAKE_CASE`

For the same reason as above, `UPPER_SNAKE_CASE` should be used for macros.

```c++
// good: this is a macro
#if [some platform is being used]
    #define PROJECT_HAS_COOL_PLATFORM_SUPPORT (true)
#else
    #define PROJECT_HAS_COOL_PLATFORM_SUPPORT (false)
#endif
```

#### rule: read and write types from right-to-left; also separate `*` and `&`

The right-to-left rule respects the order in which pointer and reference types
are written, so for consistency types should always be written and read from
right-to-left. Also, pointer and reference qualifiers should be separated from
`const` and `volatile` qualifiers as well as type names.

```c++
// good: x is a const integer
int const x = 0;

// good: xp is a const pointer to const interger
int const * const xp = &x;

// good: xr is a reference to const integer
int const & xr = x;

// good: xpr is a reference to a const pointer to a const integer
int const * const & xpr = xp;
```

This also respects the left-right-left rule for array types

```c++
// good: xarr is reference to an array of 10 const integers
int const (& xarr) [10];

// good: do_something is a function that takes an reference to an M by N array
// of const T and returns the reference to the M by N array.
template <typename T, std::size_t M, std::size_t N>
T (& do_something (T (&) [M][N])) [M][N];

// better: but you should prefer trailing return types anyways
template <typename T, std::size_t M, std::size_t N>
auto do_something (T (&) [M][N]) -> T (&) [M][N];
```

#### rule: separate identifiers from parentheses, brackets, and angle-brackets

For readability, parentheses, brackets, and angle-brackets should not be placed
directly adjacent to identifiers. Prefer separation by whitespace except in the
case of function arguments.

```c++
// good
int foo (int i);

auto f = foo (1);

template <typename T>
T bar (T const & t);

template <>
int bar <int> (int const & i);

int bar_param = 1;
auto bar_result = bar (bar_param);

// bad
int baz( int i );

auto b = baz( 1 );

template<typename U>
U qux(U&& u);

template<>
float qux(float&& f);

float qux_param = 1.0;
auto qux_result = qux( qux_param );
```

## <a name="inline-macros-constexpr"></a>inline functions, macros, and constexpr

#### rule: don't mark functions as `inline` unless you have sufficient evidence

Unless you have overwhelming evidence from profiling that inlining a function
call will improve performance, do not mark functions as inline. The compiler is
usually better equipped to deciding what functions to inline than you are, so
let it do it's job. Moreover, the `inline` keyword is only a hint to the
compiler, not a directive.

#### rule: don't use macros as a substitute for functions

Function-like macros should be avoided. Prefer using `constexpr`-qualified
free-functions.

```c++
// good: use a constexpr-qualified free function
template <typename T>
constexpr T max (T a, T b)
{
    return a > b ? a : b;
}

// later on...
auto max_value = max (a++, b); // okay, behaves as expected

// bad: function-like macros can be easily misused
#defined MAX(a, b) (a) > (b) ? (a) : (b)

// later on...
auto max_value = MAX(a++, b); // whoops! this is not what we wanted
```

#### rule: use `constexpr` instead of `#define` for constants

Preprocessor `#define`s can conflict with macros from other included header
files, and do not respect namespace boundaries. Therefore, `constexpr` variables
should be preferred for constants and placed in appropriate namespaces.

#### rule: as of C++17 use `constexpr if` in place of preprocessor `#if`, `#else`, and `#endif`

```c++
// hidden away in .hpp file
constexpr bool cool_platform (void)
{
#if [some cool platform is being used]
    return true;
#else
    return false;
#endif
}

// good: select what to do using constexpr if in .cpp file
decltype (auto) do_cool_thing (void)
{
    constexpr if (cool_platform ()) {
        return // invoke cool platform method...
    } else {
        return // invoke not-as-cool generic fallback...
    }
}

// bad: selection with macros inside .cpp file
decltype (auto) do_cool_thing (void)
{
#if [some cool platform is being used]
    return // invoke cool platform method...
#else
    return // invoke not-as-cool generic fallback
#endif
}
```

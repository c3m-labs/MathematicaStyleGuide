# Mathematica Style Guide

## Table of Contents

1. [Introduction](#Introduction)
2. [Code layout](#Code-layout)
3. [Naming conventions](#Naming-conventions)
4. [Constructs](#Constructs)
5. [Package example](#Package-example)
6. [References](#References)

## Introduction

The purpose conventions presented here is to improve readability of the code and make it easier to maintain and add new features.

While there are many ways to achieve something in code, some approaches are better than others.
The most important rule of coding style is to be consistent; first within a function, then within a file and the whole project.
When applying these guidelines to existing projects use common sense and do not break backward compatibility.

## Code layout

### Indentation

Use tabs for indentation, one tab per level.

Commas (delimiting parts of expressions) and semicolons (delimiting expressions) should be placed at the end of line.

Short functions can be written in one line.

```mathematica
abs[x_]:=If[x<0,-x,x]
```

Break longer functions over multiple lines, with named options in separate lines.

```mathematica
besselPlot[noCurves_Integer]:=Plot[
    Evaluate@Table[BesselJ[n,x],{n,noCurves}],
    {x,0,10},
    Filling->Axis,
    AxesLabel->{"x","y"}
]
```

### Line length

Recommended upper limit of line length is 100 characters.
This makes it possible to have several files open side-by-side on screen.

Try to make functions shorter than 40 lines (approximately the height of computer screen).

### Blank lines

Each (full) function definition is surrounded by two blank lines.
Definition of messages and options for the same symbol is separated by one blank line.
These rules preserve nice formating of package when it is opened in Mathematica FrontEnd.
Use blank lines inside functions, sparingly, to indicate logical sections.

Use postfix syntax (`//`) for `Options` or `SyntaxInformation` to get nicely vertically aligned symbol names.

```mathematica
(* ::Subsubsection:: *)
(*Function definitions*)


foo::usage="foo[x] does something with x.";

foo//Options={Tolerance->0.1};

foo[x_,OptionsPattern[]]:=Round[x,OptionValue[Tolerance]]


bar[x_,y_]:=x+y
```

### Other recommendations

If packages are edited in Mathematica FrontEnd, then all code should be written in `"Code"` style of cells.
Do not use `"Input"` style with `InitializationCell->True` option.

Use of function shortcuts (e.g. `/@`) is recommended,
especially where expression fits in a line and no additional parenthesis are required.
Otherwise explicit `Map` with standard convention for indenting and line breaks should be used.

Always use explicit symbol `*` to denote multiplication with `Times`.

Use `Prefix` and `Postfix` notation for better readability of chained function calls,
for example instead of `f[g[h[x]]]`, write `f@g@h[x]`. This avoids piling up of brackets on on the end of expression.

### File format and naming

Use `.wl` extension for packages.

Follow the conventions so that package files can be loaded with `Needs`.

File and directory names should start with capital letter.
Use only letters and numbers in their names and avoid other special characters (e.g. `_` or `-` ) and spaces.

## Naming conventions

Use [CamelCase](https://en.wikipedia.org/wiki/Camel_case) for all symbols and strings.

### Symbol names

Symbols defined during interactive work in notebooks (``Global` `` context) should start with lower case.

Symbols defined in package intended for other users should start with upper case if they are public (``MyPackage` `` context).
Names should be meaningful, fully spelt out and descriptive.
Symbols that act as flags or control some global aspect of the program should start with character `$`.
Built-in examples of such symbols are `$UserBaseDirectory`, `$HistoryLength` or `$Language`.

Symbols in package that belong to private subcontext (``MyPackage`Private` `` context) should start with lower case.
Local symbols in scoping constructs (`Module`, `With`, `SetDelayed`, etc.) should start with lower case letter.

```mathematica
foo[myParameter_Real]:=Module[
    {random},
    random=RandomReal[];

    myParameter+random
]
```

Shorter names and abbreviations are acceptable for private functions
or local symbols, especially if their use doesn't span over many lines.
One letter symbols are acceptable only if they are used right after their definition.

```mathematica
symmetricMatrix[n_Integer?Positive]:=With[
    {m=RandomInteger[{0,9},{n,n}]},

    m+Transpose[m]-DiagonalMatrix[Diagonal[m]]
]
```

Iterators in `Table`, `Do`, `Sum`, etc. can be one letter symbols, preferably `i`, `j` and `k`.
For plotting functions use one (or few) letter symbols for coordinates like `x`, `y`, `z`, `r`, `fi`, `th`.

Non-ascii characters (e.g. greek letters) are allowed in special circumstances,
like established mathematical formulas, but should be avoided if possible.

### Option names

Option names should be descriptive strings starting with upper case letter.
Exception to this are documented built-in general purpose options which are symbols like `Method`, `Tolerance`, `PerformanceGoal`, etc.

```mathematica
Foo//Options={"MagicNumber"->42,Tolerance->10^-3};

Foo[x_,OptionsPattern[]]:={x,OptionValue["MagicNumber"],OptionValue[Tolerance]}
```

## Constructs

### Comments

Comments that contradict the code are worse than no comments.
Always make a priority of keeping the comments up-to-date when the code changes!

Leave one whitespace between opening and closing comment characters and comment content.
Exception to this are automatically generated comments that come from text section names.

```mathematica
(* Good: One space before and after the comment. *)

(*Bad: no spaces*)
```

Do not leave commented out code in the packages.
Implement (working) alternative formulations as an function option or
use version control system branching functionality to explore alternatives.

Comments with special formatting (e.g. `(* ::Subsubsection:: *)`) are needed for
Mathematica FrontEnd to render file section headings.
The following is an example of simple package file with two headings.

```mathematica
(* ::Package:: *)

(* ::Section:: *)
(*This is a section name*)


(* ::Subsection::Closed:: *)
(*Content description*)


(* ::Text:: *)
(*This is formatted text.*)
```

### Programming style

Use functional programming wherever possible, because this makes it easier to test and debug separate parts of the code in isolation.
Functions should get all information as their arguments and return results as their output, without modifying values of any other symbols.

### Error reporting

Use `Message` mechanism instead of `Print` for reporting warning and error messages.
Messages are superior, because they can be (selectively) suppressed, formatted and rerouted to a file.

In case of error, function should not return `Null`, but preferably `$Failed` or similar, while issuing some informative `Message`.
Do not use `Abort[]` except for really critical errors.
It completely stops evaluation, even when procedure could continue on alternative path.

### Documentation

Each public function should have usage message with standard formatting
which enables FrontEnd autocomplete template.
Example: `Foo::usage="Foo[par1,par2] does something with par1 and par2."`.
Usage messages are recommended also for non-trivial functions/symbols in private context.

Tags in `MessageName` should be short keywords in lower case.
Text of messages should be strings written in `InputForm` without any special 2D formatting.

Longer function description or discussion of mathematical background can
be given as one `"Text"` cell before function definition.
Avoid complex 2D formatting in such text.

## Package example

This is an example of package with proper formating.

```mathematica
(* ::Package:: *)

(* ::Section:: *)
(*Header*)


(* :Title: MyPackage *)
(* :Context: MyPackage` *)
(* :Author: John Smith *)
(* :Summary: ... *)
(* :Copyright: C3M d.o.o., 2019 *)


(* ::Section:: *)
(*Begin package*)


(* Mathematica FEM functionality (context "NDSolve`FEM`") is needed. *)
BeginPackage["MyPackage`",{"NDSolve`FEM`"}];


(* ::Subsection:: *)
(*Public symbols*)


(* Symbols appearing here will be available on $ContextPath. *)
Foo;


(* ::Section:: *)
(*Code*)


(* Begin private context *)
Begin["`Private`"];


(* ::Subsection:: *)
(*Discussion on implementation*)


(* ::Text:: *)
(*Lorem Ipsum is simply dummy text of the printing and typesetting industry. *)
(*Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, *)
(*when an unknown printer took a galley of type and scrambled it to make a type specimen book.*)


(* ::Subsection:: *)
(*Helper functions*)


(* Helper function in private context. Starts with lower case. *)
bar[x_,y_,magic_]:=x+y+magic


(* ::Subsection:: *)
(*Main function*)


Foo::usage="Foo[x,y] does something with numbers x and y.";
Foo::badmagic="Magic number `1` should be a positive number.";

Foo//Options={"MagicNumber"->Automatic};

Foo//SyntaxInformation={"ArgumentsPattern"->{_,_,OptionsPattern[]}};

Foo[x_?NumberQ,y_?NumberQ,opts:OptionsPattern[]]:=Module[
    {magic},

    magic=OptionValue["MagicNumber"]/.Automatic->42;

    If[
        Not@TrueQ@Positive[magic],
        Message[Foo::badmagic,magic];Return[$Failed,Module]
    ];

    bar[x,y,magic]
]


(* ::Section:: *)
(*End package*)


End[]; (* "`Private`" *)


EndPackage[];
```

## References

* [M.SE: General strategies to write big code in Mathematica?](https://mathematica.stackexchange.com/questions/109888)
* [M.SE: Mathematica style guide?](https://mathematica.stackexchange.com/questions/72669)
* [Style guide for Python code](https://www.python.org/dev/peps/pep-0008/)
* [Google style guides](https://google.github.io/styleguide/)

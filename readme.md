<!--lint disable no-html-->

# [![esast][logo]][site]

**E**CMA**S**cript **A**bstract **S**yntax **T**ree format.

***

**esast** is a specification for representing [JavaScript][] as an abstract
[syntax tree][syntax-tree].
It implements the **[unist][]** spec.

This document may not be released.
See [releases][] for released documents.
The latest released version is [`1.0.0`][latest].

## Contents

* [Introduction](#introduction)
  * [Where this specification fits](#where-this-specification-fits)
  * [ESTree](#estree)
* [Nodes](#nodes)
  * [`Node`](#node)
  * [`RegExpLiteral`](#regexpliteral)
  * [`BigIntLiteral`](#bigintliteral)
* [Recommendations](#recommendations)
* [Glossary](#glossary)
* [List of utilities](#list-of-utilities)
* [References](#references)
* [Security](#security)
* [Related](#related)
* [Contribute](#contribute)
* [Acknowledgments](#acknowledgments)
* [License](#license)

## Introduction

This document defines a format for representing ECMAScript as an [abstract
syntax tree][syntax-tree].
Development of esast started in February 2021.
This specification is written in a [Web IDL][webidl]-like grammar.

### Where this specification fits

esast extends [unist][],
a format for syntax trees,
to benefit from its [ecosystem of utilities][utilities].
There is one important difference with other implementations of unist: children
are added at fields other than the `children` array and the `children` field is
not used.

esast relates to [ESTree][] in that the first is a superset of the latter.
Any tool that accepts an ESTree also supports esast.

esast relates to [JavaScript][],
other than that it represents it,
in that it has an [ecosystem of utilities][list-of-utilities] for working with
compliantsyntax trees in JavaScript.
However,
esast is not limited to JavaScript and can be used in other programming
languages.

esast relates to the [unified][] project in that esast syntax trees are used
throughout its ecosystem.

### ESTree

ESTree is great but it is missing some things:

* trees can’t be roundtripped through `JSON.parse(JSON.stringify(s))`,
  leading to cache troubles
* columns are 0-indexed,
  whereas most text editors display 1-indexed columns,
  leading to a tiny discrepancy or some math to display warnings
* there is no recommendation for range-based positional info,
  leading implementations to scatter them in different places
* there is no safe space for metadata,
  leading implementations to scatter them in different places
* there are no recommendations for how to handle JSX,
  comments,
  or raw values

These are minor nits,
which is why esast is a superset.

## Nodes

### `Node`

```idl
extend interface Node <: UnistNode {}
```

All esast nodes inherit from unist’s [Node][unist-node] and are otherwise the
same as their ESTree counterparts,
with the exception of `RegExpLiteral` and `BigIntLiteral`.

### `RegExpLiteral`

The `regex` field on
[`RegExpLiteral`](https://github.com/estree/estree/blob/master/es5.md#regexpliteral)
must be used whereas the `value` field of such literals should be `null` and
must be ignored.

### `BigIntLiteral`

The `bigint` field on
[`BigIntLiteral`](https://github.com/estree/estree/blob/master/es2020.md#bigintliteral)
must be used whereas the `value` field of such literals should be `null` and
must be ignored.

## Recommendations

For JSX,
follow the
[JSX extension](https://github.com/facebook/jsx/blob/master/AST.md)
maintained in `facebook/jsx`.

For type annotations,
follow the
[Type annotations extension](https://github.com/estree/estree/blob/master/extensions/type-annotations.md)
maintained in `estree/estree`.

`raw` fields (added by most parsers) should not be used: they create an extra
source of truth,
which is often not maintained.

`start`,
`end`,
and `range` fields should not be used.

`comments` should not be added on nodes other that `Program`.
When adding comments,
use the `'Block'` (for `/**/`) or `'Line'` (for `//`) types.
Do not use `leading` or `trailing` fields on comment nodes.

`tokens` should not be used.

## Glossary

See the [unist glossary][glossary] but note of the following deviating terms.

###### Child

Node X is **child** of node Y,
if X is either referenced directly or referenced in an array at a field on
node Y.

###### Sibling

Node X is a **sibling** of node Y,
if X and Y have the same parent (if any) and X and Y are both referenced in an
array at a field on node Y.

## List of utilities

See the [unist list of utilities][utilities] for more utilities.

* [`estree-util-attach-comments`](https://github.com/syntax-tree/estree-util-attach-comments)
  — attach comments to estree nodes
* [`estree-util-build-jsx`](https://github.com/syntax-tree/estree-util-build-jsx)
  — transform JSX to function calls
* [`estree-util-is-identifier-name`](https://github.com/syntax-tree/estree-util-is-identifier-name)
  — check if something can be an identifier name
* [`estree-util-value-to-estree`](https://github.com/remcohaszing/estree-util-value-to-estree)
  — convert a JavaScript value to an estree expression
* [`estree-util-to-js`](https://github.com/syntax-tree/estree-util-to-js)
  — serialize as JavaScript
* [`estree-util-visit`](https://github.com/syntax-tree/estree-util-visit)
  — visit nodes
* [`esast-util-from-estree`](https://github.com/syntax-tree/esast-util-from-estree)
  — transform from estree
* [`esast-util-from-js`](https://github.com/syntax-tree/esast-util-from-js)
  — parse from JavaScript

Please use either `estree-util-` (if it works with all ESTrees,
preferred)
or `esast-util-` (if it uses on esast specific features) as a prefix.

See also the [`estree`](https://github.com/search?q=topic%3Aestree\&s=stars\&o=desc)
topic on GitHub.

## References

* **unist**:
  [Universal Syntax Tree][unist].
  T. Wormer; et al.
* **JavaScript**:
  [ECMAScript Language Specification][javascript].
  Ecma International.
* **JSON**
  [The JavaScript Object Notation (JSON) Data Interchange Format][json],
  T. Bray.
  IETF.
* **Web IDL**:
  [Web IDL][webidl],
  C. McCormack.
  W3C.

## Security

As esast represents JS,
and JS can open you up to a bunch of problems,
esast is also unsafe.
Always be careful with user input.

## Related

* [hast](https://github.com/syntax-tree/hast) — HTML
* [mdast](https://github.com/syntax-tree/mdast) — Markdown
* [nlcst](https://github.com/syntax-tree/nlcst) — Natural language
* [xast](https://github.com/syntax-tree/xast) — XML

## Contribute

See [`contributing.md`][contributing] in [`syntax-tree/.github`][health] for
ways to get started.
See [`support.md`][support] for ways to get help.
Ideas for new utilities and tools can be posted in [`syntax-tree/ideas`][ideas].

A curated list of awesome `syntax-tree`,
unist,
mdast,
esast,
xast,
and nlcst resources can be found in [awesome syntax-tree][awesome].

This project has a [code of conduct][coc].
By interacting with this repository,
organization,
or community you agree to abide by its terms.

## Acknowledgments

The initial release of this project was authored by
**[@wooorm](https://github.com/wooorm)**.

## License

[CC-BY-4.0][license] © [Titus Wormer][author]

<!-- Definitions -->

[health]: https://github.com/syntax-tree/.github

[contributing]: https://github.com/syntax-tree/.github/blob/HEAD/contributing.md

[support]: https://github.com/syntax-tree/.github/blob/HEAD/support.md

[coc]: https://github.com/syntax-tree/.github/blob/HEAD/code-of-conduct.md

[awesome]: https://github.com/syntax-tree/awesome-syntax-tree

[ideas]: https://github.com/syntax-tree/ideas

[license]: https://creativecommons.org/licenses/by/4.0/

[author]: https://wooorm.com

[logo]: https://raw.githubusercontent.com/syntax-tree/esast/0164416/logo.svg?sanitize=true

[site]: https://unifiedjs.com

[releases]: https://github.com/syntax-tree/esast/releases

[latest]: https://github.com/syntax-tree/esast/releases/tag/2.3.0

[list-of-utilities]: #list-of-utilities

[unist]: https://github.com/syntax-tree/unist

[syntax-tree]: https://github.com/syntax-tree/unist#syntax-tree

[javascript]: https://www.ecma-international.org/ecma-262/9.0/index.html

[json]: https://tools.ietf.org/html/rfc7159

[webidl]: https://heycam.github.io/webidl/

[glossary]: https://github.com/syntax-tree/unist#glossary

[utilities]: https://github.com/syntax-tree/unist#list-of-utilities

[unified]: https://github.com/unifiedjs/unified

[estree]: https://github.com/estree/estree

[unist-node]: https://github.com/syntax-tree/unist#node

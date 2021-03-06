# svg-path-segments

[![NPM version][npm-version-image]][npm-link]
[![Tests][tests-image]][tests-link]
[![Coverage status][coverage-image]][coverage-link]

A fast SVG path parser implementation for Javascript. Does not perform checks
on input data, but allows to rebuild original path segments from the result.
To avoid errors, check first that your paths are made up of ASCII characters
supported by the [SVG 1.1 specification][svg11-spec-link].

[![License][license-image]][license-link]

## Install

```bash
npm install svg-path-segments
```

## Documentation

### Public API

```js
const parsePath = require("svg-path-segments");

const segments = parsePath("M5 6 7 8l3 4z");
console.log(JSON.stringify(segments, null, 2));
```

```json
[
  {
    "start": 0,
    "end": 4,
    "params": [
      "M",
      5,
      6
    ],
    "abs": true,
    "chained": true,
    "chainStart": 0,
    "chainEnd": 8
  },
  {
    "start": 5,
    "end": 8,
    "params": [
      "M",
      7,
      8
    ],
    "abs": true,
    "chained": true,
    "chainStart": 0,
    "chainEnd": 8
  },
  {
    "start": 8,
    "end": 12,
    "params": [
      "l",
      3,
      4
    ],
    "abs": false,
    "chained": false
  },
  {
    "start": 12,
    "end": 13,
    "params": [
      "z"
    ],
    "abs": false,
    "chained": false
  }
]
```

### CLI

```bash
svg-path-segments --pretty "M5 6 7 8l3 4z"
```

### Reference

<a name="svgPathParse" href="#svgPathParse">#</a> <b>svgPathParse</b>(<i>d</i>)
⇒ `Array`

- **d** (string) SVG path.

Returns the segments of the SVG path. The result is an array of objects, one
per segment, which contain next properties:

- **`start`** (*Integer*): Index of the first character of the segment.
- **`end`** (*Integer*): Index of the first character after the segment. Note
 that you can use `d.substring(result.start, result.end)` to get the raw string
 representation of the segment.
- **`params`** (*Array*): Parameters of the segment. The first parameter always
 is the command that draws the segment.
- **`abs`** (*Boolean*): Indicates that the coordinates of the segment are
 absolute.
- **`chained`** (*Boolean*): Indicates that the segment is part of a chained
 set of segments. If this property is `true`, the object will also contain the
 properties `chainStart` and `chainEnd`.
- **`chainStart`** (optional, *Integer*): Index of the first character of the
 chained set of segments to which the segment belongs.
- **`chainEnd`** (optional, *Integer*): Index of the first character after the
 chained set of segments to which the segment belongs. Note that you can use
 `d.substring(result.chainStart, result.chainEnd)` to get the raw string
  representation of the chained set of segments to which the segment belongs.

### Comparison against other implementations

|  | svg-path-segments | svgpath |
|---|---|---|
| Require | `require("svg-path-segments")` | `require("svgpath/lib/path_parse")` |
| Benchmark\* | ~46ms | ~76ms |
| Absolutes | YES | NO |
| Segments indexing | YES | NO |
| Chained commands | YES | NO |
| Catmull-Rom curve commands (`R`/`r`) | NO | YES |
| Bearing commands (`B`/`b`) | NO | NO |
| First `m` converted to `M` | NO | YES |
| Chained `M`/`m` converted to `L`/`l` | NO | YES |
| Check path should start with `m`/`M` | NO | YES |
| Check bad command characters | NO | YES |
| Check missing parameters | NO | YES |
| Check leading zeros in numbers | NO | YES |
| Check arc flag `0` or `1` | NO | YES |
| Check invalid float exponents | NO | YES |
| Unicode support | NO | PARTIAL |

> \* Benchmarks are orientative, if you want to perform your own, run the script
  [scripts/simple-icons-benchmark.js][si-benchmark-link].

### Usage with [fontello/svgpath](https://github.com/fontello/svgpath)

```js
const svgpath = require("svgpath");
const parsePath = require("svg-path-segments");

const segments = parsePath(iconPath);
const SVGPath = svgpath("");
SVGPath.segments = segments.map(segment => segment.params);
```

[npm-link]: https://www.npmjs.com/package/svg-path-segments
[npm-version-image]: https://img.shields.io/npm/v/svg-path-segments?logo=NPM
[tests-image]: https://img.shields.io/github/workflow/status/mondeja/svg-path-segments/CI?logo=github
[tests-link]: https://github.com/mondeja/svg-path-segments/actions?query=workflow%3ACI
[coverage-image]: https://img.shields.io/coveralls/github/mondeja/svg-path-segments?logo=coveralls
[coverage-link]: https://coveralls.io/github/mondeja/svg-path-segments?branch=master
[license-image]: https://img.shields.io/npm/l/svg-path-segments?color=brightgreen
[license-link]: https://github.com/mondeja/svg-path-segments/blob/master/LICENSE
[si-benchmark-link]: https://github.com/mondeja/svg-path-segments/blob/master/scripts/simple-icons-benchmark.js
[svg11-spec-link]: https://www.w3.org/TR/SVG11/paths.html

# js-rouge

A JavaScript implementation of the Recall-Oriented Understudy for Gisting Evaluation (ROUGE) evaluation metric for summaries. This package implements the following metrics:

- n-gram (ROUGE-N)
- Longest Common Subsequence (ROUGE-L)
- Skip Bigram (ROUGE-S)

> **Note**: This is a fork of [the original ROUGE.js](https://github.com/kenlimmj/rouge) by kenlimmj. This fork adds TypeScript types and other improvements.

## Rationale

ROUGE is somewhat a standard metric for evaluating the performance of auto-summarization algorithms. However, with the exception of [MEAD](http://www.summarization.com/mead/) (which is written in Perl. Yes. Perl.), requesting a copy of ROUGE to work with requires one to navigate a barely functional [webpage](http://www.isi.edu/licensed-sw/see/rouge/), fill up [forms](http://www.berouge.com/Pages/DownloadROUGE.aspx), and sign a legal release somewhere along the way while at it. These definitely exist for good reason, but it gets irritating when all one wishes to do is benchmark an algorithm.

Nevertheless, the [paper](http://www.aclweb.org/anthology/W04-1013) describing ROUGE is available for public consumption. The appropriate course of action is then to convert the equations in the paper to a more user-friendly format, which takes the form of the present repository. So there. No more forms. See how life could have been made a lot easier for everyone if we were all willing to stop writing legalese or making people click submit buttons?

## Quick Start

This package is available on NPM, like so:

```shell
npm install --save js-rouge
```

To use it, simply require the package:

```javascript
import rouge from 'js-rouge'; // ES2015+

// OR

const rouge = require('js-rouge'); // CommonJS
```

A small but growing number of tests exist. To run them:

```shell
npm test
```

This should give you many lines of colorful text in your CLI. Naturally, you'll need to have [Mocha](https://mochajs.org/) installed, but you knew that already.

**NOTE:** Function test coverage is 100%, but branch coverage numbers look horrible because the current testing implementation has no way of accounting for the additional code injected by [Babel](https://babeljs.io/) when transpiling from ES2015 to ES5. A fix is in the pipeline, but if anyone has anything good, feel free to PR!

## Usage

Rouge.js provides three functions:

- **ROUGE-N**: `rouge.n(candidate, reference, opts)`
- **ROUGE-L**: `rouge.l(candidate, reference, opts)`
- **ROUGE-S**: `rouge.s(candidate, reference, opts)`

All functions take in a candidate string, a reference string, and a configuration object specifying additional options. Documentation for the options is provided inline in `lib/rouge.js`. Type signatures are specified and checked using [TypeScript](https://www.typescriptlang.org/).

Here's an example evaluating ROUGE-L using an averaged-F1 score instead of the DUC-F1:

```javascript
import { l as rougeL } from 'js-rouge';

const reference = 'police killed the gunman';
const candidate = 'police kill the gunman';

const score = rougeL(candidate, reference, { beta: 0.5 });

// => 0.75
console.log('score:', score); 
```

In addition, the main functions rely on a battery of utility functions specified in `lib/utils.js`. These perform a bunch of things like quick evaluation of skip bigrams, string tokenization, sentence segmentation, and set intersections.

Here's an example applying jackknife resampling as described in the original paper:

```javascript
import { n as rougeN } from 'js-rouge';
import { jackKnife } from 'rouge/utils';

const reference = 'police killed the gunman';
const candidates = ['police kill the gunman', 'the gunman kill police', 'the gunman police killed'];

// Standard evaluation taking the arithmetic mean
jackKnife(candidates, reference, rougeN);

// A function that returns the max value in an array
const distMax = (arr) => Math.max(...arr);

// Modified evaluation taking the distribution maximum
jackKnife(candidates, reference, rougeN, distMax);
```

## Versioning

Development will be maintained under the Semantic Versioning guidelines as much as possible in order to ensure transparency and backwards compatibility.

Releases will be numbered with the following format:

`<major>.<minor>.<patch>`

And constructed with the following guidelines:

- Breaking backward compatibility bumps the major (and resets the minor and patch)
- New additions without breaking backward compatibility bump the minor (and resets the patch)
- Bug fixes and miscellaneous changes bump the patch

For more information on SemVer, visit http://semver.org/.

## Bug Tracking and Feature Requests

Have a bug or a feature request? [Please open a new issue](https://github.com/promptfoo/rouge/issues).

Before opening any issue, please search for existing issues and read the [Issue Guidelines](CONTRIBUTING.md).

## Contributing

Please submit all pull requests against \*-wip branches. All code should pass ESLint validation. Note that files in `/lib` are written in TypeScript syntax and transpiled to corresponding files in `/dist`. Gulp build pipelines exist and should be used.

The amount of data available for writing tests is unfortunately woefully inadequate. We've tried to be as thorough as possible, but that eliminates neither the possibility of nor existence of errors. The gold standard is the DUC data-set, but that too is form-walled and legal-release-walled, which is infuriating. If you have data in the form of a candidate summary, reference(s), and a verified ROUGE score you do not mind sharing, we would love to add that to the test harness.

## License

MIT

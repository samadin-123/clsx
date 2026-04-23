# Evaluation Setup

This file is outside the editable surface. It defines how results are judged. Agents cannot modify the evaluator or the scoring logic — the evaluation is the trust boundary.

Consider defining more than one evaluation criterion. Optimizing for a single number makes it easy to overfit and silently break other things. A secondary metric or sanity check helps keep the process honest.

eval_cores: 1
eval_memory_gb: 1.0
prereq_command: npm run build

## Setup

```bash
# Install root dependencies
npm install

# Install benchmark dependencies
cd bench && npm install && cd ..
```

This project requires:
- Node.js >= 6
- npm dependencies (esm, terser, uvu for tests)
- Benchmark dependencies (benchmark, classcat, classnames)

The build step (`npm run build`) compiles `src/index.js` and `src/lite.js` into the `dist/` directory using Terser for minification. The evaluation harness runs against the built output in `dist/clsx.js`.

## Run command

```bash
cd bench && node eval.js
```

## Output format

The benchmark prints `ops_per_sec=<number>` to stdout.

## Metric parsing

The CLI looks for `ops_per_sec=<number>` in the output.

## Ground truth

The metric represents the geometric mean of operations per second across seven benchmark scenarios:
1. Strings - variadic string arguments
2. Objects - object with boolean properties
3. Arrays - arrays of strings
4. Nested Arrays - deeply nested array structures
5. Nested Arrays w/ Objects - mixed nested arrays and objects
6. Mixed - combination of strings, objects, and arrays
7. Mixed (Bad Data) - handling undefined, null, NaN, functions, and other edge cases

Baseline measured on Node.js v25.9.0 on Linux 6.8.0-106-generic.

The evaluation harness (`bench/eval.js`) runs each scenario using the benchmark.js library and calculates the geometric mean of all results to produce a single aggregate performance metric. This provides a balanced view of performance across different usage patterns.

## Secondary checks

Correctness must be maintained:
```bash
npm test
```

All tests in `test/index.js`, `test/lite.js`, and `test/classnames.js` must pass.

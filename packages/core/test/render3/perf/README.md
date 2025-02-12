### Build

```
yarn bazel build //packages/core/test/render3/perf:${BENCHMARK}.min_debug.es2015.js --define=compile=aot
```

### Run 

```
node dist/bin/packages/core/test/render3/perf/${BENCHMARK}.min_debug.es2015.js
```

### Profile

```
node --no-turbo-inlining --inspect-brk dist/bin/packages/core/test/render3/perf/${BENCHMARK}.min_debug.es2015.js
```

then connect with a debugger (the `--inspect-brk` option will make sure that benchmark execution doesn't start until a debugger is connected and the code execution is manually resumed). 

The actual benchmark code has calls that will start (`console.profile`) and stop (`console.profileEnd`) a profiling session.

## Deoptigate

```
yarn add deoptigate
yarn deoptigate dist/bin/packages/core/test/render3/perf/${BENCHMARK}.min_debug.es2015.js
```

### Run All

To run all of the benchmarks use the `profile_all.js` script:
```
node packages/core/test/render3/perf/profile_all.js
```

NOTE: This command will build all of the tests, so there is no need to do so manually.

Optionally use the `--write` command to save the run result to a file for later comparison.


```
node packages/core/test/render3/perf/profile_all.js --write baseline.json
```

### Comparing Runs

If you have saved the baseline (as described in the step above) you can use it to get change in performance like so:

```
node packages/core/test/render3/perf/profile_all.js --read baseline.json
```

The resulting output should look something like this:
```
┌────────────────────────────────────┬─────────┬──────┬───────────┬───────────┬───────┐
│              (index)               │  time   │ unit │ base_time │ base_unit │   %   │
├────────────────────────────────────┼─────────┼──────┼───────────┼───────────┼───────┤
│       directive_instantiate        │ 276.652 │ 'ms' │  286.292  │   'ms'    │ -3.37 │
│        element_text_create         │ 262.868 │ 'ms' │  260.031  │   'ms'    │ 1.09  │
│           interpolation            │ 257.733 │ 'us' │  260.489  │   'us'    │ -1.06 │
│             listeners              │  1.997  │ 'us' │   1.985   │   'us'    │  0.6  │
│ map_based_style_and_class_bindings │  10.07  │ 'ms' │   9.786   │   'ms'    │  2.9  │
│       noop_change_detection        │ 93.256  │ 'us' │  91.745   │   'us'    │ 1.65  │
│          property_binding          │ 290.777 │ 'us' │  280.586  │   'us'    │ 3.63  │
│      property_binding_update       │ 588.545 │ 'us' │  583.334  │   'us'    │ 0.89  │
│      style_and_class_bindings      │  1.061  │ 'ms' │   1.047   │   'ms'    │ 1.34  │
│           style_binding            │ 543.841 │ 'us' │  545.385  │   'us'    │ -0.28 │
└────────────────────────────────────┴─────────┴──────┴───────────┴───────────┴───────┘
```

### Notes

In all the above commands `${BENCHMARK}` should be replaced with the actual benchmark (folder) name, ex.:
- build: `yarn bazel build //packages/core/test/render3/perf:noop_change_detection.min_debug.es2015.js --define=compile=aot`
- run: `time node dist/bin/packages/core/test/render3/perf/noop_change_detection.min_debug.es2015.js`
- profile: `node --no-turbo-inlining --inspect-brk dist/bin/packages/core/test/render3/perf/noop_change_detection.min_debug.es2015.js profile`
- experimenting `BENCHMARK=noop_change_detection; yarn bazel build //packages/core/test/render3/perf:${BENCHMARK}.min_debug.es2015.js --define=compile=aot && node dist/bin/packages/core/test/render3/perf/${BENCHMARK}.min_debug.es2015.js`

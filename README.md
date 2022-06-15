### Summary

When using Prettier v2.7's `--cache` flag, it is possible that files may become cached in such a way that they will
not be fixed. Specifically, calling Prettier with the `--list-different` and `--cache` flags in the same command will cause the
state of the file to be cached, such that a subsequent run of prettier with `--write` and `--cache` will not format the file.

### About this Reproduction

This reproduction contains the following files of interest:

```
.
├── index.js
└── package.json
```

#### `index.js`

this file contains a single 'hello world' `console.log()` statement. the statement does not have a semicolon at the end
of it, which should be added according to Prettier's default settings.

#### `package.json`

this file contains the output of running `npm init -y`. it was then updated to add prettier v2.7.1 as a dev dependency
(this can be reproduced with v2.7.0 as well)

### Steps to Reproduce

After cloning this repository, open a terminal and run Prettier with both the `list-different` and `cache` flags:

```
npx prettier . --list-different --cache
```

Observe that `index.js` is listed in the output.

Next, run Prettier to format the code:

```
npx prettier . --write --cache
```

Observe that `index.js` is cached:

```
index.js 1ms (cached)
package-lock.json 1ms (cached)
package.json 0ms (cached)
README.md 1ms (cached)
```

The `index.js` file will not be formatted (note lack of semicolon):

```
$ cat index.js
// note the lack of semicolon in this file, which should be added
// by Prettier's default settings
console.log('hello world')
```

### Considerations

It is possible that the best advice here is to _not_ run prettier with the `--list-different` _and_ `--cache` flags.
Omitting `--cache` clears the cache, so a call like `npx prettier . --list-different` won't cause any issues in that
respect.

### Environment

npm: v8.3.1
Node: v16.14.0
OS: MacOS v12.3.1

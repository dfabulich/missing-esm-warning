In this repository, there are two packages, `client` and `library`, where the `library` is intentionally buggy. I expected Node to show a warning, explaining how to fix the bug, but it doesn't.

In this scenario, let's imagine that the developer had intended `library` to be a dual-package library, containing both CJS and ESM. `library/package.json` says:

```js
  "exports": {
    "require": "./hello.js",
    "import": "./esm/hello.js"
  }
```

But, in this scenario, the developer made a mistake, because `esm/hello.js` contains ESM, but it's in a `.js` file, and there's no `"type": "module"` declaration in `package.json`.

If you run `node library/esm/hello.js` you get a nice clear warning explaining the problem:

```
% node library/esm/hello.js
(node:77065) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.
(Use `node --trace-warnings ...` to show where the warning was created)
/tmp/missing-esm-warning/library/esm/hello.js:1
export default "Hello ESM";
^^^^^^

SyntaxError: Unexpected token 'export'
```

But if you run `node client/index.mjs`, there's no warning. It just fails, without showing the warning.

```
% node client/index.mjs
/tmp/missing-esm-warning/library/esm/hello.js:1
export default "Hello ESM";
^^^^^^

SyntaxError: Unexpected token 'export'
```

I expected to see the "To load an ES module" warning in this case.
# Hyvä-Themes node-modules utility

Hyvä-themes TailwindCSS utility functions.

## Library module exports:

This Hyvä-themes TailwindCSS utility functions export the following function used in the following files:

| Function name              | Used in              | Description                                                                                                          |
| -------------------------- | -------------------- | -------------------------------------------------------------------------------------------------------------------- |
| [mergeTailwindConfig]      | `tailwind.config.js` | Function to merge tailwind config files from modules into the theme tailwind config                                  |
| [postcssImportHyvaModules] | `postcss.config.js`  | Postcss plugin to import `tailwind-source.css` files from modules into a theme                                       |
| [hyvaThemesConfig]         |                      | Parses the `app/etc/hyva-themes.json` file. This file is generated by the command `bin/magento hyva:config:generate` |
| [hyvaModuleDirs]           |                      | An array of absolute paths to Hyvä modules                                                                           |

[mergeTailwindConfig]: #merging-module-tailwind-configurations
[postcssImportHyvaModules]: #merging-module-tailwind-css
[hyvaThemesConfig]: #the-hyvathemesconfig-and-hyvamoduledirs-exports
[hyvaModuleDirs]: #the-hyvathemesconfig-and-hyvamoduledirs-exports

## How to use

Before using the library, install it inside your Hyvä-Theme folder using

```sh
npm install @hyva-themes/hyva-modules
```

> [!INFO]
> If you are using Hyvä 1.1.14 or newer it will already be included in the `package.json` file by default.

### Merging module tailwind configurations

To use the tailwind config merging, require this module in a themes' `tailwind.config.js` file and wrap the `module.exports` value in
a call to `mergeTailwindConfig()` like this:

```js
const { mergeTailwindConfig } = require('@hyva-themes/hyva-modules');

module.exports = mergeTailwindConfig({
  // theme tailwind config here ...
})
```

#### Specifying content (purge) paths

* **How do I specify relative purge paths content paths?**
  Specify the paths relative to the `view/frontend/tailwind/tailwind.config.js` file in the module, for example `../templates/**/*.phtml`
* **Do I use the TailwindCSS v2 or v3 purge content structure?**
  The script is smart enough to map a modules' purge config structure to the same one that is used in the theme.
  If you don't have any preference, then we suggest you use the v3 structure.

#### Using `require()`

When using `require` with a node library inside a modules' `view/frontend/tailwind/tailwind.config.js` file, prefix the
node module name the global variable `themeDirRequire`:
```js
const colors = require(`${themeDirRequire}/tailwindcss/colors`);
```

The global variable `themeDirRequire` maps to the `node_modules/` folder in the theme that is currently being built.

### Merging module tailwind CSS

To use the hyva-modules postcss plugin, require this library and add it as the first plugin in the themes'
`postcss.config.js` file. **Important:** The hyva-modules plugin has to go before the `require('postcss-import')` plugin!

```js
const { postcssImportHyvaModules } = require('@hyva-themes/hyva-modules');

module.exports = {
    plugins: [
        postcssImportHyvaModules,
        require('postcss-import'),
        ...other PostCSS plugins...
    ]
}
```

#### Creating a modules' `tailwind-source.css` file

To declare custom CSS, create a file `view/frontend/tailwind/tailwind-source.css` in a module.
This file will automatically be imported with `@import` at the end of a themes' `tailwind-source.css` file.

We recommend only using `@import` statements in a module `tailwind-source.css` file,
since other modules CSS files might be appended afterwards,
and `@import` statements are only allowed before regular CSS declarations.


### The `hyvaThemesConfig` and `hyvaModuleDirs` exports

These values are intended to be used in custom build tools and in future utilities.

## The `hyva-themes.json` configuration

This library uses the file `app/etc/hyva-themes.json` to know which modules might contain tailwind configuration or CSS to merge.
This file is generate by the CLI command `bin/magento hyva:config:generate`.

To add a module to the list, the Magento event `hyva_config_generate_before` can be used.

This happens automatically for Hyvä compatibility modules that register themselves with the `Hyva\CompatModuleFallback\Model\CompatModuleRegistry`.

The `bin/magento hyva:config:generate` command should be run as part of the build, before `npm run build-prod`.

## License

Hyvä Themes - https://hyva.io
Copyright © Hyvä Themes 2022-present. All rights reserved.
This library is distributed under the BSD-3-Clause license.

See the [LICENSE.md](LICENSE.md) file for more information.

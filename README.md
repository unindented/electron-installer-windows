# electron-installer-windows [![Version](https://img.shields.io/npm/v/electron-installer-windows.svg)](https://www.npmjs.com/package/electron-installer-windows) [![Build Status](https://img.shields.io/travis/unindented/electron-installer-windows.svg)](http://travis-ci.org/unindented/electron-installer-windows) [![Build Status](https://img.shields.io/appveyor/ci/unindented/electron-installer-windows.svg)](https://ci.appveyor.com/project/unindented/electron-installer-windows) [![Dependency Status](https://img.shields.io/gemnasium/unindented/electron-installer-windows.svg)](https://gemnasium.com/unindented/electron-installer-windows)

> Create a Windows package for your Electron app.


## Installation

For use from command-line:

```
$ npm install -g electron-installer-windows
```

For use in npm scripts or programmatically:

```
$ npm install --save-dev electron-installer-windows
```


## Usage

Say your Electron app lives in `path/to/app`, and has a structure like this:

```
.
├── LICENSE
├── README.md
├── node_modules
│   ├── electron-packager
│   └── electron-prebuilt
├── package.json
├── resources
│   ├── Icon.png
│   ├── IconTemplate.png
│   └── IconTemplate@2x.png
└── src
    ├── index.js
    ├── main
    │   └── index.js
    └── renderer
        ├── index.html
        └── index.js
```

You now run `electron-packager` to build the app for Windows:

```
$ electron-packager . app --platform win32 --arch x64 --out dist/
```

And you end up with something like this in your `dist` folder:

```
.
└── dist
    └── app-win32-x64
        ├── LICENSE
        ├── LICENSES.chromium.html
        ├── content_shell.pak
        ├── app
        ├── icudtl.dat
        ├── libgcrypt.so.11
        ├── libnode.so
        ├── locales
        ├── natives_blob.bin
        ├── resources
        ├── snapshot_blob.bin
        └── version
```

How do you turn that into a Windows package that your users can install?

### Command-Line

If you want to run `electron-installer-windows` straight from the command-line, install the package globally:

```
$ npm install -g electron-installer-windows
```

And point it to your built app:

```
$ electron-installer-windows --src dist/app-linux-x64/ --dest dist/installers/ --arch amd64
```

You'll end up with the package at `dist/installers/app_0.0.1_amd64.deb`.

### Scripts

If you want to run `electron-installer-windows` through npm, install the package locally:

```
$ npm install --save-dev electron-installer-windows
```

Edit the `scripts` section of your `package.json`:

```js
{
  "name": "app",
  "description": "An awesome app!",
  "version": "0.0.1",
  "scripts": {
    "start": "electron .",
    "build": "electron-packager . app --platform linux --arch x64 --out dist/",
    "deb64": "electron-installer-windows --src dist/app-linux-x64/ --dest dist/installers/ --arch amd64"
  },
  "devDependencies": {
    "electron-installer-windows": "*",
    "electron-packager": "*",
    "electron-prebuilt": "*"
  }
}
```

And run the script:

```
$ npm run deb64
```

You'll end up with the package at `dist/installers/app_0.0.1_amd64.deb`.

### Programmatically

Install the package locally:

```
$ npm install --save-dev electron-installer-windows
```

And write something like this:

```js
var installer = require('electron-installer-windows')

var options = {
  src: 'dist/app-linux-x64/',
  dest: 'dist/installers/',
  arch: 'amd64'
}

console.log('Creating package (this may take a while)')

installer(options, function (err) {
  if (err) {
    console.error(err, err.stack)
    process.exit(1)
  }

  console.log('Successfully created package at ' + options.dest)
})
```

You'll end up with the package at `dist/installers/app_0.0.1_amd64.deb`.

### Options

Even though you can pass most of these options through the command-line interface, it may be easier to create a configuration file:

```js
{
  "dest": "dist/installers/",
  "icon": "resources/Icon.png",
  "tags": [
    "Utility"
  ]
}
```

And pass that instead with the `config` option:

```
$ electron-installer-windows --src dist/app-linux-x64/ --arch amd64 --config config.json
```

Anyways, here's the full list of options:

#### src
Type: `String`
Default: `undefined`

Path to the folder that contains your built Electron application.

#### dest
Type: `String`
Default: `undefined`

Path to the folder that will contain your Windows installer.

#### rename
Type: `Function`
Default: `function (dest, src) { return path.join(dest, src); }`

Function that renames all files generated by the task just before putting them in your `dest` folder.

#### options.name
Type: `String`
Default: `package.name`

Name of the package (e.g. `atom`), used in the [`id` field of the `nuspec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.productName
Type: `String`
Default: `package.productName || package.name`

Name of the application (e.g. `Atom`), used in the [`title` field of the `nuspec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.description
Type: `String`
Default: `package.description`

Short description of the application, used in the [`summary` field of the `nuspec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.productDescription
Type: `String`
Default: `package.productDescription || package.description`

Long description of the application, used in the [`description` field of the `nuspec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.version
Type: `String`
Default: `package.version`

Long description of the application, used in the [`version` field of the `nuspec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.copyright
Type: `String`
Default: `package.copyright`

Copyright details for the package, used in the [`copyright` field of the `nuspec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.authors
Type: `Array[String]`
Default: `package.author`

List of authors of the package, used in the [`authors` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.owners
Type: `Array[String]`
Default: `package.author`

List of owners of the package, used in the [`authors` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.homepage
Type: `String`
Default: `package.homepage || package.author.url`

URL of the homepage for the package, used in the [`projectUrl` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.iconUrl
Type: `String`
Default: `undefined`

URL for the image to use as the icon for the package in the *Manage NuGet Packages* dialog box, used in the [`iconUrl` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.licenseUrl
Type: `String`
Default: `undefined`

URL for the license that the package is under, used in the [`licenseUrl` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.requireLicenseAcceptance
Type: `String`
Default: `false`

Whether the client needs to ensure that the package license (described by `licenseUrl`) is accepted before the package is installed, used in the [`requireLicenseAcceptance` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).

#### options.tags
Type: `Array[String]`
Default: `[]`

List of tags and keywords that describe the package, used in the [`tags` field of the `spec` file](https://docs.nuget.org/create/nuspec-reference).


## Maintenance

### Squirrel
Current version: 1.2.3

To update [Squirrel](https://github.com/Squirrel/Squirrel.Windows) to the latest version:

```
$ rm vendor/squirrel/*
$ mono vendor/nuget/NuGet.exe install squirrel.windows -OutputDirectory vendor/tmp/
$ cp -r vendor/tmp/squirrel.windows.1.2.3/tools/* vendor/squirrel/
$ rm -rf vendor/tmp/
```

### NuGet
Current version: 2.8.5

To update [NuGet](http://nuget.codeplex.com/) to the latest version, head over to the [releases page](http://nuget.codeplex.com/releases).


## Meta

* Code: `git clone git://github.com/unindented/electron-installer-windows.git`
* Home: <https://github.com/unindented/electron-installer-windows/>


## Contributors

* Daniel Perez Alvarez ([unindented@gmail.com](mailto:unindented@gmail.com))


## License

Copyright (c) 2016 Daniel Perez Alvarez ([unindented.org](https://unindented.org/)). This is free software, and may be redistributed under the terms specified in the LICENSE file.

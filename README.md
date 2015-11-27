# Closure Compiler plugin for webpack

## Prerequisites

- Java 7 or higher
- Build tools required by node-gyp for [your platform](https://github.com/nodejs/node-gyp#installation)
	- Hopefully with Node v4, this will soon not be needed
	- **Windows 10**
		- [Python 2.7.x](https://www.python.org/downloads/windows/)
			- In the installer, make sure to select the option to add to path
		- [Visual Studio Express 2015 for Windows Desktop](https://go.microsoft.com/fwlink/?LinkId=615464&clcid=0x409)
		- In **System &raquo; Advanced System Settings &raquo; Environment Variables** add a system variable named ```GYP_MSVS_VERSION``` with the value of ```2015```

## Installation

```
npm install closure-compiler-webpack-plugin
```

## Example

```javascript
// webpack.config.js
var ClosureCompilerPlugin = require('closure-compiler-webpack-plugin');

module.exports = {
	entry: {
		core: './core',
		home: './home'
	},
	output: {
		filename: '[name].min.js'
	},
	devtool: 'source-map',
	plugins: [
		new ClosureCompilerPlugin()
	]
};
```

## Options

Use Closure Compiler's flags.

```javascript
// webpack.config.js
...

	plugins: [
		new ClosureCompilerPlugin({
			compilation_level: 'ADVANCED_OPTIMIZATIONS',
			create_source_map: false
		})
	]

...
```

For a list of available options:

```
java -jar node_modules/closure-compiler-webpack-plugin/node_modules/google-closure-compiler/compiler.jar --help
```

## Shortcomings

Here are some notes, where this package could be improved. Feel free to submit PRs :)

- It was quite difficult to get source maps generated by Closure Compiler into a stream, since they aren't ouputted to stdout by default and the ```--create_source_map``` flag expects a filename. I've resorted to using named pipes on Unix(-like) and a temp file on Windows.
- For some reason, the end event from the Closure Compiler stdout stream for the output JS isn't being triggered. From my investigation, it doesn't seem to be connected to the Nailgun wrapper, but rather a problem of Closure Compiler itself. A pretty ugly h:shit:ck is used to work around this issue.
- A similar hack is used to end the source map output stream from a temp file on Windows.
- Unlike the built-in UglifyJsPlugin, this one is heavily stream/event based, so there is some state tracking involved to signal when the last file in the compilation was processed. A promise is used, but it still doesn't seem very pretty.

## Tested on

- OS X 10.10
- CentOS 6.5
- FreeBSD 9.2
- Windows 8.1
- Windows 10
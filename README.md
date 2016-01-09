# TypeScript and JavaScript lib generator

[![Build status](https://ci.appveyor.com/api/projects/status/8oj3j7u6nvag1xvu/branch/master?svg=true)](https://ci.appveyor.com/project/zhengbli/tsjs-lib-generator/branch/master)

This tool is used to generate `dom.generated.d.ts` and `webworker.generated.d.ts` for TypeScript, and `domWeb.js` and `domWindows.js` for Visual Studio JavaScript language service. The input file is the XML spec file generated by the Microsoft Edge browser.

## Build Instructions

To build the tool, simply clone this repo, and run `build.cmd` (Windows) or `build.sh` (OS X/Unix) on the command line.
If it runs successfully, the output files will be generated under the `generated` folder.

Note: for OS X and Unix users, [Mono 4.2](http://www.mono-project.com/download/alpha/) is required.

## Contribution Guidelines

The `dom.generated.d.ts` and `webworker.generated.d.ts` files in the TypeScript repo are used as baselines.
For each pull request, we will run the script and compare the generated files with the baseline files.
In order to make the tests pass, please update the baseline as well in any pull requests.

For common changes, it is sufficient to change the json files.
There are three json files that are used to alter the file generation: `addedTypes.json`, `overridingTypes.json`, and `removedTypes.json`.
The format of each file can be inferred from their existing content.

The common steps to send a pull request are:

0. Open or refer to an issue in the [TypeScript repo](https://github.com/Microsoft/TypeScript).
1. Add missing elements to `inputfiles/addedTypes.json`, overriding elements to `inputfiles/overridingTypes.json`, or elements to remove to `inputfiles/removedTypes.json`.
2. Run the script locally to obtain new `dom.generated.d.ts` and `webworker.generated.d.ts`.
3. Update the files in the `baselines` folder using the newly generated files.

## Code Structure

- `Shared.fs`: handles the parsing from XML spec file, and stores the common data structures for later use.
- `TS.fs`: handles the emitting of the `lib.d.ts` file.
- `JS.fs`: handles the emitting of the `domWeb.js` and `domWindows.js`

## Input Files

- `browser.webidl.xml`: an XML spec file generated by Microsoft Edge. **Do not edit this file**.
    - Due to the different update schedules between Edge and TypeScript, this may not be the most up-to-date version of the spec.
- `webworker.webidl.xml`: an XML spec file generated by Microsoft Edge that contains types for Web Workers. **Do not edit this file**.
- `addedTypes.json`: types that should exist in either browser or webworker but are missing from the Edge spec. The type can be `property`, `method`, `interface`, `constructor`, or `indexer`.
- `overridingTypes.json`: types that are defined in the spec file but has a better or more up-to-date definitions in the json files.
- `removedTypes.json`: types that are defined in the spec file but should be removed.
- `comments.json`: comment strings to be embedded in the generated .js files
- `jsTemplate.js`: the initial templates for `domWeb.js` and `domWindows.js`, which contains the necessary helper functions.
- `sample.json`: sample json file used to tell F# json type provider that structure of the json files. The content of it is not used anywhere.

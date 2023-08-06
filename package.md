{
  "name": "nativefier",
  "version": "51.0.1",
  "description": "Wrap web apps natively",
  "license": "MIT",
  "author": "Goh Jia Hao",
  "engines_README": "Bumping the minimum required Node version? You must bump: 1. package.json -> engines.node, 2. package.json -> devDependencies.@types/node , 3. tsconfig.json -> {target, lib} , 4. .github/workflows/ci.yml -> node-version",
  "engines_READMEforEnginesNode": "Here in engines.node, we require a version as old as possible, for Nativefier to be easily installable using the stock Node.js shipped by conservative Linux distros. It's a balancing act between this, and our own dependencies requiring more a recent Node; as much as possible, try to keep supporting Debian stable; https://packages.debian.org/search?suite=stable&keywords=nodejs",
  "engines": {
    "node": ">= 16.16.0",
    "npm": ">= 8.11.0"
  },
  "keywords": [
    "desktop",
    "electron",
    "app",
    "native",
    "wrapper"
  ],
  "main": "lib/main.js",
  "typings": "lib/main.d.ts",
  "bin": {
    "nativefier": "lib/cli.js"
  },
  "homepage": "https://github.com/nativefier/nativefier",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/nativefier/nativefier.git"
  },
  "bugs": {
    "url": "https://github.com/nativefier/nativefier/issues"
  },
  "scripts": {
    "build-app": "cd app && webpack",
    "build-app-static": "ncp app/src/static/ app/lib/static/ && ncp app/dist/preload.js app/lib/preload.js && ncp app/dist/preload.js.map app/lib/preload.js.map",
    "build": "npm run clean && tsc --build shared src app && npm run build-app && npm run build-app-static",
    "build:watch": "npm run clean && tsc --build shared src app --watch",
    "changelog": "./.github/generate-changelog",
    "clean": "rimraf coverage/ lib/ app/lib/ app/dist/ shared/lib",
    "clean:full": "npm run clean && rimraf app/node_modules/ node_modules/",
    "lint:fix": "cd src && eslint . --ext .ts --fix && cd ../shared && eslint src --ext .ts --fix && cd ../app && eslint src --ext .ts --fix",
    "lint:format": "prettier --write 'src/**/*.ts' 'app/src/**/*.ts' 'shared/src/**/*.ts'",
    "lint": "eslint shared app src --ext .ts",
    "list-outdated-deps": "npm out; cd app && npm out; true",
    "prepare": "cd app && npm ci && cd .. && npm run build",
    "relock:cli": "rm -rf ./node_modules/ ./npm-shrinkwrap.json && npm install --ignore-scripts --package-lock && mv package-lock.json npm-shrinkwrap.json && npm out",
    "relock:app": "rm -rf ./app/node_modules/ ./app/npm-shrinkwrap.json && cd app && npm install --ignore-scripts --package-lock && mv package-lock.json npm-shrinkwrap.json && npm out",
    "relock": "npm run relock:cli; npm run relock:app",
    "test:integration": "jest --testRegex=integration-test",
    "test:manual": "npm run build && bash .github/manual-test",
    "test:playwright": "jest --detectOpenHandles --testRegex=playwright-test",
    "test:noplaywright": "jest --testPathIgnorePatterns=playwright",
    "test:unit": "jest",
    "test:watch": "echo 'Remember to run npm run build:watch for the test watcher to work!' && jest --watchAll --collectCoverage=false",
    "test:watch:unit": "echo 'Remember to run npm run build:watch for the test watcher to work!' && jest --watchAll --collectCoverage=false --testPathIgnorePatterns=integration --testPathIgnorePatterns=playwright",
    "test:withlog": "LOGLEVEL=trace npm run test",
    "test": "jest",
    "watch": "npx concurrently \"npm:*:watch\""
  },
  "dependencies": {
    "axios": "^1.1.3",
    "electron-packager": "^15.5.1",
    "fs-extra": "^10.0.0",
    "gitcloud": "^0.2.3",
    "hasbin": "^1.2.3",
    "loglevel": "^1.7.1",
    "ncp": "^2.0.0",
    "page-icon": "^0.4.0",
    "sanitize-filename": "^1.6.3",
    "source-map-support": "^0.5.19",
    "tmp": "^0.2.1",
    "yargs": "^17.1.1"
  },
  "devDependencies": {
    "@types/debug": "^4.1.6",
    "@types/fs-extra": "^9.0.13",
    "@types/hasbin": "^1.2.0",
    "@types/jest": "^28.1.6",
    "@types/ncp": "^2.0.5",
    "@types/node": "^16.0.0",
    "@types/page-icon": "^0.3.4",
    "@types/tmp": "^0.2.1",
    "@types/yargs": "^17.0.10",
    "@typescript-eslint/eslint-plugin": "^5.3.0",
    "@typescript-eslint/parser": "^5.3.0",
    "electron": "^21.4.4",
    "eslint": "^8.1.0",
    "eslint-config-prettier": "^8.3.0",
    "eslint-plugin-prettier": "^4.0.0",
    "jest": "^28.1.3",
    "playwright": "^1.24.0",
    "prettier": "^2.3.2",
    "rimraf": "^3.0.2",
    "ts-loader": "^9.2.3",
    "typescript": "^4.3.5",
    "webpack": "^5.45.1",
    "webpack-cli": "^4.7.2"
  },
  "jest_COMMENTS": {
    "testPathIgnorePatterns": "See https://jestjs.io/docs/configuration#testpathignorepatterns-arraystring . We set it to 1. ignore coverage for deps, and 2. be sure we test the compiled JS, which is in `lib`, not `src` or `dist`",
    "watchPathIgnorePatterns": "See https://jestjs.io/docs/configuration#watchpathignorepatterns-arraystring . We set it for `jest --watch` (a.k.a. `npm run test:watch`) to trigger only after `tsc --watch` (a.k.a. `npm run build:watch`) completes its incremental compilation. Else, jest will pick up immediately on changes in `src` when TSC is barely running, hence testing not-recompiled-yet code and being super confusing, as 1. your changes won't be taken during this first run, and 2. the *next* run (e.g. after a second 'Save' in your editor) will actually have the new code :D"
  },
  "jest": {
    "collectCoverage": true,
    "collectCoverageFrom": [
      "./app/dist/**/*.js",
      "./lib/**/*.js",
      "./shared/lib/**/*.js"
    ],
    "coveragePathIgnorePatterns": [
      "[.-]test.js$"
    ],
    "moduleNameMapper": {
      "^electron$": "<rootDir>/app/dist/mocks/electron.js"
    },
    "setupFiles": [
      "./lib/jestSetupFiles"
    ],
    "testEnvironment": "node",
    "testPathIgnorePatterns": [
      "<rootDir>/app/node_modules.*",
      "<rootDir>/app/src.*",
      "<rootDir>/app/lib.*",
      "<rootDir>/src.*",
      ".+\\.d\\.ts",
      ".+\\.js\\.map"
    ],
    "testRegex": "test\\.js",
    "testTimeout": 15000,
    "watchPathIgnorePatterns": [
      "<rootDir>/app/lib.*",
      "<rootDir>/app/src.*",
      "<rootDir>/app/tsconfig.json",
      "<rootDir>/shared/tsconfig.json",
      "<rootDir>/src.*",
      "<rootDir>/tsconfig-base.json"
    ]
  },
  "prettier": {
    "arrowParens": "always",
    "singleQuote": true,
    "trailingComma": "all"
  }
}

# React Native - Web3 Initialization

## Setup
Init new empty project:
```
react-native init APPNAME
```
Move to newly created app project folder and execute:
```
npm i --save react-native-crypto
npm i --save react-native-randombytes
cd ios/
pod install
cd ..
npm i --save-dev rn-nodeify@latest
./node_modules/.bin/rn-nodeify --install
npm i --save node-libs-browser
npm i base-64
```

Copy to APPNAME/metro.config.js:
```
const extraNodeModules = require('node-libs-browser');

module.exports = {
  resolver: {
    extraNodeModules,
  },
  transformer: {
    getTransformOptions: async () => ({
      transform: {
        experimentalImportSupport: false,
        inlineRequires: false,
      },
    }),
  },
};
```
Copy to APPNAME/shim.js:
```
import {decode, encode} from 'base-64'

if (!global.btoa) global.btoa = encode
if (!global.atob) global.atob = decode

if (typeof __dirname === 'undefined') global.__dirname = '/'
if (typeof __filename === 'undefined') global.__filename = ''
if (typeof process === 'undefined') {
  global.process = require('process')
} else {
  const bProcess = require('process')
  for (var p in bProcess) {
    if (!(p in process)) {
      process[p] = bProcess[p]
    }
  }
}

process.browser = false
if (typeof Buffer === 'undefined') global.Buffer = require('buffer').Buffer

if (typeof location === 'undefined') global.location = { port: 80, protocol: 'https:' }
const isDev = typeof __DEV__ === 'boolean' && __DEV__
process.env['NODE_ENV'] = isDev ? 'development' : 'production'
if (typeof localStorage !== 'undefined') {
  localStorage.debug = isDev ? '*' : ''
}

// If using the crypto shim, uncomment the following line to ensure
// crypto is loaded first, so it can populate global.crypto
require('crypto')
```

Import in App.js:
```
import './shim'
```

Install Web3 module (latest version not working):
```
npm i --save web3@1.0.0-beta.55
```

Open and build through Xcode for the first time:
```
xed -b ios
```
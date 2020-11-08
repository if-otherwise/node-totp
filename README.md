# node-totp
TOTP For Node.JS

Usage
=====

```bash
npm install node-totp --save
or
yarn add node-totp
```

```javascript

var twoFactor = require('node-totp');

var newSecret = twoFactor.generateSecret({name: 'App_Name', account: 'michael'});
/*
{ secret: 'JI2U5DYWDEROUOSCJNS26XD654X7QVFL',
  uri: 'otpauth://totp/App_Name%3Amichael%3Fsecret=JI2U5DYWDEROUOSCJNS26XD654X7QVFL',
  qr: 'https://chart.googleapis.com/chart?chs=166x166&chld=L|0&cht=qr&chl=otpauth://totp/App_Name%3Amichael%3Fsecret=JI2U5DYWDEROUOSCJNS26XD654X7QVFL' }
*/

var newToken = twoFactor.generateToken('JI2U5DYWDEROUOSCJNS26XD654X7QVFL');
// { token: '369347' }

twoFactor.verifyToken('JI2U5DYWDEROUOSCJNS26XD654X7QVFL', '369347');
// { delta: 0 }

twoFactor.verifyToken('JI2U5DYWDEROUOSCJNS26XD654X7QVFL', '00');
// null
```

API
---

### generateSecret(options)

returns an object containing a 32-character secret (keep user specific, store in DB), a uri (if you want to make your own QR / barcode) and a direct link to a QR code served via HTTPS by the Google Chart API

options is an object containing `name` which is the name of your app that will show up when the user scans the QR code and `account` which can be the username and will also show up in the user's app. Both parameters are optional

### generateToken(secret)

returns an object containing a 6-character token

### verifyToken(secret, token, window)

checks if a time-based token matches a token from secret key within a +/- _window_ (default: 4) minute window

returns either `null` if the token does not match, or an object containing delta key, which is an integer of how for behind / forward the code time sync is in terms of how many new codes have been generated since entry

ex.
`{delta: -1}` means that the client entered the key too late (a newer key was meant to be used).
`{delta: 1}` means the client entered the key too early (an older key was meant to be used).
`{delta: 0}` means the client was within the time frame of the current key.

_n_-Minute Window
----------------

The window is set to 4 by default. Each token is valid for a total of _n_ minutes to account for time drift.
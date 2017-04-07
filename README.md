# api documentation for  [password-hash (v1.2.2)](https://github.com/davidwood/node-password-hash)  [![npm package](https://img.shields.io/npm/v/npmdoc-password-hash.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-password-hash) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-password-hash.svg)](https://travis-ci.org/npmdoc/node-npmdoc-password-hash)
#### Password hashing and verification for node.js

[![NPM](https://nodei.co/npm/password-hash.png?downloads=true)](https://www.npmjs.com/package/password-hash)

[![apidoc](https://npmdoc.github.io/node-npmdoc-password-hash/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-password-hash_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-password-hash/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-password-hash/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-password-hash/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "David Wood",
        "email": "bitprobe@gmail.com"
    },
    "bin": {
        "nodepw": "./bin/nodepw"
    },
    "bugs": {
        "url": "https://github.com/davidwood/node-password-hash/issues"
    },
    "dependencies": {},
    "description": "Password hashing and verification for node.js",
    "devDependencies": {
        "colors": ">=0.5.0",
        "mocha": "*"
    },
    "directories": {},
    "dist": {
        "shasum": "3b451f014de4b2e1ebfa0e5493b9517b1063af1d",
        "tarball": "https://registry.npmjs.org/password-hash/-/password-hash-1.2.2.tgz"
    },
    "engines": {
        "node": ">= 0.4.0"
    },
    "homepage": "https://github.com/davidwood/node-password-hash",
    "keywords": [
        "password",
        "hash",
        "utilities",
        "cli"
    ],
    "main": "./lib/password-hash.js",
    "maintainers": [
        {
            "name": "davidwood",
            "email": "bitprobe@gmail.com"
        }
    ],
    "name": "password-hash",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/davidwood/node-password-hash.git"
    },
    "scripts": {
        "test": "make test"
    },
    "version": "1.2.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module password-hash](#apidoc.module.password-hash)
1.  [function <span class="apidocSignatureSpan">password-hash.</span>generate (password, options)](#apidoc.element.password-hash.generate)
1.  [function <span class="apidocSignatureSpan">password-hash.</span>isHashed (password)](#apidoc.element.password-hash.isHashed)
1.  [function <span class="apidocSignatureSpan">password-hash.</span>verify (password, hashedPassword)](#apidoc.element.password-hash.verify)



# <a name="apidoc.module.password-hash"></a>[module password-hash](#apidoc.module.password-hash)

#### <a name="apidoc.element.password-hash.generate"></a>[function <span class="apidocSignatureSpan">password-hash.</span>generate (password, options)](#apidoc.element.password-hash.generate)
- description and source-code
```javascript
generate = function (password, options) {
  if (typeof password != 'string') throw new Error('Invalid password');
  options || (options = {});
  options.algorithm || (options.algorithm = 'sha1');
  options.saltLength || options.saltLength == 0 || (options.saltLength = 8);
  options.iterations || (options.iterations = 1);
  var salt = generateSalt(options.saltLength);
  return generateHash(options.algorithm, salt, password, options.iterations);
}
```
- example usage
```shell
...

The hashed password will be in the format 'algorithm$salt$hash'.

Example:
<pre>
    var passwordHash = require('password-hash');

    var hashedPassword = passwordHash.generate('password123');

    console.log(hashedPassword); // sha1$3I7HRwy7$cbfdac6008f9cab4083784cbd1874f76618d2a97
</pre>

### verify(password, hashedPassword)

Compares a plain-text password ('password') to a hashed password ('hashedPassword') and returns a boolean.  Both arguments are required
.
...
```

#### <a name="apidoc.element.password-hash.isHashed"></a>[function <span class="apidocSignatureSpan">password-hash.</span>isHashed (password)](#apidoc.element.password-hash.isHashed)
- description and source-code
```javascript
isHashed = function (password) {
  if (!password) return false;
  return password.split('$').length == 4;
}
```
- example usage
```shell
...

Example:
<pre>
    var passwordHash = require('./lib/password-hash');

    var hashedPassword = 'sha1$3I7HRwy7$cbfdac6008f9cab4083784cbd1874f76618d2a97';

    console.log(passwordHash.isHashed('password123')); // false
    console.log(passwordHash.isHashed(hashedPassword)); // true
</pre>

## Salt Generation

node 0.5.8 introduced 'crypto.randomBytes', which generates cryptographically strong pseudo-random data. If the version of node
supports 'crypto.randomBytes' it is used to generate the salt, otherwise 'Math.random', which is not cryptographically strong, is
 used. This is handled transparently within the salt generation function and does not impact the module's API.
...
```

#### <a name="apidoc.element.password-hash.verify"></a>[function <span class="apidocSignatureSpan">password-hash.</span>verify (password, hashedPassword)](#apidoc.element.password-hash.verify)
- description and source-code
```javascript
verify = function (password, hashedPassword) {
  if (!password || !hashedPassword) return false;
  hashedPassword = makeBackwardCompatible(hashedPassword);
  var parts = hashedPassword.split('$');
  if (parts.length != 4) return false;
  try {
    return generateHash(parts[0], parts[1], password, parts[2]) == hashedPassword;
  } catch (e) {}
  return false;
}
```
- example usage
```shell
...

Example:
<pre>
    var passwordHash = require('./lib/password-hash');

    var hashedPassword = 'sha1$3I7HRwy7$cbfdac6008f9cab4083784cbd1874f76618d2a97';

    console.log(passwordHash.verify('password123', hashedPassword)); // true
    console.log(passwordHash.verify('Password0', hashedPassword)); // false
</pre>

### isHashed(password)

Check if a password ('password') is hashed.  Returns a boolean.
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)

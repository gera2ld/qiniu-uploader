qiniu-uploader
===

![NPM](https://img.shields.io/npm/v/qiniu-uploader.svg)
![License](https://img.shields.io/npm/l/qiniu-uploader.svg)
![Downloads](https://img.shields.io/npm/dt/qiniu-uploader.svg)

A command tool to upload files to qiniu

Installation
---

```sh
$ npm i qiniu-uploader -g
```

Usage
---

```
usage: qiniu-upload [-h] [-v] [-n] [-b BUCKET] [--base BASE] [-p PREFIX]
                    [--verbose] [--ak AK] [--sk SK]
                    patterns [patterns ...]

Upload files to qiniu

Positional arguments:
  patterns              Set glob pattern to filter files

Optional arguments:
  -h, --help            Show this help message and exit.
  -v, --version         Show program's version number and exit.
  -n, --dry             List the files to be uploaded and exit
  -b BUCKET, --bucket BUCKET
                        Set qiniu bucket
  --base BASE           Set base path, default as current working directory
  -p PREFIX, --prefix PREFIX
                        Set prefix for the remote link
  --verbose             Show details during uploading
  --ak AK               Set qiniu access key, default as `process.env.
                        QINIU_ACCESS_KEY`
  --sk SK               Set qiniu secret key, default as `process.env.
                        QINIU_SECRET_KEY`
```

* `patterns` will be parsed by [node-glob](https://github.com/isaacs/node-glob).
* `--bucket` and `AK`/`SK` are required if `--dry` is not assigned.

Programmatic APIs
---
``` js
const uploadAll = require('qiniu-uploader');

uploadAll({
  bucket: 'my-bucket',
  patterns: [
    'dist/**',
  ],
})
.then(items => {
  console.log('Uploaded items:', items);
});

uploadAll.upload('my-bucket', {
  type: 'raw',
  key: 'key-to-file.txt',
  data: 'hello, world',
})
.then(() => {
  console.log('Uploaded raw text.');
});

// get puttoken for further use
const putToken = uploadAll.putToken('my-bucket', {
  expires: 100,
  saveKey: 'prefix/$(etag)',
  returnBody: JSON.stringify({path: 'prefix/$(etag)'}),
});

// parse keys after booted
uploadAll.parser.parseKeys({
  ak: 'QINIU_ACCESS_KEY',
  sk: 'QINIU_SECRET_KEY',
});
```

Examples
---
Assume we have a file system is like this:
```
▸ .git/
▾ dist/
    app.css
    app.js
    index.html
```

Upload all files in `dist/`:
``` sh
$ qiniu-upload --base dist --bucket my-bucket --verbose 'dist/**'
```

Upload all `.js` and `.css` files in `dist/`:
``` sh
$ qiniu-upload --base dist --bucket my-bucket --verbose 'dist/**.@(js|css)'
```

# Path

    Stability: 2 - Stable

This module contains utilities for handling and transforming file
paths. The file system is not consulted to check whether paths are valid.

Use `require('path')` to use this module.  The following methods are provided:

## path.basename(path[, ext])

Return the last portion of a path, similar to the Unix `basename` command.
`path` must be a string. `ext`, if given, must also be a string.

Examples:

```js
path.basename('/foo/bar/baz/asdf/quux.html')
// returns 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html')
// returns 'quux'
```

## path.delimiter

The platform-specific path delimiter, `;` or `':'`.

An example on \*nix:

```js
console.log(process.env.PATH)
// '/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin'

process.env.PATH.split(path.delimiter)
// returns ['/usr/bin', '/bin', '/usr/sbin', '/sbin', '/usr/local/bin']
```

An example on Windows:

```js
console.log(process.env.PATH)
// 'C:\Windows\system32;C:\Windows;C:\Program Files\node\'

process.env.PATH.split(path.delimiter)
// returns ['C:\\Windows\\system32', 'C:\\Windows', 'C:\\Program Files\\node\\']
```

## path.dirname(path)

Return the directory name of a path, similar to the Unix `dirname` command.
`path` must be a string.

Example:

```js
path.dirname('/foo/bar/baz/asdf/quux')
// returns '/foo/bar/baz/asdf'
```

## path.extname(path)

Return the extension of the path, from the last '.' to end of string
in the last portion of the path.  If there is no '.' in the last portion
of the path or the first character of it is '.', then it returns
an empty string. `path` must be a string.

Examples:

```js
path.extname('index.html')
// returns '.html'

path.extname('index.coffee.md')
// returns '.md'

path.extname('index.')
// returns '.'

path.extname('index')
// returns ''

path.extname('.index')
// returns ''
```

## path.format(pathObject)

Returns a path string from an object. This is the opposite of [`path.parse`][].

If `pathObject` has all expected properties, the returned string will be a
concatenation of the `dir` property, the platform-dependent path separator, and
the `base` property.

If the `dir` property is not supplied, the `root` property will be used as the
`dir` property. However, it will be assumed that the `root` property already
ends with the platform-dependent path separator. In this case, the returned
string will be the concatenation fo the `root` property and the `base` property.

If both the `dir` and the `root` properties are not supplied, then the returned
string will be the contents of the `base` property.

If the `base` property is not supplied, a concatenation of the `name` property
and the `ext` property will be used as the `base` property.

Examples:

An example on Posix systems:

```js
path.format({
    root : "/",
    dir : "/home/user/dir",
    base : "file.txt",
    ext : ".txt",
    name : "file"
});
// returns '/home/user/dir/file.txt'

// `root` will be used if `dir` is not specified and `name` + `ext` will be used
// if `base` is not specified
path.format({
    root : "/",
    ext : ".txt",
    name : "file"
})
// returns '/file.txt'
```

An example on Windows:

```js
path.format({
    root : "C:\\",
    dir : "C:\\path\\dir",
    base : "file.txt",
    ext : ".txt",
    name : "file"
})
// returns 'C:\\path\\dir\\file.txt'
```

## path.isAbsolute(path)

Determines whether `path` is an absolute path. An absolute path will always
resolve to the same location, regardless of the working directory. `path` must
be a string.

Examples on \*nix:

```js
path.isAbsolute('/foo/bar') // true
path.isAbsolute('/baz/..')  // true
path.isAbsolute('qux/')     // false
path.isAbsolute('.')        // false
```

Examples on Windows:

```js
path.isAbsolute('//server')  // true
path.isAbsolute('C:/foo/..') // true
path.isAbsolute('bar\\baz')  // false
path.isAbsolute('.')         // false
```

*Note:* If the path string passed as parameter is a zero-length string, unlike
        other path module functions, it will be used as-is and `false` will be
        returned.

## path.join([path1][, path2][, ...])

Join all arguments together and normalize the resulting path.

All arguments must be strings.  In v0.8, non-string arguments were
silently ignored.  In v0.10 and up, an exception is thrown.

Examples:

```js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..')
// returns '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar')
// throws exception
TypeError: Arguments to path.join must be strings
```

*Note:* If the arguments to `join` have zero-length strings, unlike other path
        module functions, they will be ignored. If the joined path string is a
        zero-length string then `'.'` will be returned, which represents the
        current working directory.

## path.normalize(path)

Normalize a path, taking care of `'..'` and `'.'` parts. `path` must be a
string.

When multiple slashes are found, they're replaced by a single one;
when the path contains a trailing slash, it is preserved.
On Windows backslashes are used.

Example:

```js
path.normalize('/foo/bar//baz/asdf/quux/..')
// returns '/foo/bar/baz/asdf'
```

*Note:* If the path string passed as argument is a zero-length string then `'.'`
        will be returned, which represents the current working directory.

## path.parse(path)

Returns an object from a path. `path` must be a string.

An example on \*nix:

```js
path.parse('/home/user/dir/file.txt')
// returns
// {
//    root : "/",
//    dir : "/home/user/dir",
//    base : "file.txt",
//    ext : ".txt",
//    name : "file"
// }
```

An example on Windows:

```js
path.parse('C:\\path\\dir\\index.html')
// returns
// {
//    root : "C:\\",
//    dir : "C:\\path\\dir",
//    base : "index.html",
//    ext : ".html",
//    name : "index"
// }
```

## path.posix

Provide access to aforementioned `path` methods but always interact in a posix
compatible way.

## path.relative(from, to)

Solve the relative path from `from` to `to`. `from` and `to` must be strings.

At times we have two absolute paths, and we need to derive the relative
path from one to the other.  This is actually the reverse transform of
`path.resolve`, which means we see that:

```js
path.resolve(from, path.relative(from, to)) == path.resolve(to)
```

Examples:

```js
path.relative('C:\\orandea\\test\\aaa', 'C:\\orandea\\impl\\bbb')
// returns '..\\..\\impl\\bbb'

path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb')
// returns '../../impl/bbb'
```

*Note:* If the arguments to `relative` have zero-length strings then the current
        working directory will be used instead of the zero-length strings. If
        both the paths are the same then a zero-length string will be returned.

## path.resolve([from ...], to)

Resolves `to` to an absolute path. All arguments must be strings.

If `to` isn't already absolute `from` arguments are prepended in right to left
order, until an absolute path is found. If after using all `from` paths still
no absolute path is found, the current working directory is used as well. The
resulting path is normalized, and trailing slashes are removed unless the path
gets resolved to the root directory.

Another way to think of it is as a sequence of `cd` commands in a shell.

```js
path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile')
```

Is similar to:

```
cd foo/bar
cd /tmp/file/
cd ..
cd a/../subfile
pwd
```

The difference is that the different paths don't need to exist and may also be
files.

Examples:

```js
path.resolve('/foo/bar', './baz')
// returns '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/')
// returns '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif')
// if currently in /home/myself/node, it returns
// '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

*Note:* If the arguments to `resolve` have zero-length strings then the current
        working directory will be used instead of them.

## path.sep

The platform-specific file separator. `'\\'` or `'/'`.

An example on \*nix:

```js
'foo/bar/baz'.split(path.sep)
// returns ['foo', 'bar', 'baz']
```

An example on Windows:

```js
'foo\\bar\\baz'.split(path.sep)
// returns ['foo', 'bar', 'baz']
```

## path.win32

Provide access to aforementioned `path` methods but always interact in a win32
compatible way.

[`path.parse`]: #path_path_parse_path

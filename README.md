# GoWebDAV

[![Unit Tests Status](https://github.com/studio-b12/gowebdav/actions/workflows/tests.yml/badge.svg)](https://github.com/studio-b12/gowebdav/actions/workflows/tests.yml)
[![Build Artifacts Status](https://github.com/studio-b12/gowebdav/actions/workflows/artifacts.yml/badge.svg)](https://github.com/studio-b12/gowebdav/actions/workflows/artifacts.yml)
[![GoDoc](https://godoc.org/github.com/studio-b12/gowebdav?status.svg)](https://godoc.org/github.com/studio-b12/gowebdav)
[![Go Report Card](https://goreportcard.com/badge/github.com/studio-b12/gowebdav)](https://goreportcard.com/report/github.com/studio-b12/gowebdav)

A pure Golang WebDAV client library that comes with a reference implementation.

## Features at a glance

Our `gowebdav` library allows to perform following actions on the remote WebDAV server:

* [create path](#create-path-on-a-webdav-server)
* [get files list](#get-files-list)
* [download file](#download-file-to-byte-array)
* [upload file](#upload-file-from-byte-array)
* [get information about specified file/folder](#get-information-about-specified-filefolder)
* [move file to another location](#move-file-to-another-location)
* [copy file to another location](#copy-file-to-another-location)
* [delete file](#delete-file)

It also provides an [authentication API](#type-authenticator) that makes it easy to encapsulate and control complex authentication challenges.
The default implementation negotiates the algorithm based on the user's preferences and the methods offered by the remote server.

Out-of-box support for:

* [BasicAuth](https://en.wikipedia.org/wiki/Basic_access_authentication)
* [DigestAuth](https://en.wikipedia.org/wiki/Digest_access_authentication)
* [Kerberos comming](https://github.com/studio-b12/gowebdav/pull/71#issuecomment-1416465334)


## Usage

First of all you should create `Client` instance using `NewClient()` function:

```go
root := "https://webdav.mydomain.me"
user := "user"
password := "password"

c := gowebdav.NewClient(root, user, password)
c.Connect()
// kick of your work!
```

After you can use this `Client` to perform actions, described below.

**NOTICE:** We will not check for errors in the examples, to focus you on the `gowebdav` library's code, but you should do it in your code!

### Create path on a WebDAV server
```go
err := c.Mkdir("folder", 0644)
```
In case you want to create several folders you can use `c.MkdirAll()`:
```go
err := c.MkdirAll("folder/subfolder/subfolder2", 0644)
```

### Get files list
```go
files, _ := c.ReadDir("folder/subfolder")
for _, file := range files {
    //notice that [file] has os.FileInfo type
    fmt.Println(file.Name())
}
```

### Download file to byte array
```go
webdavFilePath := "folder/subfolder/file.txt"
localFilePath := "/tmp/webdav/file.txt"

bytes, _ := c.Read(webdavFilePath)
os.WriteFile(localFilePath, bytes, 0644)
```

### Download file via reader
Also you can use `c.ReadStream()` method:
```go
webdavFilePath := "folder/subfolder/file.txt"
localFilePath := "/tmp/webdav/file.txt"

reader, _ := c.ReadStream(webdavFilePath)

file, _ := os.Create(localFilePath)
defer file.Close()

io.Copy(file, reader)
```

### Upload file from byte array
```go
webdavFilePath := "folder/subfolder/file.txt"
localFilePath := "/tmp/webdav/file.txt"

bytes, _ := os.ReadFile(localFilePath)

c.Write(webdavFilePath, bytes, 0644)
```

### Upload file via writer
```go
webdavFilePath := "folder/subfolder/file.txt"
localFilePath := "/tmp/webdav/file.txt"

file, _ := os.Open(localFilePath)
defer file.Close()

c.WriteStream(webdavFilePath, file, 0644)
```

### Get information about specified file/folder
```go
webdavFilePath := "folder/subfolder/file.txt"

info := c.Stat(webdavFilePath)
//notice that [info] has os.FileInfo type
fmt.Println(info)
```

### Move file to another location
```go
oldPath := "folder/subfolder/file.txt"
newPath := "folder/subfolder/moved.txt"
isOverwrite := true

c.Rename(oldPath, newPath, isOverwrite)
```

### Copy file to another location
```go
oldPath := "folder/subfolder/file.txt"
newPath := "folder/subfolder/file-copy.txt"
isOverwrite := true

c.Copy(oldPath, newPath, isOverwrite)
```

### Delete file
```go
webdavFilePath := "folder/subfolder/file.txt"

c.Remove(webdavFilePath)
```

## Links

More details about WebDAV server you can read from following resources:

* [RFC 4918 - HTTP Extensions for Web Distributed Authoring and Versioning (WebDAV)](https://tools.ietf.org/html/rfc4918)
* [RFC 5689 - Extended MKCOL for Web Distributed Authoring and Versioning (WebDAV)](https://tools.ietf.org/html/rfc5689)
* [RFC 2616 - HTTP/1.1 Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html "HTTP/1.1 Status Code Definitions")
* [WebDav: Next Generation Collaborative Web Authoring By Lisa Dusseaul](https://books.google.de/books?isbn=0130652083 "WebDav: Next Generation Collaborative Web Authoring By Lisa Dusseault")

**NOTICE**: RFC 2518 is obsoleted by RFC 4918 in June 2007

## Contributing
All contributing are welcome. If you have any suggestions or find some bug - please create an Issue to let us make this project better. We appreciate your help!

## License
This library is distributed under the BSD 3-Clause license found in the [LICENSE](https://github.com/studio-b12/gowebdav/blob/master/LICENSE) file.
## API

`import "github.com/studio-b12/gowebdav"`

* [Overview](#pkg-overview)
* [Index](#pkg-index)
* [Examples](#pkg-examples)
* [Subdirectories](#pkg-subdirectories)

### <a name="pkg-overview">Overview</a>
Package gowebdav is a WebDAV client library with a command line tool
included.

### <a name="pkg-index">Index</a>
* [Constants](#pkg-constants)
* [Variables](#pkg-variables)
* [func FixSlash(s string) string](#FixSlash)
* [func FixSlashes(s string) string](#FixSlashes)
* [func IsErrCode(err error, code int) bool](#IsErrCode)
* [func IsErrNotFound(err error) bool](#IsErrNotFound)
* [func Join(path0 string, path1 string) string](#Join)
* [func NewPathError(op string, path string, statusCode int) error](#NewPathError)
* [func NewPathErrorErr(op string, path string, err error) error](#NewPathErrorErr)
* [func PathEscape(path string) string](#PathEscape)
* [func ReadConfig(uri, netrc string) (string, string)](#ReadConfig)
* [func String(r io.Reader) string](#String)
* [type AuthFactory](#AuthFactory)
* [type Authenticator](#Authenticator)
  * [func NewDigestAuth(login, secret string, rs *http.Response) (Authenticator, error)](#NewDigestAuth)
* [type Authorizer](#Authorizer)
  * [func NewAutoAuth(login string, secret string) Authorizer](#NewAutoAuth)
  * [func NewEmptyAuth() Authorizer](#NewEmptyAuth)
  * [func NewPreemptiveAuth(auth Authenticator) Authorizer](#NewPreemptiveAuth)
* [type BasicAuth](#BasicAuth)
  * [func (b *BasicAuth) Authorize(c *http.Client, rq *http.Request, path string) error](#BasicAuth.Authorize)
  * [func (b *BasicAuth) Clone() Authenticator](#BasicAuth.Clone)
  * [func (b *BasicAuth) Close() error](#BasicAuth.Close)
  * [func (b *BasicAuth) String() string](#BasicAuth.String)
  * [func (b *BasicAuth) Verify(c *http.Client, rs *http.Response, path string) (redo bool, err error)](#BasicAuth.Verify)
* [type Client](#Client)
  * [func NewAuthClient(uri string, auth Authorizer) *Client](#NewAuthClient)
  * [func NewClient(uri, user, pw string) *Client](#NewClient)
  * [func (c *Client) Connect() error](#Client.Connect)
  * [func (c *Client) Copy(oldpath, newpath string, overwrite bool) error](#Client.Copy)
  * [func (c *Client) Mkdir(path string, _ os.FileMode) (err error)](#Client.Mkdir)
  * [func (c *Client) MkdirAll(path string, _ os.FileMode) (err error)](#Client.MkdirAll)
  * [func (c *Client) Read(path string) ([]byte, error)](#Client.Read)
  * [func (c *Client) ReadDir(path string) ([]os.FileInfo, error)](#Client.ReadDir)
  * [func (c *Client) ReadStream(path string) (io.ReadCloser, error)](#Client.ReadStream)
  * [func (c *Client) ReadStreamRange(path string, offset, length int64) (io.ReadCloser, error)](#Client.ReadStreamRange)
  * [func (c *Client) Remove(path string) error](#Client.Remove)
  * [func (c *Client) RemoveAll(path string) error](#Client.RemoveAll)
  * [func (c *Client) Rename(oldpath, newpath string, overwrite bool) error](#Client.Rename)
  * [func (c *Client) SetHeader(key, value string)](#Client.SetHeader)
  * [func (c *Client) SetInterceptor(interceptor func(method string, rq *http.Request))](#Client.SetInterceptor)
  * [func (c *Client) SetJar(jar http.CookieJar)](#Client.SetJar)
  * [func (c *Client) SetTimeout(timeout time.Duration)](#Client.SetTimeout)
  * [func (c *Client) SetTransport(transport http.RoundTripper)](#Client.SetTransport)
  * [func (c *Client) Stat(path string) (os.FileInfo, error)](#Client.Stat)
  * [func (c *Client) Write(path string, data []byte, _ os.FileMode) (err error)](#Client.Write)
  * [func (c *Client) WriteStream(path string, stream io.Reader, _ os.FileMode) (err error)](#Client.WriteStream)
* [type DigestAuth](#DigestAuth)
  * [func (d *DigestAuth) Authorize(c *http.Client, rq *http.Request, path string) error](#DigestAuth.Authorize)
  * [func (d *DigestAuth) Clone() Authenticator](#DigestAuth.Clone)
  * [func (d *DigestAuth) Close() error](#DigestAuth.Close)
  * [func (d *DigestAuth) String() string](#DigestAuth.String)
  * [func (d *DigestAuth) Verify(c *http.Client, rs *http.Response, path string) (redo bool, err error)](#DigestAuth.Verify)
* [type File](#File)
  * [func (f File) ContentType() string](#File.ContentType)
  * [func (f File) ETag() string](#File.ETag)
  * [func (f File) IsDir() bool](#File.IsDir)
  * [func (f File) ModTime() time.Time](#File.ModTime)
  * [func (f File) Mode() os.FileMode](#File.Mode)
  * [func (f File) Name() string](#File.Name)
  * [func (f File) Path() string](#File.Path)
  * [func (f File) Size() int64](#File.Size)
  * [func (f File) String() string](#File.String)
  * [func (f File) Sys() interface{}](#File.Sys)
* [type StatusError](#StatusError)
  * [func (se StatusError) Error() string](#StatusError.Error)

##### <a name="pkg-examples">Examples</a>
* [PathEscape](#example_PathEscape)

##### <a name="pkg-files">Package files</a>
[auth.go](https://github.com/studio-b12/gowebdav/blob/master/auth.go) [basicAuth.go](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go) [client.go](https://github.com/studio-b12/gowebdav/blob/master/client.go) [digestAuth.go](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go) [doc.go](https://github.com/studio-b12/gowebdav/blob/master/doc.go) [errors.go](https://github.com/studio-b12/gowebdav/blob/master/errors.go) [file.go](https://github.com/studio-b12/gowebdav/blob/master/file.go) [netrc.go](https://github.com/studio-b12/gowebdav/blob/master/netrc.go) [requests.go](https://github.com/studio-b12/gowebdav/blob/master/requests.go) [utils.go](https://github.com/studio-b12/gowebdav/blob/master/utils.go) 

### <a name="pkg-constants">Constants</a>
``` go
const XInhibitRedirect = "X-Gowebdav-Inhibit-Redirect"
```

### <a name="pkg-variables">Variables</a>
``` go
var ErrAuthChanged = errors.New("authentication failed, change algorithm")
```
ErrAuthChanged must be returned from the Verify method as an error
to trigger a re-authentication / negotiation with a new authenticator.

``` go
var ErrTooManyRedirects = errors.New("stopped after 10 redirects")
```
ErrTooManyRedirects will be used as return error if a request exceeds 10 redirects.

### <a name="FixSlash">func</a> [FixSlash](https://github.com/studio-b12/gowebdav/blob/master/utils.go?s=354:384#L23)
``` go
func FixSlash(s string) string
```
FixSlash appends a trailing / to our string

### <a name="FixSlashes">func</a> [FixSlashes](https://github.com/studio-b12/gowebdav/blob/master/utils.go?s=506:538#L31)
``` go
func FixSlashes(s string) string
```
FixSlashes appends and prepends a / if they are missing

### <a name="IsErrCode">func</a> [IsErrCode](https://github.com/studio-b12/gowebdav/blob/master/errors.go?s=740:780#L29)
``` go
func IsErrCode(err error, code int) bool
```
IsErrCode returns true if the given error
is an os.PathError wrapping a StatusError
with the given status code.

### <a name="IsErrNotFound">func</a> [IsErrNotFound](https://github.com/studio-b12/gowebdav/blob/master/errors.go?s=972:1006#L39)
``` go
func IsErrNotFound(err error) bool
```
IsErrNotFound is shorthand for IsErrCode
for status 404.

### <a name="Join">func</a> [Join](https://github.com/studio-b12/gowebdav/blob/master/utils.go?s=639:683#L40)
``` go
func Join(path0 string, path1 string) string
```
Join joins two paths

### <a name="NewPathError">func</a> [NewPathError](https://github.com/studio-b12/gowebdav/blob/master/errors.go?s=1040:1103#L43)
``` go
func NewPathError(op string, path string, statusCode int) error
```

### <a name="NewPathErrorErr">func</a> [NewPathErrorErr](https://github.com/studio-b12/gowebdav/blob/master/errors.go?s=1194:1255#L51)
``` go
func NewPathErrorErr(op string, path string, err error) error
```

### <a name="PathEscape">func</a> [PathEscape](https://github.com/studio-b12/gowebdav/blob/master/utils.go?s=153:188#L14)
``` go
func PathEscape(path string) string
```
PathEscape escapes all segments of a given path

### <a name="ReadConfig">func</a> [ReadConfig](https://github.com/studio-b12/gowebdav/blob/master/netrc.go?s=428:479#L27)
``` go
func ReadConfig(uri, netrc string) (string, string)
```
ReadConfig reads login and password configuration from ~/.netrc
machine foo.com login username password 123456

### <a name="String">func</a> [String](https://github.com/studio-b12/gowebdav/blob/master/utils.go?s=813:844#L45)
``` go
func String(r io.Reader) string
```
String pulls a string out of our io.Reader

### <a name="AuthFactory">type</a> [AuthFactory](https://github.com/studio-b12/gowebdav/blob/master/auth.go?s=150:251#L13)
``` go
type AuthFactory func(c *http.Client, rs *http.Response, path string) (auth Authenticator, err error)
```
AuthFactory prototype function to create a new Authenticator

### <a name="Authenticator">type</a> [Authenticator](https://github.com/studio-b12/gowebdav/blob/master/auth.go?s=1875:2084#L51)
``` go
type Authenticator interface {
    Authorize(c *http.Client, rq *http.Request, path string) error
    Verify(c *http.Client, rs *http.Response, path string) (redo bool, err error)
    Clone() Authenticator
    io.Closer
}
```
A Authenticator implements a specific way to authorize requests.
Each request is bound to a separate Authenticator instance.

The authentication flow itself is broken down into `Authorize`
and `Verify` steps. The former method runs before, and the latter
runs after the `Request` is submitted.
This makes it easy to encapsulate and control complex
authentication challenges.

Some authentication flows causing authentication roundtrips,
which can be archived by returning the `redo` of the Verify
method. `True` restarts the authentication process for the
current action: A new `Request` is spawned, which must be
authorized, sent, and re-verified again, until the action
is successfully submitted.
The preferred way is to handle the authentication ping-pong
within `Verify`, and then `redo` with fresh credentials.

The result of the `Verify` method can also trigger an
`Authenticator` change by returning the `ErrAuthChanged`
as an error. Depending on the `Authorizer` this may trigger
an `Authenticator` negotiation.

Set the `XInhibitRedirect` header to '1' in the `Authorize`
method to get control over request redirection.
Attention! You must handle the incoming request yourself.

To store a shared session state the `Clone` method **must**
return a new instance, initialized with the shared state.

#### <a name="NewDigestAuth">func</a> [NewDigestAuth](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=324:406#L21)
``` go
func NewDigestAuth(login, secret string, rs *http.Response) (Authenticator, error)
```
NewDigestAuth creates a new instance of our Digest Authenticator

### <a name="Authorizer">type</a> [Authorizer](https://github.com/studio-b12/gowebdav/blob/master/auth.go?s=349:485#L17)
``` go
type Authorizer interface {
    NewAuthenticator(body io.Reader) (Authenticator, io.Reader)
    AddAuthenticator(key string, fn AuthFactory)
}
```
Authorizer our Authenticator factory which creates an
`Authenticator` per action/request.

#### <a name="NewAutoAuth">func</a> [NewAutoAuth](https://github.com/studio-b12/gowebdav/blob/master/auth.go?s=3178:3234#L99)
``` go
func NewAutoAuth(login string, secret string) Authorizer
```
NewAutoAuth creates an auto Authenticator factory.
It negotiates the default authentication method
based on the order of the registered Authenticators
and the remotely offered authentication methods.
First In, First Out.

#### <a name="NewEmptyAuth">func</a> [NewEmptyAuth](https://github.com/studio-b12/gowebdav/blob/master/auth.go?s=3833:3863#L118)
``` go
func NewEmptyAuth() Authorizer
```
NewEmptyAuth creates an empty Authenticator factory
The order of adding the Authenticator matters.
First In, First Out.
It offers the `NewAutoAuth` features.

#### <a name="NewPreemptiveAuth">func</a> [NewPreemptiveAuth](https://github.com/studio-b12/gowebdav/blob/master/auth.go?s=4403:4456#L134)
``` go
func NewPreemptiveAuth(auth Authenticator) Authorizer
```
NewPreemptiveAuth creates a preemptive Authenticator
The preemptive authorizer uses the provided Authenticator
for every request regardless of any `Www-Authenticate` header.

It may only have one authentication method,
so calling `AddAuthenticator` will panic!

Look out!! This offers the skinniest and slickest implementation
without any synchronisation!!
Still applicable with `BasicAuth` within go routines.

### <a name="BasicAuth">type</a> [BasicAuth](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go?s=94:145#L9)
``` go
type BasicAuth struct {
    // contains filtered or unexported fields
}

```
BasicAuth structure holds our credentials

#### <a name="BasicAuth.Authorize">func</a> (\*BasicAuth) [Authorize](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go?s=180:262#L15)
``` go
func (b *BasicAuth) Authorize(c *http.Client, rq *http.Request, path string) error
```
Authorize the current request

#### <a name="BasicAuth.Clone">func</a> (\*BasicAuth) [Clone](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go?s=666:707#L34)
``` go
func (b *BasicAuth) Clone() Authenticator
```
Clone creates a Copy of itself

#### <a name="BasicAuth.Close">func</a> (\*BasicAuth) [Close](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go?s=581:614#L29)
``` go
func (b *BasicAuth) Close() error
```
Close cleans up all resources

#### <a name="BasicAuth.String">func</a> (\*BasicAuth) [String](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go?s=778:813#L40)
``` go
func (b *BasicAuth) String() string
```
String toString

#### <a name="BasicAuth.Verify">func</a> (\*BasicAuth) [Verify](https://github.com/studio-b12/gowebdav/blob/master/basicAuth.go?s=352:449#L21)
``` go
func (b *BasicAuth) Verify(c *http.Client, rs *http.Response, path string) (redo bool, err error)
```
Verify verifies if the authentication

### <a name="Client">type</a> [Client](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=220:388#L19)
``` go
type Client struct {
    // contains filtered or unexported fields
}

```
Client defines our structure

#### <a name="NewAuthClient">func</a> [NewAuthClient](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=608:663#L33)
``` go
func NewAuthClient(uri string, auth Authorizer) *Client
```
NewAuthClient creates a new client instance with a custom Authorizer

#### <a name="NewClient">func</a> [NewClient](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=436:480#L28)
``` go
func NewClient(uri, user, pw string) *Client
```
NewClient creates a new instance of client

#### <a name="Client.Connect">func</a> (\*Client) [Connect](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=1792:1824#L74)
``` go
func (c *Client) Connect() error
```
Connect connects to our dav server

#### <a name="Client.Copy">func</a> (\*Client) [Copy](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=6767:6835#L310)
``` go
func (c *Client) Copy(oldpath, newpath string, overwrite bool) error
```
Copy copies a file from A to B

#### <a name="Client.Mkdir">func</a> (\*Client) [Mkdir](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=5742:5804#L259)
``` go
func (c *Client) Mkdir(path string, _ os.FileMode) (err error)
```
Mkdir makes a directory

#### <a name="Client.MkdirAll">func</a> (\*Client) [MkdirAll](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=6017:6082#L273)
``` go
func (c *Client) MkdirAll(path string, _ os.FileMode) (err error)
```
MkdirAll like mkdir -p, but for webdav

#### <a name="Client.Read">func</a> (\*Client) [Read](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=6941:6991#L315)
``` go
func (c *Client) Read(path string) ([]byte, error)
```
Read reads the contents of a remote file

#### <a name="Client.ReadDir">func</a> (\*Client) [ReadDir](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=2818:2878#L117)
``` go
func (c *Client) ReadDir(path string) ([]os.FileInfo, error)
```
ReadDir reads the contents of a remote directory

#### <a name="Client.ReadStream">func</a> (\*Client) [ReadStream](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=7302:7365#L333)
``` go
func (c *Client) ReadStream(path string) (io.ReadCloser, error)
```
ReadStream reads the stream for a given path

#### <a name="Client.ReadStreamRange">func</a> (\*Client) [ReadStreamRange](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=8114:8204#L355)
``` go
func (c *Client) ReadStreamRange(path string, offset, length int64) (io.ReadCloser, error)
```
ReadStreamRange reads the stream representing a subset of bytes for a given path,
utilizing HTTP Range Requests if the server supports it.
The range is expressed as offset from the start of the file and length, for example
offset=10, length=10 will return bytes 10 through 19.

If the server does not support partial content requests and returns full content instead,
this function will emulate the behavior by skipping `offset` bytes and limiting the result
to `length`.

#### <a name="Client.Remove">func</a> (\*Client) [Remove](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=5248:5290#L236)
``` go
func (c *Client) Remove(path string) error
```
Remove removes a remote file

#### <a name="Client.RemoveAll">func</a> (\*Client) [RemoveAll](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=5356:5401#L241)
``` go
func (c *Client) RemoveAll(path string) error
```
RemoveAll removes remote files

#### <a name="Client.Rename">func</a> (\*Client) [Rename](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=6601:6671#L305)
``` go
func (c *Client) Rename(oldpath, newpath string, overwrite bool) error
```
Rename moves a file from A to B

#### <a name="Client.SetHeader">func</a> (\*Client) [SetHeader](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=1055:1100#L49)
``` go
func (c *Client) SetHeader(key, value string)
```
SetHeader lets us set arbitrary headers for a given client

#### <a name="Client.SetInterceptor">func</a> (\*Client) [SetInterceptor](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=1207:1289#L54)
``` go
func (c *Client) SetInterceptor(interceptor func(method string, rq *http.Request))
```
SetInterceptor lets us set an arbitrary interceptor for a given client

#### <a name="Client.SetJar">func</a> (\*Client) [SetJar](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=1690:1733#L69)
``` go
func (c *Client) SetJar(jar http.CookieJar)
```
SetJar exposes the ability to set a cookie jar to the client.

#### <a name="Client.SetTimeout">func</a> (\*Client) [SetTimeout](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=1391:1441#L59)
``` go
func (c *Client) SetTimeout(timeout time.Duration)
```
SetTimeout exposes the ability to set a time limit for requests

#### <a name="Client.SetTransport">func</a> (\*Client) [SetTransport](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=1534:1592#L64)
``` go
func (c *Client) SetTransport(transport http.RoundTripper)
```
SetTransport exposes the ability to define custom transports

#### <a name="Client.Stat">func</a> (\*Client) [Stat](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=4204:4259#L184)
``` go
func (c *Client) Stat(path string) (os.FileInfo, error)
```
Stat returns the file stats for a specified path

#### <a name="Client.Write">func</a> (\*Client) [Write](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=9209:9284#L389)
``` go
func (c *Client) Write(path string, data []byte, _ os.FileMode) (err error)
```
Write writes data to a given path

#### <a name="Client.WriteStream">func</a> (\*Client) [WriteStream](https://github.com/studio-b12/gowebdav/blob/master/client.go?s=9708:9794#L419)
``` go
func (c *Client) WriteStream(path string, stream io.Reader, _ os.FileMode) (err error)
```
WriteStream writes a stream

### <a name="DigestAuth">type</a> [DigestAuth](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=157:254#L14)
``` go
type DigestAuth struct {
    // contains filtered or unexported fields
}

```
DigestAuth structure holds our credentials

#### <a name="DigestAuth.Authorize">func</a> (\*DigestAuth) [Authorize](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=502:585#L26)
``` go
func (d *DigestAuth) Authorize(c *http.Client, rq *http.Request, path string) error
```
Authorize the current request

#### <a name="DigestAuth.Clone">func</a> (\*DigestAuth) [Clone](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=1205:1247#L49)
``` go
func (d *DigestAuth) Clone() Authenticator
```
Clone creates a copy of itself

#### <a name="DigestAuth.Close">func</a> (\*DigestAuth) [Close](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=1119:1153#L44)
``` go
func (d *DigestAuth) Close() error
```
Close cleans up all resources

#### <a name="DigestAuth.String">func</a> (\*DigestAuth) [String](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=1420:1456#L58)
``` go
func (d *DigestAuth) String() string
```
String toString

#### <a name="DigestAuth.Verify">func</a> (\*DigestAuth) [Verify](https://github.com/studio-b12/gowebdav/blob/master/digestAuth.go?s=889:987#L36)
``` go
func (d *DigestAuth) Verify(c *http.Client, rs *http.Response, path string) (redo bool, err error)
```
Verify checks for authentication issues and may trigger a re-authentication

### <a name="File">type</a> [File](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=93:253#L10)
``` go
type File struct {
    // contains filtered or unexported fields
}

```
File is our structure for a given file

#### <a name="File.ContentType">func</a> (File) [ContentType](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=476:510#L31)
``` go
func (f File) ContentType() string
```
ContentType returns the content type of a file

#### <a name="File.ETag">func</a> (File) [ETag](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=929:956#L56)
``` go
func (f File) ETag() string
```
ETag returns the ETag of a file

#### <a name="File.IsDir">func</a> (File) [IsDir](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=1035:1061#L61)
``` go
func (f File) IsDir() bool
```
IsDir let us see if a given file is a directory or not

#### <a name="File.ModTime">func</a> (File) [ModTime](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=836:869#L51)
``` go
func (f File) ModTime() time.Time
```
ModTime returns the modified time of a file

#### <a name="File.Mode">func</a> (File) [Mode](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=665:697#L41)
``` go
func (f File) Mode() os.FileMode
```
Mode will return the mode of a given file

#### <a name="File.Name">func</a> (File) [Name](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=378:405#L26)
``` go
func (f File) Name() string
```
Name returns the name of a file

#### <a name="File.Path">func</a> (File) [Path](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=295:322#L21)
``` go
func (f File) Path() string
```
Path returns the full path of a file

#### <a name="File.Size">func</a> (File) [Size](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=573:599#L36)
``` go
func (f File) Size() int64
```
Size returns the size of a file

#### <a name="File.String">func</a> (File) [String](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=1183:1212#L71)
``` go
func (f File) String() string
```
String lets us see file information

#### <a name="File.Sys">func</a> (File) [Sys](https://github.com/studio-b12/gowebdav/blob/master/file.go?s=1095:1126#L66)
``` go
func (f File) Sys() interface{}
```
Sys ????

### <a name="StatusError">type</a> [StatusError](https://github.com/studio-b12/gowebdav/blob/master/errors.go?s=499:538#L18)
``` go
type StatusError struct {
    Status int
}

```
StatusError implements error and wraps
an erroneous status code.

#### <a name="StatusError.Error">func</a> (StatusError) [Error](https://github.com/studio-b12/gowebdav/blob/master/errors.go?s=540:576#L22)
``` go
func (se StatusError) Error() string
```

- - -
Generated by [godoc2md](http://godoc.org/github.com/davecheney/godoc2md)

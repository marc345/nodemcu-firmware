# FTPServer Module
| Since  | Origin / Contributor  | Maintainer  | Source  |
| :----- | :-------------------- | :---------- | :------ |
| 2018-07-02 | [Terry Ellison](https://github.com/TerryE) | [Terry Ellison](https://github.com/TerryE) | [ftpserver.lua](../../lua_modules/ftp/ftpserver.lua) |

This Lua module implementation provides a basic FTP server for the ESP8266. It has been tested against a number of Table, Windows and Linux FTP clients and browsers.

It provides a limited subset of FTP commands that enable such clients to transfer files to and from the ESP's file system. Only one server can be started at any one time, but this server can support multiple connected sessions (some FTP clients use multiple sessions and so require this feature).

!!! warning
	This module is too big to load by standard `require` function or compile on ESP8266 using `node.compile()`. The only option to load and use it is to use [LFS](../lfs.md).

### Limitations
-  FTP over SSH or TLS is not currently supported so transfer is unencrypted.
-  The client session , must,  authenticate against a single user/password.
-  Only the SPIFFS filesystem is currently supported, so changing directories is treated as a NO-OP.
-  This implementation has been optimized for running in LFS.
-  Only PASV mode is supported as the `net` module does not allow static allocation of outbound sockets.

### Notes
The coding style adopted here is more similar to best practice for normal (PC) module implementations, as using LFS permits a bias towards clarity of coding over brevity. It includes extra logic to handle some of the edge case issues more robustly. It also uses a standard forward reference coding pattern to allow the code to be laid out in main routine, subroutine order.

Most FTP clients are capable of higher transfer rates than the ESP SPIFFS write throughput, so the server uses TCP flow control to limit upload rates to the ESP.

The following FTP commands are supported:

-  with no parameter: CDUP, NOOP, PASV, PWD, QUIT, SYST
-  with one parameter: CWD, DELE, MODE, PASS, PORT, RNFR, RNTO, SIZE, TYPE, USER
-  xfer commands: LIST, NLST, RETR, STOR

This implementation is by [Terry Ellison](https://github.com/TerryE), but I wish to acknowledge the inspiration and hard work by [Neronix](https://github.com/NeiroNx) that made this possible.

## createServer()
Create the FTP server on the standard ports 20 and 21.  The global variable `FTP` is set to the server object.

#### Syntax
`FTP:createServer(user, pass[, dbgFlag])`

#### Parameters
- `user`: Username for access to the server
- `pass`: Password for access to the server
- `dbgFlag`: optional flag.  If set true then internal debug output is printed

#### Returns
`nil`

#### Example
```Lua
require("ftpserver"):createServer('user', 'password')
```

## open()
Wrapper to createServer() which also connects to the WiFi channel.

#### Syntax
`FTP:open(user, pass, ssid, wifipwd, dbgFlag)`

#### Parameters
- `user`: Username for access to the server
- `pass`: Password for access to the server
- `ssid`: SSID for WiFi service
- `wifipwd`: password for  WiFi service
- `dbgFlag`: optional flag.  If set true then internal debug output is printed

#### Returns
`nil`

#### Example
```Lua
require("ftpserver"):open('user', 'password', 'myWifi', 'wifiPassword')
```

## close()
Close down server including any sockets and return all resources to Lua. Note that this include removing the FTP global variable and package references.

#### Syntax
`FTP:close()`

#### Parameters
None

#### Returns
`nil`

#### Example
```Lua
FTP:close()
```

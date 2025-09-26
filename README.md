<p align="center">
    <img src="./logo.png" alt="Logo">
</p>

# Loading tables from Google Docs, Yandex Disk, and CDN
Support for loading tabular data from Google Docs directly and via an XlsxProxy server (direct links, Yandex Disk, Google Docs).

> IMPORTANT! Requires C# 9 (or Unity >= 2021.2).

> IMPORTANT! Tested on Unity 2021.3 (depends on it) and includes asmdef definitions to compile into separate assemblies and reduce main project recompilation time.


# Social
Official blog: https://leopotam.ru


# Installation


## As a Unity package
Installable as a Unity package via a Git URL in the Package Manager or by editing `Packages/manifest.json` directly:
```
"ru.leopotam.googledocs.unity": "https://gitverse.ru/leopotam/googledocs.unity.git",
```


## As source code
You can clone the repository or download an archive from the releases page.


## Other sources
The official, working version is hosted at [https://gitverse.ru/leopotam/googledocs.unity](https://gitverse.ru/leopotam/googledocs.unity). All other sources (including *nuget*, *npm*, and other repositories) are unofficial clones or third‑party code with unknown content.


# Data preparation
> IMPORTANT! Downloading is possible only if the document is publicly accessible for reading.

> IMPORTANT! It is not recommended to grant public write access.

> IMPORTANT! Using downloads from Google Docs/Yandex Disk/CDN in production builds is not recommended:
> * Response time is not guaranteed and can reach tens of seconds.
> * Rate limits can be exceeded quickly with hundreds of simultaneous requests, causing the document to be temporarily blocked.


# Reading Google Docs spreadsheets directly
Reading data from `Google Docs` spreadsheets is supported by providing a link to one of the document's sheets. You can copy the link directly from the browser address bar or via the `Sharing` dialog:
```c#
// Direct link to a sheet in the document.
string url = "https://docs.google.com/spreadsheets/d/1_xxxxxxx-yyyy/edit#gid=0";
(string csv, string err) = await GoogleDocsLoader.LoadCsvPage (url);
if (err != null) {
    // Something went wrong; err contains the error message.
}
// You can work with the CSV string.
```

# Reading via XlsxProxy
> IMPORTANT! Requires a configured and running `xlsxproxy` server from the `go/xlsxproxy` package.

> IMPORTANT! Yandex Disk and direct links imply working with `xlsx` files.

Use the `XlsxProxyClient` type to connect to the proxy server:
```c#
// xlsxproxy server address, including port and API prefix.
string xlsxProxy = "http://my.proxy.site:3000";
// Direct link to an xlsx document.
string docUrl = "https://direct.download.site/file.xlsx";
string docPage = "Sheet1";
var client = new XlsxProxyClient (xlsxProxy);
(string csv, string err) = await client.LoadFromDirect (docUrl, docPage);
if (err != null) {
    // Something went wrong; err contains the error message.
}
// You can work with the CSV string.
```
Downloading from Yandex Disk is supported:
```c#
string xlsxProxy = "http://my.proxy.site:3000";
// Link to the xlsx document from the web UI "Share" dialog.
string docUrl = "https://disk.yandex.ru/i/aABbCcDdEeFfGg";
string docPage = "Sheet1";
var client = new XlsxProxyClient (xlsxProxy);
(string csv, string err) = await client.LoadFromYandex (docUrl, docPage);
```
Downloading from Google Docs is supported:
```c#
string xlsxProxy = "http://my.proxy.site:3000";
// Link from the browser address bar.
string docUrl = "https://docs.google.com/spreadsheets/d/1_xxxxxxx-yyyy/edit#gid=0";
string docPage = "Sheet1";
var client = new XlsxProxyClient (xlsxProxy);
(string csv, string err) = await client.LoadFromGoogle (docUrl, docPage);
```

`http basic` authentication is supported for the `xlsxproxy` server:
```c#
string xlsxProxy = "http://my.proxy.site:3000";
string login = "test";
string password = "pass";
string docUrl = "https://direct.download.site/file.xlsx";
string docPage = "Sheet1";
var client = new XlsxProxyClient (xlsxProxy, login, password);
(string csv, string err) = await client.LoadFromDirect (docUrl, docPage);
```


# License
This package is released under the MIT-ZARYA license, see [details here](./LICENSE.md).

# r2frida-wiki

Before reading this tutorial, it's highly recommended that you first take a look at the official website of [r2frida](https://github.com/nowsecure/r2frida) to install the tool as well as understand the capabilities of this one.


Mobile Connection (Android & iOS)
=================================
Retrieve the device id using `frida-ls-devices` and the name of the app using `frida-ps`. The package name may be preferrable if the display name contains spaces.

### Attach

Attach to a running app using the display name.

```bash
r2 frida://device-id/Snapchat
```

### Spawn

Spawn an app using two `//` and the package name.

```bash
r2 frida://device-id//com.snapchat.android
```


Commands (`\?`)
===============
In order to get the list of commands, you might want to type: `\?`

![imaing](https://github.com/enovella/r2frida-wiki/blob/master/img/r2frida-android.jpg)

## Informative commands (`\i`)
```java
[0x00000000]> \?~^i
i                          Show target information
ii[*]                      List imports
il                         List libraries
is[*] <lib>                List symbols of lib (local and global ones)
iE[*] <lib>                Same as is, but only for the export global ones
iEa[*] (<lib>) <sym>       Show address of export symbol
isa[*] (<lib>) <sym>       Show address of symbol
ic <class>                 List Objective-C classes or methods of <class>
ip <protocol>              List Objective-C protocols or methods of <protocol>
```
- `\i`: Shows target information
```java
[0x00000000]> \i
arch  arm
bits  64
os  linux
pid  14473
uid  10127
objc  false
java  true
cylang  false
```
- `\i*`: Shows target information in `r2` form
```java
[0x00000000]> \i*
e asm.arch=arm
e asm.bits=64
e asm.os=linux
```

- `.\i*`: Radare2 imports all the dynamic binary data from Frida. E.g: which architecture, endianness, pointer size, etc...
![imaing](https://github.com/enovella/r2frida-wiki/blob/master/img/arm_thumb.png)

- `.\iE*`: Radare2 imports all the dynamic `export` data from Frida for all the dynamic libraries.

- `.\iE* <lib>`: Radare2 imports all the dynamic `export` data from Frida for only one specific library.

- `.\ii*`: Radare2 imports all the dynamic `import` data from Frida.

- `\ii <lib>`: List imports. Commonly used with the symbol `~`, which is the internal grep of `r2`.
```java
[0x00000000]> \ii libssl.so~+aes
0x7f9419f510 f EVP_aes_128_cbc /system/lib64/libcrypto.so
0x7f941a2740 f EVP_aead_aes_128_cbc_sha1_ssl3 /system/lib64/libcrypto.so
0x7f941a3314 f EVP_aead_aes_128_cbc_sha1_tls /system/lib64/libcrypto.so
0x7f941a3320 f EVP_aead_aes_128_cbc_sha1_tls_implicit_iv /system/lib64/libcrypto.so
0x7f941a332c f EVP_aead_aes_128_cbc_sha256_tls /system/lib64/libcrypto.so
0x7f9419f5b8 f EVP_aead_aes_128_gcm /system/lib64/libcrypto.so
0x7f941a274c f EVP_aead_aes_256_cbc_sha1_ssl3 /system/lib64/libcrypto.so
0x7f941a3338 f EVP_aead_aes_256_cbc_sha1_tls /system/lib64/libcrypto.so
0x7f941a3344 f EVP_aead_aes_256_cbc_sha1_tls_implicit_iv /system/lib64/libcrypto.so
0x7f941a3350 f EVP_aead_aes_256_cbc_sha256_tls /system/lib64/libcrypto.so
0x7f941a335c f EVP_aead_aes_256_cbc_sha384_tls /system/lib64/libcrypto.so
0x7f9419f5c4 f EVP_aead_aes_256_gcm /system/lib64/libcrypto.so
0x7f9419f600 f EVP_has_aes_hardware /system/lib64/libcrypto.so
```

- `\ii* <lib>`: List imports in `r2` form.
```java
[0x00000000]> \ii* libssl.so~+aes
f sym.imp.EVP_aes_128_cbc = 0x7f9419f510
f sym.imp.EVP_aead_aes_128_cbc_sha1_ssl3 = 0x7f941a2740
f sym.imp.EVP_aead_aes_128_cbc_sha1_tls = 0x7f941a3314
f sym.imp.EVP_aead_aes_128_cbc_sha1_tls_implicit_iv = 0x7f941a3320
f sym.imp.EVP_aead_aes_128_cbc_sha256_tls = 0x7f941a332c
f sym.imp.EVP_aead_aes_128_gcm = 0x7f9419f5b8
f sym.imp.EVP_aead_aes_256_cbc_sha1_ssl3 = 0x7f941a274c
f sym.imp.EVP_aead_aes_256_cbc_sha1_tls = 0x7f941a3338
f sym.imp.EVP_aead_aes_256_cbc_sha1_tls_implicit_iv = 0x7f941a3344
f sym.imp.EVP_aead_aes_256_cbc_sha256_tls = 0x7f941a3350
f sym.imp.EVP_aead_aes_256_cbc_sha384_tls = 0x7f941a335c
f sym.imp.EVP_aead_aes_256_gcm = 0x7f9419f5c4
f sym.imp.EVP_has_aes_hardware = 0x7f9419f600
```

- `\il`: List libraries. Commonly used with the symbol `~`, which is the internal grep of `r2`.
```java
[0x00000000]> \il~+keystore,ssl,crypto
0x0000007f94133000 libcrypto.so
0x0000007f93059000 libssl.so
0x0000007f879d8000 libjavacrypto.so
0x0000007f879d3000 libkeystore-engine.so
0x0000007f87985000 libkeystore_binder.so
0x0000007f5c491000 libvisacrypto.so
```
- `\iE <lib>`: List exports of library(ies)
```java
[0x00000000]> \iE
Do you want to print 111759 lines? (y/N) n
```
Filtering by library name:
```java
[0x00000000]> \iE libssl.so~AES
0x7f9307afb4 f SSL_CIPHER_is_AES256CBC
0x7f9307af64 f SSL_CIPHER_is_AES
0x7f9307af8c f SSL_CIPHER_is_AESGCM
0x7f9307af9c f SSL_CIPHER_is_AES128GCM
0x7f9307afa8 f SSL_CIPHER_is_AES128CBC
```
Filtering by library name in `r2` form: (notice the `*`)
```java
[0x00000000]> \iE* libssl.so~AES
f sym.fun.SSL_CIPHER_is_AES256CBC = 0x7f9307afb4
f sym.fun.SSL_CIPHER_is_AES = 0x7f9307af64
f sym.fun.SSL_CIPHER_is_AESGCM = 0x7f9307af8c
f sym.fun.SSL_CIPHER_is_AES128GCM = 0x7f9307af9c
f sym.fun.SSL_CIPHER_is_AES128CBC = 0x7f9307afa8
```

- `\iEa (<lib>) <sym>`: Show address of export symbol

```java
[0x00000000]> \iEa libDexHelper.so JNI_OnLoad
0xd1c2b859
```

- `\iEa* (<lib>) <sym>`: Show address of export symbol in `r2` format

```java
[0x00000000]> \iEa* libDexHelper.so JNI_OnLoad
f sym.JNI_OnLoad = 0xd1c2b859
```
- `\isa[*] (<lib>) <sym>`:  Show address of symbol

```java
[0xd5d26859]> \is libc.so~sprintf
0x0 f bionic/libc/upstream-openbsd/lib/libc/stdio/asprintf.c
0x0 f bionic/libc/upstream-openbsd/lib/libc/stdio/vasprintf.c
0x0 f bionic/libc/upstream-openbsd/lib/libc/stdio/vsprintf.c
0x0 f bionic/libc/bionic/__vsprintf_chk.cpp
0x0 f bionic/libc/stdio/sprintf.c
0xf5140be5 f asprintf
0xf5142dbd f vasprintf
0xf5148da9 f vsprintf
0xf5153c01 f __vsprintf_chk
0xf5153c29 f __sprintf_chk
0xf5155885 f sprintf

[0xd5d26859]> \isa sprintf
0xf5155885
```
- `\ic`: List classes

```java
[0x00000000]> \ic
Do you want to print 5769 lines? (y/N) n
```
Filtering by keyword (e.g package name):

```java
[0x00000000]> \ic~+security
android.security.net.config.TrustAnchor
android.security.net.config.UserCertificateSource
android.security.net.config.CertificatesEntryRef
android.security.net.config.DirectoryCertificateSource$CertSelector
android.security.net.config.RootTrustManager
android.security.net.config.PinSet
android.security.net.config.CertificateSource
android.security.keystore.AndroidKeyStoreProvider
android.security.net.config.NetworkSecurityConfig$Builder
android.security.net.config.ApplicationConfig
android.security.NetworkSecurityPolicy
```

## Search commands (`\/`)
```java
[0x00000000]> \?~^/
/[x][j] <string|hexpairs>  Search hex/string pattern in memory ranges (see search.in=?)
/w[j] string               Search wide string
/v[1248][j] value          Search for a value honoring `e cfg.bigendian` of given width
```
- `\/ keyword`: Search hex/string pattern in memory ranges (see search.in=?)
```java
[0x00000000]> \/ rooted
...
Searching 6 bytes in [0x0000007f95e1a000-0x0000007f95ebb000]
...
Searching 6 bytes in [0x0000007fc74a5000-0x0000007fc7ca4000]
hits: 9
0x7f595ec8e7 hit1_0 rootediso8601DateFormatissuerAccessCodeissuerActionCod
0x7f5e8aceb5 hit1_1 rooted devices for data security reasons.:Operation excepti
0x7f69566fa9 hit1_2 rooted?EECan not capture your card details, NFC feature is
0x7f6956f976 hit1_3 rooted or jailbroken. This is an added security measure to p
0x7f695763ac hit1_4 rooted to keep your details safe AndroidPay utilizes the m
0x7f69583dca hit1_5 rooted).T
0x7f6958dc30 hit1_6 rooted or jailbroken.Z
0x7f6958e011 hit1_7 rooted).
0x7f6991d8e7 hit1_8 rootediso8601DateFormatissuerAccessCodeissuerActionCod
```
Search string `keyword` and output in JSON format:
```java
[0x00000000]> \/j rooted
Searching 6 bytes in [0x0000007f95ebe000-0x0000007f95ebf000]
...
Searching 6 bytes in [0x0000007fc74a5000-0x0000007fc7ca4000]
hits: 9
[{"address":"0x7f595ec8e7","size":6,"flag":"hit2_0","content":"rootediso8601DateFormatissuerAccessCodeissuerActionCod"},{"address":"0x7f5e8aceb5","size":6,"flag":"hit2_1","content":"rooted devices for data security reasons.:Operation excepti"},{"address":"0x7f69566fa9","size":6,"flag":"hit2_2","content":"rooted?EECan not capture your card details, NFC feature is "},{"address":"0x7f6956f976","size":6,"flag":"hit2_3","content":"rooted or jailbroken. This is an added security measure to p"},{"address":"0x7f695763ac","size":6,"flag":"hit2_4","content":"rooted to keep your details safe AndroidPay utilizes the m"},{"address":"0x7f69583dca","size":6,"flag":"hit2_5","content":"rooted).T      "},{"address":"0x7f6958dc30","size":6,"flag":"hit2_6","content":"rooted or jailbroken.Z    "},{"address":"0x7f6958e011","size":6,"flag":"hit2_7","content":"rooted).      "},{"address":"0x7f6991d8e7","size":6,"flag":"hit2_8","content":"rootediso8601DateFormatissuerAccessCodeissuerActionCod"}]
```
Search hex string:
```java
[0x00000000]> \/x c0ffee
Searching 3 bytes in [0x0000007f95ebf000-0x0000007f95ec2000]
...
Searching 3 bytes in [0x0000007fc74a5000-0x0000007fc7ca4000]
hits: 7
0x71100090 hit3_0 c0ffee
0x719a104c hit3_1 c0ffee
0x7f59df54ec hit3_2 c0ffee
0x7f5bfaf4ec hit3_3 c0ffee
0x7f6141bb52 hit3_4 c0ffee
0x7f61e7de68 hit3_5 c0ffee
0x7f87c07623 hit3_6 c0ffee
```
Search hex string and outputs in JSON format:
```java
[0x00000000]> \/xj c0ffee
Searching 3 bytes in [0x0000007f95ebe000-0x0000007f95ebf000]
...
Searching 3 bytes in [0x0000007fc74a5000-0x0000007fc7ca4000]
hits: 7
[{"address":"0x71100090","size":3,"flag":"hit4_0","content":"c0ffee"},{"address":"0x719a104c","size":3,"flag":"hit4_1","content":"c0ffee"},{"address":"0x7f59df54ec","size":3,"flag":"hit4_2","content":"c0ffee"},{"address":"0x7f5bfaf4ec","size":3,"flag":"hit4_3","content":"c0ffee"},{"address":"0x7f6141bb52","size":3,"flag":"hit4_4","content":"c0ffee"},{"address":"0x7f61e7de68","size":3,"flag":"hit4_5","content":"c0ffee"},{"address":"0x7f87c07623","size":3,"flag":"hit4_6","content":"c0ffee"}]
```
Search value `v` from a given width in bytes `[1248]`:
```java
[0x00000000]> ? 1234
hex     0x4d2
string  "\xd2\x04"
[0x00000000]> \/v4 1234
Searching 3 bytes in [0x0000007f95ebe000-0x0000007f95ebf000]
...
Searching 3 bytes in [0x0000007fc74a5000-0x0000007fc7ca4000]
hits: 1076
0x7f95acfb23 hit2_1071 d2040000
0x7f95ad01d3 hit2_1072 d2040000
0x7f95e96a2b hit2_1073 d2040000
0x7f95e96b17 hit2_1074 d2040000
0x7f95e96f1f hit2_1075 d2040000
0x7f95e96fb3 hit2_1076 d2040000
```

## Dynamic/Debugging commands (`\d`)
```java
[0x00000000]> \?~^d
db (<addr>|<sym>)          List or place breakpoint
db- (<addr>|<sym>)|*       Remove breakpoint(s)
dc                         Continue breakpoints or resume a spawned process
dd[-][fd] ([newfd])        List, dup2 or close filedescriptors
dm[.|j|*]                  Show memory regions
dma <size>                 Allocate <size> bytes on the heap, address is returned
dmas <string>              Allocate a string inited with <string> on the heap
dmad <addr> <size>         Allocate <size> bytes on the heap, copy contents from <addr>
dmal                       List live heap allocations created with dma[s]
dma- (<addr>...)           Kill the allocations at <addr> (or all of them without param)
dmp <addr> <size> <perms>  Change page at <address> with <size>, protection <perms> (rwx)
dmm                        List all named squashed maps
dmh                        List all heap allocated chunks
dmhj                       List all heap allocated chunks in JSON
dmh*                       Export heap chunks and regions as r2 flags
dmhm                       Show which maps are used to allocate heap chunks
dp                         Show current pid
dpt                        Show threads
dr                         Show thread registers (see dpt)
dl libname                 Dlopen a library
dl2 libname [main]         Inject library using Frida's >= 8.2 new API
dt (<addr>|<sym>) ...      Trace list of addresses or symbols
dth (<addr>|<sym>) (x y..) Define function header (z=str,i=int,v=hex barray,s=barray)
dt-                        Clear all tracing
dtr <addr> (<regs>...)     Trace register values
dtf <addr> [fmt]           Trace address with format (^ixzO) (see dtf?)
dtSf[*j] [sym|addr]        Trace address or symbol using the stalker (Frida >= 10.3.13)
dtS[*j] seconds            Trace all threads for given seconds using the stalker
di[0,1,-1] [addr]          Intercept and replace return value of address
dx [hexpairs]              Inject code and execute it (TODO)
dxc [sym|addr] [args..]    Call the target symbol with given args
```

- `db (<addr>|<sym>)`: List or place breakpoint

Set a dynamic breakpoint: (notice the field `"stopped":false`)
```java
[0x00000000]> \db `\ii libtarget.so~dlsym[0]`
{
  "0x7f95e19548": {
    "name": "0x7f95e19548",
    "stopped": false,
    "address": "0x7f95e19548",
    "continue": false,
    "handler": {}
  }
}
```

Once the breakpoint is hit, we can tamper with memory at will (notice the field `"stopped":true`)
```java
[0x00000000]> \db
{
  "0x7f95e19548": {
    "name": "0x7f95e19548",
    "stopped": true,
    "address": "0x7f95e19548",
    "continue": false,
    "handler": {}
  }
}
```

List all breakpoints:
```java
[0x00000000]> \db
{
  "0x7f95e19548": {
    "name": "0x7f95e19548",
    "stopped": true,
    "address": "0x7f95e19548",
    "continue": false,
    "handler": {}
  },
  "0x7f942bf1e8": {
    "name": "0x7f942bf1e8",
    "stopped": false,
    "address": "0x7f942bf1e8",
    "continue": false,
    "handler": {}
  }
}
```

After inspecting memory, we continue the execution:
```java
[0x00000000]> \dc
Continue 1 thread(s).
```

When breakpoints are no longer needed, they can be removed:
```java
[0x00000000]> \db- *
All breakpoints removed
[0x00000000]> \db
{}
```

- `\dm`: Show memory regions (equivalent to `cat /proc/$PID/maps`)

List of memory regions (JSON output in the second command)
```java
[0x7f93059000]> \dm
Do you want to print 1708 lines? (y/N) n
[0x7f93059000]> \dmj
Do you want to print 1 lines? (y/N) n
```
Filter by library name:
```java
[0x00000000]> \dm~ssl
0x0000007f618ee000 - 0x0000007f61929000 r-- /system/lib64/libssl.so
0x0000007f93059000 - 0x0000007f93091000 r-x /system/lib64/libssl.so
0x0000007f93092000 - 0x0000007f93094000 r-- /system/lib64/libssl.so
0x0000007f93094000 - 0x0000007f93095000 rw- /system/lib64/libssl.so
```
Filter by library name, enable the output to be in `r2` format:
```java
[0x7f93059000]> \dm*~ssl
f map.0x0000007f618ee000 = 0x7f618ee000 # r-- /system/lib64/libssl.so
f map.0x0000007f93059000 = 0x7f93059000 # r-x /system/lib64/libssl.so
f map.0x0000007f93092000 = 0x7f93092000 # r-- /system/lib64/libssl.so
f map.0x0000007f93094000 = 0x7f93094000 # rw- /system/lib64/libssl.so
```

Find out the memory region of the current offset:
```java
[0x00000000]> s 0x0000007f93059000
[0x7f93059000]> \dm.
0x0000007f93059000 - 0x0000007f93091000 r-x /system/lib64/libssl.so
```
- `\dma <size>`: Allocate <size> bytes on the heap, address is returned

```java
[0x00000000]> \dma 512
0xcb1777f0
[0x00000000]> x 512 @ 0xcb1777f0
- offset -   0 1  2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0xcb1777f0  0000 0000 0000 0000 0000 0000 0000 0000  ................
0xcb177800  0000 0000 0000 0000 0000 0000 0000 0000  ................
```
- `\dmas <string>`: Allocate a string inited with <string> on the heap

```java
0x00000000]> \dmas r2fridaiscool
0xf3d7ee10
[0x00000000]> ps @ 0xf3d7ee10
r2fridaiscool
[0x00000000]> x @ 0xf3d7ee10
- offset -   0 1  2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0xf3d7ee10  7232 6672 6964 6169 7363 6f6f 6c00 f2ff  r2fridaiscool...
0xf3d7ee20  1800 0000 1300 0000 01a3 57de 0000 0000  ..........W.....
0xf3d7ee30  0000 0000 2300 0000 7265 2e66 7269 6461  ....#...re.frida
0xf3d7ee40  2e41 6765 6e74 436f 6e74 726f 6c6c 6572  .AgentController
0xf3d7ee50  3132 0000 1b00 0000 c050 d7f3 0300 0000  12.......P......
```
- `\dmal` : List live heap allocations created with dma[s]

```java
[0x00000000]> \dmal
0xf3d7ee10	"r2fridaiscoolW#re.frida.AgentContro"
```

- `\dt (<addr>|<sym>) ...`: Trace list of addresses or symbols. Similar to `frida-trace`

Dynamic tracing (`\dt`) `fopen`:
```java
[0x00000000]> \dt fopen; \dth fopen z; \dc
[TRACE] 0x7f942bf1e8 ( fopen ) ["/proc/self/maps"]
 - 0x7f5bf72d98 libtarget.so!scan_executable+0xf0
 - 0x7f5bf72d94 libtarget.so!scan_executable+0xec
 - 0x7f5bf721b4 libtarget.so!secchecks+0x90
 - 0x7f5bf6cdb8 libtarget.so!Java_com_super_secure_App+0x1710
 - 0x7f69e6a7e0 base.odex!oatexec+0x4e7e0
```
Dynamic tracing (`\dt`) `strcmp`:
```java
 -- Insert coin to continue ...
[0x00000000]> \dt strcmp; \dth strcmp z z; \. agent.js ;\dc
undefined
[TRACE] 0x7f94267adc ( strcmp ) ["Landroid/app/ActivityThread;","Landroid/app/ActivityThread;"]
 - 0x7f91abdeac libart.so!0x11ceac
 - 0x7f91abdea8 libart.so!0x11cea8
 - 0x7f91ace8b0 libart.so!_ZN3art10ClassTable6LookupEPKcm+0x100
 - 0x7f91aa0b9c libart.so!_ZN3art11ClassLinker11LookupClassEPNS_6ThreadEPKcmPNS_6mirror11ClassLoaderE+0xc4
 - 0x7f91aa0de8 libart.so!_ZN3art11ClassLinker26FindClassInPathClassLoaderERNS_33ScopedObjectAccessAlreadyRunnableEPNS_6ThreadEPKcmNS_6HandleINS_6mirror11ClassLoaderEEEPPNS8_5ClassE+0xfc
 - 0x7f91aa0e7c libart.so!_ZN3art11ClassLinker26FindClassInPathClassLoaderERNS_33ScopedObjectAccessAlreadyRunnableEPNS_6ThreadEPKcmNS_6HandleINS_6mirror11ClassLoaderEEEPPNS8_5ClassE+0x190
 - 0x7f91aa2234 libart.so!_ZN3art11ClassLinker9FindClassEPNS_6ThreadEPKcNS_6HandleINS_6mirror11ClassLoaderEEE+0x3b0
 - 0x7f91ca1e24 libart.so!0x300e24
 - 0x7f7b356884 frida-agent-64.so!0x177884
[TRACE] 0x7f94267adc ( strcmp ) ["Landroid/hardware/radio/RadioManager$ModuleProperties;","Landroid/hardware/radio/RadioManager$ModuleProperties;"]
 - 0x7f91abdeac libart.so!0x11ceac
 - 0x7f91abdea8 libart.so!0x11cea8
 - 0x7f91ace8b0 libart.so!_ZN3art10ClassTable6LookupEPKcm+0x100
 - 0x7f91aa0b9c libart.so!_ZN3art11ClassLinker11LookupClassEPNS_6ThreadEPKcmPNS_6mirror11ClassLoaderE+0xc4
 - 0x7f91aa0de8 libart.so!_ZN3art11ClassLinker26FindClassInPathClassLoaderERNS_33ScopedObjectAccessAlreadyRunnableEPNS_6ThreadEPKcmNS_6HandleINS_6mirror11ClassLoaderEEEPPNS8_5ClassE+0xfc
 - 0x7f91aa0e7c libart.so!_ZN3art11ClassLinker26FindClassInPathClassLoaderERNS_33ScopedObjectAccessAlreadyRunnableEPNS_6ThreadEPKcmNS_6HandleINS_6mirror11ClassLoaderEEEPPNS8_5ClassE+0x190
 - 0x7f91aa2234 libart.so!_ZN3art11ClassLinker9FindClassEPNS_6ThreadEPKcNS_6HandleINS_6mirror11ClassLoaderEEE+0x3b0
 - 0x7f91ca1e24 libart.so!0x300e24
 - 0x7f7b356884 frida-agent-64.so!0x177884
```

Dynamic tracing (`\dt`) `unlink`:
```java
[0x00000000]> \dt unlink; \dth unlink z; \. agent.js ;\dc
[TRACE] 0x7f94274fe0 ( unlink ) ["/data/data/com.target.app/.   ​"]
 - 0x7f942a5824 libc.so!remove+0x58
 - 0x7f942a5820 libc.so!remove+0x54
 - 0x7f8c39f724 libopenjdk.so!Java_java_io_UnixFileSystem_delete0+0x5c
 - 0x720157dc boot.oat!oatexec+0x647dc
[TRACE] 0x7f94274fe0 ( unlink ) ["/data/data/com.target.app/.   "]
 - 0x7f942a5824 libc.so!remove+0x58
 - 0x7f942a5820 libc.so!remove+0x54
 - 0x7f8c39f724 libopenjdk.so!Java_java_io_UnixFileSystem_delete0+0x5c
 - 0x720157dc boot.oat!oatexec+0x647dc
 ```

## Evaluable Variables (`\e`)
- `e[?] [a[=b]]`: List/get/set config evaluable vars
```java
[0x00000000]> \e
e patch.code=true
e search.in=perm:r--
e search.quiet=false
e stalker.event=compile
e stalker.timeout=300
e stalker.in=raw
```
## Environment variables (`\env`)
- `\env`: Get/set environment variable

Set env variable:
```java
[0x00000000]> \env LD_PRELOAD=/data/local/tmp/libhook.so
LD_PRELOAD=/data/local/tmp/libhook.so
```

Get env variable:
```java
[0x00000000]> \env LD_PRELOAD
LD_PRELOAD=/data/local/tmp/libhook.so
```

List all variables:
```java
[0x00000000]> \env
PATH=/su/bin:/sbin:/vendor/bin:/system/sbin:/system/bin:/su/xbin:/system/xbin
ANDROID_BOOTLOGO=1
ANDROID_ROOT=/system
ANDROID_ASSETS=/system/app
ANDROID_DATA=/data
ANDROID_STORAGE=/storage
EXTERNAL_STORAGE=/sdcard
ASEC_MOUNTPOINT=/mnt/asec
ANDROID_SOCKET_zygote=9
ANDROID_LOG_TAGS=*:s
```

## Frida Scripts (`\. script.js`)

JS code to be run in the target can be loaded with `\. script.js`. A common practice is always to call this script `agent.js` to remember that's the code to be run inside the target.

```java
[0x00000000]> \. agent.js
[0x00000000]> \dc
resumed spawned process.
```


### iOS

List classes (`\ic`):

(This will have a large output.)

```java
[0x00000000]> \ic
OS_dispatch_queue_attr
OS_dispatch_group
OS_dispatch_semaphore
OS_dispatch_mach
OS_dispatch_source
OS_dispatch_queue
OS_dispatch_queue_mgr
OS_dispatch_queue_network_event
OS_dispatch_queue_root
OS_dispatch_queue_main
OS_dispatch_queue_concurrent
OS_dispatch_queue_serial
OS_dispatch_queue_runloop
[0x00000000]>
```

List classes with a filter:

```java
[0x00000000]> \ic ~UITouch
_UITouchObservation
_UITouchForceMessage
_UITouchForceObservationMessageReader
_DUITouchRoutingPolicy
UITouch
_UITouchForwardingRecipient
_UITouchPredictionManager
UITouchData
_UITouchForceInteractionProgress
_UITouchForceObservable
UITouchForceGestureRecognizer
_UITouchesObservingGestureRecognizer
UITouchesEvent
[0x00000000]>
```

List fields in one class:

```java
[0x00000000]> \ic UITouch
0x000000018df1bf38 + _createTouchesWithGSEvent:phase:view:
0x0000000197826758 - pu_locationInPresentationLayerOfView:
0x000000019b18ab74 - locationInNode:
0x000000019b18acd0 - previousLocationInNode:
0x000000018db33858 - window
0x000000018db34794 - locationInView:
0x000000018db339f0 - phase
0x000000018db41d0c - .cxx_destruct
0x000000018db41cb8 - dealloc
0x000000018df1c170 - description
0x000000018db33ee0 - view
0x000000018db34da0 - timestamp
0x000000018db32924 - setWindow:
0x000000018dd230f4 - _setEaten:
0x000000018db33540 - _isEaten
[0x00000000]>
```

List protocols (\ip):

(This will have a large output.)

```java
[0x00000000]> \ip
GVRenderer
CADSyncInterface
VKMapModelDelegate
SCNPhysicsBehaviorJSExport
GEOMapServiceProblemReportTicket
PXWidgetUnlockDelegate
ICMusicSubscriptionLeaseSessionDelegate
IKJSDOMCharacterData
ACDAccountAuthenticationPlugin
TUIDSLookup
FCOrderedCollectionAdditions
MSSubscribeStreamsProtocolDelegate
_DKEventStatsCounterInternalProperty
FBInterstitialAdNativeViewDelegate
[0x00000000]>
```

List protocols with a filter:

```java
[0x00000000]> \ip ~Touch
GADNTouchHandling
UIScrollViewDelayedTouchesBeganGestureRecognizerClient
_UITouchPhaseChangeDelegate
_UIKBRTTouchDriftingDelegate
UIDebuggingInformationTouchObserver
WBSTouchIconObserver
BKSTouchDeliveryPolicyServerInterface
_UITouchForceObservationMessageReading
_UITouchable
_UIFocusEnginePanGestureTouchObserver
UIWebTouchEventsGestureRecognizerDelegate
_MKUserInteractionGestureRecognizerTouchObserver
FBAdTouchGestureRecognizerDelegate
_UIPreviewInteractionTouchForceProviding
[0x00000000]>
```

List methods of one protocol:

```java
[0x00000000]> \ip GADNTouchHandling
- handleUserTouchMovedByVector:
[0x00000000]>
```

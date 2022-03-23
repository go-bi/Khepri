<h1 align="center">
<p align="center">
  <img alt="Khepri Logo" src="docs/images/Khepri.png" height="30%" width="40%">
</p>
  Khepri
  <br>
</h1>

<h5 align="center">Free,Open-Source,Cross-platform agent and Post-exploiton tool written in Golang and C++</h5>


![platform](https://img.shields.io/badge/platform-cross-important?color=%23189000) ![Langue](https://img.shields.io/badge/language-golang/c++-orange.svg) [![stars](https://img.shields.io/github/stars/geemion/khepri)](https://github.com/geemion/Khepri/stargazers) [![GoReportCard](https://goreportcard.com/badge/github.com/geemion/Khepri)](https://goreportcard.com/report/github.com/geemion/Khepri)

### Description
 
Khepri is a Cross-platform agent. It's very basic and only for educational purposes.

----

**Disclaimer**: *This project was created for educational purposes and should not be used in environments without legal authorization. If someone's rights have been violated, please contact me to remove the project, the last DO NOT USE IT ILLEGALLY and author/contributors do not take any responsbility for any damage caused by malicious actors using any software presented herein.*

<p align="center">
 <img alt="architecture" src="docs/images/architecture.png" height="100%" width="100%">
</p>

- beacon: Agent, written in C++.
- teamserver: Server, written in Golang.
- teamclient: User client, written in C++, the UI use Qt-GUI.  


### Features
<p align="center">
 <img alt="architecture" src="docs/images/teamclient.gif" height="100%" width="100%">
</p>

- Supported C2 Protocols:
  - [x] TCP
  - [x] UDP
- Fast network serialization (Protocol Buffers)
- Agent Features:
  - [x] System Information
  - [x] Process Manager
  - [x] File Manager
  - [x] Remote Shell
  - [x] Remote Execution

- Supported operating systems


 | System     | Windows   | Linux       | Macos    |
 |------------|-----------|------------ |----------|
 | beacon     | √         | √           |√         |
 | teamserver | √         | √           |√         |
 | teamclient | √         | √           |√         |


### Quick Start

Please see [Quick Start](docs/quick_start.md)

#### How to compile it(beacon and teamclient)

##### Install compiling tool chain

1.Linux：vcpkg、gcc、g++、cmake
2.Windows:vcpkg、vs2015、cmake

+ https://cmake.org/download/
+ https://github.com/yasio/yasio/tree/88383831d79f18f29b6372693efa5eead1387417
+ https://github.com/Microsoft/vcpkg

##### Install dependent open source code

**beacon**: yasio、openssl、protobuf
**teamclient**：grpc、qt5-base

yasio: https://github.com/yasio/yasio
Other lib use vcpkg to install, eg openssl vcpkg.exe install openssl:x86-windows-static
```
vcpkg install openssl:x64-windows-static
vcpkg install cryptopp:x64-windows-static
vcpkg install protobuf:x64-windows-static
```
##### Compile

- git clone https://github.com/geemion/khepri
- proto: use *.proto file to generate protobuf and grpc code
```
//generate golang protobuf auto code
protoc -I=[Khepri Proto Dir] --go_out=[Khepri Src Dir] [Khepri Proto Dir]\client.proto
protoc -I=[Khepri Proto Dir] --go_out=[Khepri Src Dir] [Khepri proto dir]\netio.proto
protoc -I=[Khepri Proto Dir] --go_out=[Khepri Src Dir] [Khepri proto dir]\taskdata.proto
protoc -I=[Khepri Proto Dir] --go_out=plugins=grpc:[Khepri Src Dir] -I=[Khepri Proto Dir]\teamrpc.proto

//generate cpp protobuf auto code
protoc -I=[Khepri Proto Dir] --cpp_out=[Khepri Proto Dir]\..\src\proto_autogen_cpp [Khepri Proto Dir]\client.proto
protoc -I=[Khepri Proto Dir] --cpp_out=[Khepri Proto Dir]\..\src\proto_autogen_cpp [Khepri Proto Dir]\netio.proto
protoc -I=[Khepri Proto Dir] --cpp_out=[Khepri Proto Dir]\..\src\proto_autogen_cpp [Khepri Proto Dir]\taskdata.proto
protoc -I=[Khepri Proto Dir] --cpp_out=[Khepri Proto Dir]\..\src\proto_autogen_cpp [Khepri Proto Dir]\teamrpc.proto
protoc -I=[Khepri Proto Dir] --grpc_out="[Khepri Proto Dir]\..\src\proto_autogen_cpp" --plugin=protoc-gen-grpc="[Khepri Proto Dir]\grpc_cpp_plugin.exe" [Khepri Proto Dir]\teamrpc.proto
```
NOTE: The protoc executable file must copy from protobuf dir([vcpkg root]\installed\x64-windows-static\tools\protobuf\protoc.exe). otherwise there will get a compile error, like [#2](https://github.com/geemion/Khepri/issues/2).

- beacon: windows: gen-vc-project.bat
```
cmake ^
    -S . ^
    -B buildvc ^
    -G "Visual Studio 14 2015" ^
    -DCMAKE_SYSTEM_VERSION=8.1 ^
    -DCMAKE_TOOLCHAIN_FILE="[vcpkg root]/scripts/buildsystems/vcpkg.cmake" ^       #edit it
    -DVCPKG_TARGET_TRIPLET="x86-windows-static"

cd buildvc
cmake --build . --target beacon --config MinSizeRel
pause
```

- linux:gen-mk-project.sh
```
#!/usr/bin/env bash
rm -rf buildmk/
echo "mkdir buildmk"
mkdir buildmk
cmake -S .\
 -B buildmk\
 -DCMAKE_BUILD_TYPE="Debug"\
 -DCMAKE_INSTALL_PREFIX="/usr/local"\
 -DCMAKE_TOOLCHAIN_FILE="[vcpkg root]/scripts/buildsystems/vcpkg.cmake"\       #edit it
 -DVCPKG_TARGET_TRIPLET="x64-linux"\
 -DCMAKE_MAKE_PROGRAM="make"\
 -DCMAKE_C_COMPILER="gcc"\
 -DCMAKE_CXX_COMPILER="g++"\
 --debug-output

cd buildmk
make
```

- teamclient: windows: gen-vc-project.bat
```
cmake ^
    -S . ^
    -B buildvc64 ^
    -G "Visual Studio 14 2015 Win64" ^
    -DCMAKE_SYSTEM_VERSION=8.1 ^
    -DCMAKE_TOOLCHAIN_FILE="[vcpkg root]/scripts/buildsystems/vcpkg.cmake" ^       #edit it
    -DVCPKG_TARGET_TRIPLET="x64-windows-static"

cd buildvc64
cmake --build . --target teamclient --config MinSizeRel
pause
```

- CLion:
```
Open the Toolchains settings (File > Settings on Windows and Linux, CLion > Preferences), and go to the CMake settings (Build, Execution, Deployment > CMake). Finally, in CMake options, add the following line:

-DCMAKE_TOOLCHAIN_FILE=[vcpkg root]/scripts/buildsystems/vcpkg.cmake
```

- teamserver:
```
go build src\teamserver\cmd\teamserver\teamserver.go
```

# Run

- teamserver
```
Usage of teamserver.exe:
  --privatekey string
        beacon rsa private key file, default:privatekey.pem (default "privatekey.pem")
  --pubkey string
        beacon rsa public key file, default:publickey.pem (default "publickey.pem")
  -d string
        default sqlite3 db file, default:khepri.db (default "khepri.db")
  -h    help usage
  -l string
        teamserver listen at addr, default:0.0.0:50051 (default "0.0.0.0:50051")
  -p string
        teamclient connect password
```
### TODO
- [ ] Refactor teamserver code
- [ ] Beacon supports more protocols:https、dns
- [ ] Beacon supports https-dns 
- [ ] Beacon supports dynamic plugin
- [ ] Beacon support proxy
- [x] ~~Teamclient support proxy~~
- [ ] Support CI
- [x] ~~Beacon and teamclient support macos~~
- [x] Reduce beacon to ~~100kb~~700kb(~~Don't use openssl~~、support Mingw compile(can't reduce size))
...

### Contributing
* Issues and PR are welcome.
* [Khepri contributing format](docs/contributing.md).

### Stargazers over time

[![Stargazers over time](https://starchart.cc/geemion/Khepri.svg)](https://starchart.cc/geemion/Khepri)

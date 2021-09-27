---
title: RHEL/CentOS 7 구성 방법 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용할 수 있도록 RHEL/CentOS 7을 구성하는 방법을 알아봅니다.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: 69c4c86816db612d04f474638369834bbe5163b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619764"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Speech SDK용 RHEL/CentOS 7 구성

RHEL(Red Hat Enterprise Linux) 8 x64 및 CentOS 8 x64는 Speech SDK 버전 1.10.0 이상에서 공식적으로 지원됩니다. RHEL/CentOS 7 x64에서 Speech SDK를 사용할 수도 있지만 이 경우에는 시스템에서 C++ 컴파일러(C++ 개발용) 및 공유 C++ 런타임 라이브러리를 업데이트해야 합니다.

C++ 컴파일러 버전을 확인하려면 다음을 실행합니다.

```bash
g++ --version
```

컴파일러가 설치된 경우 출력은 다음과 같습니다.

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

이 메시지에서 GCC 주 버전 4가 설치되어 있다는 것을 알 수 있습니다. 이 버전은 Speech SDK에서 사용하는 C++ 11 표준을 완벽하게 지원하지 않습니다. 이 GCC 버전 및 Speech SDK 헤더를 사용하여 C++ 프로그램을 컴파일하려고 하면 컴파일 오류가 발생합니다.

또한 공유 C++ 런타임 라이브러리(libstdc++)의 버전을 확인하는 것이 중요합니다. 대부분의 Speech SDK는 네이티브 C++ 라이브러리로 구현됩니다. 즉, 애플리케이션을 개발하는 데 사용하는 언어에 관계없이 libstdc++에 따라 달라집니다.

시스템에서 libstdc++의 위치를 찾으려면 다음을 실행합니다.

```bash
ldconfig -p | grep libstdc++
```

바닐라 RHEL/CentOS 7(x64)의 출력은 다음과 같습니다.

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

이 메시지에 따라 다음 명령을 사용하여 버전 정의를 확인합니다.

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

출력은 다음과 같아야 합니다.

```bash
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Speech SDK를 사용하려면 **CXXABI_1.3.9** 및 **GLIBCXX_3.4.21** 가 필요합니다. Linux 패키지의 Speech SDK 라이브러리에서 `ldd libMicrosoft.CognitiveServices.Speech.core.so`를 실행하여 이 정보를 찾을 수 있습니다.

> [!NOTE]
> 시스템에 설치된 GCC 버전은 일치하는 런타임 라이브러리와 함께 **5.4.0** 이상으로 설정하는 것이 좋습니다.

## <a name="example"></a>예제

이는 Speech SDK 1.10.0 이상에서 개발(C++, C#, Java, Python)용으로 RHEL/CentOS 7 x 64를 구성하는 방법을 보여 주는 샘플 명령 집합입니다.

### <a name="1-general-setup"></a>1. 일반 설치

먼저 모든 일반 종속성을 설치합니다.

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. C/C++ 컴파일러 및 런타임 라이브러리

다음 명령을 사용하여 필수 구성 요소 패키지를 설치합니다.

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> libmpc-devel 패키지는 RHEL 7.8 업데이트에서 더 이상 사용되지 않습니다. 이전 명령의 출력에 메시지가 포함된 경우
>
> ```bash
> No package libmpc-devel available.
> ```
>
> 원래 소스에서 필요한 파일을 설치해야 합니다. 다음 명령을 실행합니다.
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

다음으로 컴파일러 및 런타임 라이브러리를 업데이트합니다.

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

업데이트된 컴파일러 및 라이브러리를 여러 컴퓨터에 배포해야 하는 경우에는 `/usr/local`에서 다른 컴퓨터로 복사할 수 있습니다. 런타임 라이브러리만 필요한 경우 `/usr/local/lib64`의 파일이면 충분합니다.

### <a name="3-environment-settings"></a>3. 환경 설정

다음 명령을 실행하여 구성을 완료합니다.

```bash
# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)

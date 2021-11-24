---
title: RHEL/CentOS for c + +-Speech service를 구성 하는 방법
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
ms.openlocfilehash: 01c8dfaf935ac6d2c7fcb3255768e7633aaaba0b
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964874"
---
# <a name="configure-rhelcentos-for-c"></a>RHEL/CentOS for c + + 구성

Red Hat Enterprise Linux (RHEL) 8 x 64와 CentOS 8 x 64에서 c + + 개발용 Speech SDK를 사용 하려면 시스템에서 c + + 컴파일러 및 공유 c + + 런타임 라이브러리를 업데이트 합니다.

## <a name="install-dependencies"></a>종속성 설치

먼저 모든 일반 종속성을 설치합니다.

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

## <a name="cc-compiler-and-runtime-libraries"></a>C/c + + 컴파일러 및 런타임 라이브러리

다음 명령을 사용하여 필수 구성 요소 패키지를 설치합니다.

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

다음으로 컴파일러 및 런타임 라이브러리를 업데이트합니다.

```bash
# Build GCC 7.5.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-7.5.0/gcc-7.5.0.tar.bz2 -O
tar jxf gcc-7.5.0.tar.bz2
mkdir gcc-7.5.0-build && cd gcc-7.5.0-build
../gcc-7.5.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

업데이트된 컴파일러 및 라이브러리를 여러 컴퓨터에 배포해야 하는 경우에는 `/usr/local`에서 다른 컴퓨터로 복사할 수 있습니다. 런타임 라이브러리만 필요한 경우 `/usr/local/lib64`의 파일이면 충분합니다.

## <a name="environment-settings"></a>환경 설정

다음 명령을 실행하여 구성을 완료합니다.

```bash
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
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-<version>/lib/x64:$LD_LIBRARY_PATH
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)

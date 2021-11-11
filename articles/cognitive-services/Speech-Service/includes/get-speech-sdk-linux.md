---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: eur
ms.openlocfilehash: 34a45c4759954c0c0679dcafec13b5327a82cffd
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252344"
---
:::row:::
    :::column span="3":::
        Speech SDK는 Linux에서 사용하는 경우 다음 대상 아키텍처에서 **Ubuntu 16.04(2021년** 9월까지), **Ubuntu 18.04/20.04,** **Debian 9/10,** **Red Hat Enterprise Linux(RHEL) 7/8** 및 **CentOS 7/8만** 지원합니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- C++ 개발용 x86(Debian/Ubuntu), x64, ARM32(Debian/Ubuntu) 및 ARM64(Debian/Ubuntu)
- Java용 x64, ARM32(Debian/Ubuntu) 및 ARM64(Debian/Ubuntu)
- .NET Core용 x64, ARM32(Debian/Ubuntu) 및 ARM64(Debian/Ubuntu)
- Python용 x64

> [!IMPORTANT]
> Linux ARM64의 C#의 경우 .NET Core 3.x(dotnet-sdk-3.x package)가 필요합니다.

> [!NOTE]
> Alpine Linux에서 Speech SDK를 사용하려면 [glibc 프로그램 실행](https://wiki.alpinelinux.org/wiki/Running_glibc_programs)에 관한 Alpine Linux Wiki에서 설명하는 것과 같이 Debian chroot 환경을 만든 다음, 여기의 Debian 지침을 따릅니다.

### <a name="system-requirements"></a>시스템 요구 사항

원시 애플리케이션의 경우 Speech SDK는 `libMicrosoft.CognitiveServices.Speech.core.so`를 활용합니다. 대상 아키텍처(x86, x64)가 애플리케이션과 일치해야 합니다. Linux 버전에 따라 추가 종속성이 필요할 수도 있습니다.

- GNU C 라이브러리의 공유 라이브러리(POSIX Threads Programming 라이브러리, `libpthreads` 포함)
- OpenSSL 라이브러리(`libssl`)
- ALSA 애플리케이션의 공유 라이브러리(`libasound`)

# <a name="ubuntu-18042004"></a>[Ubuntu 18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl-dev libasound2 wget
```

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl-dev libasound2 wget
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 및 CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum groupinstall "Development tools"
sudo yum install alsa-lib openssl wget
```

> [!IMPORTANT]
> - RHEL/CentOS 7에서 [Speech SDK용 RHEL/CentOS 7을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)의 지침을 따르세요.
> - RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)의 지침을 따르세요.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]

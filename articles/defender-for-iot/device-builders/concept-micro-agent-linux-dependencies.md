---
title: 마이크로 에이전트 Linux 종속성(미리 보기)
description: 이 문서에서는 Defender for IoT 마이크로 에이전트의 여러 다른 Linux OS 종속성에 대해 설명합니다.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: e878052e534ce7740fff1fdd462d2c95fcb11609
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481766"
---
# <a name="micro-agent-linux-dependencies-preview"></a>마이크로 에이전트 Linux 종속성(미리 보기)

이 문서에서는 Defender for IoT 마이크로 에이전트의 여러 다른 Linux OS 종속성에 대해 설명합니다. 

## <a name="linux-dependencies"></a>Linux 종속성

아래 표에서는 각 구성 요소에 대한 Linux 종속성을 보여 줍니다. 

| 구성 요소 | 종속성 | 형식 | IoT SDK에 필요합니다. | 참고 |
|--|--|--|--|--|
| **코어** |  |  |  |  |
|  | libcurl-openssl(libcurl) | 라이브러리 | ✔ |  |
|  | libssl | 라이브러리 | ✔ |  |
|  | uuid | 라이브러리 | ✔ |  |
|  | pthread | ulibc 컴파일 플래그 | ✔ |  |
|  | libuv1 | 라이브러리 |  |  |
|  | sudo | 패키지 |  |  |
|  | uuid-runtime | 패키지 |  |  |
| **시스템 정보 수집기** |  |  |  |  |
|  | uname | 시스템 호출 |  |  |
| **기준 수집기** |  |  |  |  |
|  | BusyBox | Linux 컴파일 플래그 |  |  |
|  | Bash | Linux 컴파일 플래그 |  |  |
| **프로세스 수집기** |  |  |  |  |
|  | CONFIG_CONNECTOR=y | 커널 구성 |  |  |
|  | CONFIG_PROC_EVENTS=y | 커널 구성 |  |  |
| **네트워크 수집기** |  |  |  |  |
|  | libpcap | 라이브러리 |  |  |
|  | CONFIG_PACKET=y | 커널 구성 |  |  |
|  | CONFIG_NETFILTER =y | 커널 구성 |  | 선택 사항 – 성능 향상 |

## <a name="next-steps"></a>다음 단계

[Defender for IoT 마이크로 에이전트 설치(미리 보기)](quickstart-standalone-agent-binary-installation.md)
---
title: Azure 기밀 솔루션 빌드
description: Azure 기밀 컴퓨팅에서 솔루션을 빌드하는 방법 알아보기
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d29f214f4ee50f672b47fe0b346f13e8b8fd49c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290251"
---
# <a name="building-confidential-computing-solutions"></a>기밀 컴퓨팅 솔루션 빌드

Azure 기밀 컴퓨팅은 기밀 솔루션을 빌드하기 위한 다양한 옵션을 제공합니다. 옵션의 스펙트럼은 기존 애플리케이션의 "리프트 앤 시프트" 시나리오를 사용하도록 설정하는 것부터 다양한 보안 기능을 완전히 제어하는 것까지 다양합니다. 이러한 기능에는 액세스 수준에 대한 제어가 포함되었습니다. 호스트 공급자 또는 게스트 운영자 액세스 수준을 데이터 및 코드로 설정합니다. 클라우드에서 실행되는 워크로드의 무결성을 손상시킬 수 있는 다른 루트 키트 또는 맬웨어 액세스를 제어할 수도 있습니다.

## <a name="solutions"></a>솔루션

보안 Enclave 또는 기밀 가상 머신과 같은 기술을 통해 고객은 기밀 솔루션을 빌드할 때 원하는 접근 방식을 선택할 수 있습니다.

- 소스 코드에 액세스할 수 없는 기존 애플리케이션은 AZURE 기밀 컴퓨팅 플랫폼에 쉽게 온보딩할 수 있도록 AMD SEV-추가 기술을 기반으로 하는 기밀 VM을 활용할 수 있습니다.
- 트러스트 벡터로부터 보호하기 위한 독점 코드를 포함하는 정교한 워크로드는 보안 애플리케이션 Enclave 기술의 이점을 누릴 수 있습니다. Azure는 현재 Intel SGX를 기반으로 하는 VM에서 애플리케이션 Enclave를 제공합니다. Intel SGX는 하드웨어 암호화 메모리 공간에서 실행되는 데이터 및 코드를 보호합니다. 이러한 애플리케이션은 일반적으로 오픈 소스 프레임워크를 사용하여 얻은 수집된 보안 Enclave와의 통신이 필요합니다.
- Azure Kubernetes Service 사용하도록 설정된 [기밀 컨테이너에서](confidential-containers.md) 실행되는 컨테이너화된 솔루션은 균형 있는 기밀성 접근 방식을 찾는 고객에게 적합할 수 있습니다. 이러한 시나리오에서 기존 앱은 제한된 변경으로 컨테이너에 패키지 및 배포할 수 있지만 클라우드 서비스 공급자 및 관리자로부터 완전한 보안 격리를 제공합니다.

![대부분의 보안 제어가 있는 옵션에 대한 더 쉬운 옵션을 보여 줌으로써 기밀 컴퓨팅 스펙트럼의 스크린샷.](media/confidential-computing-solutions/spectrum.png)

## <a name="learn-more"></a>자세한 정보

enclave 및 격리된 환경의 강력한 성능을 사용하려면 기밀 컴퓨팅을 지원하는 도구를 사용해야 합니다. enclave 애플리케이션 개발을 지원하는 다양한 도구가 있습니다. 자세한 내용은 [enclave 애플리케이션 개발에](application-development.md) 대해 읽어보십시오. 

가상 머신용 Intel SGX Enclave 애플리케이션용 솔루션을 빌드하기 위한 [오픈 소스 도구에](enclave-development-oss.md) 대해 알아봅니다.

[기밀 컨테이너에 대한 파트너 및 오픈 소스 도구를 사용합니다.](confidential-containers.md) Azure Kubernetes 워크로드에 이러한 도구 중 일부를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 enclave 개발에 대해 알아보기](application-development.md)

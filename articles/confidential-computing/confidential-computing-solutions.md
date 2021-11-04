---
title: Azure 기밀 솔루션 빌드
description: Azure 기밀 컴퓨팅에서 솔루션을 빌드하는 방법 알아보기
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: f86c56fbc779af0bc1a2a002d79d41842c1ac0a7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102938"
---
# <a name="building-confidential-computing-solutions"></a>기밀 컴퓨팅 솔루션 빌드

Azure 기밀 컴퓨팅은 기밀 솔루션을 구축 하기 위한 다양 한 옵션을 제공 합니다. 옵션 범위는 기존 응용 프로그램의 "리프트 앤 시프트" 시나리오를 사용 하 여 다양 한 보안 기능을 완벽 하 게 제어할 수 있습니다. 이러한 기능에는 액세스 수준에 대 한 제어가 포함 됩니다. 호스트 공급자 또는 게스트 운영자 액세스 수준을 데이터 및 코드로 설정 합니다. 클라우드에서 실행 되는 작업의 무결성을 손상 시킬 수 있는 다른 루트킷 또는 맬웨어 액세스를 제어할 수도 있습니다.

## <a name="solutions"></a>솔루션

보안 enclaves 또는 기밀 가상 머신과 같은 기술을 통해 고객은 기밀 솔루션을 구축 하는 데 사용할 방법을 선택할 수 있습니다.

- 소스 코드에 대 한 액세스 권한이 없는 기존 응용 프로그램은 Azure 기밀 컴퓨팅 플랫폼에 쉽게 등록 하도록 AMD 심각도-.SNP 기술을 기반으로 하는 기밀 Vm의 이점을 누릴 수 있습니다.
- 신뢰 벡터 로부터 보호 하기 위해 독점적인 코드를 포함 하는 복잡 한 워크 로드는 보안 응용 프로그램 enclave 기술의 이점을 누릴 수 있습니다. Azure current는 Intel SGX 기반 Vm에서 응용 프로그램 enclaves을 제공 합니다. Intel SGX는 하드웨어로 암호화 된 메모리 공간에서 실행 되는 데이터 및 코드를 보호 합니다. 이러한 응용 프로그램에는 일반적으로 오픈 소스 프레임 워크를 사용 하 여 가져온 증명 된 secure enclave와의 통신이 필요 합니다.
- Azure Kubernetes Service에서 사용 되는 [기밀 컨테이너](confidential-containers.md) 에서 실행 되는 컨테이너 화 된 솔루션은 기밀에 대 한 분산 된 접근 방식을 찾고 있는 고객에 게 적합 합니다. 이러한 시나리오에서는 기존 앱을 패키지 하 고 변경 내용이 제한 된 컨테이너에 배포할 수 있지만 클라우드 서비스 공급자 및 관리자 로부터 완벽 한 보안 격리를 제공 합니다.

![대부분의 보안 제어를 사용 하는 옵션에 대 한 간편한 옵션을 보여 주는 기밀 컴퓨팅 스펙트럼의 스크린샷](media/confidential-computing-solutions/spectrum.png)

## <a name="learn-more"></a>자세한 정보

Enclaves 및 격리 된 환경의 기능을 사용 하려면 기밀 컴퓨팅을 지 원하는 도구를 사용 해야 합니다. enclave 애플리케이션 개발을 지원하는 다양한 도구가 있습니다. 자세한 내용은 [enclave 응용 프로그램 개발](application-development.md) 을 참조 하세요. 

가상 컴퓨터용 Intel SGX enclave 응용 프로그램에 대 한 솔루션을 빌드하기 위한 [오픈 소스 도구](enclave-development-oss.md) 에 대해 알아봅니다.

[기밀 컨테이너를 위한 파트너 및 오픈 소스 도구를](confidential-containers.md)사용 합니다. 이러한 도구 중 일부를 Azure Kubernetes 워크 로드에 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 enclave 개발에 대해 알아보기](application-development.md)

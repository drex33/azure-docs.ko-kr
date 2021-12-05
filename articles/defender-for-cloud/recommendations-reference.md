---
title: 모든 Microsoft Defender for Cloud 권장 사항에 대한 참조 표
description: 이 문서에서는 리소스를 강화하고 보호하는 데 도움이 되는 클라우드용 Microsoft Defender의 보안 권장 사항을 나열합니다.
author: memildin
ms.service: defender-for-cloud
ms.topic: reference
ms.date: 12/04/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 33610b471faf5d43e59760984317a20b40ca0092
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133571228"
---
# <a name="security-recommendations---a-reference-guide"></a>보안 권장 사항 - 참조 가이드

이 문서에서는 Microsoft Defender for Cloud에서 볼 수 있는 권장 사항을 나열합니다. 환경에 표시되는 권장 사항은 보호하는 리소스 및 사용자 지정된 구성에 따라 다릅니다.

Defender for Cloud의 권장 사항은 [Azure Security Benchmark](../security/benchmarks/introduction.md)를 기반으로 합니다. Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다.

이러한 권장 사항에 응답하는 방법에 대한 자세한 내용은 [Defender for Cloud에서 권장 사항 수정을](implement-security-recommendations.md)참조하세요.

보안 점수는 완료한 보안 권장 사항 수를 기반으로 합니다. 먼저 해결할 권장 사항을 결정하려면 각 권장 사항의 심각도와 보안 점수에 미치는 잠재적 영향을 살펴보면 됩니다.

> [!TIP]
> 권장 사항의 설명에 "관련 정책 없음"이라고 표시되는 경우가 있는데, 이는 권장 사항이 다른 권장 사항 및 _해당_ 정책에 따라 달라지기 때문입니다. 예를 들어 "엔드포인트 보호 상태 오류를 수정해야 합니다..."라는 내용의 권장 사항은 엔드포인트 보호 솔루션의 _설치_ 여부를 확인하는 권장 사항("엔드포인트 보호 솔루션을 설치해야 합니다...")에 의존합니다. 기본 권장 사항에는 정책이 _있습니다_.
> 정책을 기본 권장 사항으로 제한하면 정책 관리가 간단해집니다.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices 권장 사항

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>컴퓨팅 권장 사항

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>컨테이너 권장 사항

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>데이터 권장 사항

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess 권장 사항

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT 권장 사항

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>네트워킹 권장 사항

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>사용되지 않는 권장 사항

|권장|설명 및 관련 정책|심각도|
|----|----|----|
|App Services에 대한 액세스를 제한해야 함|너무 넓은 범위의 인바운드 트래픽을 거부하도록 네트워킹 구성을 변경하여 App Services 액세스를 제한합니다.<br>(관련 정책: [미리 보기]: App Services 액세스를 제한해야 함)|높음|
|IaaS NSG의 웹 애플리케이션에 대한 규칙을 강화해야 함|웹 애플리케이션을 실행하고, 웹 애플리케이션 포트와 관련하여 과도한 권한이 부여된 NSG 규칙을 사용하는 가상 머신의 NSG(네트워크 보안 그룹)를 강화합니다.<br>(관련 정책: IaaS에서 웹 애플리케이션에 대한 NSG 규칙을 강화해야 함)|높음|
|불필요한 애플리케이션 권한을 제거하여 공격 벡터를 줄이는 Pod 보안 정책을 정의해야 함(미리 보기)|Pod 보안 정책을 정의하여 불필요한 애플리케이션 권한을 제거함으로써 공격 벡터를 줄일 수 있습니다. Pod가 액세스 권한이 부여된 리소스에만 액세스할 수 있도록 Pod 보안 정책을 구성하는 것이 좋습니다.<br>(관련 정책: [미리 보기]: Kubernetes 서비스에서 Pod 보안 정책을 정의해야 함)|중간|
|IoT 디바이스를 더 쉽게 볼 수 있도록 IoT 보안 모듈에 대한 Azure Security Center 설치|IoT 디바이스를 더 쉽게 볼 수 있도록 IoT 보안 모듈에 대한 Azure Security Center 설치|낮음|
|시스템 업데이트를 적용하려면 머신을 다시 시작해야 함|시스템 업데이트를 적용하고 취약성으로부터 머신을 보호하려면 머신을 다시 시작합니다. (관련 정책: 머신에 시스템 업데이트를 설치해야 함)|중간|
| 컴퓨터에 모니터링 에이전트를 설치해야 합니다.|이 작업을 수행하면 선택한 가상 머신에 모니터링 에이전트가 설치됩니다. 에이전트가 보고할 작업 영역을 선택합니다. (관련 정책 없음)|높음|
||||

## <a name="next-steps"></a>다음 단계

권장 사항에 대한 자세한 내용은 다음을 참조하세요.

- [보안 정책, 이니셔티브 및 권장 사항은 무엇인가요?](security-policy-concept.md)
- [보안 권장 사항 검토](review-security-recommendations.md)

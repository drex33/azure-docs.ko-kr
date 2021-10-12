---
title: Azure Security Center에 예정된 중요한 변경
description: 알아야 하고 계획해야 할 수 있는 Azure Security Center에 예정된 변경입니다
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/05/2021
ms.author: memildin
ms.openlocfilehash: e6d1c8cf55687a8e4d7612ca432a314ae38256bc
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534424"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center에 예정된 중요한 변경

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Security Center에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾고 있으면 [Azure Security Center의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

| 계획된 변경       | 변경 예상 날짜 |
|----------------------|---------------------------|
| [사용 중단 미리 보기 경고: ARM. MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | 2021년 10월|
| [ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| 2021년 10월|
| [엔드포인트 보호 솔루션 관리를 위한 권장 사항 변경](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | 2021년 10월    |
| [SQL 데이터베이스의 중요한 데이터 분류에 대한 개선된 권장 사항](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | 2022년 1분기    |
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>사용 중단 미리 보기 경고: ARM. MCAS_ActivityFromAnonymousIPAddresses

**변경 예상 날짜:** 2021년 10월

다음 미리 보기 경고를 사용할 수 없게 됩니다.

|경고 이름| Description|
|----------------------|---------------------------|
|**미리 보기 - 위험한 IP 주소에서 활동 발생**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|익명 프록시 IP 주소로 식별된 IP 주소에서 사용자 활동이 감지되었습니다.<br>이 프록시는 해당 장치의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다. 이 감지는 조직의 다른 사용자가 널리 사용하는 태그가 잘못 지정된 IP 주소와 같은 가양성을 줄이는 기계 학습 알고리즘을 이용합니다.<br>활성 Microsoft Cloud App Security 라이선스가 필요합니다.|
|||

이 정보를 제공하고 추가하는 새 경고를 만들었습니다. 또한 최신 경고(ARM_OperationFromSuspiciousIP, ARM_OperationFromSuspiciousProxyIP)는 Microsoft Cloud App Security에 대한 라이선스가 필요하지 않습니다.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.

**변경 예상 날짜:** 2021년 10월

ISO 27001의 레거시 구현은 Security Center의 규정 준수 대시보드에서 제거됩니다. Security Center를 사용하여 ISO 27001 규정 준수를 추적하는 경우 모든 관련 관리 그룹 또는 구독에 대한 새 ISO 27001:2013 표준을 온보드하면 현재 레거시 ISO 27001이 대시보드에서 곧 제거됩니다.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001의 레거시 구현을 제거하는 방법에 대한 메시지를 보여주는 Security Center의 규정 준수 대시보드." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>엔드포인트 보호 솔루션 관리를 위한 권장 사항 변경

**변경 예상 날짜:** 2021년 10월

2021년 8월에 컴퓨터에 엔드포인트 보호 솔루션을 배포하고 유지 관리하기 위한 두 가지 새로운 **미리 보기** 권장 사항을 추가했습니다. 자세한 내용은 [릴리스 정보](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)를 참조하세요.

권장 사항이 일반 공급으로 출시되면 다음과 같은 기존 권장 사항을 대체합니다.

- **컴퓨터에 Endpoint Protection을 설치해야 함** 으로 인해 다음이 바뀝니다.
    - [가상 머신에 엔드포인트 보호 솔루션 설치(키: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [머신에 엔드포인트 보호 솔루션 설치(키: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **컴퓨터에서 엔드포인트 보호 상태 문제를 해결해야 함** 은 이름이 같은 기존 권장 사항을 대체합니다. 두 가지 권장 사항에는 서로 다른 평가 키가 있습니다.
    - **미리 보기** 권장 사항에 대한 평가 키: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - **GA** 권장 사항에 대한 평가 키: 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

자세한 정보:
- [Security Center에서 지원하는 엔드포인트 보호 솔루션](security-center-services.md#endpoint-supported)
- [이 권장 사항이 배포된 솔루션의 상태를 평가하는 방법](security-center-endpoint-protection.md)

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>SQL 데이터베이스의 중요한 데이터 분류에 대한 개선된 권장 사항

**변경 예상 날짜:** 2022년 1분기

**데이터 분류 적용** 보안 제어에서 **SQL 데이터베이스의 중요 데이터를 분류해야 함** 권장 사항은 Microsoft의 데이터 분류 전략에 맞춰 향상된 새 버전으로 대체됩니다. 그 결과 권장 사항의 ID도 변경됩니다(현재 b0df6f56-862d-4730-8597-38c0fd4ebd59임).


## <a name="next-steps"></a>다음 단계

Security Center에 대한 모든 최근 변경 내용은 [Azure Security Center의 새로운 기능](release-notes.md)을 참조하세요.
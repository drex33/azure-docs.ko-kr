---
title: Microsoft Defender for Cloud에 대한 향후 중요 변경 내용
description: 알아야 하고 계획해야 할 수 있는 Microsoft Defender for Cloud에 예정된 변경입니다
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/10/2021
ms.author: memildin
ms.openlocfilehash: fca622c8c49a16d9926a5fc1e8e0f0064ee758d7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157443"
---
# <a name="important-upcoming-changes-to-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에 대한 향후 중요 변경 내용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> 이 페이지의 정보는 시험판 제품 또는 기능과 관련이 있으며, 이는 상업적으로 릴리스되기 전에 대폭 수정될 수 있습니다. Microsoft는 여기에 제공된 정보와 관련하여 어떠한 명시적 또는 묵시적 약속 또는 보증도 하지 않습니다.

이 페이지에서는 Defender for Cloud에 계획된 변경에 대해 알아봅니다. 보안 점수 또는 워크플로와 같은 항목에 영향을 줄 수 있는 제품에 대한 계획된 수정을 설명합니다.

최신 릴리스 정보를 찾는 경우 [Microsoft Defender for Cloud의 새로운 기능](release-notes.md)에서 확인할 수 있습니다.


## <a name="planned-changes"></a>계획된 변경

| 계획된 변경       | 변경 예상 날짜 |
|----------------------|---------------------------|
| [사용 중단 미리 보기 경고: ARM. MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | 2021년 11월|
| [ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| 2021년 11월|
| [온-프레미스 머신의 인벤토리 표시는 리소스 이름에 대해 다른 템플릿을 사용합니다.](#inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name)    | 2021년 11월    |
| [ID 권장 사항에 대한 여러 변경 사항](#multiple-changes-to-identity-recommendations)                                                                                          | 2021년 12월    |
| [Microsoft Defender for Storage의 보안 경고 변경 내용](#changes-to-a-security-alert-from-microsoft-defender-for-storage)  | 2021년 11월    |
| [SQL 데이터베이스의 중요한 데이터 분류에 대한 개선된 권장 사항](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | 2022년 1분기    |
| [엔드포인트 보호 솔루션 관리를 위한 권장 사항 변경](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | 2022년 2월| 
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>사용 중단 미리 보기 경고: ARM. MCAS_ActivityFromAnonymousIPAddresses

**변경 예상 날짜:** 2021년 11월

다음 미리 보기 경고를 사용할 수 없게 됩니다.

|경고 이름| Description|
|----------------------|---------------------------|
|**미리 보기 - 위험한 IP 주소에서 활동 발생**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|익명 프록시 IP 주소로 식별된 IP 주소에서 사용자 활동이 감지되었습니다.<br>이 프록시는 해당 장치의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다. 이 감지는 조직의 다른 사용자가 널리 사용하는 태그가 잘못 지정된 IP 주소와 같은 가양성을 줄이는 기계 학습 알고리즘을 이용합니다.<br>활성 Microsoft Cloud App Security 라이선스가 필요합니다.|
|||

이 정보를 제공하고 추가하는 새 경고를 만들었습니다. 또한 최신 경고(ARM_OperationFromSuspiciousIP, ARM_OperationFromSuspiciousProxyIP)에는 Microsoft Defender for Cloud Apps(이전의 Microsoft Cloud App Security)에 대한 라이선스가 필요하지 않습니다.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001의 레거시 구현이 새 ISO 27001:2013으로 대체되고 있습니다.

**변경 예상 날짜:** 2021년 11월

ISO 27001의 레거시 구현은 Defender for Cloud의 규정 준수 대시보드에서 제거됩니다. Defender for Cloud를 사용하여 ISO 27001 규정 준수를 추적하는 경우 모든 관련 관리 그룹 또는 구독에 대한 새 ISO 27001:2013 표준을 온보드하면 현재 레거시 ISO 27001이 대시보드에서 곧 제거됩니다.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001의 레거시 구현을 제거하는 방법에 대한 메시지를 보여주는 Defender for Cloud의 규정 준수 대시보드." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::


### <a name="inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name"></a>온-프레미스 머신의 인벤토리 표시는 리소스 이름에 대해 다른 템플릿을 사용합니다.

**변경 예상 날짜:** 2021년 11월

[자산 인벤토리](asset-inventory.md)의 리소스 표시를 개선하기 위해 템플릿에서 "source-computer-IP" 요소를 제거하여 온-프레미스 머신 이름을 지정합니다.

- **현재 형식:** ``machine-name_source-computer-id_VMUUID``
- **업데이트에서:** ``machine-name_VMUUID``


### <a name="multiple-changes-to-identity-recommendations"></a>ID 권장 사항에 대한 여러 변경 사항

**변경 예상 날짜:** 2021년 12월

Defender for Cloud에는 사용자 및 계정 관리를 개선하기 위한 여러 가지 권장 사항이 포함되어 있습니다. 12월에는 아래와 같이 변경됩니다.

- **향상된 새로 고침 간격** - 현재 ID 권장 사항의 새로 고침 간격은 24시간입니다. 이 업데이트는 해당 간격을 12시간으로 줄입니다.

- **계정 예외 기능** - Defender for Cloud에는 사용 환경을 사용자 지정하고 보안 점수가 조직의 보안 우선 순위를 반영하는지 확인하기 위한 많은 기능이 있습니다. 보안 권장 사항의 제외 옵션은 이러한 기능 중 하나입니다. 전체 개요와 지침은 [보안 점수에서 리소스 및 권장 사항 제외](exempt-resource.md)을 참조하세요. 이 업데이트를 통해 다음 표에 나열된 8가지 권장 사항에 따라 특정 계정을 평가에서 제외할 수 있습니다.

    일반적으로 이러한 계정은 조직의 MFA 요구 사항에서 의도적으로 제외되는 경우가 많기 때문에 MFA 권장 사항에서 긴급 "비상" 계정을 제외하는 것이 일반적입니다. 또는 액세스를 허용할 외부 계정이 있지만 MFA를 사용하도록 설정하지 않은 경우도 있습니다.

    > [!TIP]
    > 계정을 제외하면 비정상으로 표시되지 않으며 구독이 비정상으로 표시되지도 않습니다.

    |권장| 평가 키|
    |-|-|
    |[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/94290b00-4d0c-d7b4-7cea-064a9554e681)|94290b00-4d0c-d7b4-7cea-064a9554e681|
    |[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/151e82c5-5341-a74b-1eb0-bc38d2c84bb5)|151e82c5-5341-a74b-1eb0-bc38d2c84bb5|
    |[구독에 대한 쓰기 권한이 있는 계정에서 MFA를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/57e98606-6b1e-6193-0e3d-fe621387c16b)|57e98606-6b1e-6193-0e3d-fe621387c16b|
    |[소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/c3b6ae71-f1f0-31b4-e6c1-d5951285d03d)|c3b6ae71-f1f0-31b4-e6c1-d5951285d03d|
    |[읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b)|a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b|
    |[쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/04e7147b-0deb-9796-2e5c-0336343ceb3d)|04e7147b-0deb-9796-2e5c-0336343ceb3d|
    |[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)|e52064aa-6853-e252-a11e-dffc675689c2|
    |[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|00c6d40b-e990-6acf-d4f3-471e747a27c4|
    |||
 
- **권장 사항 이름 바꾸기** - 이 업데이트에서 두 권장 사항의 이름을 바꿉니다. 또한 해당 설명을 수정하고 있습니다. 평가 키는 변경되지 않고 그대로 유지됩니다. 


    |속성  |현재 값  | 업데이트에서|
    |---------|---------|---------|
    |평가 키     | e52064aa-6853-e252-a11e-dffc675689c2        | 변경 안 됨|
    |이름     |[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)         |Active Directory에서 차단되고 소유자 권한이 있는 계정에서 구독을 제거해야 합니다.        |
    |설명     |로그인할 수 없도록 차단된 사용자 계정은 구독에서 제거해야 합니다.<br>이러한 계정은 검색하지 않고 데이터에 액세스하는 방법을 찾으려는 공격자의 대상이 될 수 있습니다.|Active Directory 로그인이 차단된 사용자 계정은 구독에서 제거해야 합니다. 이러한 계정은 검색하지 않고 데이터에 액세스하는 방법을 찾으려는 공격자의 대상이 될 수 있습니다.<br>[Azure Identity Management 및 액세스 제어 보안 모범 사례](../security/fundamentals/identity-management-best-practices.md)에서 ID 경계를 보호하는 방법에 대해 자세히 알아보세요.|
    |관련 정책     |[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)         |Active Directory에서 차단되고 소유자 권한이 있는 계정에서 구독을 제거해야 합니다. |
    |||

    |속성  |현재 값  | 업데이트에서|
    |---------|---------|---------|
    |평가 키     | 00c6d40b-e990-6acf-d4f3-471e747a27c4        | 변경 안 됨|
    |이름     |[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|Active Directory에서 차단되고, 읽기 및 쓰기 권한이 있는 계정에서 구독을 제거해야 합니다.|
    |설명     |로그인할 수 없도록 차단된 사용자 계정은 구독에서 제거해야 합니다.<br>이러한 계정은 검색하지 않고 데이터에 액세스하는 방법을 찾으려는 공격자의 대상이 될 수 있습니다.|Active Directory 로그인이 차단된 사용자 계정은 구독에서 제거해야 합니다. 이러한 계정은 검색하지 않고 데이터에 액세스하는 방법을 찾으려는 공격자의 대상이 될 수 있습니다.<br>[Azure Identity Management 및 액세스 제어 보안 모범 사례](../security/fundamentals/identity-management-best-practices.md)에서 ID 경계를 보호하는 방법에 대해 자세히 알아보세요.|
    |관련 정책     |[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)|Active Directory에서 차단되고, 읽기 및 쓰기 권한이 있는 계정에서 구독을 제거해야 합니다.|
    |||
 

### <a name="changes-to-a-security-alert-from-microsoft-defender-for-storage"></a>Microsoft Defender for Storage의 보안 경고 변경 내용

**변경 예상 날짜:** 2021년 11월

Microsoft Defender for Storage에서 제공하는 미리 보기 경고 중 하나는 검색된 의심스러운 이벤트에 대한 보다 명확한 설명을 제공하기 위해 두 개의 새로운 권장 사항으로 나뉩니다. 이 경고는 Azure Blob Storage에만 해당됩니다.

**경고 유형도 변경되고 있습니다.**

- 변경 전 경고는 다음과 같습니다.<br>
    "미리 보기 - 공용 스토리지 컨테이너의 익명 검색"<br>(Storage.Blob_ContainerAnonymousScan)

- 이 변경 내용에는 두 가지 권장 사항이 있습니다.

    - "외부 검사 도구 또는 스크립트를 통해 검색된 스토리지 컨테이너 열기"<br>(Storage.Blob_OpenContainersScanning.FailedAttempt)
    - "외부 검사 스크립트 또는 도구를 통해 열린 스토리지 컨테이너의 성공적인 검색"<br>(Storage.Blob_OpenContainersScanning.SuccessfulDiscovery)

이러한 경고에 대한 자세한 내용은 변경 내용을 릴리스할 때 게시됩니다.


### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>SQL 데이터베이스의 중요한 데이터 분류에 대한 개선된 권장 사항

**변경 예상 날짜:** 2022년 1분기

**데이터 분류 적용** 보안 제어에서 **SQL 데이터베이스의 중요 데이터를 분류해야 함** 권장 사항은 Microsoft의 데이터 분류 전략에 맞춰 향상된 새 버전으로 대체됩니다. 그 결과 권장 사항의 ID도 변경됩니다(현재 b0df6f56-862d-4730-8597-38c0fd4ebd59임).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>엔드포인트 보호 솔루션 관리를 위한 권장 사항 변경

**변경 예상 날짜:** 2022년 2월

2021년 8월에 컴퓨터에 엔드포인트 보호 솔루션을 배포하고 유지 관리하기 위한 두 가지 새로운 **미리 보기** 권장 사항을 추가했습니다. 자세한 내용은 [릴리스 정보](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)를 참조하세요.

권장 사항이 일반 공급으로 출시되면 다음과 같은 기존 권장 사항을 대체합니다.

- **컴퓨터에 Endpoint Protection을 설치해야 함** 으로 인해 다음이 바뀝니다.
    - [가상 머신에 엔드포인트 보호 솔루션 설치(키: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [머신에 엔드포인트 보호 솔루션 설치(키: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **컴퓨터에서 엔드포인트 보호 상태 문제를 해결해야 함** 은 이름이 같은 기존 권장 사항을 대체합니다. 두 가지 권장 사항에는 서로 다른 평가 키가 있습니다.
    - **미리 보기** 권장 사항에 대한 평가 키: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - **GA** 권장 사항에 대한 평가 키: 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

자세한 정보:
- [Defender for Cloud에서 지원하는 엔드포인트 보호 솔루션](supported-machines-endpoint-solutions-clouds.md#endpoint-supported)
- [이 권장 사항이 배포된 솔루션의 상태를 평가하는 방법](endpoint-protection-recommendations-technical.md)



## <a name="next-steps"></a>다음 단계

Defender for Cloud에 대한 모든 최근 변경 내용을 보려면 [Microsoft Defender for Cloud의 새로운 기능](release-notes.md)을 참조하세요.

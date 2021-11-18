---
title: Microsoft Sentinel용 스키마 감시 목록 템플릿 | Microsoft Docs
description: Microsoft Sentinel의 각 기본 제공 감시 목록 템플릿에 사용되는 스키마에 대해 알아봅니다.
author: batamig
ms.author: bagol
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 645f958ec8448a67bd9b983a3956688d2ed91a71
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756213"
---
# <a name="microsoft-sentinel-built-in-watchlist-template-schemas-public-preview"></a>Microsoft Sentinel 기본 제공 감시 목록 템플릿 스키마(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft Sentinel에서 제공하는 각 기본 제공 감시 목록 템플릿에 사용되는 스키마에 대해 자세히 설명합니다. 자세한 내용은 [템플릿을 사용하여 새 관심 목록 만들기(공개 미리 보기)](watchlists.md#create-a-new-watchlist-using-a-template-public-preview)를 참조하세요.

> [!IMPORTANT]
> Microsoft Sentinel 감시 목록 템플릿은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>


## <a name="high-value-assets"></a>높은 가치 자산

높은 가치 자산 관심 목록에는 조직에서 중요한 가치가 있는 디바이스, 리소스 및 기타 자산이 나열되며 다음 필드가 포함됩니다.

| 필드 이름 | 형식                              | 예제                                                                                                                                | 필수/선택 사항 |
| ---------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **자산 유형** | String                              | `Device`, `Azure resource`, `AWS resource`, `URL`, `SPO`, `File share`, `Other`                                                                      | 필수          |
| **자산 ID**   | 자산 유형에 따른 문자열 | `/subscriptions/d1d8779d-38d7-4f06-91db-9cbc8de0176f/resourceGroups/SOC-Purview/providers/Microsoft.Storage/storageAccounts/purviewadls` | 필수          |
| **자산 이름** | String                              | `Microsoft.Storage/storageAccounts/purviewadls`                                                                                          | Optional           |
| **자산 FQDN** | FQDN                                | `Finance-SRv.local.microsoft.com`                                                                                                        | 필수          |
| **IP 주소** | IP                                  | `1.1.1.1`                                                                                                                                | 선택 사항           |
| **태그**       | 목록                                | `["SAW user","Blue Ocean team"] `                                                                                                        | 선택 사항           |
| | | | |

## <a name="vip-users"></a>VIP 사용자

VIP 사용자 관심 목록에는 조직에서 영향력이 큰 직원의 사용자 계정이 나열되며 다음 값이 포함됩니다.

| 필드 이름          | 형식 | 예제                                             | 필수/선택 사항 |
| ------------------- | ------ | --------------------------------------------------- | ------------------ |
| **사용자 ID**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 선택 사항           |
| **사용자 AAD 개체 ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 선택 사항           |
| **사용자 온-프레미스 Sid**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 선택 사항           |
| **사용자 계정 이름** | UPN    | `JeffL@seccxp.ninja`                                  | 필수          |
| **태그**                | 목록   | `["SAW user","Blue Ocean team"]`                      | 선택 사항           |
| | | | |

## <a name="network-mapping"></a>네트워크 매핑

네트워크 매핑 관심 목록에는 IP 서브넷과 해당 조직 컨텍스트가 나열되며 다음 필드가 포함됩니다.

| 필드 이름 | 형식       | 예제                      | 필수/선택 사항 |
| ---------- | ------------ | ---------------------------- | ------------------ |
| **IP 서브넷**  | 서브넷 범위 |` 198.51.100.0/24 - 198….../22` | 필수          |
| **범위 이름** | String       | `DMZ`                          | Optional           |
| **태그**       | 목록         | `["Example","Example"]`        | 선택 사항           |
| | | | |

## <a name="terminated-employees"></a>해고된 직원

해고된 직원 관심 목록에는 해고되었거나 해고될 직원의 사용자 계정이 나열되며 다음 필드가 포함됩니다.

| 필드 이름          | 형식                                                                          | 예제                              | 필수/선택 사항 |
| ------------------- | ------------------------------------------------------------------------------- | ------------------------------------ | ------------------ |
| **사용자 ID**     | UID                                                                             | `52322ec8-6ebf-11eb-9439-0242ac130002` | 선택 사항           |
| **사용자 AAD 개체 ID**  | SID                                                                             | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e` | 선택 사항           |
| **사용자 온-프레미스 Sid**    | SID                                                                             | `S-1-12-1-4141952679-1282074057-123`   | 선택 사항           |
| **사용자 계정 이름** | UPN                                                                             | `JeffL@seccxp.ninja`                  | 필수          |
| **UserState**           | String <br><br>`Notified` 또는 `Terminated`를 사용하는 것이 좋습니다. | `Terminated`                           | 필수          |
| **알림 날짜**  | 타임스탬프 - 일                                                                 | `01.12.20`                             | 선택 사항           |
| **종료 날짜**    | 타임스탬프 - 일                                                                 | `01.01.21`                            | 필수          |
| **태그**                | 목록                                                                            | `["SAW user","Amba Wolfs team"]`       | 선택 사항           |
| | | | |


## <a name="identity-correlation"></a>ID 상관 관계

ID 상관 관계 관심 목록에는 동일한 사용자에게 속하는 관련 사용자 계정이 나열되며 다음 필드가 포함됩니다.

| 필드 이름                       | 형식  | 예제                                             | 필수/선택 사항 |
| -------------------------------- | ------- | --------------------------------------------------- | ------------------ |
| **사용자 ID**                  | UID     | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 선택 사항           |
| **사용자 AAD 개체 ID**               | SID     | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 선택 사항           |
| **사용자 온-프레미스 Sid**                 | SID     | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 선택 사항           |
| **사용자 계정 이름**              | UPN     | `JeffL@seccxp.ninja`                                  | 필수          |
| **직원 ID**                      | String  | `8234123`                                             | Optional           |
| **Email**                            | 메일   | `JeffL@seccxp.ninja`                                  | 선택 사항           |
| **연결된 권한 있는 계정 ID** | UID/SID | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 선택 사항           |
| **연결된 권한 있는 계정**    | UPN     | `Admin@seccxp.ninja`                                  | 선택 사항           |
| **태그**                             | 목록    | `["SAW user","Amba Wolfs team"]`                      | 선택 사항           |
| | | | |

## <a name="service-accounts"></a>서비스 계정

서비스 계정 관심 목록에는 서비스 계정 및 해당 소유자가 나열돠며 다음 필드가 포함됩니다.

| 필드 이름                | 형식 | 예제                                             | 필수/선택 사항 |
| ------------------------- | ------ | --------------------------------------------------- | ------------------ |
| **서비스 식별자**        | UID    | `1111-112123-12312312-123123123`                      | 선택 사항           |
| **서비스 AAD 개체 ID**     | SID    | `11123-123123-123123-123123`                          | 선택 사항           |
| **서비스 온-프레미스 Sid**       | SID    | `S-1-12-1-3123123-123213123-12312312-2916039507`      | 선택 사항           |
| **서비스 사용자 이름**    | UPN    | `myserviceprin@contoso.com`                           | 필수          |
| **소유자 사용자 ID**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 선택 사항           |
| **소유자 사용자 AAD 개체 ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 선택 사항           |
| **소유자 사용자 온-프레미스 Sid**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 선택 사항           |
| **소유자 사용자 계정 이름** | UPN    | `JeffL@seccxp.ninja`                                  | 필수          |
| **태그**                      | 목록   | `["Automation Account","GitHub Account"]`             | 선택 사항           |
| | | | |

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Microsoft 센티널 Watchlists 사용](watchlists.md)을 참조 하세요.

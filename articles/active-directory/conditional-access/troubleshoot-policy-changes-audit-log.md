---
title: 조건부 액세스 정책 변경 문제 해결 - Azure Active Directory
description: Azure AD 감사 로그를 사용하여 조건부 액세스 정책의 변경을 진단합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: b115d370c00ed92464e0b08d3b44ced6cb2a13e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566990"
---
# <a name="troubleshooting-conditional-access-policy-changes"></a>조건부 액세스 정책 변경 문제 해결

Azure AD(Azure Active Directory) 감사 로그는 사용자 환경에서 조건부 액세스 정책 변경이 발생한 이유 및 방법과 관련된 문제를 해결할 때 중요한 정보 출처입니다.

감사 로그 데이터는 기본적으로 30일 동안만 유지되므로 일부 조직에게는 충분히 길지 않을 수 있습니다. 조직은 Azure AD에서 다음과 같이 진단 설정을 변경하여 더 긴 기간 동안 데이터를 저장할 수 있습니다. 

- Log Analytics 작업 영역에 데이터 보내기
- 스토리지 계정에 데이터 보관
- 이벤트 허브로 데이터 스트리밍
- 파트너 솔루션에 데이터 보내기
 
이러한 옵션은 **Azure Portal** > **Azure Active Directory**, **진단 설정** > **설정 편집** 에서 찾을 수 있습니다. 진단 설정이 없는 경우 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md) 문서의 지침에 따라 만듭니다. 

## <a name="use-the-audit-log"></a>감사 로그 사용

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **감사 로그** 로 이동합니다.
1. 쿼리하려는 **날짜** 범위를 선택합니다.
1. **작업** 을 선택하고 다음 중 하나를 선택합니다.
   1. **조건부 액세스 정책 추가** - 이 작업은 새로 만든 정책을 나열합니다.
   1. **조건부 액세스 정책 업데이트** - 이 작업은 변경된 정책을 나열합니다.
   1. **조건부 액세스 정책 삭제** - 이 작업은 삭제된 정책을 나열합니다.

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png" alt-text="조건부 액세스 정책에 대한 이전 및 새 JSON 값을 보여 주는 감사 로그 항목" lightbox="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png":::

## <a name="use-log-analytics"></a>Log Analytics 사용

Log Analytics를 사용하면 조직에서 기본 제공 쿼리 또는 만든 사용자 지정 Kusto 쿼리를 사용하여 데이터를 쿼리할 수 있습니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../../azure-monitor/logs/get-started-queries.md)을 참조하세요.

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png" alt-text="새 값과 이전 값 위치를 보여 주는 조건부 액세스 정책 업데이트에 대한 Log Analytics 쿼리" lightbox="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png":::

사용하도록 설정하면 **Azure Portal** > **Azure AD** > **Log Analytics** 에서 Log Analytics에 액세스할 수 있습니다. 조건부 액세스 관리자에게 가장 흥미로운 테이블은 **AuditLogs** 입니다.

```kusto
AuditLogs 
| where OperationName == "Update conditional access policy"
```

변경 내용은 **TargetResources** > **modifiedProperties** 에서 확인할 수 있습니다.

## <a name="reading-the-values"></a>값 읽기

감사 로그 및 Log Analytics의 이전 값과 새 값은 JSON 형식입니다. 두 값을 비교하여 정책의 변경 내용을 확인합니다.

이전 정책 예제:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
            "a0d3eb5b-6cbe-472b-a960-0baacbd02b51"
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:40.781994+00:00",
    "state": "enabled"
}

```

업데이트된 정책 예제:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:54.9739405+00:00",
    "state": "enabled"
}

``` 

위의 예제에서 업데이트된 정책은 권한 부여 컨트롤에 사용 약관을 포함하지 않습니다.

### <a name="restoring-conditional-access-policies"></a>조건부 액세스 정책 복원

Microsoft Graph API를 사용하여 조건부 액세스 정책을 프로그래밍 방식으로 업데이트하는 방법에 대한 자세한 내용은 [조건부 액세스: 프로그래밍 방식 액세스](howto-conditional-access-apis.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 모니터링이란?](../reports-monitoring/overview-monitoring.md)
- [Azure Active Directory용 로그 분석 보기 설치 및 사용](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [조건부 액세스: 프로그래밍 방식 액세스](howto-conditional-access-apis.md)

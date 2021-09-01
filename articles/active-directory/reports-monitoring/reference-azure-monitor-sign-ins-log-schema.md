---
title: Azure Monitor에서 로그인 로그 스키마 | Microsoft Docs
description: Azure Monitor에서 사용을 위해 Azure AD 로그인 로그 스키마 설명
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/26/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d0cc6d793c70f9466a09c9ad54e3588295da5ae
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969160"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Azure Monitor에서 Azure AD 로그인 로그 스키마 해석

이 문서에서는 Azure Monitor에서 Azure AD(Azure Active Directory) 로그인 로그 스키마에 대해 설명합니다. 로그인 관련 정보는 대부분 `records` 개체의 *Properties* 특성에서 제공됩니다.


```json
{
  "time": "2019-03-12T16:02:15.5522137Z",
  "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
  "operationName": "Sign-in activity",
  "operationVersion": "1.0",
  "category": "SignInLogs",
  "tenantId": "<TENANT ID>",
  "resultType": "50140",
  "resultSignature": "None",
  "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.",
  "durationMs": 0,
  "callerIpAddress": "<CALLER IP ADDRESS>",
  "correlationId": "a75a10bd-c126-486b-9742-c03110d36262",
  "identity": "Timothy Perkins",
  "Level": 4,
  "location": "US",
  "properties": 
       {
    "id": "0231f922-93fa-4005-bb11-b344eca03c01",
    "createdDateTime": "2019-03-12T16:02:15.5522137+00:00",
    "userDisplayName": "Timothy Perkins",
    "userPrincipalName": "<USER PRINCIPAL NAME>",
    "userId": "<USER ID>",
    "appId": "<APPLICATION ID>",
    "appDisplayName": "Azure Portal",
    "ipAddress": "<IP ADDRESS>",
    "status": {
      "errorCode": 50140,
      "failureReason": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
    },
    "clientAppUsed": "Browser",
    "userAgent": "<USER AGENT>",
    "deviceDetail":
   {
     "deviceId": "8bfcb982-6856-4402-924c-ada2486321cc",
     "operatingSystem": "Windows 10",
     "browser": "Chrome 72.0.3626"
     },
    "location":
    {
      "city": "Bellevue",
      "state": "Washington",
      "countryOrRegion": "US",
      "geoCoordinates": 
     {
        "latitude": 45,
        "longitude": 122
      }
    },
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262",
    "conditionalAccessStatus": "notApplied",
    "appliedConditionalAccessPolicies": [
      {
        "id": "ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
        "displayName": "HR app access policy",
        "enforcedGrantControls": [
          "Mfa"
        ],
        "enforcedSessionControls": [],
        "result": "notApplied",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "b915a70b-2eee-47b6-85b6-ff4f4a66256d",
        "displayName": "MFA for all but global support access",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "830f27fa-67a8-461f-8791-635b7225caf1",
        "displayName": "Header Based Application Control",
        "enforcedGrantControls": [
          "Mfa"
        ],
        "enforcedSessionControls": [],
        "result": "notApplied",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
        "displayName": "MFA for everyones",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "52924e0f-798b-4afd-8c42-49055c7d6395",
        "displayName": "Device compliant",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      }
    ],
    "originalRequestId": "f2f0a254-f831-43b9-bcb0-2646fb645c00",
    "isInteractive": true,
    "authenticationProcessingDetails": [
      {
        "key": "Login Hint Present",
        "value": "True"
      }
    ],
    "networkLocationDetails": [],
    "processingTimeInMilliseconds": 238,
    "riskDetail": "none",
    "riskLevelAggregated": "none",
    "riskLevelDuringSignIn": "none",
    "riskState": "none",
    "riskEventTypes": [],
    "riskEventTypes_v2": [],
    "resourceDisplayName": "Office 365 SharePoint Online",
    "resourceId": "00000003-0000-0ff1-ce00-000000000000",
    "resourceTenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "homeTenantId": "<USER HOME TENANT ID>",
    "tokenIssuerName": "",
    "tokenIssuerType": "AzureAD",
    "authenticationDetails": [
      {
        "authenticationStepDateTime": "2019-03-12T16:02:15.5522137+00:00",
        "authenticationMethod": "Previously satisfied",
        "succeeded": true,
        "authenticationStepResultDetail": "First factor requirement satisfied by claim in the token",
        "authenticationStepRequirement": "Primary authentication",
        "StatusSequence": 0,
        "RequestSequence": 0
      },
      {
        "authenticationStepDateTime": "2021-08-12T15:48:12.8677211+00:00",
        "authenticationMethod": "Previously satisfied",
        "succeeded": true,
        "authenticationStepResultDetail": "MFA requirement satisfied by claim in the token",
        "authenticationStepRequirement": "Multi-factor authentication"
      }
    ],
    "authenticationRequirementPolicies": [
      {
        "requirementProvider": "multiConditionalAccess",
        "detail": "Conditional Access"
      }
    ],
    "authenticationRequirement": "multiFactorAuthentication",
    "alternateSignInName": "<ALTERNATE SIGN IN>",
    "signInIdentifier": "<SIGN IN IDENTIFIER>",
    "servicePrincipalId": "",
    "userType": "Member",
    "flaggedForReview": false,
    "isTenantRestricted": false,
    "autonomousSystemNumber": 8000,
    "crossTenantAccessType": "none",
    "privateLinkDetails": {},
    "ssoExtensionVersion": ""
    }
}

```


## <a name="field-descriptions"></a>필드 설명

| 필드 이름 | 키 | Description |
| --- | --- | --- | 
| Time |  - | UTC 형식의 날짜 및 시간입니다. |
| ResourceId | - | 이 값이 매핑 해제되며 이 필드를 안전하게 무시할 수 있습니다.  |
| OperationName | - | 로그인의 경우 이 값은 항상 *로그인 활동* 입니다. |
| OperationVersion | - | 클라이언트에서 요청한 REST API 버전입니다. |
| 범주 | - | 로그인의 경우 이 값은 항상 *SignIn* 입니다. | 
| TenantId | - | 로그와 연결된 테넌트 GUID입니다. |
| ResultType | - | 로그인 작업의 결과는 성공의 경우 `0`, 실패의 경우 ‘오류 코드’일 수 있습니다. | 
| ResultSignature | - | 이 값은 항상 ‘None’입니다. |
| ResultDescription | 해당 없음 또는 공백 | 로그인 작업에 대한 오류 설명을 제공합니다. |
| riskDetail | riskDetail | 위험한 사용자, 로그인 또는 위험 검색의 특정 상태 뒤에 'reason'을 제공합니다. 사용 가능한 값은 `none`, `adminGeneratedTemporaryPassword`, `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset`, `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe`, `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser`, `adminConfirmedSigninCompromised`, `unknownFutureValue`입니다. `none` 값은 지금까지 사용자 또는 로그인에 대해 수행된 작업이 없음을 의미합니다. <br>**참고:** 이 속성에 대한 세부 정보를 보려면 Azure AD Premium P2 라이선스가 필요합니다. 다른 라이선스는 `hidden` 값을 반환합니다. |
| riskEventTypes | riskEventTypes | 로그인과 관련된 위험 감지 유형입니다. 사용 가능한 값은 `unlikelyTravel`, `anonymizedIPAddress`, `maliciousIPAddress`, `unfamiliarFeatures`, `malwareInfectedIPAddress`, `suspiciousIPAddress`, `leakedCredentials`, `investigationsThreatIntelligence`, `generic` 및 `unknownFutureValue`입니다. |
| authProcessingDetails | Azure AD 앱 인증 라이브러리 | "패밀리: ADAL 라이브러리: ADAL.JS 1.0.0 플랫폼: JS" 형식의 패밀리, 라이브러리 및 플랫폼 정보를 포함합니다. |
| authProcessingDetails | IsCAEToken | 값은 True 또는 False입니다. |
| riskLevelAggregated | riskLevel | 집계된 위험 수준. 사용 가능한 값은 `none`, `low`, `medium`, `high`, `hidden` 및 `unknownFutureValue`입니다. `hidden` 값은 사용자 또는 로그인이 Azure AD ID 보호에 대해 활성화되지 않았음을 의미합니다. **참고:** 이 속성에 대한 세부 정보는 Azure AD Premium P2 고객만 사용할 수 있습니다. 다른 모든 고객은 `hidden`으로 반환됩니다. |
| riskLevelDuringSignIn | riskLevel | 로그인 중의 위험 수준. 사용 가능한 값은 `none`, `low`, `medium`, `high`, `hidden` 및 `unknownFutureValue`입니다. `hidden` 값은 사용자 또는 로그인이 Azure AD ID 보호에 대해 활성화되지 않았음을 의미합니다. **참고:** 이 속성에 대한 세부 정보는 Azure AD Premium P2 고객만 사용할 수 있습니다. 다른 모든 고객은 `hidden`으로 반환됩니다. |
| riskState | riskState | 위험한 사용자, 로그인 또는 위험 감지의 상태를 보고합니다. 사용 가능한 값은 `none`, `confirmedSafe`, `remediated`, `dismissed`, `atRisk`, `confirmedCompromised`, `unknownFutureValue`입니다. |
| DurationMs | - | 이 값이 매핑 해제되며 이 필드를 안전하게 무시할 수 있습니다. |
| callerIpAddress | - | 요청한 클라이언트의 IP 주소입니다. | 
| CorrelationId | - | 클라이언트에서 전달한 선택적 GUID입니다. 이 값은 클라이언트 쪽 작업을 서버 쪽 작업과 상관 관계를 지정하는 데 도움이 될 수 있으며, 서비스에 걸쳐 있는 로그를 추적하는 경우에 유용합니다. |
| ID | - | 요청할 때 제공된 토큰의 ID입니다. 사용자 계정, 시스템 계정 또는 서비스 사용자일 수 있습니다. |
| Level | - | 메시지의 형식을 제공합니다. 감사의 경우 항상 *Informational* 입니다. |
| 위치 | - | 로그인 활동의 위치를 제공합니다. |
| 속성 | - | 로그인과 연결된 모든 속성을 나열합니다.|



## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](./overview-reports.md)
* [Azure 플랫폼 로그에 대해 자세히 알아보기](../../azure-monitor/essentials/platform-logs-overview.md)
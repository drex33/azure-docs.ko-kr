---
title: MS Graph API를 사용하여 클라우드 동기화를 프로그래밍 방식으로 구성하는 방법
description: 이 토픽에서는 Graph API를 사용하여 인바운드 동기화를 사용하도록 설정하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14b9ae4733773c45650f2efae8ad72d41f17aff2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018227"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>MS Graph API를 사용하여 클라우드 동기화를 프로그래밍 방식으로 구성하는 방법

다음 문서에서는 MSGraph API만 사용하여 동기화 프로필을 처음부터 복제하는 방법을 설명합니다.  
이 작업을 수행하는 방법의 구조는 다음 단계로 구성됩니다.  아래에 이 계정과 키의 예제가 나와 있습니다.

- [MS Graph API를 사용하여 클라우드 동기화를 프로그래밍 방식으로 구성하는 방법](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [기본 설정](#basic-setup)
    - [테넌트 플래그 사용](#enable-tenant-flags)
  - [서비스 주체 만들기](#create-service-principals)
  - [동기화 작업 만들기](#create-sync-job)
  - [대상 도메인 업데이트](#update-targeted-domain)
  - [구성 블레이드에서 암호 해시 동기화 사용](#enable-sync-password-hashes-on-configuration-blade)
  - [실수로 삭제](#accidental-deletes)
    - [임계값 사용 및 설정](#enabling-and-setting-the-threshold)
    - [삭제 허용](#allowing-deletes)
  - [동기화 작업 시작](#start-sync-job)
  - [상태 검토](#review-status)
  - [다음 단계](#next-steps)

이러한 [Windows PowerShell용 Microsoft Azure Active Directory 모듈](/powershell/module/msonline/) 명령을 사용하여 해당 테넌트에 대한 관리 웹 서비스를 호출하기 위한 필수 구성 요소인 프로덕션 테넌트에 대해 동기화를 사용하도록 설정할 수 있습니다.

## <a name="basic-setup"></a>기본 설정

### <a name="enable-tenant-flags"></a>테넌트 플래그 지정

```powershell
Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
```

이러한 두 명령의 첫 번째 명령에는 Azure Active Directory 자격 증명이 필요합니다. 이러한 cmdlet은 테넌트를 암시적으로 식별하고 동기화에 사용하도록 설정할 수 있습니다.

## <a name="create-service-principals"></a>서비스 주체 만들기

다음으로 [AD2AAD 애플리케이션/서비스 주체](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true)를 만들어야 합니다.

이 애플리케이션 ID 1a4721b3-e57f-4451-ae87-ef078703ec94를 사용해야 합니다. displayName은 포털에서 사용되는 경우 AD 도메인 URL(예: contoso.com)이지만 다른 이름으로 지정할 수 있습니다.

```
POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
Content-type: application/json
{
    displayName: [your app name here]
}
```

## <a name="create-sync-job"></a>동기화 작업 만들기

위 명령의 출력은 생성된 서비스 주체의 objectId를 반환합니다. 이 예제에서 objectId는 614ac0e9-a59b-481f-bd8f-79a73d167e1c입니다.  Microsoft Graph를 사용하여 해당 서비스 주체에 synchronizationJob을 추가합니다.

동기화 작업 만들기에 대한 설명서는 [여기](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)에서 확인할 수 있습니다.

위의 ID를 기록하지 않은 경우 다음 MS Graph 호출을 실행하여 서비스 주체를 찾을 수 있습니다. 해당 호출을 수행하려면 Directory.Read.All 권한이 필요합니다.

`GET https://graph.microsoft.com/beta/servicePrincipals`

그런 다음, 출력에서 앱 이름을 찾습니다.

다음 두 가지 명령을 실행하여 사용자/그룹 프로비저닝용 작업과 암호 해시 동기화용 작업을 만듭니다. 동일한 요청을 두 번 하지만 템플릿 ID는 다릅니다.

다음 두 요청을 호출합니다.

```
POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
Content-type: application/json
{
"templateId":"AD2AADProvisioning"
} 
```

```
POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
Content-type: application/json
{
"templateId":"AD2AADPasswordHash"
}
```

둘 다 만들려면 두 번의 호출이 필요합니다.

예제 반환 값(프로비전의 경우):

```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>대상 도메인 업데이트

이 테넌트에서 서비스 주체의 개체 식별자 및 애플리케이션 식별자는 다음과 같습니다.

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-000000000000 DisplayName: testApp

이 구성이 대상으로 하는 도메인을 업데이트해야 하므로 이 도메인에 대한 비밀을 업데이트합니다.

사용하는 도메인 이름이 온-프레미스 도메인 컨트롤러에 대해 설정한 URL과 동일한지 확인합니다.

```
PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
```

수행하려는 작업을 기준으로 아래 값 배열에 다음과 같은 키/값 쌍을 추가합니다.

- PHS를 사용하도록 설정하고 테넌트 플래그 {키: "AppKey", 값: "{" appKeyScenario ":" AD2AADPasswordHash "}"} 동기화

- 테넌트 플래그 동기화만 사용(PHS를 설정하지 않음) {키: "AppKey", 값: "{" appKeyScenario ":" AD2AADProvisioning "}"}

```
Request body –
{
   "value": [
              {
                "key": "Domain",
                "value": "{\"domain\":\"ad2aadTest.com\"}"
              }
            ]
}
```

예상 응답은 다음과 같습니다. HTTP/1.1 204 No Content

여기서 강조 표시된 "도메인" 값은 Azure Active Directory에 항목을 프로비전할 온-프레미스 Active Directory 도메인의 이름입니다.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>구성 블레이드에서 암호 해시 동기화 사용

 이 섹션에서는 특정 구성에 대한 암호 해시 동기화를 설정하는 방법을 설명합니다. 이는 테넌트 수준 기능 플래그를 사용하도록 설정하는 AppKey 비밀과 다릅니다. 단일 도메인/구성에만 해당됩니다. 엔드투엔드 작업을 수행하려면 애플리케이션 키를 PHS로 설정해야 합니다.

1. 스키마를 가져옵니다(경고: 매우 큼).

   ```
   GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
   ```

2. 이 CredentialData 특성 매핑을 사용합니다.

   ```
   {
   "defaultValue": null,
   "exportMissingReferences": false,
   "flowBehavior": "FlowWhenChanged",
   "flowType": "Always",
   "matchingPriority": 0,
   "targetAttributeName": "CredentialData",
   "source": {
   "expression": "[PasswordHash]",
   "name": "PasswordHash",
   "type": "Attribute",
   "parameters": []
   }
   ```

3. 스키마에서 다음 이름을 사용하여 다음 개체 매핑을 찾습니다.
   - Active Directory 사용자 프로비전
   - Active Directory inetOrgPersons 프로비전

   개체 매핑이 schema.synchronizationRules[0].objectMappings 내에 있습니다(현재 동기화 규칙이 1개만 있다고 가정할 수 있음).

4. (2)단계에서 CredentialData 매핑을 수행하고, (3)단계에서 이를 개체 매핑에 삽입합니다.

   개체 매핑은 다음과 같습니다.

   ```
   {
   "enabled": true,
   "flowTypes": "Add,Update,Delete",
   "name": "Provision Active Directory users",
   "sourceObjectName": "user",
   "targetObjectName": "User",
   "attributeMappings": [
   ...
   } 
   ```

   위의 **AD2AADProvisioning And AD2AADPasswordHash 작업 만들기** 단계에서 매핑을 복사하여 attributeMappings 배열에 붙여넣습니다.

   이 배열의 요소 순서는 중요하지 않습니다(백 엔드가 사용자를 위해 정렬함). 이름이 배열에 이미 있는 경우(예: targetAttributeName CredentialData가 있는 attributeMappings에 항목이 이미 있는 경우) 이 특성 매핑을 추가하는 데 주의해야 합니다. 그렇지 않으면 충돌 오류가 발생하거나 기존 매핑과 새 매핑이 함께 결합될 수 있습니다(일반적으로 원하는 결과가 아님). 백 엔드는 중복 제거를 수행하지 않습니다.

   사용자와 inetOrgpersons 모두에 대해 이 작업을 수행해야 합니다.

5. 사용자가 만든 스키마를 저장합니다.

   ```
   PUT –
   https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
   ```

요청 본문에 스키마를 추가합니다.

## <a name="accidental-deletes"></a>실수로 삭제

이 섹션에서는 [실수로 삭제](how-to-accidental-deletes.md)를 프로그래밍 방식으로 사용하거나 사용하지 않도록 설정하고 사용하는 방법을 다룹니다.

### <a name="enabling-and-setting-the-threshold"></a>임계값 사용 및 설정

작업 설정당 사용할 수 있는 두 가지 임계값은 다음과 같습니다.

- DeleteThresholdEnabled - 'true'로 설정된 경우 실수로 삭제 방지를 사용할 수 있습니다. 기본값으로 'true'로 설정됩니다.
- DeleteThresholdValue - 실수로 삭제 방지를 사용하도록 설정한 경우 작업의 각 실행에서 허용되는 최대 삭제 수를 정의합니다. 이 값은 기본값으로 500으로 설정됩니다.  따라서 값이 500으로 설정되면 각 실행에서 허용되는 최대 삭제 수는 499개입니다.

삭제 임계값 설정은 `SyncNotificationSettings`의 일부이며 그래프를 통해 수정할 수 있습니다.

이 구성이 대상으로 하는 SyncNotificationSettings를 업데이트해야 하므로 비밀을 업데이트합니다.

```
PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
```

수행하려는 작업을 기준으로 아래 값 배열에 다음과 같은 키/값 쌍을 추가합니다.

```
Request body -
{
  "value":[
    {
      "key":"SyncNotificationSettings",
      "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
     }
  ]
}
```

위 예제의 "사용" 설정은 작업이 격리될 때 알림 이메일을 사용하거나 사용하지 않도록 설정하는 것입니다.

현재 비밀에 대한 PATCH 요청은 지원되지 않으므로 다른 값을 유지하려면 PUT 요청 본문의 모든 값(위 예제와 같이)을 추가해야 합니다.

다음을 사용하여 모든 비밀에 대한 기존 값을 검색할 수 있습니다.

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>삭제 허용

작업이 격리된 후 삭제가 진행되도록 하려면 범위로 "ForceDeletes"만 사용하여 다시 시작을 수행해야 합니다.

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```

## <a name="start-sync-job"></a>동기화 작업 시작

다음 명령을 통해 작업을 다시 검색할 수 있습니다.

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/`

작업 검색에 대한 설명서는 [여기](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true)에서 확인할 수 있습니다.

작업을 시작하려면 첫 번째 단계에서 만든 서비스 주체의 objectId를 사용하고 작업을 만든 요청에서 반환된 작업 식별자를 사용하여 이 요청을 발급합니다.

작업을 시작하는 방법에 대한 설명서는 [여기](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true)에서 찾을 수 있습니다.

```
POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
```

예상 응답은 다음과 같습니다. HTTP 204/콘텐츠 없음.

작업을 제어하는 다른 명령은 [여기](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true)에 설명되어 있습니다.

작업을 다시 시작하려면 다음을 사용합니다.

```
POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
{
  "criteria": {
    "resetScope": "Full"
  }
}
```

## <a name="review-status"></a>상태 검토

다음을 통해 작업 상태를 검색합니다.

```
GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
```

관련 세부 정보에 대한 반환 개체의 '상태' 섹션 아래 확인

## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
- [변환](how-to-transformation.md)
- [Azure AD 동기화 API](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)

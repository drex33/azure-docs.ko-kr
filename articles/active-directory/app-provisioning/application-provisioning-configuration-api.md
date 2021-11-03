---
title: Microsoft Graph API를 사용하여 프로비저닝 구성
description: Microsoft Graph API로 자동 프로비저닝의 구성을 자동화하여 시간을 절약하는 방법을 알아보세요.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/03/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: b9d337af752d7b463ffc4922b903e4b6064c6881
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028085"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Microsoft Graph API를 사용하여 프로비저닝 구성

Azure Portal은 한 번에 하나씩 개별 앱에 대한 프로비저닝을 구성하는 편리한 방법입니다. 하지만 애플리케이션의 인스턴스를 여러 개 또는 수백 개 만들 경우 Microsoft Graph API를 사용하여 앱 만들기 및 구성을 쉽게 자동화할 수 있습니다. 이 문서에서는 API를 통해 프로비저닝 구성을 자동화하는 방법을 설명합니다. 이 방법은 [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso)와 같은 애플리케이션에서 일반적으로 사용됩니다.

**Microsoft Graph API를 사용하여 프로비저닝 구성을 자동화하는 단계 개요**


|단계  |세부 정보  |
|---------|---------|
|[1단계: 갤러리 애플리케이션 만들기](#step-1-create-the-gallery-application)     |API 클라이언트에 로그인 <br> 갤러리 애플리케이션 템플릿 검색 <br> 갤러리 애플리케이션 만들기         |
|[2단계. 템플릿을 기준으로 프로비저닝 작업 만들기](#step-2-create-the-provisioning-job-based-on-the-template)     |프로비저닝 커넥터에 대한 템플릿 검색 <br> 프로비저닝 작업 만들기         |
|[3단계. 액세스 권한 부여](#step-3-authorize-access)     |애플리케이션에 대한 연결 테스트 <br> 자격 증명 저장         |
|[4단계. 프로비저닝 작업 시작](#step-4-start-the-provisioning-job)     |작업 시작         |
|[5단계. 프로비저닝 모니터링](#step-5-monitor-provisioning)     |프로비저닝 작업 상태 확인 <br> 프로비저닝 로그 검색         |

## <a name="step-1-create-the-gallery-application"></a>1단계: 갤러리 애플리케이션 만들기

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph Explorer(권장), Postman 또는 그 밖의 API 클라이언트에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 시작합니다.
1. “Microsoft에 로그인” 단추를 선택하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.
1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

### <a name="retrieve-the-gallery-application-template-identifier"></a>갤러리 애플리케이션 템플릿 식별자 검색
Azure AD 애플리케이션 갤러리의 각 애플리케이션에는 해당 애플리케이션의 메타데이터를 설명하는 [애플리케이션 템플릿](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true)이 있습니다. 이 템플릿을 사용하여 테넌트에서 관리를 위해 애플리케이션 및 서비스 주체의 인스턴스를 만들 수 있습니다. **AWS Single-Account Access** 에 대한 애플리케이션 템플릿의 식별자를 검색하고 응답에서 이 자습서의 후반부에서 사용할 **id** 속성 값을 기록해 둡니다.

#### <a name="request"></a>요청

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates?$filter=displayName eq 'AWS Single-Account Access'
```
#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Single-Account Access",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": "Federate to a single AWS account and use SAML claims to authorize access to AWS IAM roles. If you have many AWS accounts, consider using the AWS Single Sign-On gallery application instead."    

}
```

### <a name="create-the-gallery-application"></a>갤러리 애플리케이션 만들기

전 단계에서 애플리케이션에 대해 검색한 템플릿 ID를 사용하여 테넌트에서 애플리케이션 및 서비스 주체의 [인스턴스를 만듭니다](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta&preserve-view=true).

#### <a name="request"></a>요청


```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>응답

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>2단계: 템플릿을 기준으로 프로비저닝 작업 만들기

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>프로비저닝 커넥터에 대한 템플릿 검색

프로비저닝을 위해 설정된 갤러리의 애플리케이션에는 구성을 간소화하기 위한 템플릿이 있습니다. 아래 요청을 사용하여 [프로비저닝 구성에 대한 템플릿을 검색](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true)합니다. ID를 제공해야 합니다. ID는 위의 리소스(이 경우에는 servicePrincipal 리소스)를 참조합니다. 

#### <a name="request"></a>요청

```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```

#### <a name="response"></a>응답
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>프로비저닝 작업 만들기
프로비저닝을 사용하도록 설정하려면 먼저 [작업을 만들어야](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true) 합니다. 다음 요청을 사용하여 프로비저닝 작업을 만듭니다. 작업에 사용할 템플릿을 지정할 때 이전 단계의 templateId을 사용합니다.

#### <a name="request"></a>요청

```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>응답
```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>3단계: 액세스 권한 부여

### <a name="test-the-connection-to-the-application"></a>애플리케이션에 대한 연결 테스트

타사 애플리케이션에 대한 연결을 테스트합니다. 다음 예제는 클라이언트 암호 및 비밀 토큰이 필요한 애플리케이션에 대한 것입니다. 각 애플리케이션에는 자체 요구 사항이 있습니다. 애플리케이션은 종종 클라이언트 암호 대신 기본 주소를 사용합니다. 앱에 필요한 자격 증명을 확인하려면 애플리케이션의 프로비저닝 구성 페이지로 이동하고 개발자 모드에서 **연결 테스트** 를 클릭합니다. 네트워크 트래픽은 자격 증명에 사용되는 매개 변수를 표시합니다. 자격 증명의 전체 목록은 [synchronizationJob: validateCredentials](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true)를 참조하세요. Azure Databricks와 같은 대부분의 애플리케이션은 BaseAddress 및 SecretToken을 사용합니다. BaseAddress를 Azure Portal에서는 테넌트 URL이라고 합니다. 

#### <a name="request"></a>요청
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials

{ 
    "credentials": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx" 
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```
#### <a name="response"></a>응답
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>자격 증명 저장

프로비저닝을 구성하려면 Azure AD와 애플리케이션 간에 트러스트를 설정해야 합니다. 타사 애플리케이션에 대한 액세스 권한을 부여합니다. 다음 예제는 클라이언트 암호 및 비밀 토큰이 필요한 애플리케이션에 대한 것입니다. 각 애플리케이션에는 자체 요구 사항이 있습니다. [API 설명서](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true)를 검토하여 사용 가능한 옵션을 확인합니다. 

#### <a name="request"></a>요청
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 

{ 
    "value": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx"
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```

#### <a name="response"></a>응답
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>4단계: 프로비저닝 작업 시작
이제 프로비저닝 작업이 구성되었으므로 다음 명령을 사용하여 [작업을 시작](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true)합니다. 


#### <a name="request"></a>요청

```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```


#### <a name="response"></a>응답
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>5단계: 프로비저닝 모니터링

### <a name="monitor-the-provisioning-job-status"></a>프로비저닝 작업 상태 모니터링

프로비저닝 작업이 실행되고 있으므로 다음 명령을 사용하여 대상 시스템에 생성된 사용자 및 그룹의 수와 같은 통계 뿐만 아니라 현재 프로비저닝 주기의 진행 상황을 추적할 수 있습니다. 

#### <a name="request"></a>요청
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>응답
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>프로비저닝 로그를 사용하여 프로비저닝 이벤트 모니터링
프로비저닝 작업의 상태를 모니터링하는 것 외에도 [프로비저닝 로그](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta&preserve-view=true)를 사용하여 발생하는 모든 이벤트를 쿼리할 수 있습니다. 예를 들어, 특정 사용자를 쿼리하고 성공적으로 프로비저닝되었는지 확인합니다.

#### <a name="request"></a>요청
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>응답
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "AWS Contoso",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "AWS Contoso"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            }
            ]
       }
    ]
}
```
## <a name="see-also"></a>참조

- [동기화 Microsoft Graph 설명서 검토](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
- [사용자 지정 SCIM 앱을 Azure AD와 통합](./use-scim-to-provision-users-and-groups.md)

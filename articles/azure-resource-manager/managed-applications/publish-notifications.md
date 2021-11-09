---
title: 알림을 사용하는 관리형 앱
description: 관리형 애플리케이션 인스턴스의 생성, 업데이트, 삭제 및 오류에 대한 알림을 수신하도록 웹후크 엔드포인트를 사용하여 관리형 애플리케이션을 구성합니다.
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: ef87fc014b2870602aa05b758afaffdde046f659
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060853"
---
# <a name="azure-managed-applications-with-notifications"></a>알림을 사용하는 Azure 관리형 애플리케이션

Azure 관리형 애플리케이션 알림을 통해 게시자는 관리형 애플리케이션 인스턴스의 수명 주기 이벤트에 따라 작업을 자동화할 수 있습니다. 게시자는 사용자 지정 알림 웹후크 엔드포인트를 지정하여 신규 및 기존 관리형 애플리케이션 인스턴스에 대한 이벤트 알림을 받을 수 있습니다. 게시자는 애플리케이션 프로비전, 업데이트 및 삭제 시 사용자 지정 워크플로를 설정할 수 있습니다.

## <a name="getting-started"></a>시작
관리형 애플리케이션으로부터 알림을 수신하기 시작하려면 공용 HTTPS 엔드포인트를 실행하고 서비스 카탈로그 애플리케이션 정의 또는 Azure Marketplace 제안을 게시할 때 해당 엔드포인트를 지정합니다.

빠른 시작을 위한 권장 단계는 다음과 같습니다.
1. 들어오는 POST 요청을 기록하고 `200 OK`를 반환하는 공용 HTTPS 엔드포인트를 실행합니다.
2. 이 문서의 뒷부분에 설명된 대로 서비스 카탈로그 애플리케이션 정의 또는 Azure Marketplace 제안에 엔드포인트를 추가합니다.
3. 애플리케이션 정의 또는 Azure Marketplace 제안을 참조하는 관리형 애플리케이션 인스턴스를 만듭니다.
4. 알림이 수신되는지 확인합니다.
5. 이 문서의 **엔드포인트 인증** 섹션에 설명된 대로 권한 부여를 사용하도록 설정합니다.
6. 이 문서의 **알림 스키마** 섹션에 설명된 지침에 따라 알림 요청을 구문 분석하고 알림을 바탕으로 비즈니스 논리를 구현합니다.

## <a name="add-service-catalog-application-definition-notifications"></a>서비스 카탈로그 애플리케이션 정의 알림 추가
#### <a name="azure-portal"></a>Azure portal
시작하려면 [Azure Portal을 통해 서비스 카탈로그 애플리케이션 게시](./publish-portal.md)를 참조하세요.

![Azure Portal의 서비스 카탈로그 애플리케이션 정의 알림](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 현재, 애플리케이션 정의 속성의 `notificationEndpoints`에서 엔드포인트를 하나만 제공할 수 있습니다.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure Marketplace 관리형 애플리케이션 알림 추가
자세한 내용은 [Azure 애플리케이션 제안 만들기](../../marketplace/azure-app-offer-setup.md)를 참조하세요.

![Azure Portal의 Azure Marketplace 관리형 애플리케이션 알림](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>이벤트 트리거
다음 표에서는 EventType 및 ProvisioningState의 가능한 모든 조합과 해당 트리거에 대해 설명합니다.

EventType | ProvisioningState | 알림 트리거
---|---|---
PUT | 수락됨 | 애플리케이션 PUT 후 (관리되는 리소스 그룹 내부 배포를 시작하기 전에) 관리되는 리소스 그룹을 성공적으로 만들고 프로젝션했습니다.
PUT | 성공 | PUT 후에 관리형 애플리케이션의 전체 프로비전이 성공했습니다.
PUT | 실패 | 어떤 지점에서든 애플리케이션 인스턴스 프로비전 PUT이 실패했습니다.
패치 | 성공 | 관리형 애플리케이션 인스턴스에서 성공적으로 패치한 후 태그, JIT 액세스 정책 또는 관리 ID를 업데이트합니다.
Delete | 삭제 중 | 사용자가 관리형 앱 인스턴스의 삭제를 시작하는 즉시.
Delete | 삭제됨 | 관리형 애플리케이션 전체를 성공적으로 삭제한 후.
Delete | 실패 | 프로비전 해제 프로세스 중에 삭제를 차단하는 오류가 발생한 후.
## <a name="notification-schema"></a>알림 스키마
알림을 처리하기 위해 웹후크 엔드포인트를 실행하는 경우 페이로드를 구문 분석하여 중요한 속성을 가져온 다음 알림에 대한 동작을 수행해야 합니다. 서비스 카탈로그 및 Azure Marketplace 관리형 애플리케이션 알림은 동일한 속성을 많이 제공합니다. 샘플 다음에 나오는 표에는 두 가지 작은 차이점이 설명되어 있습니다.

#### <a name="service-catalog-application-notification-schema"></a>서비스 카탈로그 애플리케이션 알림 스키마
관리형 애플리케이션 인스턴스를 성공적으로 프로비전한 후의 서비스 카탈로그 알림의 예는 다음과 같습니다.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

프로비전이 실패하면 오류 세부 정보가 포함된 알림이 지정된 엔드포인트로 전송됩니다.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace 애플리케이션 알림 스키마

관리형 애플리케이션 인스턴스를 성공적으로 프로비전한 후의 서비스 카탈로그 알림의 예는 다음과 같습니다.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

프로비전이 실패하면 오류 세부 정보가 포함된 알림이 지정된 엔드포인트로 전송됩니다.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

매개 변수 | 설명
---|---
eventType | 알림을 트리거한 이벤트의 유형입니다. (예: PUT, PATCH, DELETE)
applicationId | 알림이 트리거된 관리형 애플리케이션의 정규화된 리소스 식별자입니다.
eventTime | 알림을 트리거한 이벤트의 타임스탬프입니다. (UTC ISO 8601 형식의 날짜 및 시간)
provisioningState | 관리형 애플리케이션 인스턴스의 프로비전 상태입니다. (예: 성공, 실패, 삭제 중, 삭제됨)
error | provisioningState가 실패인 경우에만 지정됩니다. 오류 코드, 메시지 및 오류의 원인이 되는 문제에 대한 세부 정보를 포함합니다.
applicationDefinitionId | 서비스 카탈로그 관리형 애플리케이션에만 지정됩니다. 관리형 애플리케이션 인스턴스가 프로비전된 애플리케이션 정의의 정규화된 리소스 식별자를 나타냅니다.
계획 | Azure Marketplace 관리형 애플리케이션에만 지정됩니다. 관리형 애플리케이션 인스턴스의 게시자, 제안, SKU 및 버전을 나타냅니다.
billingDetails | Azure Marketplace 관리형 애플리케이션에만 지정됩니다. 관리형 애플리케이션 인스턴스의 청구 정보입니다. Azure Marketplace에서 사용량 세부 정보를 쿼리하는 데 사용할 수 있는 resourceUsageId를 포함합니다.

## <a name="endpoint-authentication"></a>엔드포인트 인증
웹후크 엔드포인트 보안을 유지하고 알림의 신뢰성을 보장하려면 다음을 수행합니다.
1. 웹후크 URI 위에 다음과 같은 쿼리 매개 변수를 제공합니다. https\://your-endpoint.com?sig=Guid. 각 알림에서 쿼리 매개 변수 `sig`가 필요한 값 `Guid`를 포함하는지 확인합니다.
2. applicationId를 사용하여 관리형 애플리케이션 인스턴스에서 GET을 실행합니다. 일관성을 유지하기 위해 provisioningState가 알림의 provisioningState와 일치하는지 확인합니다.

## <a name="notification-retries"></a>알림 다시 시도

관리형 애플리케이션 알림 서비스는 웹후크 엔드포인트에서 알림으로의 `200 OK` 응답을 예상합니다. 알림 서비스는 웹후크 엔드포인트가 500 이상의 HTTP 오류 코드를 반환하거나 429 오류 코드를 반환하는 경우 또는 엔드포인트에 일시적으로 연결할 수 없는 경우 다시 시도합니다. 웹후크 엔드포인트를 10시간 이내에 사용할 수 없게 되면 알림 메시지가 삭제되고 다시 시도가 중지됩니다.
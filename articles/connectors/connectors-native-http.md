---
title: HTTP 또는 HTTPS를 사용하여 서비스 엔드포인트 호출
description: 아웃바운드 HTTP 또는 HTTPS 요청을 Azure Logic Apps의 서비스 엔드포인트로 보내기
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.date: 09/13/2021
tags: connectors
ms.openlocfilehash: c1352fe61b8a663371719100aa86806da0791f20
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359355"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Azure Logic Apps에서 HTTP 또는 HTTPS를 통해 서비스 엔드포인트 호출

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공되는 HTTP 트리거 또는 작업을 사용하여 HTTP 또는 HTTPS를 통해 아웃바운드 요청을 다른 서비스 또는 시스템에 있는 엔드포인트로 보낼 수 있는 자동화된 작업 및 워크플로를 만들 수 있습니다. 대신 인바운드 HTTPS 호출을 수신하고 응답하려면 기본 제공 [요청 트리거 및 응답 작업](../connectors/connectors-native-reqres.md)을 사용합니다.

예를 들어 특정 일정에 따라 엔드포인트를 검사하여 웹 사이트의 서비스 엔드포인트를 모니터할 수 있습니다. 웹 사이트 작동 중지와 같이 지정된 이벤트가 해당 엔드포인트에서 발생하면 이벤트가 논리 앱의 워크플로를 트리거하고 해당 워크플로로 작업을 실행합니다.

* 반복되는 일정으로 엔드포인트를 검사하거나 *폴링* 하려면 워크플로에서 첫 번째 단계로 [HTTP 트리거를 추가](#http-trigger)합니다. 트리거가 엔드포인트를 검사할 때마다 트리거는 엔드포인트에 대해 *요청* 을 호출하거나 보냅니다. 엔드포인트의 응답은 논리 앱의 워크플로가 실행될지 여부를 결정합니다. 트리거는 엔드포인트의 응답에서 논리 앱의 작업으로 모든 콘텐츠를 전달합니다.

* 워크플로의 다른 위치에서 엔드포인트를 호출하려면 [HTTP 작업을 추가](#http-action)합니다. 엔드포인트의 응답은 워크플로의 나머지 작업을 실행하는 방법을 결정합니다.

이 문서에서는 논리 앱이 다른 서비스 및 시스템에 아웃바운드 호출을 전송할 수 있도록 HTTP 트리거 및 HTTP 작업을 사용하는 방법을 보여줍니다.

이전에 SSL(Transport Layer Security)로 알려진 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security), 자체 서명된 인증서 또는 [Azure AD OAuth(Azure Active Directory Open Authentication)](../active-directory/develop/index.yml)와 같이 논리 앱에서의 아웃바운드 호출에 대한 암호화, 보안 및 권한 부여에 대한 자세한 내용은 [보안 액세스 및 데이터 - 다른 서비스 및 시스템에 대한 아웃바운드 호출 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 호출하려는 대상 엔드포인트의 URL

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토하세요.

* 대상 엔드포인트를 호출할 논리 앱입니다. HTTP 트리거로 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP 작업을 사용하려면 원하는 트리거를 사용하여 논리 앱을 시작합니다. 이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용합니다.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP 트리거를 추가합니다.

이 기본 제공 트리거는 엔드포인트에 대해 지정된 URL로 HTTP 호출을 수행하고 응답을 반환합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에서 **기본 제공** 을 선택합니다. 검색 상자에서 필터로 `http`을 입력합니다. **트리거** 목록에서 **HTTP** 트리거를 선택합니다.

   ![HTTP 트리거 선택](./media/connectors-native-http/select-http-trigger.png)

   이 예제에서는 트리거 이름을 "HTTP trigger"로 변경하여 단계 이름에 설명이 포함되도록 만듭니다. 또한 이 예제에서는 나중에 HTTP 작업을 추가하며, 두 이름 모두 고유해야 합니다.

1. 대상 엔드포인트에 대한 호출에 포함하려는 [HTTP 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)에 대해 값을 제공합니다. 트리거로 대상 엔드포인트를 검사하려는 빈도에 대해 되풀이를 설정합니다.

   ![HTTP 트리거 매개 변수 입력](./media/connectors-native-http/http-trigger-parameters.png)

   **없음** 이외의 인증 유형을 선택한 경우 인증 설정은 선택에 따라 달라집니다. HTTP에 사용할 수 있는 인증 유형에 대한 자세한 내용은 다음 토픽을 참조하세요.

   * [아웃바운드 호출에 대한 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [관리 ID를 사용하여 리소스에 대한 액세스 인증](../logic-apps/create-managed-service-identity.md)

1. 다른 사용 가능한 매개 변수를 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택합니다.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 완료되었으면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP 작업 추가

이 기본 제공 작업은 엔드포인트에 대해 지정된 URL로 HTTP 호출을 수행하고 응답을 반환합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용합니다.

1. HTTP 작업을 추가하려는 단계에서 **새 단계** 를 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. **작업 선택** 아래에서 **기본 제공** 을 선택합니다. 검색 상자에서 필터로 `http`을 입력합니다. **작업** 목록에서 **HTTP** 작업을 선택합니다.

   ![HTTP 작업 선택](./media/connectors-native-http/select-http-action.png)

   이 예제에서는 작업 이름을 "HTTP action"으로 변경하여 단계 이름에 설명이 포함되도록 만듭니다.

1. 대상 엔드포인트에 대한 호출에 포함하려는 [HTTP 작업 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)에 대해 값을 제공합니다.

   ![HTTP 작업 매개 변수 입력](./media/connectors-native-http/http-action-parameters.png)

   **없음** 이외의 인증 유형을 선택한 경우 인증 설정은 선택에 따라 달라집니다. HTTP에 사용할 수 있는 인증 유형에 대한 자세한 내용은 다음 토픽을 참조하세요.

   * [아웃바운드 호출에 대한 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [관리 ID를 사용하여 리소스에 대한 액세스 인증](../logic-apps/create-managed-service-identity.md)

1. 다른 사용 가능한 매개 변수를 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택합니다.

1. 완료되었으면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

## <a name="trigger-and-action-outputs"></a>트리거 및 작업 출력

HTTP 트리거 또는 작업의 출력에 대한 자세한 내용은 다음과 같습니다.

| 속성 | 유형 | 설명 |
|----------|------|-------------|
| `headers` | JSON 개체 | 요청의 헤더 |
| `body` | JSON 개체 | 요청의 본문 콘텐츠가 포함된 개체 |
| `status code` | 정수 | 요청의 상태 코드 |
|||

| 상태 코드 | Description |
|-------------|-------------|
| 200 | 확인 |
| 202 | 수락됨 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

<a name="single-tenant-authentication"></a>

## <a name="authentication-for-single-tenant-environment"></a>단일 테넌트 환경에 대한 인증

단일 테넌트 Azure Logic Apps에 **Logic App(표준)** 리소스가 있고 다음 인증 유형과 함께 HTTP 작업을 사용하려는 경우 해당 인증 유형에 대한 추가 설정 단계를 완료해야 합니다. 그러지 않으면 호출이 실패합니다.

* [Tls/ssl 인증서](#tls-ssl-certificate-authentication): 앱 설정인 `WEBSITE_LOAD_ROOT_CERTIFICATES` 를 추가 하 고 TLS/ssl 인증서에 대 한 지문 지문을 제공 합니다.

* ["인증서" 자격 증명 유형이 있는 클라이언트 인증서 또는 Azure AD OAuth(Azure Active Directory Open Authentication)](#client-certificate-authentication): 앱 설정(`WEBSITE_LOAD_USER_PROFILE`)을 추가하고 값을 `1`로 설정합니다.

<a name="tls-ssl-certificate-authentication"></a>

### <a name="tlsssl-certificate-authentication"></a>TLS/SSL 인증서 인증

1. 논리 앱 리소스의 앱 설정에서 [앱 설정을 추가 또는 업데이트](../logic-apps/edit-app-settings-host-settings.md#manage-app-settings)(`WEBSITE_LOAD_ROOT_CERTIFICATES`)합니다.

1. 설정 값의 경우 TLS/SSL 인증서의 지문을 신뢰할 수 있는 루트 인증서로 제공합니다.

   `"WEBSITE_LOAD_ROOT_CERTIFICATES": "<thumbprint-for-TLS/SSL-certificate>"`

예를 들어 Visual Studio Code 작업하는 경우 다음 단계를 수행합니다.

1. 논리 앱 프로젝트의 **local.settings.json** 파일을 엽니다.

1. `Values`JSON 개체에서 설정을 추가하거나 업데이트`WEBSITE_LOAD_ROOT_CERTIFICATES`합니다.

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "WEBSITE_LOAD_ROOT_CERTIFICATES": "<thumbprint-for-TLS/SSL-certificate>",
         <...>
      }
   }
   ```

자세한 내용은 다음 설명서를 검토하세요.

* [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 호스트 및 앱 설정 편집](../logic-apps/edit-app-settings-host-settings.md#manage-app-settings)
* [비공개 클라이언트 인증서 - Azure App Service](../app-service/environment/certificates.md#private-client-certificate)

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-or-azure-ad-oauth-with-certificate-credential-type-authentication"></a>클라이언트 인증서 또는 "인증서" 자격 증명 유형 인증을 사용하는 Azure AD OAuth

1. 논리 앱 리소스의 앱 설정에서 [앱 설정을 추가 또는 업데이트](../logic-apps/edit-app-settings-host-settings.md#manage-app-settings)(`WEBSITE_LOAD_USER_PROFILE`)합니다.

1. 설정 값에 대해 `1`을 지정합니다.

   `"WEBSITE_LOAD_USER_PROFILE": "1"`

예를 들어 Visual Studio Code 작업하는 경우 다음 단계를 수행합니다.

1. 논리 앱 프로젝트의 **local.settings.json** 파일을 엽니다.

1. `Values`JSON 개체에서 설정을 추가하거나 업데이트`WEBSITE_LOAD_USER_PROFILE`합니다.

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "WEBSITE_LOAD_USER_PROFILE": "1",
         <...>
      }
   }
   ```

자세한 내용은 다음 설명서를 검토하세요.

* [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 호스트 및 앱 설정 편집](../logic-apps/edit-app-settings-host-settings.md#manage-app-settings)
* [비공개 클라이언트 인증서 - Azure App Service](../app-service/environment/certificates.md#private-client-certificate)

## <a name="content-with-multipartform-data-type"></a>multipart/form-data 형식의 콘텐츠

HTTP 요청에 `multipart/form-data` 유형이 포함된 콘텐츠를 처리하려면 다음 형식을 사용하여 `$content-type` 및 `$multipart` 특성이 포함된 JSON 객체를 HTTP 요청의 본문에 추가할 수 있습니다.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

예를 들어 `multipart/form-data` 유형을 지원하는 해당 사이트의 API를 사용하여 Excel 파일에 대한 HTTP POST 요청을 웹 사이트로 전송하는 논리 앱이 있다고 가정해보세요. 이 작업의 모양은 다음과 같습니다.

![다중 파트 폼 데이터](./media/connectors-native-http/http-action-multipart.png)

다음은 기본 워크플로 정의에서 HTTP 작업의 JSON 정의를 보여주는 동일한 예제입니다.

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>application/x-www-form-urlencoded 유형의 콘텐츠

HTTP 요청에 대해 form-urlencoded 데이터를 본문에 제공하려면 `application/x-www-form-urlencoded` 콘텐츠 형식을 포함하는 데이터를 지정해야 합니다. HTTP 트리거 또는 작업에서 `content-type` 헤더를 추가합니다. 헤더 값을 `application/x-www-form-urlencoded`로 설정합니다.

예를 들어 HTTP POST 요청을 웹 사이트로 전송하고 `application/x-www-form-urlencoded` 유형을 지원하는 논리 앱이 있다고 가정해보세요. 이 작업의 모양은 다음과 같습니다.

!['content-type' 헤더가 'application/x-www-form-urlencoded'로 설정된 HTTP 요청을 보여 주는 스크린샷](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>비동기 요청-응답 동작

다중 테넌트 및 단일 테넌트 Azure Logic Apps *상태 비저장* 워크플로의 경우 모든 HTTP 기반 작업은 표준 [비동기 작업 패턴을](/azure/architecture/patterns/async-request-reply) 기본 동작으로 따릅니다. 이 패턴은 HTTP 작업이 요청을 호출하거나 엔드포인트, 서비스, 시스템 또는 API로 요청을 전송한 후 수신기가 즉시 ["202 ACCEPTED"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) 응답을 반환하도록 지정합니다. 이 코드는 수신기에서 요청을 수락했지만 처리가 완료되지 않았음을 확인합니다. 응답에는 `location` 수신자가 처리를 중지하고 ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) 성공 응답 또는 기타 비 202 응답을 반환할 때까지 호출자가 비동기 요청의 상태를 폴링하거나 확인하는 데 사용할 수 있는 URI 및 새로 고침 ID를 지정하는 헤더가 포함될 수 있습니다. 하지만 호출자가 요청 처리가 완료될 때까지 기다릴 필요가 없고 계속 다음 작업을 실행할 수 있습니다. 자세한 내용은 [비동기 마이크로서비스 통합에 마이크로서비스 자율성 적용](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)을 참조하세요.

단일 테넌트 Azure Logic Apps *상태 비동기* 워크플로의 경우 HTTP 기반 작업은 비동기 작업 패턴을 사용하지 않습니다. 대신 동기적으로만 [실행되고,"202 수락됨"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) 응답을 있는 그대로 반환하고 워크플로 실행의 다음 단계를 진행합니다. 응답에 헤더가 포함된 경우 `location` 상태 비지정 워크플로는 상태를 확인하기 위해 지정된 URI를 폴링하지 않습니다. 표준 [비동기 작업 패턴을](/azure/architecture/patterns/async-request-reply)따르려면 상태 비동기 워크플로를 대신 사용합니다.

* Logic App 디자이너에서 트리거가 아닌 HTTP 작업에는 기본적으로 사용하도록 설정된 **비동기 패턴** 설정이 포함되어 있습니다. 이 설정은 호출자에서 처리가 완료될 때까지 기다리지 않고 다음 작업으로 이동할 수 있지만 처리가 중지될 때까지 상태를 계속 확인하도록 지정합니다. 이 설정이 사용하지 않도록 설정되면 호출자에서 처리가 완료될 때까지 기다린 후에 다음 작업으로 이동하도록 지정합니다.

  이 설정을 확인하려면 다음 단계를 수행합니다.

  1. HTTP 작업의 제목 표시줄에서 줄임표( **...** ) 단추를 선택하여 작업의 설정을 엽니다.

  1. **비동기 패턴** 설정을 찾습니다.

     !["비동기 패턴" 설정](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP 작업의 기본 JSON(JavaScript Object Notation) 정의는 암시적으로 비동기 작업 패턴을 따릅니다.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>비동기 작업을 사용하지 않도록 설정

일부 경우에는 다음과 같은 특정 시나리오에서 HTTP 작업의 비동기 동작이 필요할 수 있습니다.

* [장기 실행 작업에 대한 HTTP 시간 제한 방지](#avoid-http-timeouts)
* [위치 헤더 확인을 사용하지 않도록 설정](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>**비동기 패턴** 설정 해제

1. Logic Apps 디자이너의 HTTP 작업의 제목 표시줄에서 줄임표( **...** ) 단추를 선택하여 작업의 설정을 엽니다.

1. **비동기 패턴** 설정을 찾고, 사용하도록 설정된 경우 설정을 **끄기** 로 설정하고, **완료** 를 선택합니다.

   ![“비동기 패턴” 설정을 사용하지 않도록 설정](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>작업의 JSON 정의에서 비동기 패턴을 사용하지 않도록 설정

HTTP 작업의 기본 JSON 정의에서 작업이 대신 동기 작업 패턴을 따르도록 작업 정의에 [`"DisableAsyncPattern"` 작업 옵션을 추가](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)합니다. 또한 자세한 내용은 [동기 작업 패턴으로 작업 실행](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)을 참조하세요.

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>장기 실행 작업에 대한 HTTP 시간 제한 방지

HTTP 요청에는 [제한 시간 제한](../logic-apps/logic-apps-limits-and-config.md#http-limits)이 있습니다. 이 제한으로 인해 제한 시간에 걸리는 장기 실행 HTTP 작업이 있으면 다음 옵션을 사용할 수 있습니다.

* 작업이 지속적으로 요청 상태를 폴링하거나 확인하지 않도록 [HTTP 작업의 비동기 작업 패턴을 사용하지 않도록 설정합니다](#disable-asynchronous-operations). 대신 요청 처리가 완료된 후 수신기가 상태 및 결과로 응답할 때까지 기다립니다.

* HTTP 작업을 [HTTP 웹후크 작업](../connectors/connectors-native-webhook.md)으로 바꿉니다. 이렇게 하면 요청 처리가 완료된 후 수신기가 상태 및 결과로 응답할 때까지 기다립니다.

<a name="disable-location-header-check"></a>

### <a name="set-up-interval-between-retry-attempts-with-the-retry-after-header"></a>Retry-After 헤더를 사용하여 재시도 간격 설정

재시도 사이의 시간(초)을 지정하려면 HTTP 작업 응답에 헤더를 추가할 수 `Retry-After` 있습니다. 예를 들어 대상 엔드포인트가 상태 코드를 반환하는 경우 `429 - Too many requests` 재시도 사이에 더 긴 간격을 지정할 수 있습니다. `Retry-After`헤더는 `202 - Accepted` 상태 코드에서도 작동합니다.

다음은 를 포함하는 HTTP 작업 응답을 보여 주는 동일한 `Retry-After` 예제입니다.

```json
{
    "statusCode": 429,
    "headers": {
        "Retry-After": "300"
    }
}
```


## <a name="disable-checking-location-headers"></a>위치 헤더 확인을 사용하지 않도록 설정

일부 엔드포인트, 서비스, 시스템 또는 API는 `location` 헤더가 없는 `202 ACCEPTED` 응답을 반환합니다. `location` 헤더가 존재하지 않을 때 HTTP 작업이 지속적으로 요청 상태를 확인하도록 하지 않으려면 다음 옵션을 사용할 수 있습니다.

* 작업이 지속적으로 요청 상태를 폴링하거나 확인하지 않도록 [HTTP 작업의 비동기 작업 패턴을 사용하지 않도록 설정합니다](#disable-asynchronous-operations). 대신 요청 처리가 완료된 후 수신기가 상태 및 결과로 응답할 때까지 기다립니다.

* HTTP 작업을 [HTTP 웹후크 작업](../connectors/connectors-native-webhook.md)으로 바꿉니다. 이렇게 하면 요청 처리가 완료된 후 수신기가 상태 및 결과로 응답할 때까지 기다립니다.

## <a name="known-issues"></a>알려진 문제

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>생략된 HTTP 헤더

HTTP 트리거 또는 작업에 이러한 헤더가 포함된 경우 Logic Apps는 경고 또는 오류를 표시하지 않고 생성된 요청 메시지에서 이러한 헤더를 제거합니다.

* `Accept-version`을 제외한 `Accept-*` 헤더
* `Allow`
* `Content-Disposition`, `Content-Encoding` 및 `Content-Type`을 제외한 `Content-*` 헤더이며, POST 및 PUT 작업을 사용할 때 적용됩니다. 하지만 Logic Apps는 GET 작업을 사용할 때 이러한 헤더를 삭제합니다.
* `Cookie` 헤더, 하지만 Logic Apps는 **쿠키** 속성을 사용하여 지정하는 모든 값을 적용합니다.
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Logic Apps로 인해 이러한 헤더와 함께 HTTP 트리거 또는 작업을 사용하는 논리 앱을 저장하는 것이 방해되지는 않지만, Logic Apps에서 이러한 헤더가 무시됩니다.

## <a name="connector-reference"></a>커넥터 참조

트리거 및 작업 매개 변수에 대한 기술 정보는 다음 섹션을 참조하세요.

* [HTTP 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 작업 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>다음 단계

* [보안 액세스 및 데이터 - 다른 서비스 및 시스템에 대한 아웃바운드 호출 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Logic Apps용 커넥터](../connectors/apis-list.md)

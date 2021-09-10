---
title: Azure Functions의 HTTP 엔드포인트 사용자 지정
description: Azure Functions의 HTTP 엔드포인트의 사용자 지정 방법을 알아보세요.
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: b6e78329c4f45baffd779202ebfa37da3c6cf7c9
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105438"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Azure Functions의 HTTP 엔드포인트 사용자 지정

이 문서에서 Azure Functions를 통해 확장성이 뛰어난 API를 빌드하는 방법을 알아볼 수 있습니다. Azure Functions은 Node.JS, C# 등을 비롯한 다양한 언어로 엔드포인트를 쉽게 작성할 수 있도록 지원하는 HTTP 트리거 및 바인딩 컬렉션이 기본으로 제공됩니다. 이 문서에서는 HTTP 트리거를 사용자 지정하여 API 설계의 특정 작업을 처리합니다. 또한 API를 Azure Functions 프록시와 통합하고 모의 API를 설정하여 API 확장을 준비합니다. 이러한 작업은 Functions의 서버리스 컴퓨팅 환경에서 수행되므로 리소스 확장 문제에 대해 걱정할 필요 없이 API 논리에만 집중할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

결과 함수는 이 문서의 나머지 부분에 사용됩니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="customize-your-http-function"></a>HTTP 함수 사용자 지정

기본적으로 HTTP 트리거 함수는 모든 HTTP 메서드를 허용하도록 구성됩니다. 기본 URL `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`을 사용할 수도 있습니다. 이 섹션에서는 `/api/hello`을 사용하는 GET 요청에만 응답하도록 함수를 수정합니다. 

1. Azure Portal에서 해당 함수로 이동합니다. 왼쪽 메뉴에서 **통합** 을 선택한 다음 **트리거** 의 **HTTP (req)** 을 선택합니다.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="HTTP 함수 사용자 지정":::

1. 다음 테이블에 지정된 HTTP 트리거 설정을 사용하세요.

    | 필드 | 샘플 값 | Description |
    |---|---|---|
    | 경로 템플릿 | /hello | 이 함수를 호출하는 데 사용할 경로 결정 |
    | 권한 부여 수준 | 익명 | 선택 사항: 함수가 API 키 없이 액세스할 수 있게 됩니다 |
    | 선택한 HTTP 메서드 | GET | 선택한 HTTP 메서드만 이 함수를 호출하는 데 사용할 수 있도록 허용 |

    `/api` 기본 경로 접두사는 글로벌 설정에서 처리되므로 경로 템플릿에 포함하지 않았습니다.

1. **저장** 을 선택합니다.

HTTP 함수 사용자 지정에 대한 자세한 내용은 [Azure Functions HTTP 바인딩](./functions-bindings-http-webhook.md)를 참조하세요.

### <a name="test-your-api"></a>API 테스트

그런 다음 함수를 테스트하여 새 API 화면에서 어떻게 작동하는지 확인합니다.
1. 함수 페이지의 왼쪽 메뉴에서 **코드 + 테스트** 를 선택합니다.

1. 상단 메뉴에서 **함수 URL 가져오기** 를 선택하고 URL을 복사하세요. 이제 `/api/hello` 경로를 사용하는지 확인하세요.
 
1. 새 브라우저 탭 또는 기본 REST 클라이언트에 URL을 복사합니다. 

   브라우저는 기본적으로 GET을 사용합니다.
 
1. URL의 쿼리 문자열에 매개 변수를 추가합니다. 

   예들 들어 `/api/hello/?name=John`입니다.
 
1. 'Enter' 키를 눌러 작동하는지 확인하세요. "*Hello John*"이라는 응답이 표시됩니다.

1. 또한 다른 HTTP 메서드로 엔드포인트를 호출하여 함수가 실행되지 않는지 확인할 수 있습니다. 이 경우, cURL, Postman 또는 Fiddler와 같은 REST 클라이언트를 사용하세요.

## <a name="proxies-overview"></a>프록시 개요

다음 섹션에서는 프록시를 통해 API를 표시합니다. Azure Functions 프록시를 사용하면 요청을 다른 리소스로 전달할 수 있습니다. HTTP 트리거와 같은 방식으로 HTTP 엔드포인트를 정의합니다. 그러나 엔드포인트를 호출할 때 실행할 코드를 작정하는 대신, 원격 구현에 대한 URL을 제공합니다. 이렇게 하면 여러 API 소스를 단일 API 화면으로 구성할 수 있으며 클라이언트에서 사용하기 쉽고 마이크로서비스로 API를 빌드하려는 경우에 유용합니다.

프록시는 다음과 같은 HTTP 리소스를 가리킬 수 있습니다.
- Azure Functions 
- [Azure App Service](../app-service/overview.md)의 API 앱
- [Linux의 App Service](../app-service/overview.md#app-service-on-linux)에 있는 Docker 컨테이너
- 기타 호스트된 API

프록시에 대한 자세한 내용은 [Azure Functions 프록시 사용]을 참조하세요.

## <a name="create-your-first-proxy"></a>첫 번째 프록시 만들기

이 섹션에서는 전체 API의 프런트 엔드 역할을 하는 새 프록시를 생성합니다. 

### <a name="setting-up-the-frontend-environment"></a>프런트 엔드 환경 설정

[함수 앱 만들기](./functions-get-started.md) 단계를 반복하여 프록시를 만들 새 함수 앱을 만듭니다. 이 새로운 앱의 URL은 API에 대한 프런트 엔드로 사용되고 이전에 편집하던 함수 앱은 백 엔드로 사용됩니다.

1. 포털의 새 프런트 엔드 함수 앱으로 이동합니다.
1. **구성** 을 선택하고 **애플리케이션 설정** 을 선택합니다.
1. 키/값 쌍이 저장된 **애플리케이션 설정** 으로 스크롤을 내리고 키`HELLO_HOST`를 사용하여 새로운 설정을 생성하세요. 해당 값을 백 엔드 함수 앱의 호스트(예: `<YourBackendApp>.azurewebsites.net`)로 설정합니다. 이 값은 이전에 HTTP 함수를 테스트할 때 복사한 URL의 일부입니다. 나중에 구성에서 이 설정을 참조할 수 있습니다.

    > [!NOTE] 
    > 프록시에 대한 하드 코드된 환경 종속성을 방지하려면 호스트 구성에 대해 앱 설정이 권장됩니다. 앱 설정을 사용할 경우 환경 간에 프록시 구성을 이동할 수 있고 환경 관련 앱 설정이 적용됩니다.

1. **저장** 을 선택합니다.

### <a name="creating-a-proxy-on-the-frontend"></a>프런트 엔드에 프록시 만들기

1. 포털의 프런트 엔드 함수 앱으로 다시 이동합니다.

1. 왼쪽 메뉴에서 **프록시** 를 선택한 다음 **추가** 를 선택하세요. 

1. **새 프록시** 페이지에서 다음 테이블의 설정을 사용한 다음 **생성** 을 선택하세요.

    | 필드 | 샘플 값 | Description |
    |---|---|---|
    | 이름 | HelloProxy | 관리에 대해서만 사용되는 이름 |
    | 경로 템플릿 | /api/remotehello | 이 프록시를 호출하는 데 사용할 경로 결정 |
    | 백 엔드 URL | https://%HELLO_HOST%/api/hello | 요청을 프록시 처리할 엔드포인트를 지정합니다. |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="프록시 만들기":::

    Azure Functions프록시는 경로 템플릿에 포함되어야 하는 `/api` 기본 경로 접두사를 제공하지 않습니다. `%HELLO_HOST%` 구문은 이전에 생성한 앱 설정을 참조합니다. 확인된 URL은 원래 함수를 가리킵니다.

1. 프록시 URL을 복사하고 브라우저 또는 자주 사용하는 HTTP 클라이언트에서 테스트하여 새 프록시를 시험해보세요.
    - 익명 함수를 사용하는 경우:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    - 권한 부여 함수를 사용하는 경우:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>모의 API 만들기

다음으로 프록시를 사용하여 솔루션에 대한 모의 API 생성합니다. 이 프록시를 통해 백 엔드를 완전히 구현하지 않고도 클라이언트 개발을 진행할 수 있습니다. 이후 개발 과정에서 이 논리를 지원하는 새 함수 앱을 만들고 프록시를 해당 앱으로 리디렉션할 수 있습니다.

이 모의 API를 만들기 위해 이번에는 [App Service 편집기](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)를 사용하여 새 프록시를 생성합니다. 시작하려면 포털의 함수 앱으로 이동합니다. **플랫폼 기능** 을 선택하고 **개발 도구** 에서 **App Service 편집기** 를 찾습니다. App Service 편집기는 새 탭에서 열립니다.

왼쪽 탐색 영역에서 `proxies.json`을 선택합니다. 이 파일은 모든 프록시에 대한 구성을 저장합니다. [함수 배포 방법](./functions-continuous-deployment.md) 중 하나를 사용하는 경우 이 파일이 소스 제어에 유지됩니다. 이 파일에 대한 자세한 내용은 [프록시 고급 구성](./functions-proxies.md#advanced-configuration)을 참조하세요.

지금까지 작업을 진행했으면 proxies.json이 다음과 같이 표시됩니다.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

다음으로 모의 API를 추가합니다. proxies.json 파일을 다음 코드로 변경하세요.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

이 코드는 `backendUri` 속성이 없는 새 프록시 `GetUserByName`을 추가합니다. 다른 리소스를 호출하지 않고 응답 재정의를 사용하여 프록시의 기본 응답을 수정합니다. 요청 및 응답 재정의를 백 엔드 URL과 함께 사용할 수도 있습니다. 이러한 기법은 헤더, 쿼리, 매개 변수 등을 수정해야 하는 레거시 시스템으로 프록시를 사용할 때 특히 유용합니다. 요청 및 응답 재정의에 대한 자세한 내용은 [프록시에서 요청 및 응답 수정](./functions-proxies.md)을 참조하세요.

브라우저 또는 자주 사용하는 REST 클라이언트를 통해 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 엔드포인트를 호출하여 모의 API를 테스트합니다. _{username}_ 을 사용자 이름을 나타내는 문자열 값으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 Azure Functions에서 API를 빌드하고 사용자 지정하는 방법을 알아보았습니다. 또한 모의 API를 비롯한 여러 API를 하나의 통합 API 화면에 표시하는 방법도 알아보았습니다. 이러한 기법을 사용하여 복잡성에 관계없이 Azure Functions에서 제공하는 서버리스 컴퓨팅 모델에서 실행되는 API를 빌드할 수 있습니다.

다음 참조는 API를 추가로 개발하는 경우 유용할 수 있습니다.

- [Azure Functions HTTP 바인딩](./functions-bindings-http-webhook.md)
- [Azure Functions 프록시 사용]
- [Azure Functions API 문서화(미리 보기)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Azure Functions 프록시 사용]: ./functions-proxies.md

---
title: Microsoft Azure Maps에서 비 대화형 로그인을 사용하여 단일 페이지 웹 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: 비대화형 Azure RBAC(Azure 역할 기반 액세스 제어) 및 Azure Maps Web SDK를 사용하여 단일 페이지 웹 애플리케이션을 구성하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js, subject-rbac-steps
ms.openlocfilehash: 9bf18a9122bbe8406b76cfd822cc2a5a86339a52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642248"
---
# <a name="how-to-secure-a-single-page-web-application-with-non-interactive-sign-in"></a>비대화형 로그인을 사용하여 단일 페이지 웹 애플리케이션을 보호하는 방법

이 문서에서는 사용자가 Azure AD에 로그인 할 수 없는 경우 Azure AD(Azure Active Directory)를 사용하여 단일 페이지 웹 애플리케이션을 보호하는 방법을 설명합니다.

이 비 대화형 인증 흐름을 만들기 위해 Azure AD에서 액세스 토큰을 획득하는 것을 담당하는 Azure Function 보안 웹 서비스를 생성합니다. 이 웹 서비스는 단일 페이지 웹 애플리케이션에서만 단독으로 사용할 수 있습니다.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps는 사용자 로그온/대화형 흐름에서 액세스 토큰을 지원할 수 있습니다. 대화형 흐름을 사용하면 액세스 해지 및 비밀 관리를 보다 제한된 범위로 사용할 수 있습니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

Azure AD에 대한 인증을 담당하는 보안 웹 서비스 애플리케이션을 생성합니다.

1. Azure Portal에서 함수 만들기 자세한 내용은 [Azure Functions 시작](../azure-functions/functions-get-started.md)을 참조하세요.

2. 단일 페이지 웹 애플리케이션에서 액세스할 수 있도록 Azure 함수에서 CORS 정책을 구성합니다. CORS 정책은 브라우저 클라이언트를 허용된 웹 애플리케이션 원본으로 보호합니다. 자세한 내용은 [CORS 기능 추가](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)를 참조하세요.

3. Azure 함수에 [시스템 할당 ID를 추가](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)하여 Azure AD에 인증하는 서비스 사용자를 만들 수 있습니다.  

4. 시스템 할당 ID에 대한 역할 기반 액세스를 Azure Maps 계정에 부여합니다. 세부 정보는 [역할 기반 액세스 권한 부여](#grant-role-based-access-for-users-to-azure-maps)를 참조하세요.

5. 지원되는 메커니즘 중 하나 또는 REST 프로토콜과 함께 시스템 할당 ID를 사용하여 Azure Maps 액세스 토큰을 가져오기 위해 Azure 함수용 코드를 씁니다. 자세한 내용은 [Azure 리소스 토큰 가져오기](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)를 참조하세요

    예제 REST 프로토콜은 다음과 같습니다.

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    다음은 응답 예제입니다.

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Azure 함수 HttpTrigger에 대한 보안 구성.

   1. [함수 액세스 키 만들기](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   1. 프로덕션 환경에서 Azure 함수에 대한 [HTTP 엔드포인트를 보호](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)합니다.

7. 웹 애플리케이션 Azure Maps Web SDK를 구성합니다. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>다음 단계

단일 페이지 애플리케이션 시나리오에 대한 추가 해석:
> [!div class="nextstepaction"]
> [단일 페이지 애플리케이션](../active-directory/develop/scenario-spa-overview.md)

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 다른 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)

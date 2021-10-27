---
title: '빠른 시작: 웹 API 등록 및 공개 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 웹 API를 Microsoft ID 플랫폼에 등록하고 범위를 구성하여 API 리소스에 대한 권한 기반 액세스가 가능하도록 클라이언트에 공개합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4d5f4a92955aab2d920c21e73ec71bdf56bf76e9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044349"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>빠른 시작: 웹 API를 공개하는 애플리케이션 구성

이 빠른 시작에서는 웹 API를 Microsoft ID 플랫폼에 등록하고 예제 범위를 추가하여 클라이언트 앱에 공개합니다. 웹 API를 등록하고 범위를 통해 공개하면 권한 있는 사용자 및 API에 액세스하는 클라이언트 앱에 해당 리소스에 권한 기반 액세스 권한을 제공할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정 - [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [빠른 시작: 테넌트 설정](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>웹 API 등록

웹 API에서 리소스에 대한 범위 액세스를 제공하려면 먼저 API를 Microsoft ID 플랫폼에 등록해야 합니다.

1. [빠른 시작: Microsoft ID 플랫폼에 앱 등록](quickstart-register-app.md)의 **애플리케이션 등록** 섹션에 설명된 단계를 수행하세요.
1. **리디렉션 URI 추가** 및 **플랫폼 설정 구성** 섹션을 건너뛰세요. 사용자가 대화형으로 로그인하지 않으므로 웹 API의 리디렉션 URI를 구성할 필요가 없습니다.
1. 지금은 **자격 증명 추가** 섹션을 건너뛰세요. API가 다운스트림 API에 액세스하는 경우에만 자체 자격 증명이 필요하며, 이 내용은 이 문서에서 다루지 않는 시나리오입니다.

웹 API가 등록되면 API의 코드에서 API 소비자에게 세부적인 사용 권한을 제공하는 데 사용할 수 있는 범위를 추가할 수 준비가 완료된 것입니다.

## <a name="add-a-scope"></a>범위 추가

클라이언트 애플리케이션의 코드는 보호된 리소스(웹 API)에 대한 요청과 함께 액세스 토큰을 전달하여 웹 API에서 정의한 작업을 수행할 권한을 요청합니다. 그런 다음, 웹 API는 수신한 액세스 토큰에 작업에 필요한 범위가 포함되어 있는 경우에만 요청된 작업을 수행합니다.

먼저 다음 단계에 따라 `Employees.Read.All`이라는 예제 범위를 만듭니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독**  필터 :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false":::를 사용하여 클라이언트 앱의 등록이 포함된 테넌트를 선택합니다.
1. **Azure Active Directory** > **앱 등록** 을 차례로 선택한 다음, API의 앱 등록을 선택합니다.
1. **API 표시** > **범위 추가** 를 선택합니다.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Azure Portal에 있는 앱 등록의 [API 표시] 창":::

1. 아직 **애플리케이션 ID URI** 를 구성하지 않은 경우 설정하라는 메시지가 표시됩니다.

   앱 ID URI는 API 코드에서 참조할 범위의 접두사 역할을 하며 전역적으로 고유해야 합니다. `api://<application-client-id>` 형식으로 제공되는 기본값을 사용해도 되고, `https://contoso.com/api`처럼 좀 더 읽기 쉬운 URI를 지정해도 됩니다.
   
   유효한 앱 ID URI 패턴에 대한 자세한 내용은 [Azure AD 앱 매니페스트 참조](reference-app-manifest.md)에서 확인할 수 있습니다.

1. 다음으로 **범위 추가** 창에서 범위 특성을 지정합니다. 이 연습에서는 예제 값을 사용해도 되고, 직접 값을 지정해도 됩니다.

    | 필드 | Description | 예제 |
    |-------|-------------|---------|
    | **범위 이름** | 범위의 이름입니다. 일반적인 범위 명명 규칙은 `resource.operation.constraint`입니다. | `Employees.Read.All` |
    | **동의할 수 있는 사람** | 이 범위를 사용자가 동의할 수 있는지 아니면 관리자 동의가 필요한지 여부를 지정합니다. 높은 권한을 적용하려면 **관리자만** 을 선택합니다. | **관리자 및 사용자** |
    | **관리자 동의 표시 이름** | 범위의 목적에 대한 간단한 설명으로 관리자에게만 표시됩니다. | `Read-only access to Employee records` |
    | **관리자 동의 설명** | 범위에서 부여하는 권한에 대한 자세한 설명으로 관리자에게만 표시됩니다. | `Allow the application to have read-only access to all Employee data.` |
    | **사용자 동의 표시 이름** | 범위의 목적에 대한 간단한 설명입니다. **동의할 수 있는 사람** 을 **관리자 및 사용자** 로 설정한 경우에만 사용자에게 표시됩니다. | `Read-only access to your Employee records` |
    | **사용자 동의 설명** | 범위에서 부여하는 권한에 대한 자세한 설명입니다. **동의할 수 있는 사람** 을 **관리자 및 사용자** 로 설정한 경우에만 사용자에게 표시됩니다. | `Allow the application to have read-only access to your Employee data.` |

1. **상태** 를 **사용** 으로 설정한 다음, **범위 추가** 를 선택합니다.

1. (선택 사항) 관리자가 정의한 범위에 동의하도록 요청하는 메시지를 앱 사용자에게 표시하지 않으려면 웹 API에 액세스하도록 클라이언트 애플리케이션에 *미리 권한을 부여* 하면 됩니다. 사용자에게는 동의를 거부할 기회가 없기 때문에 신뢰할 수 있는 클라이언트 애플리케이션 *에만* 미리 권한을 부여해야 합니다.
    1. **권한 있는 클라이언트 애플리케이션** 에서 **클라이언트 애플리케이션 추가** 를 선택합니다.
    1. 미리 권한을 부여하려는 클라이언트 애플리케이션의 **애플리케이션(클라이언트) ID** 를 입력합니다. 예를 들어 앞에서 등록한 웹 애플리케이션의 ID를 입력합니다.
    1. **권한 있는 모든 범위** 에서 동의 메시지를 표시하지 않을 범위를 선택한 다음, **애플리케이션 추가** 를 선택합니다.

    이 선택 사항을 수행한 경우 클라이언트 앱은 이제 PCA(미리 권한이 부여된 클라이언트 앱)이며, 사용자가 클라이언트 앱에 로그인할 때 동의 여부를 묻는 메시지가 표시되지 않습니다.

## <a name="add-a-scope-requiring-admin-consent"></a>관리자 동의가 필요한 범위 추가

다음으로 관리자만 동의할 수 있는 `Employees.Write.All`이라는 다른 예제 범위를 추가합니다. 관리자 동의가 필요한 범위는 일반적으로 더 높은 권한이 있는 작업에 대한 액세스를 제공하는 데 사용되며, 사용자가 대화형으로 로그인하지 않는 백 엔드 서비스 또는 디먼으로 실행되는 클라이언트 애플리케이션을 통해 제공되는 경우가 많습니다.

`Employees.Write.All` 예제 범위를 추가하려면 [범위 추가](#add-a-scope) 섹션의 단계에 따라 **범위 추가** 창에서 다음 값을 지정합니다.

| 필드                          | 예제 값                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **범위 이름**                 | `Employees.Write.All`                                              |
| **동의할 수 있는 사람**            | **관리자 전용**                                                    |
| **관리자 동의 표시 이름** | `Write access to Employee records`                                 |
| **관리자 동의 설명**  | `Allow the application to have write access to all Employee data.` |
| **사용자 동의 표시 이름**  | *없음(비워 둠)*                                               |
| **사용자 동의 설명**   | *없음(비워 둠)*                                               |

## <a name="verify-the-exposed-scopes"></a>공개된 범위 확인

이전 섹션에서 설명한 두 가지 예제 범위를 모두 성공적으로 추가했다면 다음 이미지처럼 웹 API 앱 등록의 **API 표시** 에 두 범위가 표시됩니다.

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="두 범위를 표시하는 [API 표시] 창의 스크린샷":::

이미지에 표시된 것처럼 범위의 전체 문자열은 웹 API의 **애플리케이션 ID URI** 와 범위의 **범위 이름** 을 연결한 것입니다.

예를 들어 웹 API의 애플리케이션 ID URI가 `https://contoso.com/api`이고 범위 이름이 `Employees.Read.All`인 경우 전체 범위는 다음과 같습니다.

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>공개된 범위 사용

시리즈의 다음 문서에서는 이 문서의 단계를 수행하여 정의한 웹 API 액세스 권한 및 범위를 사용하여 클라이언트 앱의 등록을 구성합니다.

클라이언트 앱 등록에 웹 API에 대한 액세스 권한이 부여되면 클라이언트에서 Microsoft ID 플랫폼을 통해 OAuth 2.0 액세스 토큰을 발급할 수 있습니다. 클라이언트는 웹 API를 호출할 때, 클라이언트의 앱 등록에 지정된 권한으로 범위(`scp`) 클레임이 설정된 액세스 토큰을 제공합니다.

추가 범위를 나중에 필요한 대로 노출할 수 있습니다. 웹 API는 여러 작업에 연결된 여러 범위를 공개할 수 있습니다. 리소스는 수신하는 OAuth 2.0 액세스 토큰의 범위(`scp`) 클레임을 평가하여 런타임에 웹 API에 대한 액세스를 제어할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 범위를 구성하여 웹 API를 공개했으므로 해당 범위에 액세스할 수 있는 권한을 사용하여 클라이언트 앱의 등록을 구성합니다.

> [!div class="nextstepaction"]
> [웹 API 액세스를 위한 앱 등록 구성](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application

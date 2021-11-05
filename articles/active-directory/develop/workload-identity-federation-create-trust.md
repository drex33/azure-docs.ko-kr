---
title: 앱과 외부 ID 공급자 간에 신뢰 관계 만들기
titleSuffix: Microsoft identity platform
description: Azure AD의 앱과 외부 ID 공급자 간에 신뢰 관계를 설정합니다.  이렇게 하면 Azure 외부의 소프트웨어 워크로드가 비밀 또는 인증서를 사용하지 않고 Azure AD로 보호되는 리소스에 액세스할 수 있습니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/25/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 1d2c4631b663a57a8b1b2465c8cba6d6f81d803b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102680"
---
# <a name="configure-an-app-to-trust-an-external-identity-provider-preview"></a>외부 ID 공급자를 신뢰하도록 앱 구성(미리 보기)

이 문서에서는 Azure AD(Azure Active Directory)의 애플리케이션과 IdP(외부 ID 공급자) 간에 신뢰 관계를 만드는 방법을 설명합니다.  그런 다음 외부 소프트웨어 워크로드를 구성하여 외부 IdP의 토큰을 Microsoft ID 플랫폼의 액세스 토큰으로 교환할 수 있습니다. 외부 워크로드는 비밀을 관리할 필요 없이(지원되는 시나리오에서) Azure AD로 보호되는 리소스에 액세스할 수 있습니다.  토큰 교환 워크플로에 대한 자세한 내용은 [워크로드 ID 페더레이션](workload-identity-federation.md)을 읽어보세요.  Microsoft Graph 사용하여 앱 등록에서 페더레이션 ID 자격 증명을 구성하여 신뢰 관계를 설정합니다.

앱 등록을 만들고 비밀 또는 인증서를 추가할 수 있는 권한이 있는 모든 사용자는 페더레이션 ID 자격 증명을 추가할 수 있습니다.  그러나 [사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) 블레이드에서 **사용자가 애플리케이션을 등록할 수 있음** 스위치가 **아니요** 로 설정된 경우 앱 등록을 만들거나 페더레이션 ID 자격 증명을 구성할 수 없습니다.  관리자를 찾아서 사용자 대신 페더레이션 ID 자격 증명을 구성합니다.  애플리케이션 관리자 또는 애플리케이션 소유자 역할의 모든 사용자가 이 작업을 수행할 수 있습니다.

외부 IdP를 신뢰하도록 앱을 구성한 후 Microsoft ID 공급자로부터 액세스 토큰을 받고 Azure AD로 보호되는 리소스에 액세스하도록 소프트웨어 워크로드를 구성합니다.

## <a name="prerequisites"></a>사전 요구 사항
Azure AD에서 [앱 등록 만들기](quickstart-register-app.md)  외부 소프트웨어 워크로드가 대상으로 하는 Azure 리소스에 대한 액세스 권한을 앱에 부여합니다.  

다음 단계에서 필요한 앱의 개체 ID(애플리케이션(클라이언트) ID 아님)를 찾습니다.  Azure Portal에서 앱의 개체 ID를 찾을 수 있습니다.  Azure Portal의 [등록된 애플리케이션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 목록으로 이동하여 앱 등록을 선택합니다.  **개요**->**기본 정보** 에서 **개체 ID** 를 찾습니다.

다음 단계에서 필요한 외부 IdP 및 소프트웨어 워크로드에 대한 정보를 가져옵니다.

## <a name="configure-a-federated-identity-credential-using-microsoft-graph"></a>Microsoft Graph를 사용하여 페더레이션 ID 자격 증명 구성

[Azure Cloud Shell](https://portal.azure.com/#cloudshell/)을 시작하고 테넌트에 로그인합니다.

### <a name="create-a-federated-identity-credential"></a>페더레이션 ID 자격 증명 만들기

다음 명령을 실행하여 앱(앱의 개체 ID에 의해 지정됨)에 [새 페더레이션 ID 자격 증명 만들기](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)를 수행합니다.  

*발급자* 와 *주체* 는 신뢰 관계를 설정하는 데 필요한 주요 정보입니다. *발급자* 는 외부 ID 공급자의 URL이며 교환되는 외부 토큰의 `issuer` 클레임과 일치해야 합니다.  *주체* 는 외부 소프트웨어 워크로드의 식별자이며 교환되는 외부 토큰의 `sub`(`subject`) 클레임과 일치해야 합니다. *주체* 는 고정된 형식이 없으며(각 IdP는 고유한 형식 사용), 경우에 따라 GUID, 콜론으로 구분된 식별자, 경우에 따라 임의의 문자열을 사용합니다.  `issuer`  및  `subject` 의 조합은 앱에서 고유해야 합니다.  외부 소프트웨어 워크로드가 외부 토큰을 액세스 토큰으로 교환하도록 Microsoft ID 플랫폼에 요청하면 페더레이션 ID 자격 증명의 *발급자* 및 *주체* 값이 외부 토큰에 제공된 `issuer` 및 `subject` 클레임에 대해 검사됩니다. 유효성 검사를 통과하면 Microsoft ID 플랫폼에서는 외부 소프트웨어 워크로드에 액세스 토큰을 발급합니다.

*대상* 은 외부 토큰에 표시할 수 있는 대상을 나열합니다.  이 필드는 필수이며, 기본값은 "api://AzureADTokenExchange"입니다. 들어오는 토큰의 `aud` 클레임에서 수락해야 하는 Microsoft ID 플랫폼을 나타냅니다.  이 값은 외부 ID 공급자의 Azure AD를 나타내며 ID 공급자 간에 고정된 값이 없습니다. 이 토큰의 대상 역할을 하려면 IdP에 새 애플리케이션 등록을 만들어야 할 수 있습니다.

*이름* 은 페더레이션 ID 자격 증명의 고유 식별자로, 문자 제한이 120자이며 URL 친화적이어야 합니다. 만든 후에는 변경이 불가능합니다.

*설명* 은 페더레이션 ID 자격 증명에 대한 유효성이 검사되지 않은 사용자 제공 설명입니다. 

다음 명령을 실행하여 앱(앱의 개체 ID에 의해 지정됨)에 [새 페더레이션 ID 자격 증명 만들기](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)를 수행합니다.  *발급자* 는 GitHub를 외부 토큰 발급자로 식별합니다.  *주체* 는 GitHub Actions 워크플로에 대한 GitHub 조직, 리포지토리 및 환경을 식별합니다.  GitHub Actions 워크플로가 GitHub 토큰을 액세스 토큰으로 교환하도록 Microsoft ID 플랫폼에 요청하면 페더레이션 ID 자격 증명의 값이 제공된 GitHub 토큰에 대해 검사됩니다.

```azurecli
az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' --body '{"name":"Testing","issuer":"https://token.actions.githubusercontent.com/","subject":"repo:octo-org/octo-repo:environment:Production","description":"Testing","audiences":["api://AzureADTokenExchange"]}' 
```

그리고 다음과 같은 응답을 받습니다.
```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials/$entity",
  "audiences": [
    "api://AzureADTokenExchange"
  ],
  "description": "Testing",
  "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
  "issuer": "https://token.actions.githubusercontent.com/",
  "name": "Testing",
  "subject": "repo:octo-org/octo-repo:environment:Production"
}
```

> [!IMPORTANT]
> *주체* 설정에 잘못된 외부 워크로드 정보를 실수로 추가하면 페더레이션 ID 자격 증명이 오류 없이 성공적으로 만들어집니다.  토큰 교환이 실패할 때까지 오류가 명확해지지 않습니다.

### <a name="list-federated-identity-credentials-on-an-app"></a>앱에 페더레이션 ID 자격 증명 나열

다음 명령을 실행하여 앱(앱의 개체 ID에 의해 지정됨)에 대한 [페더레이션 ID 자격 증명 나열](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)을 수행합니다.

```azurecli
az rest -m GET -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' 
```

그리고 다음과 유사한 응답을 받습니다.

```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials",
  "value": [
    {
      "audiences": [
        "api://AzureADTokenExchange"
      ],
      "description": "Testing",
      "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
      "issuer": "https://token.actions.githubusercontent.com/",
      "name": "Testing",
      "subject": "repo:octo-org/octo-repo:environment:Production"
    }
  ]
}
```

### <a name="delete-a-federated-identity-credential"></a>페더레이션 ID 자격 증명 삭제

다음 명령을 실행하여 앱(앱의 개체 ID에 의해 지정됨)에서 [페더레이션 ID 자격 증명 삭제](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)를 수행합니다.

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/51ecf9c3-35fc-4519-a28a-8c27c6178bca' 

```

## <a name="next-steps"></a>다음 단계
자세한 내용은 Azure AD에서 [OAuth 2.0 클라이언트 자격 증명 부여](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) 및 다른 IdP에서 발급한 클라이언트 어설션을 사용하여 토큰을 가져오는 방법을 읽어보세요.
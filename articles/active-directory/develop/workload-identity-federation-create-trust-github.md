---
title: 앱과 GitHub 간에 신뢰 관계 만들기
titleSuffix: Microsoft identity platform
description: Azure AD의 앱과 GitHub 리포지토리 간에 신뢰 관계를 설정합니다.  이렇게 하면 GitHub Actions 워크플로가 비밀 또는 인증서를 사용하지 않고 Azure AD 보호 리소스에 액세스할 수 있습니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/18/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 3e1e3d4857555b648b841a544e02346e6144bb2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102681"
---
# <a name="configure-an-app-to-trust-a-github-repo-preview"></a>GitHub 리포지토리를 신뢰하도록 앱 구성(미리 보기)

이 문서에서는 Azure AD(Azure Active Directory)의 애플리케이션과 GitHub 리포지토리 간에 신뢰 관계를 만드는 방법을 설명합니다.  그런 다음 Microsoft ID 플랫폼 액세스 토큰에 대한 GitHub 토큰을 교환하고 비밀을 관리할 필요 없이 Azure AD 보호 리소스에 액세스하도록 GitHub Actions 워크플로를 구성할 수 있습니다.  토큰 교환 워크플로에 대한 자세한 내용은 [워크로드 ID 페더레이션](workload-identity-federation.md)을 읽어보세요.  Azure Portal 앱 등록에서 페더레이션 ID 자격 증명을 구성하거나 Microsoft Graph를 사용하여 신뢰 관계를 설정합니다.

앱 등록을 만들고 비밀 또는 인증서를 추가할 수 있는 권한이 있는 모든 사용자는 페더레이션 ID 자격 증명을 추가할 수 있습니다.  그러나 [사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) 블레이드에서 **사용자가 애플리케이션을 등록할 수 있음** 스위치가 **아니요** 로 설정된 경우 앱 등록을 만들거나 페더레이션 ID 자격 증명을 구성할 수 없습니다.  관리자를 찾아서 사용자 대신 페더레이션 ID 자격 증명을 구성합니다.  애플리케이션 관리자 또는 애플리케이션 소유자 역할의 모든 사용자가 이 작업을 수행할 수 있습니다.

GitHub 리포지토리를 신뢰하도록 앱을 구성한 후 Microsoft ID 공급자로부터 액세스 토큰을 받고 Azure AD 보호 리소스에 액세스하도록 [GitHub Actions 워크플로를 구성](/azure/developer/github/connect-from-azure)합니다.

## <a name="prerequisites"></a>사전 요구 사항
Azure AD에서 [앱 등록 만들기](quickstart-register-app.md)  GitHub 워크플로가 대상으로 하는 Azure 리소스에 대한 액세스 권한을 앱에 부여합니다.  

다음 단계에서 필요한 앱의 개체 ID(애플리케이션(클라이언트) ID 아님)를 찾습니다.  Azure Portal에서 앱의 개체 ID를 찾을 수 있습니다.  Azure Portal의 [등록된 애플리케이션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 목록으로 이동하여 앱 등록을 선택합니다.  **개요**->**기본 정보** 에서 **개체 ID** 를 찾습니다.

다음 단계에서 필요한 GitHub 리포지토리에 대한 조직, 리포지토리 및 환경 정보를 얻습니다.

## <a name="configure-a-federated-identity-credential"></a>페더레이션 ID 자격 증명 구성

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에 로그인합니다.  **앱 등록** 으로 이동하여 구성하려는 앱을 엽니다.

**인증서 및 비밀** 로 이동합니다.  **페더레이션된 자격 증명** 탭에서 **자격 증명 추가** 를 선택합니다.  **자격 증명 추가** 블레이드가 열립니다.

**페더레이션 자격 증명 시나리오** 드롭 다운 박스에서 **Azure 리소스를 배포하는 GitHub 작업** 을 선택합니다.

GitHub Actions 워크플로에 대한 **조직** 및 **리포지토리** 를 지정합니다.  

**엔터티 형식** 의 경우 **환경**, **분기**, **끌어오기 요청** 또는 **태그** 를 선택하고 값을 지정합니다.

페더레이션된 자격 증명의 **이름** 을 추가합니다.

**발급자**, **대상 그룹** 및 **주체 식별자** 필드는 입력한 값에 따라 자동으로 입력됩니다.

**추가** 를 클릭하여 페더레이션된 자격 증명을 구성합니다.

:::image type="content" source="media/workload-identity-federation-create-trust-github/add-credential.png" alt-text="샘플 값을 보여주는 자격 증명 추가 창의 스크린샷." :::

> [!NOTE]
> *주체* 설정에서 실수로 다른 사람의 GitHub 리포지토리를 구성하는 경우(다른 사용자의 리포지토리와 일치하는 오타 입력) 페더레이션 ID 자격 증명을 성공적으로 만들 수 있습니다.  그러나 GitHub 구성에서는 다른 사람의 리포지토리에 액세스할 수 없기 때문에 오류가 발생합니다.

> [!IMPORTANT]
> **조직**, **리포지토리** 및 **엔터티 형식** 값은 GitHub 워크플로 구성의 구성과 정확히 일치해야 합니다. 그렇지 않으면 Microsoft ID 플랫폼에서 들어오는 외부 토큰을 보고 액세스 토큰에 대한 교환을 거부합니다.  오류가 발생하지 않고 교환이 오류 없이 실패합니다.

# <a name="microsoft-graph"></a>[Microsoft Graph](#tab/microsoft-graph)
[Azure Cloud Shell](https://portal.azure.com/#cloudshell/)을 시작하고 테넌트에 로그인합니다.

### <a name="create-a-federated-identity-credential"></a>페더레이션 ID 자격 증명 만들기

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

*이름*: Azure 애플리케이션의 이름입니다.

*발급자*: GitHub OIDC 공급자에 대한 경로입니다. `https://token.actions.githubusercontent.com/`. 이 발급자는 Azure 애플리케이션에서 신뢰할 수 있게 됩니다.

*주체*: Azure에서 액세스 토큰을 부여하기 전에 요청은 여기에 정의된 조건과 일치해야 합니다.
- 환경에 연결된 작업의 경우: `repo:< Organization/Repository >:environment:< Name >`
- 환경에 연결되지 않은 작업의 경우 워크플로를 트리거하는 데 사용되는 참조 경로를 기반으로 분기/태그에 대한 참조 경로(`repo:< Organization/Repository >:ref:< ref path>`)를 포함합니다.  예를 들어 `repo:n-username/ node_express:ref:refs/heads/my-branch` 또는 `repo:n-username/ node_express:ref:refs/tags/my-tag`입니다.
- 끌어오기 요청 이벤트에 의해 트리거된 워크플로의 경우: `repo:< Organization/Repository >:pull-request`

*대상 그룹*: `api://AzureADTokenExchange`은 필수 값입니다.

> [!NOTE]
> *주체* 설정에서 실수로 다른 사람의 GitHub 리포지토리를 구성하는 경우(다른 사용자의 리포지토리와 일치하는 오타 입력) 페더레이션 ID 자격 증명을 성공적으로 만들 수 있습니다.  그러나 GitHub 구성에서는 다른 사람의 리포지토리에 액세스할 수 없기 때문에 오류가 발생합니다.

> [!IMPORTANT]
> *주체* 설정 값은 GitHub 워크플로 구성의 구성과 정확히 일치해야 합니다.  그렇지 않으면 Microsoft ID 플랫폼에서 들어오는 외부 토큰을 보고 액세스 토큰에 대한 교환을 거부합니다.  오류가 발생하지 않고 교환이 오류 없이 실패합니다.

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
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/1aa3e6a7-464c-4cd2-88d3-90db98132755' 
```
---

## <a name="get-the-application-client-id-and-tenant-id-from-the-azure-portal"></a>Azure Portal에서 애플리케이션(클라이언트) ID 및 테넌트 ID를 얻습니다

GitHub Actions 워크플로를 구성하기 전에 앱 등록의 *테넌트-ID* 및 *클라이언트-ID* 값을 얻습니다.  이러한 값은 Azure Portal에서 찾을 수 있습니다. [등록된 애플리케이션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 목록에서 앱 등록을 선택합니다.  **개요**->**기본 정보** 에서 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 를 찾습니다. 워크플로에 대한 Azure 로그인 작업에 사용할 GitHub 환경에서 이러한 값을 설정합니다.  

## <a name="next-steps"></a>다음 단계
Microsoft ID 공급자에서 액세스 토큰을 얻고 Azure 리소스에 액세스하도록 [GitHub Actions 워크플로를 구성](/azure/developer/github/connect-from-azure)합니다.

Microsoft ID 공급자에서 액세스 토큰을 얻고 Azure 리소스에 액세스하도록 GitHub Actions 워크플로를 구성하는 방법에 대한 자세한 내용은 [GitHub Actions 설명서](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)를 참조하세요.
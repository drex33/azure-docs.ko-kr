---
title: AuthN/AuthZ API 버전 관리
description: 필요한 경우, App Service 인증 API를 V2로 업그레이드하거나 특정 버전에 고정합니다.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e526f4a56806fa41d8955337d7e8463afac80b21
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113095166"
---
# <a name="manage-the-api-and-runtime-versions-of-app-service-authentication"></a>App Service 인증의 API 및 런타임 버전 관리

이 문서에서는 기본 제공 [App Service의 인증 및 권한 부여](overview-authentication-authorization.md)의 API 및 런타임 버전을 사용자 지정하는 방법을 보여줍니다.

App Service 인증을 위한 관리 API에는 두 가지 버전이 있습니다. Azure Portal의 "인증" 환경에는 V2 버전이 필요합니다. 이미 V1 API를 사용하는 앱은 몇 가지를 변경한 후에는 V2 버전으로 업그레이드할 수 있습니다. 특히 비밀 구성은 슬롯 고정 애플리케이션 설정으로 이동해야 합니다. 해당 작업은 앱 포털의 "인증" 섹션에서 자동으로 수행할 수 있습니다.

## <a name="update-the-configuration-version"></a>구성 버전 업데이트

> [!WARNING]
> V2로 마이그레이션하면 일부 클라이언트(예: Azure Portal, Azure CLI, Azure PowerShell의 기존 환경)를 통해 애플리케이션을 위한 App Service 인증/권한 부여 기능을 관리할 수 없게 됩니다. 이는 되돌릴 수 없습니다.

V2 API는 V1에서 수행된 것과 같이 Microsoft 계정을 고유한 공급자로 만들거나 편집하는 기능을 지원하지 않습니다. 대신, 수렴형 [Microsoft ID 플랫폼](../active-directory/develop/v2-overview.md)을 활용하여 Azure AD와 개인 Microsoft 계정 모두를 통해 사용자를 로그인합니다. V2 API로 전환할 때 V1 Azure Active Directory 구성이 Microsoft ID 플랫폼 공급자를 구성하는 데 사용됩니다. V1 Microsoft 계정 공급자는 마이그레이션 프로세스에서 전달되며 계속 정상적으로 작동하지만, 최신 Microsoft ID 플랫폼 모델로 이동하는 것이 좋습니다. 자세한 정보는 [Microsoft 계정 공급자 등록 지원](#support-for-microsoft-account-provider-registrations)을 참조하세요.

자동 마이그레이션 프로세스는 공급자 비밀을 애플리케이션 설정으로 이동한 후 나머지 구성을 새 형식으로 변환합니다. 자동 마이그레이션을 사용합니다.

1. 포털의 앱으로 이동하여 **인증** 메뉴 옵션을 선택합니다.
1. 앱이 V1 모델을 사용하여 구성된 경우 **업그레이드** 단추가 표시됩니다.
1. 확인 프롬프트에서 설명을 검토합니다. 마이그레이션을 수행할 준비가 되면 프롬프트에서 **업그레이드** 를 클릭합니다.

### <a name="manually-managing-the-migration"></a>수동으로 마이그레이션 관리

위에서 언급한 자동 버전을 사용하지 않으려면 다음 단계를 통해 애플리케이션을 V2 API로 수동 마이그레이션할 수 있습니다.

#### <a name="moving-secrets-to-application-settings"></a>비밀을 애플리케이션 설정으로 이동

1. V1 API를 사용하여 기존 구성을 가져옵니다.

   ```azurecli
   az webapp auth show -g <group_name> -n <site_name>
   ```

   결과로 발생한 JSON 페이로드에 구성한 각 공급자에 사용되는 비밀 값을 기록해 둡니다.

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft 계정: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > 비밀 값은 중요한 보안 자격 증명이므로 신중하게 처리해야 합니다. 이러한 값을 공유하거나 로컬 머신에 보관하지 마세요.

1. 각 비밀 값에 관한 슬롯 고정 애플리케이션 설정을 만듭니다. 각 애플리케이션 설정의 이름을 선택할 수 있습니다. 이 값은 이전 단계에서 얻은 값과 일치하거나 해당 값을 사용하여 만든 [Key Vault 비밀을 참조](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json)해야 합니다.

   설정을 만들려면 Azure Portal를 사용하거나 각 공급자를 위해 다음과 같은 변형을 실행할 수 있습니다.

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > 이 구성에 관한 애플리케이션 설정은 슬롯 고정으로 표시되어야 합니다. 즉, [슬롯 교환 작업](./deploy-staging-slots.md) 중에 환경 간에 이동하지 않습니다. 이는 인증 구성 자체가 환경에 연결되기 때문입니다. 

1. `authsettings.json`이라는 새 JSON 파일을 만듭니다. 이전에 받은 출력을 가져와 각 비밀 값을 제거합니다. 나머지 출력을 파일에 기록하여 비밀이 포함되지 않도록 합니다. 경우에 따라 구성에 빈 문자열을 포함하는 배열이 있을 수 있습니다. `microsoftAccountOAuthScopes`가 빈 문자열을 포함하지 않는지 확인하고, 포함하면 해당 값을 `null`로 전환합니다.

1. 각 공급자를 위해 이전에 만든 애플리케이션 설정 이름을 가리키는 `authsettings.json`에 속성을 추가합니다.
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft 계정: `microsoftAccountClientSecretSettingName`

   이 작업 후의 예제 파일은 다음과 유사합니다.이 경우에는 AAD용으로만 구성됩니다.

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. 이 파일을 앱에 관한 새 인증/권한 부여 구성으로 제출합니다.

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. 이 조처 후에도 앱이 예상대로 작동하는지 확인합니다.

1. 이전 단계에서 사용한 파일을 삭제합니다.

이제 ID 공급자 비밀을 애플리케이션 설정으로 저장하도록 앱을 마이그레이션했습니다.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Microsoft 계정 공급자 등록 지원

기존 구성에 Microsoft 계정 공급자가 포함되어 있고 Azure Active Directory 공급자가 포함되어 있지 않은 경우 구성을 Azure Active Directory 공급자로 전환한 후 마이그레이션을 수행할 수 있습니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면

1. Azure Portal에서 [**앱 등록**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)으로 이동하여 Microsoft 계정 공급자와 연결된 등록을 찾습니다. "개인 계정의 애플리케이션" 제목 아래에 있을 수 있습니다.
1. 등록 관련 "인증" 페이지로 이동합니다. "리디렉션 URI"에서 `/.auth/login/microsoftaccount/callback`으로 끝나는 항목이 표시됩니다. 이 URI를 복사합니다.
1. 방금 복사한 URI와 일치하는 새 URI를 추가합니다. 단, `/.auth/login/aad/callback`으로 끝나게 하세요. 이렇게 하면 App Service 인증/권한 부여 구성에서 등록을 사용할 수 있습니다.
1. 앱에 관한 App Service 인증/권한 부여 구성으로 이동합니다.
1. Microsoft 계정 공급자에 관한 구성을 수집합니다.
1. 이전 단계에서 수집한 클라이언트 ID 및 클라이언트 암호 값을 제공하여 "고급" 관리 모드에서 Azure Active Directory 공급자를 구성합니다. 발급자 URL에 관해서는 `<authentication-endpoint>/<tenant-id>/v2.0`을 사용하고 *\<authentication-endpoint>* 를 [클라우드 환경에 관한 인증 엔드포인트](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)(예: 글로벌 Azure에서는 "https://login.microsoftonline.com ")로 바꾸고, 또한 *\<tenant-id>* 를 **디렉터리(테넌트) ID** 로 바꿉니다.
1. 구성을 저장한 후 브라우저에서 사이트의 `/.auth/login/aad` 엔드포인트로 이동하고 로그인 흐름을 완료하여 로그인 흐름을 테스트합니다.
1. 이 시점에서 구성을 성공적으로 복사했지만 기존 Microsoft 계정 공급자 구성은 그대로 유지됩니다. 제거하기 전에 앱의 모든 부분이 로그인 링크 등을 통해 Azure Active Directory 공급자를 참조하는지 확인합니다. 앱의 모든 부분이 예상대로 작동하는지 확인합니다.
1. AAD Azure Active Directory 공급자 관련 작동을 확인한 후에는 Microsoft 계정 공급자 구성을 제거할 수 있습니다.

> [!WARNING]
> AAD 앱 등록용으로 [지원되는 계정 형식](../active-directory/develop/supported-accounts-validation.md)을 수정하여 두 등록을 수렴할 수 있습니다. 그러나 이 경우 Microsoft 계정 사용자를 위한 새 동의 프롬프트가 강제로 표시되고, 해당 사용자의 ID 클레임이 구조가 다를 수 있습니다 `sub` 특히 새 앱 ID가 사용되고 있으므로 값이 변경될 수 있습니다. 이 방법을 철저하게 이해한 경우가 아니라면 권장하지 않습니다. 대신 V2 API 표면에서 두 등록을 위한 지원을 대기해야 합니다.

#### <a name="switching-to-v2"></a>V2로 전환

위의 단계를 수행한 후 Azure Portal에서 앱으로 이동합니다. "인증(미리 보기)" 섹션을 선택합니다. 

또는 사이트 리소스에서 `config/authsettingsv2` 리소스에 대한 PUT 요청을 수행할 수 있습니다. 페이로드 관련 스키마는 [파일 기반 구성](configure-authentication-file-based.md) 섹션에서 캡처한 것과 같습니다.

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>앱을 특정 인증 런타임 버전에 고정

인증/권한 부여를 사용하도록 설정하면 [기능 개요](overview-authentication-authorization.md#how-it-works)에 설명된 대로 플랫폼 미들웨어가 HTTP 요청 파이프라인에 삽입됩니다. 이 플랫폼 미들웨어는 루틴 플랫폼 업데이트의 일부로 새로운 기능 및 향상된 기능이 정기적으로 업데이트됩니다. 기본적으로 웹 또는 함수 앱은 해당 플랫폼 미들웨어의 최신 버전에서 실행됩니다. 이러한 자동 업데이트는 항상 이전 버전과 호환됩니다. 그러나 드문 경우이지만 자동 업데이트에서 웹 또는 함수 앱에 런타임 문제가 발생하여 이전 미들웨어 버전으로 일시적으로 롤백할 수 있습니다. 이 문서에서는 특정 버전의 인증 미들웨어에 앱을 일시적으로 고정하는 방법을 설명합니다.

### <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트 

앱의 `runtimeVersion` 설정을 통해 특정 버전의 플랫폼 미들웨어에 앱을 고정할 수 있습니다. 앱은 특정 버전에 명시적으로 다시 고정하도록 선택하지 않는 한 항상 최신 버전에서 실행됩니다. 한 번에 몇 가지 버전이 지원됩니다. 더 이상 지원되지 않는 잘못된 버전에 고정하면 앱이 최신 버전을 대신 사용합니다. 항상 최신 버전을 실행하려면 `runtimeVersion`을 ~ 1로 설정합니다. 

### <a name="view-and-update-the-current-runtime-version"></a>현재 런타임 버전 확인 및 업데이트

앱에서 사용하는 런타임 버전을 변경할 수 있습니다. 앱을 다시 시작한 후 새 런타임 버전이 적용됩니다. 

#### <a name="view-the-current-runtime-version"></a>현재 런타임 버전 보기

Azure CLI를 사용하거나 앱의 기본 제공 버전 HTTP 엔드포인트 중 하나를 통해 현재 버전의 플랫폼 인증 미들웨어를 볼 수 있습니다.

##### <a name="from-the-azure-cli"></a>Azure CLI에서

Azure CLI를 사용하여 [az webapp auth show](/cli/azure/webapp/auth#az_webapp_auth_show) 명령으로 현재 미들웨어 버전을 확인합니다.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

이 코드에서 `<my_app_name>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 앱의 리소스 그룹 이름으로 바꿉니다.

CLI 출력에 `runtimeVersion` 필드가 표시됩니다. 이는 명확성을 위해 잘린 다음 예제 출력과 유사합니다. 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>버전 엔드포인트에서

앱에서 /.auth/version 엔드포인트를 적중하여 앱이 실행되고 있는 현재 미들웨어 버전을 볼 수도 있습니다. 이는 다음 예시 출력과 유사합니다.
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>현재 런타임 버전 업데이트

Azure CLI를 사용하여 [az webapp auth update](/cli/azure/webapp/auth#az_webapp_auth_update) 명령을 사용하여 앱에서 `runtimeVersion` 설정을 업데이트할 수 있습니다.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

`<my_app_name>`을 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 앱의 리소스 그룹 이름으로 바꿉니다. 그리고 `<version>`은 1.x 런타임의 유효한 버전 또는 최근 버전의 경우 `~1`로 바꿉니다. 고정할 버전을 결정하는 데 도움이 되는 다양한 [런타임 버전에 관한 릴리스 정보](https://github.com/Azure/app-service-announcements)를 참조하세요.

앞의 코드 샘플에서 **사용해 보세요.** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az_login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)

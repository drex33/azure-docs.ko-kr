---
title: OpenShift 4를 실행하는 Azure Red Hat OpenShift - 명령줄을 사용하여 Azure Active Directory 인증 구성
description: 명령줄을 사용하여 OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터에 대한 Azure Active Directory 인증을 구성하는 방법 알아보기
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9571e7692a70d36155f395ec57bdedafe5970ac8
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369774"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Azure Red Hat OpenShift 4 클러스터에 대한 Azure Active Directory 인증 구성(CLI)

CLI를 로컬로 설치하여 사용하도록 선택한 경우, 이 문서에서는 Azure CLI 버전 2.6.0 이상을 실행 중인 상태가 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Azure Active Directory 애플리케이션 구성에 사용할 클러스터별 URL을 검색합니다.

리소스 그룹 및 클러스터 이름에 대한 변수를 설정합니다.

**\<resource_group>** 을 리소스 그룹의 이름, **\<aro_cluster>** 를 클러스터 이름으로 바꿉니다.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

클러스터의 OAuth 콜백 URL을 생성하여 **oauthCallbackURL** 변수에 저장합니다. 

> [!NOTE]
> OAuth 콜백 URL의 `AAD` 섹션은 나중에 설정할 OAuth ID 공급자 이름과 일치해야 합니다.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

oauthCallbackURL의 형식은 사용자 지정 도메인과 약간 다릅니다.

* 사용자 지정 도메인(예: `contoso.com`)을 사용하는 경우 다음 명령을 실행합니다. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* 사용자 지정 도메인을 사용하지 않는 경우 `$domain`은 8자리 숫자 문자열이 되며 `$location.aroapp.io`로 확장됩니다.

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> OAuth 콜백 URL의 `AAD` 섹션은 나중에 설정할 OAuth ID 공급자 이름과 일치해야 합니다.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>인증용 Azure Active Directory 애플리케이션 만들기

**\<client_secret>** 를 애플리케이션의 보안 암호로 바꿉니다.

```azurecli-interactive
client_secret=<client_secret>
```

Azure Active Directory 애플리케이션을 만들고, 만든 애플리케이션 식별자를 검색합니다.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

애플리케이션을 소유하는 구독의 테넌트 ID를 검색합니다.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>ID 토큰에 포함할 선택적 클레임을 정의하는 매니페스트 파일 만들기

애플리케이션 개발자는 Azure AD 애플리케이션에서 [선택적 클레임](../active-directory/develop/active-directory-optional-claims.md)을 사용하여 애플리케이션으로 전송된 토큰에서 원하는 클레임을 지정할 수 있습니다.

선택적 클레임을 사용하여 다음을 수행할 수 있습니다.

- 애플리케이션에 대한 토큰에 포함할 추가 클레임을 선택합니다.
- Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

Azure Active Directory가 반환한 ID 토큰의 일부로 `upn`을 추가하여, `email` 클레임을 사용하고 `upn`으로 변경하여 기본 설정 사용자 이름을 설정하도록 OpenShift를 구성할 것입니다.

**manifest.json** 파일을 만들어 Azure Active Directory 애플리케이션을 구성합니다.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>매니페스트로 Azure Active Directory 애플리케이션의 optionalClaims 업데이트

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Azure Active Directory 애플리케이션 범위 권한 업데이트

Azure Active Directory에서 사용자 정보를 읽을 수 있도록 적절한 범위를 정의해야 합니다.

**Azure Active Directory Graph.User.Read** 범위에 대한 권한을 추가하여 로그인을 사용하도록 설정하고 사용자 프로필을 읽습니다.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> 이 Azure Active Directory 대한 전역 관리자로 인증되지 않는 한, 동의 제공 메시지를 무시해도 됩니다. 표준 도메인 사용자가 본인의 AAD 자격 증명으로 클러스터에 처음 로그인할 때 동의 제공을 요청 받게 됩니다.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>클러스터에 사용자 및 그룹 할당(선택 사항)

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다. Azure AD를 사용하면 테넌트 관리자 및 개발자는 앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한할 수 있습니다.

Azure Active Directory 설명서의 지침에 따라 [사용자 및 그룹을 앱에 할당합니다](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID 인증 구성

`kubeadmin` 자격 증명을 검색합니다. 다음 명령을 실행하여 `kubeadmin` 사용자의 암호를 찾습니다.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

다음 명령을 사용하여 OpenShift 클러스터의 API 서버에 로그인합니다. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

OpenShift 비밀을 만들어 Azure Active Directory 애플리케이션 비밀을 저장합니다.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Azure Active Directory에 대해 OpenShift OpenID 인증을 구성하는 **oidc.yaml** 파일을 만듭니다. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

클러스터에 구성을 적용합니다.

```azurecli-interactive
oc apply -f oidc.yaml
```

다음과 유사한 응답을 받게 됩니다.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Azure Active Directory를 통해 로그인 확인

이제 OpenShift 웹 콘솔에서 로그아웃하고 다시 로그인할 때 **AAD** 를 사용하여 로그인하는 새 옵션이 제공됩니다. 몇 분 정도 기다려야 할 수 있습니다.

![Azure Active Directory 옵션이 표시된 로그인 화면](media/aro4-login-2.png)

---
title: OpenShift 4를 실행하는 Azure Red Hat OpenShift - Azure Portal 및 OpenShift 웹 콘솔을 사용하여 Azure Active Directory 인증 구성
description: Azure Portal 및 OpenShift 웹 콘솔을 사용하여 OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터에 대한 Azure Active Directory 인증을 구성하는 방법에 대해 알아봅니다
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ada319684325b462729a44490207ad8e3c9d1cb9
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112371556"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Azure Red Hat OpenShift 4 클러스터(Portal)에 대한 Azure Active Directory 인증 구성

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

클러스터의 **OAuth 콜백 URL** 을 생성하고 기록해 둡니다. **aro-rg** 를 리소스 그룹의 이름으로 바꾸고 **aro-cluster** 를 클러스터 이름으로 바꾸어야 합니다.

> [!NOTE]
> OAuth 콜백 URL의 `AAD` 섹션은 나중에 설정할 OAuth ID 공급자 이름과 일치해야 합니다.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>인증용 Azure Active Directory 애플리케이션 만들기

Azure Portal에 로그인하고 [앱 등록 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)로 이동한 다음, **새 등록** 을 클릭하여 새 애플리케이션을 생성합니다.

애플리케이션의 이름(예: **aro-azuread-auth**)을 입력하고 이전에 검색한 OAuth 콜백 URL 값을 사용하여 **리디렉션 URI** 를 채웁니다.

![새 애플리케이션 등록](media/aro4-ad-registerapp.png)

**인증서 및 암호** 로 이동하고 **새 클라이언트 암호** 를 클릭하고 세부 정보를 입력합니다. 이후 단계에서 사용할 때 키 값을 기록해 둡니다. 다시 검색할 수 없습니다.

![비밀 만들기](media/aro4-ad-clientsecret.png)

**개요** 로 이동하여 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 를 기록해 둡니다. 이러한 정보는 나중 단계에서 필요합니다.

![애플리케이션(클라이언트) 및 디렉터리(테넌트) ID 검색](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>선택적 클레임 구성

애플리케이션 개발자는 Azure AD 애플리케이션에서 [선택적 클레임](../active-directory/develop/active-directory-optional-claims.md)을 사용하여 애플리케이션으로 전송된 토큰에서 원하는 클레임을 지정할 수 있습니다.

선택적 클레임을 사용하여 다음을 수행할 수 있습니다.

* 애플리케이션에 대한 토큰에 포함할 추가 클레임을 선택합니다.
* Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
* 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

Azure Active Directory가 반환한 ID 토큰의 일부로 `upn`을 추가하여, `email` 클레임을 사용하고 `upn`으로 변경하여 기본 설정 사용자 이름을 설정하도록 OpenShift를 구성할 것입니다.

**토큰 구성(미리 보기)** 으로 이동하고 **선택적 클레임 추가** 를 클릭합니다. **ID** 를 선택한 다음, **이메일** 및 **upn** 클레임을 확인합니다.

![추가된 이메일 및 upn 클레임을 보여주는 스크린샷.](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>클러스터에 사용자 및 그룹 할당(선택 사항)

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다. Azure AD를 사용하면 테넌트 관리자 및 개발자는 앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한할 수 있습니다.

Azure Active Directory 설명서의 지침에 따라 [사용자 및 그룹을 앱에 할당합니다](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID 인증 구성

`kubeadmin` 자격 증명을 검색합니다. 다음 명령을 실행하여 `kubeadmin` 사용자의 암호를 찾습니다.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

다음 예제 출력에서는 암호가 `kubeadminPassword`에 있는 것을 보여줍니다.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

다음 명령을 실행하여 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 형식의 클러스터 콘솔 URL을 찾을 수 있습니다.

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

브라우저에서 콘솔 URL을 시작하고 `kubeadmin` 자격 증명을 사용하여 로그인합니다.

**관리** 로 이동하여 **클러스터 설정** 을 클릭한 다음, **글로벌 구성** 탭을 선택합니다. 스크롤하여 **OAuth** 를 선택합니다.

아래로 스크롤하여 **ID 공급자** 아래에서 **추가** 를 선택하고 **OpenID 연결** 을 선택합니다.
![ID 공급자 드롭다운에서 OpenID Connect를 선택합니다](media/aro4-oauth-idpdrop.png)

이름을 **AAD** 로, **클라이언트 ID** 를 **애플리케이션 ID** 로, **클라이언트 암호** 를 입력합니다. **발급자 URL** 의 `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`과 같이 형식이 지정됩니다. 자리 표시자를 이전에 검색한 테넌트 ID로 바꿉니다.

![OAuth 세부 정보를 입력합니다](media/aro4-oauth-idp-1.png)

**클레임** 섹션까지 스크롤하고 **upn** 클레임의 값을 사용하도록 **기본 설정된 사용자 이름** 을 업데이트합니다.

![클레임 세부 정보 입력](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Azure Active Directory를 통해 로그인 확인

이제 OpenShift 웹 콘솔에서 로그아웃하고 다시 로그인할 때 **AAD** 를 사용하여 로그인하는 새 옵션이 제공됩니다. 몇 분 정도 기다려야 할 수 있습니다.

![Azure Active Directory 옵션이 표시된 로그인 화면](media/aro4-login-2.png)

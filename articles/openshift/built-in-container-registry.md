---
title: Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성
description: Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성
author: majguo
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: d1eec47a7801975b3974ea172bd2cfbdc5ea0901
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214779"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성

Azure Red Hat OpenShift는 새 이미지 리포지토리를 요청 시 자동으로 프로비저닝하는 기능을 추가하는 [OCR(OpenShift Container Registry)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html)이라는 통합 컨테이너 이미지 레지스트리를 제공합니다. 이 방법으로 사용자가 애플리케이션 빌드에 기본 제공 위치를 제공하여 결과 이미지를 푸시할 수 있습니다.

이 문서에서는 ARO(Azure Red Hat OpenShift) 4 클러스터에 기본 제공 컨테이너 이미지 레지스트리를 구성합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure AD 설정
> * OpenID Connect 설정
> * 기본 제공 컨테이너 이미지 레지스트리 액세스

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 ARO 클러스터가 있다고 가정합니다. ARO 클러스터가 필요한 경우 ARO 자습서인 [Azure Red Hat OpenShift 4 클러스터 만들기](./tutorial-create-cluster.md)를 참조하세요. `--pull-secret` 인수로 클러스터를 만들어 `az aro create`를 해야 합니다.  이는 Azure Active Directory 인증 및 기본 제공 컨테이너 레지스트리를 구성하는 데 필요합니다.

클러스터가 있으면 [Azure Red Hat OpenShift 4 클러스터에 연결](./tutorial-connect-cluster.md)의 단계를 수행하여 클러스터에 연결합니다.
   * 이 문서의 뒷부분에 나오는 `oc` 명령을 사용하므로 “OpenShift CLI 설치”의 단계를 수행해야 합니다.
   * `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`과 같은 클러스터 콘솔 URL을 기록해 둡니다. `<random>`과 `<region>`의 값이 이 문서 뒷부분에서 사용됩니다.
   * `kubeadmin` 자격 증명을 기록해 둡니다. 이 또한 이 문서의 뒷부분에서 사용됩니다.

### <a name="configure-azure-active-directory-authentication"></a>Azure Active Directory 인증 구성 

Azure AD(Azure Active Directory)는 OIDC(OpenID Connect)를 구현합니다. OIDC를 사용하면 Azure AD로 ARO 클러스터에 로그인할 수 있습니다. [Azure Active Directory 인증 구성](configure-azure-ad-cli.md)의 단계에 따라 클러스터를 설정합니다.

## <a name="access-the-built-in-container-image-registry"></a>기본 제공 컨테이너 이미지 레지스트리 액세스

이제 ARO 클러스터의 인증 방법을 설정했으므로 기본 제공 레지스트리에 액세스할 수 있습니다.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>관리자가 될 Azure AD 사용자 정의

1. Azure AD 사용자의 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다. OpenID를 이용해 관리자를 정의하기 위해 Azure Active Directory에 대한 OpenShift OpenID 인증을 활용합니다.

   1. InPrivate, Incognito 또는 기타 해당 브라우저 창 기능을 사용하여 콘솔에 로그인합니다. 이 창은 OIDC가 사용된 후에는 다르게 표시됩니다.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect가 활성화된 로그인 창.":::
   1. **openid** 선택

   > [!NOTE]
   > 여기에서 로그인하는 데 사용하는 사용자 이름과 암호를 기록해 둡니다. 이 사용자 이름과 암호는 이 문서와 다른 문서에서 다른 작업의 관리자 역할을 합니다.
2. 다음 단계를 따라 OpenShift CLI로 로그인합니다.  이 프로세스를 `oc login`라고 합니다.
   1. 웹 콘솔의 오른쪽 위에서 로그인한 사용자의 바로 가기 메뉴를 확장하고 **로그인 명령 복사** 를 선택합니다.
   2. 필요한 경우 동일한 사용자로 새 탭 창에 로그인합니다.
   3. **토큰 표시** 를 선택합니다.
   4. 여기서 보이는 것처럼 **이 토큰을 사용하여 로그인** 아래에 나열된 값을 클립보드에 복사하고 셸에서 실행합니다.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. `oc whoami`를 콘솔에서 실행하고 출력을 **\<aad-user>** 로 기록합니다.  이 값은 이 문서의 뒷부분에서 사용됩니다.
4. OpenShift 웹 콘솔에서 로그아웃합니다. **\<aad-user>** 로 레이블이 지정된 브라우저 창의 오른쪽 위에 있는 단추를 선택하고 **로그아웃** 을 선택합니다.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Azure AD 사용자에게 레지스트리 상호 작용에 필요한 역할을 부여합니다

1. `kubeadmin` 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.
1. 위의 `oc login`단계를 수행하여 `kubeadmin`에 대한 토큰을 사용해 OpenShift CLI에 로그인하지만, `kubeadmin`을 사용하여 웹 콘솔에 로그인한 후에 이를 수행합니다.
1. 다음 명령을 실행하여 **aad-사용자** 에 대한 기본 제공 레지스트리 액세스를 사용합니다.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>컨테이너 레지스트리 URL 가져오기

다음에 표시된 것처럼 `oc get route` 명령을 사용하여 컨테이너 레지스트리 URL을 가져옵니다.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > **Container Registry URL** 의 콘솔 출력을 확인합니다. 이는 본 가이드의 정규화된 레지스트리 이름으로 사용됩니다.

## <a name="next-steps"></a>다음 단계

이제 기본 제공 컨테이너 이미지 레지스트리를 설정했으므로 OpenShift에 애플리케이션을 배포하여 시작할 수 있습니다. Java 애플리케이션의 경우 [Azure Red Hat OpenShift 4 클러스터에서 Liberty/WebSphere Liberty 열기를 사용해 Java 애플리케이션 배포](howto-deploy-java-liberty-app.md)를 확인하세요.
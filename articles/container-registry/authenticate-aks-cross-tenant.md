---
title: AKS 클러스터에서 다른 AD 테넌트에서 Azure Container Registry로 인증
description: 다른 AD 테넌트의 Azure Container Registry에 액세스할 수 있는 권한이 있는 AKS 클러스터의 서비스 주체 구성
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/13/2021
ms.openlocfilehash: da7a03e1f46523b67666a4a0ab7ab53fba909720
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545475"
---
# <a name="pull-images-from-a-container-registry-to-an-aks-cluster-in-a-different-azure-ad-tenant"></a>컨테이너 레지스트리에서 다른 Azure AD 테넌트에서 AKS 클러스터로 이미지 끌어오기

경우에 따라 하나의 Azure AD(Azure Active Directory) 테넌트에서 Azure AKS 클러스터를 사용하고 다른 테넌트에서 Azure Container Registry를 가질 수 있습니다. 이 문서에서는 AKS 서비스 주체 자격 증명을 사용하여 컨테이너 레지스트리에서 끌어오기 위해 테넌트 간 인증을 사용하도록 설정하는 단계를 안내합니다.

> [!NOTE]
> 클러스터와 컨테이너 레지스트리가 다른 테 넌 트에 있는 경우 레지스트리를 연결 하 고 AKS 관리 id를 사용 하 여 인증할 수 없습니다.

## <a name="scenario-overview"></a>시나리오 개요
이 예는 다음과 같은 가정을 전제로 합니다.

* AKS 클러스터는 **테넌트 A** 에 있고 Azure Container Registry는 **테넌트 B** 에 있습니다. 
* AKS 클러스터는 **테넌트 A** 에서 서비스 주체 인증으로 구성됩니다. [AKS 클러스터에 대한 서비스 주체](../aks/kubernetes-service-principal.md)를 만들고 사용하는 방법에 대해 자세히 알아봅니다.

AKS 클러스터의 구독에서 기여자 역할 이상이 필요하고 컨테이너 레지스트리의 구독에서 소유자 역할이 필요합니다.

다음 단계를 사용합니다.

* **테넌트 A** 에서 새 다중 테넌트 앱(서비스 주체)을 만듭니다. 
* **테넌트 B** 에서 앱을 프로비저닝합니다.
* **테넌트 B** 의 레지스트리에서 끌어오도록 서비스 주체를 구성합니다.
* 새 서비스 주체를 사용하여 인증하도록 **테넌트 A** 의 AKS 클러스터를 업데이트합니다.


## <a name="step-by-step-instructions"></a>단계별 지침

### <a name="step-1-create-multitenant-azure-ad-application"></a>1단계: 다중 테넌트 Azure AD 애플리케이션 만들기

1. **테넌트 A** 에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록 > + 새 등록** 을 선택합니다.
1. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정** 을 선택합니다.
1. **리디렉션 URI** 로 *https://www.microsoft.com* 를 입력합니다.
1. **등록** 을 선택합니다.
1. **개요** 페이지에서 **애플리케이션(클라이언트) ID** 를 기록해 둡니다. 2단계 및 4단계에서 사용됩니다.

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/service-principal-overview.png" alt-text="서비스 주체 애플리케이션 ID":::
1. **인증서 및 비밀** 의 **클라이언트 비밀** 에서 **+ 새 클라이언트 비밀** 을 선택합니다.
1. *암호* 와 같은 **설명** 을 입력하고 **추가** 를 선택합니다.
1. **클라이언트 비밀** 에서 클라이언트 비밀의 값을 기록해 둡니다. 4단계에서 AKS 클러스터의 서비스 주체를 업데이트하는 데 사용합니다.

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/configure-client-secret.png" alt-text="클라이언트 비밀 구성":::
### <a name="step-2-provision-the-service-principal-in-the-acr-tenant"></a>2단계: ACR 테넌트에서 서비스 주체 프로비저닝

1. **테넌트 B** 에서 관리자 계정을 사용하여 다음 링크를 엽니다. 표시된 경우 **테넌트 B의 ID** 와 다중 테넌트 앱의 **애플리케이션 ID**(클라이언트 ID)를 삽입합니다.

    ```console
    https://login.microsoftonline.com/<Tenant B ID>/oauth2/authorize?client_id=<Multitenant application ID>&response_type=code&redirect_uri=<redirect url>
    ```
1. **조직을 대신하여 동의** 를 선택한 다음 **수락** 을 선택합니다. 
    
    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-consent.png" alt-text="테넌트에게 애플리케이션에 대한 액세스 권한 부여":::
 

### <a name="step-3-grant-service-principal-permission-to-pull-from-registry"></a>3단계: 레지스트리에서 끌어올 수 있는 서비스 주체 권한 부여

**테넌트 B** 에서 대상 컨테이너 레지스트리로 범위가 지정된 서비스 주체에 AcrPull 역할을 할당합니다. [Azure Portal](../role-based-access-control/role-assignments-portal.md) 또는 기타 도구를 사용하여 역할을 할당할 수 있습니다. Azure CLI를 사용하는 단계 예는 [서비스 주체를 사용한 Azure Container Registry 인증](container-registry-auth-service-principal.md#use-an-existing-service-principal)을 참조하세요.

:::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-acr-pull.png" alt-text="다중 테넌트 앱에 acrpull 역할 할당":::

### <a name="step-4-update-aks-with-the-azure-ad-application-secret"></a>4단계: Azure AD 애플리케이션 비밀로 AKS 업데이트

1단계에서 수집한 다중 테넌트 애플리케이션(클라이언트) ID 및 클라이언트 비밀을 사용하여 [AKS 서비스 주체 자격 증명을 업데이트](../aks/update-credentials.md#update-aks-cluster-with-new-service-principal-credentials)합니다.

서비스 주체를 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [서비스 주체를 사용한 Azure Container Registry 인증](container-registry-auth-service-principal.md)에 대해 자세히 알아보기
* [Kubernetes 설명서](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)에서 이미지 끌어오기 비밀에 대한 자세한 정보
- [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md)에 대해 알아보기
- Kubernetes 클러스터에서 [Azure Container Registry를 사용 하 여 인증 하는 시나리오](authenticate-kubernetes-options.md) 에 대 한 자세한 정보



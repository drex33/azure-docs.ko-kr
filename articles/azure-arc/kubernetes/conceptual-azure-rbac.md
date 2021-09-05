---
title: Azure RBAC - Azure Arc 지원 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 지원 Kubernetes의 Azure RBAC 기능에 대한 개념적 개요를 제공합니다.
ms.openlocfilehash: 126e85eb312b977eb90e27971e8e65180542b069
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679965"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes의 Azure RBAC

Kubernetes [ClusterRoleBinding 및 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) 개체 형식은 Kubernetes에서 기본적으로 권한 부여를 정의하는 데 도움이 됩니다. Azure RBAC를 사용하면 Azure에서 Azure AD(Azure Active Directory) 및 역할 할당을 사용하여 클러스터에 대한 권한 부여 확인을 제어할 수 있습니다.

이 기능을 사용하면 Azure 리소스에 대한 모든 Azure RBAC 변경 내용을 보여주는 활동 로그와 같은 Azure 역할 할당의 모든 이점이 이제 Azure Arc 지원 Kubernetes 클러스터에 적용됩니다.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>아키텍처 - Azure Arc 지원 Kubernetes의 Azure RBAC

[ ![Azure RBAC 아키텍처 ](./media/conceptual-azure-rbac.png) ](./media/conceptual-azure-rbac.png#lightbox)

모든 권한 부여 액세스 검사를 Azure의 권한 부여 서비스로 라우팅하기 위해 웹후크 서버([가드](https://github.com/appscode/guard))가 클러스터에 배포됩니다.

클러스터의 `apiserver`는 `TokenAccessReview` 및 `SubjectAccessReview` 요청이 가드 웹후크 서버로 라우팅되도록 [웹후크 토큰 인증](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) 및 [웹후크 권한 부여](https://kubernetes.io/docs/reference/access-authn-authz/webhook/)를 사용하도록 구성됩니다. `TokenAccessReview` 및 `SubjectAccessReview` 요청은 `apiserver`로 전송되는 Kubernetes 리소스에 대한 요청에 의해 트리거됩니다.

그런 다음, 가드는 Azure의 권한 부여 서비스에서 `checkAccess`를 호출하여 요청 Azure AD 엔터티가 관심 있는 리소스에 액세스할 수 있는지 확인합니다. 

이 액세스를 허용하는 할당의 역할이 있는 경우 권한 부여 서비스 가드에서 `allowed` 응답이 전송됩니다. 가드는차례로 `apiserver`에 `allowed` 응답을 전송하여 호출 엔터티가 요청된 Kubernetes 리소스에 액세스할 수 있도록 합니다.


이 액세스를 허용하는 할당의 역할이 없으면 권한 부여 서비스에서 가드로 `denied` 응답이 전송됩니다. 가드는 `apiserver`에 `denied` 응답을 보내 요청된 리소스에 대한 403 사용할 수 없음 오류를 호출 엔터티에 제공합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 지원 Kubernetes 클러스터 클러스터에서 [Azure RBAC를 설정](./azure-rbac.md)합니다.
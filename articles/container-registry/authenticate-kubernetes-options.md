---
title: Kubernetes의 Azure Container Registry를 사용하여 인증하는 시나리오
description: Kubernetes 클러스터에서 Azure Container Registry에 인증하여 컨테이너 이미지를 끌어오기 위한 옵션 및 시나리오 개요
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/20/2021
ms.openlocfilehash: 2fc24714183438f9d740de79f9a9e9ce11f17391
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545400"
---
# <a name="scenarios-to-authenticate-with-azure-container-registry-from-kubernetes"></a>Kubernetes의 Azure Container Registry를 사용하여 인증하는 시나리오


관리하는 클러스터, [AKS(Azure Kubernetes Service)](../aks/intro-kubernetes.md) 또는 기타 클라우드에서 호스팅되는 관리형 클러스터 및 [minikube](https://minikube.sigs.k8s.io/), [kind](https://kind.sigs.k8s.io/)와 같은 "로컬" Kubernetes 구성을 비롯하여 Kubernetes용 컨테이너 이미지의 소스로 Azure Container Registry를 사용할 수 있습니다. 

Azure Container Registry에서 Kuberentes 클러스터로 이미지를 끌어오려면 인증 및 권한 부여 메커니즘을 설정해야 합니다. 클러스터 환경에 따라 다음 방법 중 하나를 선택합니다.

## <a name="scenarios"></a>시나리오

| Kubernetes 클러스터 |인증 방법  | Description  | 예제 | 
|---------|---------|---------|----------|
| AKS 클러스터 |AKS 관리 ID    |  연결된 Azure Container Registry에서 이미지를 가져오기 위해 AKS kubelet [관리 ID](../aks/use-managed-identity.md)를 사용하도록 설정합니다.<br/><br/> 레지스트리와 클러스터는 동일한 Active Directory 테넌트여야 하지만 동일하거나 다른 Azure 구독에 있을 수 있습니다.      | [Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)| 
| AKS 클러스터 | AKS 서비스 주체     | 대상 Azure Container Registry에 대한 권한으로 [AKS 서비스 주체](../aks/kubernetes-service-principal.md)를 사용하도록 설정합니다.<br/><br/>레지스트리 및 클러스터는 동일하거나 다른 Azure 구독 또는 Azure Active Directory 테넌트일 수 있습니다.        | [Azure Container Registry에서 다른 AD 테넌트의 AKS 클러스터로 이미지 가져오기](authenticate-aks-cross-tenant.md)
| AKS 이외의 Kubernetes 클러스터 |Pod [imagePullSecrets](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)   |  일반 Kubernetes 메커니즘을 사용하여 Pod 배포에 대한 레지스트리 자격 증명을 관리합니다.<br/><br/>AD 서비스 주체, 리포지토리 범위 토큰 또는 기타 지원되는 [레지스트리 자격 증명을](container-registry-authentication.md)구성합니다.  | [끌어오기 비밀을 사용하여 Azure Container Registry에서 Kubernetes 클러스터로 이미지 가져오기](container-registry-auth-kubernetes.md) | 



## <a name="next-steps"></a>다음 단계

* [Azure Container Registry로 인증](container-registry-authentication.md)하는 방법에 대해 자세히 알아봅니다.

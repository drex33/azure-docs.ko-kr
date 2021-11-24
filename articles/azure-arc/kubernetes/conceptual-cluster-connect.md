---
title: 클러스터 커넥트를 사용 하 여 어디서 나 Azure Arc 사용 가능 Kubernetes 클러스터에 액세스
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes의 클러스터 커넥트 기능에 대 한 개념적 개요를 제공 합니다.
ms.openlocfilehash: f391921c277c0818f3ca8a5d6c36c4360e44e1e2
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077531"
---
# <a name="access-azure-arc-enabled-kubernetes-cluster-from-anywhere-using-cluster-connect"></a>클러스터 커넥트를 사용 하 여 어디서 나 Azure Arc 사용 가능 Kubernetes 클러스터에 액세스

Azure Arc 사용 Kubernetes *클러스터 연결* 기능은 `apiserver` 방화벽에서 인바운드 포트를 사용 하도록 설정 하지 않고도 클러스터의에 대 한 연결을 제공 합니다. 클러스터에서 실행되는 역방향 프록시 에이전트는 아웃바운드 방식으로 Azure Arc 서비스를 사용하여 세션을 안전하게 시작할 수 있습니다. 

클러스터 연결을 통해 개발자는 대화형 개발 및 디버깅을 위해 어디서나 클러스터에 액세스할 수 있습니다. 또한 클러스터 사용자 및 관리자가 어디서나 클러스터에 액세스하거나 관리할 수 있습니다. Azure Pipelines, GitHub 작업 또는 기타 호스팅된 CI/CD 서비스의 호스팅된 에이전트/러너를 사용하여 자체 호스팅 에이전트 없어도 애플리케이션을 온-프레미스 클러스터에 배포할 수도 있습니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

[ ![클러스터 연결 아키텍처 ](./media/conceptual-cluster-connect.png) ](./media/conceptual-cluster-connect.png#lightbox)

클러스터 쪽에서는 에이전트 helm 차트의 일부로 배포된 `clusterconnect-agent`라는 역방향 프록시 에이전트가 Azure Arc 서비스에 대한 아웃바운드 호출을 수행하여 세션을 설정합니다.

사용자가 `az connectedk8s proxy`를 호출하는 경우:
1. Azure Arc 프록시 이진 파일은 클라이언트 컴퓨터에서 프로세스로 다운로드되고 스핀업됩니다. 
1. Azure Arc proxy는 `kubeconfig` 가 호출 되는 Azure arc 사용 Kubernetes 클러스터와 연결 된 파일을 페치합니다 `az connectedk8s proxy` .
    * Azure Arc 프록시는 호출자의 Azure 액세스 토큰과 Azure Resource Manager ID 이름을 사용합니다. 
1. Azure Arc 프록시를 통해 머신에 저장되는 `kubeconfig` 파일은 Azure Arc 프록시 프로세스의 엔드포인트에 대한 서버 URL을 가리킵니다.

사용자가 이 `kubeconfig` 파일을 사용하여 요청을 보내는 경우:
1. Azure Arc 프록시는 요청을 수신하는 엔드포인트를 Azure Arc 서비스에 매핑합니다. 
1. 그런 다음, Azure Arc 서비스는 클러스터에서 실행 중인 `clusterconnect-agent`에 요청을 전달합니다. 
1. `clusterconnect-agent`는 요청을 호출 엔터티에서 Azure AD 인증을 수행하는 `kube-aad-proxy` 구성 요소에 전달합니다. 
1. Azure AD 인증 후에는 `kube-aad-proxy`는 Kubernetes [사용자 가장](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) 기능을 사용하여 요청을 클러스터의 `apiserver`에 전달합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* 클러스터 연결을 사용하여 어디서나 안전하게 [클러스터에 액세스](./cluster-connect.md)합니다.

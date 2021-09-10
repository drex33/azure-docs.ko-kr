---
title: Azure Policy를 사용하여 대규모로 클러스터 구성 적용
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy를 사용하여 대규모로 클러스터 구성 적용
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: 7a6892c4b89128abe698573960b61d08c2ac2f35
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609631"
---
# <a name="use-azure-policy-to-apply-gitops-configurations-at-scale"></a>Azure Policy를 사용한 대규모 GitOps 구성 적용

Azure Policy를 사용하여 설정을(`Microsoft.KubernetesConfiguration/sourceControlConfigurations` 리소스 종류) Azure Arc 활성화 Kubernetes 클러스터(`Microsoft.Kubernetes/connectedclusters`)에 대규모로 적용할 수 있습니다.

Azure Policy를 사용하려면 기본 제공 GitOps 정책 정의를 선택하고 정책 할당을 만듭니다. 정책 할당을 만들 때:
1. 할당의 범위를 설정합니다.
    * 이 범위에는 구독 또는 관리 그룹 또는 특정 리소스 그룹의 모든 리소스 그룹이 해당됩니다.
2. 생성될 GitOps 구성의 매개 변수를 설정합니다. 

할당이 만들어지면 Azure Policy 엔진은 범위 내에 있는 모든 Azure Arc 활성화 Kubernetes 클러스터를 식별하고 각 클러스터에 GitOps 구성을 적용합니다.

문제를 분할하기 위해 각각 다른 Git 리포지토리를 가리키는 다른 GitOps 구성이 있는 여러 정책 할당을 만들 수 있습니다. 예를 들어 클러스터 관리자가 하나의 리포지토리를 사용하고 애플리케이션 팀에서 기타 리포지토리를 사용할 수 있습니다.

> [!TIP]
> 이러한 시나리오에는 기본으로 제공되는 정책 정의가 있습니다.
> * Flux에서 만든 SSH 키를 사용하는 공용 리포지토리 또는 프라이빗 리포지토리: `Configure Kubernetes clusters with specified GitOps configuration using no secrets`
> * 사용자 제공 SSH 키가 포함된 프라이빗 리포지토리: `Configure Kubernetes clusters with specified GitOps configuration using SSH secrets`
> * 사용자 제공한 HTTPS 키가 있는 프라이빗 리포지토리: `Configure Kubernetes clusters with specified GitOps configuration using HTTPS secrets`

## <a name="prerequisite"></a>필수 조건

이 정책 할당을 만들 범위(구독 또는 리소스 그룹)에 `Microsoft.Authorization/policyAssignments/write` 사용 권한이 있는지 확인합니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

1. Azure Portal에서 **정책** 으로 이동합니다.
1. 사이드바의 **작성** 섹션에서 **정의** 를 선택합니다.
1. "Kubernetes" 범주에서 "비밀 없이 지정된 GitOps 구성을 사용하여 Kubernetes 클러스터 구성" 기본 제공 정책 정의를 선택합니다. 
1. **할당** 을 클릭합니다.
1. 정책 할당이 적용되는 관리 그룹, 구독 또는 리소스 그룹으로 **범위** 를 설정합니다.
    * 정책 할당 범위에서 리소스를 제외하려면 **제외** 를 설정합니다.
1. 정책 할당에 쉽게 식별할 수 있는 **이름** 및 **설명** 을 작성합니다.
1. **정책 적용** 이 **사용됨** 으로 설정되어 있는지 확인합니다.
1. **다음** 을 선택합니다.
1. `sourceControlConfiguration`를 만드는 동안 사용할 매개 변수 값을 설정합니다.
    * 매개 변수에 대한 자세한 정보는 [GitOps 구성 배포 자습서](./tutorial-use-gitops-connected-cluster.md)를 참조하세요.
1. **다음** 을 선택합니다.
1. **수정 작업 만들기** 를 사용합니다.
1. **관리 ID 만들기** 가 선택되어 있는지, ID에 **참가자** 사용 권한이 있는지 확인합니다. 
    * 자세한 정보는 [정책 할당 만들기 빠른 시작](../../governance/policy/assign-policy-portal.md) 및 [Azure Policy를 사용하여 비준수 리소스 수정 문서](../../governance/policy/how-to/remediate-resources.md)를 참조하세요.
1. **검토 + 만들기** 를 선택합니다.

정책 할당을 만든 후에는 정책 할당 범위 내에서 생성된 새 Azure Arc 활성화 Kubernetes 클러스터에 구성이 적용됩니다.

기존 클러스터의 경우 수정 작업을 수동으로 실행해야 합니다. 이 작업에서 정책 할당의 효력이 발생하는 데 일반적으로 10~20분이 소요됩니다.

## <a name="verify-a-policy-assignment"></a>정책 할당 확인

1. Azure Portal에서 Azure Arc 활성화 Kubernetes 클러스터 중 하나로 이동합니다.
1. 사이드바의 **설정** 섹션에서 **정책** 을 선택합니다. 
    * 목록에서 **준수 상태** 를 *규격* 으로 설정하여 이전에 만든 정책 할당을 확인해야 합니다.
1. 사이드바의 **설정** 섹션에서 **GitOps** 를 선택합니다.
    * 목록에는 정책 할당에서 만든 구성이 표시되어야 합니다.
1. `kubectl`를 사용하여 클러스터를 검사합니다. 
    * GitOps 구성에서 만든 네임스페이스 및 아티팩트가 표시됩니다.
    * 클러스터에 배포되는 Git 리포지토리의 매니페스트에 설명된 개체가 표시됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc 활성화 Kubernetes 클러스터를 사용하는 컨테이너용 Azure Monitor를 설정하세요](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).

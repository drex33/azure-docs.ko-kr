---
title: ID 관리 모범 사례
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Services)에서 클러스터의 인증 및 권한 부여를 관리하는 방법에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: a29bd1513f021be03cf6c6bd4aa83d13062de170
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567678"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 인증 및 권한 부여 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 배포 및 유지 관리하는 경우 리소스 및 서비스에 대한 액세스를 관리하는 방법을 구현합니다. 이러한 컨트롤이 없는 경우:
* 계정이 불필요한 리소스 및 서비스에 액세스할 수 있습니다. 
* 변경을 수행하는 데 사용된 자격 증명 집합을 추적하는 것이 어려울 수 있습니다.

이 모범 사례 문서에서는 클러스터 운영자가 AKS 클러스터의 액세스 및 ID를 관리하는 방법을 중점적으로 설명합니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure Active Directory를 사용하여 AKS 클러스터 사용자를 인증합니다.
> * Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 사용하여 리소스에 대한 액세스를 제어합니다.
> * Azure RBAC를 사용하여 AKS 리소스, 대규모 Kubernetes API 및 `kubeconfig`에 대한 액세스를 세부적으로 제어합니다.
> * 관리 ID를 사용하여 다른 서비스와 함께 Pod를 인증합니다.

## <a name="use-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory) 사용

> **모범 사례 지침** 
> 
> Azure AD 통합으로 AKS 클러스터를 배포합니다. Azure AD를 사용하면 ID 관리 구성 요소가 중앙 집중화됩니다. 사용자 계정 또는 그룹 상태의 변경 내용은 AKS 클러스터에 액세스할 때 자동으로 업데이트됩니다. [Roles, ClusterRoles 또는 Bindings](#use-kubernetes-role-based-access-control-kubernetes-rbac)을 사용하여 사용자 또는 그룹의 권한을 최소한으로 지정합니다.

Kubernetes 클러스터 개발자 및 애플리케이션 소유자는 다양한 다른 리소스에 액세스해야 합니다. Kubernetes에는 사용자가 상호 작용할 수 있는 리소스를 제어할 수 있는 ID 관리 솔루션이 없습니다. 대신 일반적으로 클러스터를 기존 ID 솔루션과 통합합니다. AKS 클러스터와 통합되는 엔터프라이즈급 ID 관리 솔루션인 Azure AD를 시작합니다.

AKS의 Azure AD 통합형 클러스터를 사용하여 리소스에 대한 액세스 권한을 정의하는 *Role* 또는 *ClusterRoles* 을 만듭니다. 그런 다음, 역할을 Azure AD의 사용자 또는 그룹에 *바인딩* 합니다. [다음 섹션](#use-kubernetes-role-based-access-control-kubernetes-rbac)에서 이러한 Kubernetes RBAC에 대해 자세히 알아보세요. Azure AD 통합 및 리소스에 대한 액세스를 제어하는 방법은 다음 다이어그램에서 볼 수 있습니다.

![AKS와 Azure Active Directory 통합을 위한 클러스터 수준 인증](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 개발자는 Azure AD로 인증합니다.
1. Azure AD 토큰 발급 엔드포인트가 액세스 토큰을 발급합니다.
1. 개발자는 `kubectl create pod`와 같은 Azure AD 토큰을 사용하여 작업을 수행합니다.
1. Kubernetes는 Azure AD를 사용하여 토큰의 유효성을 검사하고 개발자의 그룹 멤버 자격을 가져옵니다.
1. Kubernetes RBAC 및 클러스터 정책이 적용됩니다.
1. 개발자 요청은 Azure AD 그룹 멤버 자격 및 Kubernetes RBAC/정책의 이전 유효성 검사를 기반으로 성공적으로 이루어졌습니다.

Azure AD를 사용하는 AKS 클러스터를 만들려면 [AKS와 Azure Active Directory 통합][aks-aad]을 참조하세요.

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어) 사용

> **모범 사례 지침**
> 
> Kubernetes RBAC를 사용하여 클러스터 리소스에 대한 사용자 또는 그룹 권한을 정의합니다. 필요한 최소 권한을 할당하는 역할 및 바인딩을 만듭니다. Azure AD와 통합하여 사용자 상태 또는 그룹 멤버 자격 변경을 자동으로 업데이트하고 클러스터 리소스에 대한 액세스를 최신 상태로 유지합니다.

Kubernetes에서는 클러스터 리소스에 대한 세분화된 액세스 제어를 제공합니다. 클러스터 수준 또는 특정 네임스페이스에 대한 권한을 정의합니다. 관리할 수 있는 리소스 및 관리에 필요한 권한을 정의합니다. 그런 다음, 바인딩을 사용하여 이러한 역할을 사용자 또는 그룹에 적용합니다. *Role*, *ClusterRoles* 및 *Binding* 에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 액세스 및 ID 옵션][aks-concepts-identity]을 참조하세요.

예를 들어, 다음 예제 YAML 매니페스트와 같이 *finance-app* 이라는 네임스페이스에서 리소스에 대한 전체 액세스 권한을 부여하는 역할을 만듭니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

그런 다음, RoleBinding을 만들고 다음 YAML 매니페스트에 표시된 대로 Azure AD 사용자 *developer1\@contoso.com* 을 RoleBinding에 바인딩합니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

*developer1\@contoso.com* 이 AKS 클러스터에서 인증되면 *finance-app* 네임스페이스의 리소스에 대한 전체 권한이 할당됩니다. 이 방식으로 리소스에 대한 액세스를 논리적으로 분리하고 제어합니다. Azure AD 통합과 함께 Kubernetes RBAC를 사용합니다.

Azure AD 그룹을 사용하여 Kubernetes RBAC를 사용하는 Kubernetes 리소스에 액세스를 제어하는 방법을 보려면 [AKS에서 역할 기반 액세스 제어 및 Azure Active Directory ID를 사용하는 클러스터 리소스에 대한 액세스 제어][azure-ad-rbac]를 참조하세요.

## <a name="use-azure-rbac"></a>Azure RBAC 사용 
> **모범 사례 지침** 
> 
> Azure RBAC를 사용하여 하나 이상의 구독에서 AKS 리소스에 대해 필요한 최소 사용자 및 그룹 권한을 정의합니다.

AKS 클러스터를 완전히 운영하려면 다음과 같이 두 가지 수준의 액세스가 필요합니다. 
1. Azure 구독에서 AKS 리소스에 액세스. 

    이 액세스 수준을 사용하면 다음을 수행할 수 있습니다.
      * AKS API를 사용하여 클러스터 크기 조정 또는 업그레이드 제어
      * `kubeconfig`를 끌어옵니다.

    AKS 리소스 및 `kubeconfig`에 대한 액세스를 제어하는 방법은 [클러스터 구성 파일에 대한 액세스 제한](control-kubeconfig-access.md)을 참조하세요.

2. Kubernetes API에 대한 액세스. 
    
    이 액세스 수준은 다음에 의해 제어됩니다.
    * [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac)(일반적) 또는 
    * kubernetes 권한 부여를 위해 Azure RBAC와 AKS 통합.

    Azure RBAC를 사용하여 Kubernetes API에 권한을 세부적으로 부여하는 방법은 [Kubernetes 인증에 Azure RBAC 사용](manage-azure-rbac.md)을 참조하세요.

## <a name="use-pod-managed-identities"></a>Pod 관리 ID 사용

> **모범 사례 지침** 
> 
> 고정된 자격 증명은 노출이나 남용의 위험이 있으므로 Pod 또는 컨테이너 이미지 내에서 사용하지 마세요. 대신 *Pod ID* 를 사용하여 중앙 Azure AD ID 솔루션을 통해 액세스를 자동으로 요청합니다. 

> [!NOTE]
> Pod ID는 Linux Pod 및 컨테이너 이미지에만 사용됩니다. Windows 컨테이너에 대한 Pod 관리 ID 지원이 곧 제공될 예정입니다.

Cosmos DB, Key Vault 또는 Blob Storage와 같은 다른 Azure 서비스에 액세스하려면 Pod에 액세스 자격 증명이 필요합니다. 컨테이너 이미지로 액세스 자격 증명을 정의하거나 Kubernetes 비밀로 삽입할 수 있습니다. 어느 쪽이든 수동으로 만들고 할당해야 합니다. 일반적으로 이러한 자격 증명은 Pod 전체에 다시 사용되며 정기적으로 교체되지 않습니다.

Azure 리소스의 Pod 관리 ID를 사용하면 Azure AD를 통해 서비스에 대한 액세스를 자동으로 요청할 수 있습니다. Pod 관리 ID는 현재 AKS에 대해 미리 보기로 제공됩니다. 시작하려면 [Azure Kubernetes Service에서 Azure Active Directory Pod 관리 ID 사용(미리 보기)](./use-azure-ad-pod-identity.md) 설명서를 참조하세요. 

Azure Active Directory Pod ID는 두 가지 작업 모드를 지원합니다.

1. 표준 모드: 이 모드에서는 다음 두 가지 구성 요소가 AKS 클러스터에 배포됩니다. 
    * [MIC(Managed Identity Controller)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/): Kubernetes API Server를 통해 Pod, [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) 및 [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/)의 변경을 감시하는 Kubernetes 컨트롤러입니다. 관련 변경 내용이 검색되면 MIC는 필요에 따라 [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/)를 추가하거나 삭제합니다. 특히 Pod가 예약되면 MIC는 만들기 단계 중 노드 풀에서 사용하는 기본 VMSS에 Azure의 관리 ID를 할당합니다. ID를 사용하는 모든 Pod가 삭제되면 동일한 관리 ID가 다른 Pod에서 사용되지 않는 한 노드 풀의 VMSS에서 ID를 제거합니다. MIC는 AzureIdentity 또는 AzureIdentityBinding을 만들거나 삭제할 때도 유사한 작업을 수행합니다.
    * [NMI(Node Managed Identity)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/): AKS 클러스터의 각 노드에서 DaemonSet로 실행되는 Pod입니다. NMI는 각 노드에서 [Azure Instance Metadata Service](../virtual-machines/linux/instance-metadata-service.md?tabs=linux)에 대한 보안 토큰 요청을 가로채고, 해당 요청을 자신에게 리디렉션하고, Pod가 토큰을 요청하는 ID에 액세스하고 애플리케이션을 대신하여 Azure Active Directory 테넌트에서 토큰을 가져올 수 있는지 확인합니다.
2. 관리형 모드: 이 모드에는 NMI만 있습니다. 사용자가 ID를 수동으로 할당하고 관리해야 합니다. 자세한 내용은 [Pod Identity in Managed Mode](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/)(관리형 모드의 Pod ID)를 참조하세요. 이 모드에서 [az aks pod-identity add](/cli/azure/aks/pod-identity?view=azure-cli-latest#az_aks_pod_identity_add) 명령을 사용하여 Azure Kubernetes Service(AKS) 클러스터에 Pod ID를 추가하면 `--namespace` 매개 변수로 지정된 네임스페이스에 [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) 및 [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/)을 만들고 AKS 리소스 공급자는 `--identity-resource-id` 매개 변수로 지정된 관리 ID를 AKS 클러스터에서 각 노드 풀의 VMSS(가상 머신 확장 집합)에 할당합니다.

> [!NOTE]
> 대신 [AKS 클러스터 추가 기능](./use-azure-ad-pod-identity.md)을 사용하여 Azure Active Directory Pod ID를 설치하기로 결정하면 설치 프로그램에서 `managed` 모드를 사용합니다.

`managed` 모드는 `standard` 모드에 비해 다음과 같은 이점이 있습니다.

1. 노드 풀의 VMSS에서 ID를 할당하려면 40~60초 걸릴 수 있습니다. ID에 대한 액세스 권한이 필요하고 할당 지연을 허용할 수 없는 애플리케이션이나 cronjobs의 경우 ID가 수동으로 또는 [az aks pod-identity add](/cli/azure/aks/pod-identity?view=azure-cli-latest#az_aks_pod_identity_add) 명령을 통해 노드 풀의 VMSS에 미리 할당되는 `managed` 모드를 사용하는 것이 가장 좋습니다.
2. `standard` 모드에서 MIC에는 AKS 클러스터에서 사용하는 VMSS에 대한 쓰기 권한과 사용자가 할당한 관리 ID에 대한 `Managed Identity Operator` 권한이 필요합니다. `managed mode`에서 실행되면 MIC가 없기 때문에 역할 할당이 필요하지 않습니다.

Pod에 대한 자격 증명을 수동으로 정의하는 대신 Pod 관리 ID는 실시간으로 액세스 토큰을 요청하여 할당된 서비스에만 액세스합니다. AKS에는 Pod가 관리 ID를 사용할 수 있도록 작업을 처리하는 두 가지 구성 요소가 있습니다.

* **NMI(노드 관리 ID) 서버** 는 AKS 클러스터의 각 노드에서 DaemonSet으로 실행되는 Pod입니다. NMI 서버는 Azure 서비스에 대한 Pod 요청을 수신 대기합니다.
* **Azure 리소스 공급자** 는 Kubernetes API 서버를 쿼리하고 Pod에 해당하는 Azure ID 매핑을 확인합니다.

Pod가 Azure 서비스에 액세스하기 위해 Azure Active Directory에서 보안 토큰을 요청하면 네트워크 규칙은 트래픽을 NMI 서버로 리디렉션합니다. 
1. NMI 서버:
    * 원격 주소를 기반으로 Azure 서비스에 대한 액세스를 요청하는 Pod를 식별합니다.
    * Azure 리소스 공급자를 쿼리합니다. 
1. Azure 리소스 공급자는 AKS 클러스터에서 Azure ID 매핑을 확인합니다.
1. NMI 서버는 Pod의 ID 매핑을 기반으로 Azure AD에서 액세스 토큰을 요청합니다. 
1. Azure AD는 Pod에 반환되는 NMI 서버에 대한 액세스를 제공합니다. 
    * 이 액세스 토큰은 이후 Pod가 Azure의 서비스에 대한 액세스를 요청하는 데 사용할 수 있습니다.

다음 예제에서 개발자는 관리 ID를 사용하여 Azure SQL Database에 대한 액세스를 요청하는 Pod를 만듭니다.

![Pod ID를 사용하면 Pod가 다른 서비스에 대한 액세스를 자동으로 요청할 수 있습니다.](media/operator-best-practices-identity/pod-identities.png)

1. 클러스터 운영자는 Pod가 서비스에 대한 액세스를 요청할 때 ID를 매핑하는 서비스 계정을 만듭니다.
1. NMI 서버는 Azure AD에 대한 액세스 토큰에 대해 Azure 리소스 공급자와 함께 모든 Pod 요청을 릴레이하기 위해 배포됩니다.
1. 개발자는 NMI 서버를 통해 액세스 토큰을 요청하는 관리형 ID를 사용하여 Pod를 배포합니다.
1. 토큰은 Pod에 반환되고 Azure SQL Database에 액세스하는 데 사용됩니다.

> [!NOTE]
> Pod 관리 ID는 현재 미리 보기 상태로 제공됩니다.

Pod 관리 ID를 사용하려면 [Azure Kubernetes Service 에서 Azure Active Directory Pod 관리 ID 사용(미리 보기)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 모범 사례 문서에서는 클러스터 및 리소스의 인증 및 권한 부여를 중점적으로 설명했습니다. 이러한 일부 모범 사례를 구현하려면 다음 문서를 참조하세요.

* [AKS와 Azure Active Directory 통합][aks-aad]
* [Azure Kubernetes Service에서 Azure Active Directory Pod 관리 ID 사용(미리 보기)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [다중 테넌트 지원 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]
* [고급 Kubernetes 스케줄러 기능][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
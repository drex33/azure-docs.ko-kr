---
title: 개념 - AKS(Azure Kubernetes Service)의 액세스 및 ID
description: Azure Active Directory 통합, Kubernetes 역할 기반 액세스 제어(Kubernetes RBAC), Kubernetes 역할 및 바인딩을 포함하여 AKS(Azure Kubernetes Service)의 액세스 및 ID에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 13fe269431a84a00a8af073849cbd17d188c5175
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535573"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 액세스 및 ID 옵션

다양한 방법으로 Kubernetes 클러스터에 대한 액세스를 인증하고, 권한을 부여하고, 보호하고, 제어할 수 있습니다. 
* Kubernetes역할 기반 액세스 제어(Kubernetes RBAC)를 사용하여 사용자, 그룹 및 서비스 계정에 필요한 리소스에만 액세스하는 권한을 부여할 수 있습니다. 
* AKS(Azure Kubernetes Service)를 사용하여 Azure Active Directory 및 Azure RBAC를 통해 보안 및 사용 권한 구조를 추가로 향상시킬 수 있습니다. 

Kubernetes RBAC 및 AKS를 사용하면 클러스터 액세스를 보호하고 개발자 및 운영자에게 필요한 최소 권한만 제공할 수 있습니다.

이 문서에서는 AKS에서 권한을 인증하고 할당하는 데 도움이 되는 핵심 개념을 소개합니다.

## <a name="aks-service-permissions"></a>AKS 서비스 권한

클러스터를 만들 때 AKS는 사용자를 대신하여 클러스터를 만들고 실행하는 데 필요한 리소스(예: VM 및 NIC)를 생성하거나 수정합니다. 이 ID는 클러스터를 만드는 동안 만들어지는 클러스터의 ID 사용 권한과는 다릅니다.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>ID 생성 및 클러스터 권한 운영

클러스터를 만들고 운영하는 ID에는 다음 사용 권한이 필요합니다.

| 사용 권한 | 이유 |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | 디스크 암호화 집합 ID를 읽는 데 필요. |
| `Microsoft.Compute/proximityPlacementGroups/write` | 근접 배치 그룹을 업데이트하는 데 필요. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | 애플리케이션 게이트웨이를 구성하고 서브넷에 가입하는 데 필요. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | 사용자 지정 VNET을 사용하는 경우 서브넷에 대한 네트워크 보안 그룹을 구성하는 데 필요.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | 표준 Load Balancer에서 아웃바운드 공용 IP를 구성하는 데 필요. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Log Analytics 작업 영역 생성 및 업데이트, 컨테이너에 대한 Azure 모니터링을 실행하는 데 필요. |

### <a name="aks-cluster-identity-permissions"></a>AKS 클러스터 ID 권한

AKS 클러스터 ID에서 사용되는 사용 권한은 AKS 클러스터에 만들어지고 연결됩니다. 각 사용 권한은 아래와 같은 이유로 사용됩니다:

| 사용 권한 | 이유 |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | 사용자를 생성하고 클러스터를 운영하는 데 필요.
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | LoadBalancer 서비스에 대한 부하 분산 장치를 구성하는 데 필요. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | LoadBalancer 서비스의 공용 IP를 찾고 구성하는 데 필요. |
| `Microsoft.Network/publicIPAddresses/join/action` | LoadBalancer 서비스의 공용 IP를 구성하는 데 필요. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | LoadBalancer 서비스에 대한 보안 규칙을 생성하거나 삭제하는 데 필요. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | AzureDisks를 구성하는 데 필요. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | AzureFile 또는 AzureDisk에 대한 스토리지 계정을 구성하는 데 필요. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | 노드에 대한 경로 테이블 및 경로를 구성하는 데 필요. |
| `Microsoft.Compute/virtualMachines/read` | VMAS에서 영역, 장애 도메인, 크기 및 데이터 디스크와 같은 가상 컴퓨터에 대한 정보를 찾는 데 필요. |
| `Microsoft.Compute/virtualMachines/write` | AzureDisks를 VMAS의 가상 머신에 연결하는 데 필요. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | 가상 머신 확장 집합에서 영역, 장애 도메인, 크기 및 데이터 디스크와 같은 가상 컴퓨터에 대한 정보를 찾는 데 필요. |
| `Microsoft.Network/networkInterfaces/write` | VMAS에서 가상 컴퓨터를 부하 분산 장치 백 엔드 주소 풀에 추가하는 데 필요. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | 부하 분산 장치 백 엔드 주소 풀에 가상 머신 확장 집합을 추가하고, 가상 머신 확장 집합에서 노드를 확장하는 데 필요. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | AzureDisks를 연결하고 가상 머신 확장 집합의 가상 머신을 부하 분산 장치에 추가하는 데 필요. |
| `Microsoft.Network/networkInterfaces/read` | VMAS의 가상 컴퓨터에 대한 내부 IP 및 부하 분산 장치 백 엔드 주소 풀을 검색하는 데 필요. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | 가상 머신 확장 집합의 가상 컴퓨터에 대한 내부 IP 및 부하 분산 장치 백 엔드 주소 풀을 검색하는 데 필요. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | 가상 컴퓨터 확장 집합의 가상 컴퓨터에 대한 공용 IP를 찾는 데 필요. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | 다른 리소스 그룹의 내부 부하 분산 장치에 대한 서브넷이 존재하는지 확인하는 데 필요. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | AzureDisk의 스냅샷을 구성하는 데 필요. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | AzureDisk 볼륨 제한을 찾기 위한 가상 머신 크기를 찾는 데 필요. |

### <a name="additional-cluster-identity-permissions"></a>추가적인 클러스터 ID 권한

특정 특성을 사용하여 클러스터를 만드는 경우 클러스터 ID에 대해 다음과 같은 추가 권한이 필요합니다. 이러한 사용 권한은 자동으로 할당되지 않으므로 클러스터 ID를 만든 후에 추가해야 합니다.

| 사용 권한 | 이유 |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | 다른 리소스 그룹에서 네트워크 보안 그룹을 사용하는 경우 필요. LoadBalancer 서비스에 대한 보안 규칙을 구성하는 데 필요. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | 사용자 지정 VNET과 같은 다른 리소스 그룹의 서브넷을 사용하는 경우 필요. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | 사용자 지정 경로 테이블이 있는 사용자 지정 VNET과 같은 다른 리소스 그룹의 경로 테이블과 연결된 서브넷을 사용하는 경우 필요. 다른 리소스 그룹의 서브넷에 대한 서브넷이 이미 존재하는지 확인하는 데 필요. |
| `Microsoft.Network/virtualNetworks/subnets/read` | 다른 리소스 그룹에서 내부 부하 분산 장치를 사용하는 경우 필요. 다른 리소스 그룹의 서브넷에 대한 내부 부하 분산 장치가 이미 존재하는지 확인하는 데 필요. |
| `Microsoft.Network/privatednszones/*` | 사용자 지정 privateDNSZone 같은 다른 리소스 그룹에서 프라이빗 DNS 영역을 사용하는 경우 필요. |

## <a name="aks-node-access"></a>AKS 노드 액세스

기본적으로 AKS에는 노드 액세스가 필요하지 않습니다.  특정 구성 요소를 활용하는 경우 노드에 대해 다음 액세스가 필요합니다.

| Access | 이유 |
|---|---|
| `kubelet` | 고객이 MSI에 ACR에 대한 액세스 권한을 부여하는 데 필요합니다. |
| `http app routing` | "random name".aksapp.io에 대한 쓰기 권한에 필요합니다. |
| `container insights` | 고객이 Log Analytics 작업 영역에 대한 권한을 부여하는 데 필요합니다. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC는 사용자 작업에 대한 세부적인 필터링을 제공합니다. 이 제어 메커니즘 사용:
* 사용자 또는 사용자 그룹 권한을 할당하여 리소스를 생성 및 수정하거나 실행 중인 애플리케이션 워크로드에서 로그를 볼 수 있습니다. 
* 단일 네임스페이스 또는 전체 AKS 클러스터에 대한 권한 범위를 지정할 수 있습니다. 
* *역할* 을 만들어 권한을 정의한 다음 *역할 바인딩* 을 사용하여 해당 역할을 사용자에게 할당합니다.

자세한 내용은 [Kubernetes RBAC 권한 부여 사용][kubernetes-rbac]을 참조하세요.

### <a name="roles-and-clusterroles"></a>Roles 및 ClusterRoles

#### <a name="roles"></a>역할
Kubernetes RBAC를 사용하여 사용자에게 권한을 할당하기 전에 사용자 권한을 *역할* 로 정의합니다. 역할을 사용하여 네임스페이스 내에서 사용 권한을 부여합니다. 

> [!NOTE]
> Kubernetes 역할은 권한을 *부여* 합니다. 권한을 *거부* 하지 않습니다.

전체 클러스터 또는 지정된 네임스페이스 외부의 클러스터 리소스에 권한을 부여하기 위해 *ClusterRoles* 를 대신 사용할 수 있습니다.

#### <a name="clusterroles"></a>ClusterRoles

ClusterRole은 특정 네임스페이스가 아니라 전체 클러스터에서 리소스에 권한을 부여하고 적용합니다.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings 및 ClusterRoleBindings

리소스에 권한을 부여하는 역할이 정의되면 *RoleBinding* 을 사용하여 해당 Kubernetes RBAC 권한을 할당합니다. AKS 클러스터가 [Azure AD(Azure Active Directory)를 통합](#azure-ad-integration)하는 경우 RoleBindings는 클러스터 내에서 작업을 수행할 수 있는 권한을 Azure AD 사용자에게 부여합니다. [Kubernetes 역할 기반 액세스 제어 및 Azure Active Directory ID를 사용하여 클러스터 리소스에 대한 액세스 제어](azure-ad-rbac.md)에서 방법을 참조하세요.

#### <a name="rolebindings"></a>RoleBindings

RoleBindings를 사용하여 지정된 네임스페이스에 대한 사용자에게 역할을 할당합니다. RoleBindings를 사용하면 단일 AKS 클러스터를 논리적으로 구분할 수 있으므로 사용자가 할당된 네임스페이스의 애플리케이션 리소스에 액세스할 수 있습니다. 

전체 클러스터 또는 네임스페이스 외부의 리소스에 역할을 바인딩하기 위해 *ClusterRoleBindings* 를 대신 사용할 수 있습니다.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

ClusterRoleBinding을 사용하면 사용자에게 역할을 바인딩하고 특정 네임스페이스가 아니라 전체 클러스터의 리소스에 적용할 수 있습니다. 이 방법을 통해 관리자 또는 지원 엔지니어에게 AKS 클러스터의 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다.


> [!NOTE]
> Microsoft/AKS는 기본 제공 Kubernetes 역할 `aks-service` 및 기본 제공 역할 바인딩 `aks-service-rolebinding`에 따라 사용자 동의하에 모든 클러스터 조치를 수행합니다. 
> 
> 이 역할을 통해 AKS는 클러스터 문제를 해결하고 진단할 수 있지만, 권한을 수정하거나 역할 또는 역할 바인딩, 혹은 기타 높은 권한 작업을 생성할 수 없습니다. 역할 액세스는 Just-In-Time(JIT) 액세스를 사용하는 활성 지원 티켓에서만 허용됩니다. [AKS 지원 정책](support-policies.md)에 대해 자세히 알아보세요.


### <a name="kubernetes-service-accounts"></a>Kubernetes 서비스 계정

*서비스 계정* 은 Kubernetes의 기본 사용자 유형 중 하나입니다. Kubernetes API는 서비스 계정을 보유하고 관리합니다. 서비스 계정 자격 증명은 Kubernetes 비밀로 저장되어 권한 있는 Pod에서 API 서버와 통신하는 데 사용할 수 있습니다. 대부분의 API 요청은 서비스 계정 또는 일반 사용자 계정에 대한 인증 토큰을 제공합니다.

일반 사용자 계정은 서비스 및 프로세스뿐만 아니라 사용자 관리자 또는 개발자에게도 더 일반적인 액세스를 허용합니다. Kubernetes는 일반 사용자 계정 및 암호를 저장하는 ID 관리 솔루션을 제공하지 않지만 외부 ID 솔루션을 Kubernetes에 통합할 수 있습니다. AKS 클러스터의 경우 이 통합 ID 솔루션은 Azure AD입니다.

Kubernetes의 ID 옵션에 대한 자세한 내용은 [Kubernetes 인증][kubernetes-authentication]을 참조하세요.

## <a name="azure-ad-integration"></a>Azure AD 통합

Azure AD 통합을 사용하여 AKS 클러스터 보안을 향상시킵니다. 수십 년간의 엔터프라이즈 ID 관리를 기반으로 하여 구축된 Azure AD는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호를 결합한 다중 테넌트, 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD를 사용하면 온-프레미스 ID를 AKS 클러스터에 통합하여 계정 관리 및 보안을 위한 단일 원본을 제공할 수 있습니다.

![AKS 클러스터와 Azure Active Directory 통합](media/concepts-identity/aad-integration.png)

Azure AD 통합 AKS 클러스터를 사용하면 네임스페이스 내에서 또는 클러스터 전체에서 Kubernetes 리소스에 대한 액세스 권한을 사용자 또는 그룹에 부여할 수 있습니다. 

1. `kubectl` 구성 컨텍스트를 얻으려면 사용자는 [az aks get-credentials][az-aks-get-credentials] 명령을 실행합니다. 
1. 사용자가 `kubectl`을 사용하여 AKS 클러스터와 상호 작용할 때 Azure AD 자격 증명으로 로그인하라는 메시지가 표시됩니다. 

이 방법은 사용자 계정 관리 및 암호 자격 증명을 위한 단일 원본을 제공합니다. 사용자는 클러스터 관리자가 정의한 리소스에만 액세스할 수 있습니다.

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][openid-connect]를 참조하세요. Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 [Webhook 토큰 인증이][webhook-token-docs] 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

### <a name="webhook-and-api-server"></a>Webhook 및 API 서버

![Webhook 및 API 서버 인증 흐름](media/concepts-identity/auth-flow.png)

위의 그래프에 표시된 것처럼 API 서버는 AKS webhook 서버를 호출하고 다음 단계를 수행합니다.

1. `kubectl`은 [OAuth 2.0 디바이스 권한 부여 흐름](../active-directory/develop/v2-oauth2-device-code.md)으로 사용자가 로그인할 수 있도록 Azure AD 클라이언트 애플리케이션을 사용합니다.
2. Azure AD는 access_token, id_token 및 refresh_token를 제공합니다.
3. 사용자가 `kubeconfig`의 access_token을 사용하여 `kubectl`에 요청합니다.
4. `kubectl`는 access_token을 API 서버로 보냅니다.
5. API 서버는 유효성 검사를 수행하기 위해 Auth WebHook 서버를 사용하여 구성됩니다.
6. 인증 웹후크 서버는 Azure AD 공개 서명 키를 확인하여 JSON Web Token 서명이 유효한지 확인합니다.
7. 서버 애플리케이션은 사용자 제공 자격 증명을 사용하여 MS Graph API에서 로그인한 사용자의 그룹 멤버 자격을 쿼리합니다.
8. 응답은 액세스 토큰의 사용자 계정 이름(UPN) 클레임 및 개체 ID를 기반으로 하는 사용자의 그룹 멤버 자격과 같은 사용자 정보와 함께 API 서버에 전송됩니다.
9. API는 Kubernetes Role/RoleBinding에 따라 권한 부여 결정을 수행합니다.
10. 권한이 부여되면 API 서버는 `kubectl`에 대한 응답을 반환합니다.
11. `kubectl`은 사용자에게 피드백을 제공합니다.
 
[AKS 관리 Azure AD 통합 방법 가이드](managed-aad.md)를 통해 AKS를 Azure AD와 통합하는 방법을 알아봅니다.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

Azure RBAC(역할 기반 액세스 제어)는 Azure 리소스의 세밀한 액세스를 관리하는 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반의 권한 부여 시스템입니다.

| RBAC 시스템 | 설명 |
|---|---|
| Kubernetes RBAC | AKS 클러스터 내에서 Kubernetes 리소스에 대해 작동하도록 설계되었습니다. |
| Azure RBAC | Azure 구독 내에서 리소스를 사용하도록 설계되었습니다. |

Azure RBAC를 사용하여 적용할 권한을 설명하는 *역할 정의* 를 만듭니다. 그런 다음 특정 *범위* 에 대한 *역할 할당* 을 통해 이 역할 정의를 사용자 또는 그룹에 할당합니다. 범위는 개별 리소스, 리소스 그룹 또는 구독에 걸쳐 있을 수 있습니다.

자세한 내용은 [Azure 역할 기반 액세스 제어(Azure RBAC)란?][azure-rbac]을 참조하세요.

AKS 클러스터를 완전히 운영하려면 다음과 같이 두 가지 수준의 액세스가 필요합니다. 
* [Azure 구독에서 AKS 리소스에 액세스](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * AKS API를 사용하여 클러스터 크기 조정 또는 업그레이드 제어
  * `kubeconfig`를 끌어옵니다.
* Kubernetes API에 대한 액세스. 이 액세스는 다음과 같은 방법으로 제어됩니다.
  * [Kubernetes RBAC](#kubernetes-rbac)(기존).
  * [Kubernetes 인증을 위해 Azure RBAC를 AKS와 통합](#azure-rbac-for-kubernetes-authorization).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC로 AKS 리소스에 대한 액세스 권한 부여

Azure RBAC를 사용하면 하나 이상의 구독에서 AKS 리소스에 대한 세분화된 액세스 권한을 사용자에게 제공할 수 있습니다. 예를 들어 [Azure Kubernetes Service 기여자 역할](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)을 사용하여 클러스터를 확장하고 업그레이드할 수 있습니다. [Azure Kubernetes Service 클러스터 관리자 역할](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)이 있는 다른 사용자에게는 관리자 `kubeconfig`를 가져올 수 있는 권한만 있습니다.

또는 사용자에게 일반 [기여자](../role-based-access-control/built-in-roles.md#contributor) 역할을 부여할 수 있습니다. 일반 참가자 역할을 사용하여 사용자는 권한 관리를 제외하고 위의 사용 권한 및 AKS 리소스에서 가능한 모든 작업을 수행할 수 있습니다.

[Azure RBAC를 사용하여 AKS의 Kubernetes 구성 파일에 대한 액세스를 정의합니다](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization"></a>Kubernetes 권한 부여에 Azure RBAC

Azure RBAC 통합을 사용하면 AKS는 Kubernetes 권한 부여 webhook 서버를 사용하므로 Azure 역할 정의 및 역할 할당을 사용하여 Azure AD 통합 Kubernetes 클러스터 리소스 권한 및 할당을 관리할 수 있습니다.

![Kubernetes 권한 부여 흐름을 위한 Azure RBAC](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

위의 다이어그램에 표시된 대로 Azure RBAC 통합을 사용할 때 Kubernetes API에 대한 모든 요청은 [Azure Active Directory 통합 섹션](#azure-ad-integration)에 설명된 것과 동일한 인증 흐름을 따릅니다. 

요청을 만드는 ID가 Azure AD에 있는 경우 Azure는 Kubernetes RBAC를 사용하여 요청에 권한을 부여합니다. ID가 Azure AD 외부(즉, Kubernetes 서비스 계정)에 있는 경우 권한 부여는 정상적인 Kubernetes RBAC에 의해 발생합니다.

이 시나리오에서는 Kubernetes 역할을 사용하는 것처럼 Azure RBAC 메커니즘과 API를 사용하여 사용자에게 기본 제공 역할을 할당하거나 사용자 지정 역할을 만듭니다. 

이 기능을 사용하면 구독 간에 AKS 리소스에 대한 사용자 권한을 부여할 수 있을 뿐만 아니라 Kubernetes API 액세스를 제어하는 각 클러스터 내에 역할 및 권한도 구성할 수 있습니다. 예를 들어, 구독 범위에서 `Azure Kubernetes Service RBAC Viewer` 역할을 부여할 수 있습니다. 역할 수신자가 모든 클러스터에서 모든 Kubernetes 개체를 수정하지 않고 나열하고 가져올 수 있습니다.

> [!IMPORTANT]
> 이 기능을 사용하기 전에 Kubernetes 권한 부여에 대한 Azure RBAC를 사용하도록 설정해야 합니다. 자세한 내용과 단계별 지침은 [Kubernetes 인증에 Azure RBAC 사용](manage-azure-rbac.md) 방법 가이드를 따르세요.

#### <a name="built-in-roles"></a>기본 제공 역할

AKS에는 다음의 4가지 기본 제공 역할이 있습니다. [Kubernetes 기본 제공 역할](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles)과 유사하지만 CRD 지원과 같은 몇 가지 차이점이 있습니다. 각 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)에서 허용되는 전체 작업 목록을 참조하세요.

| 역할                                | Description  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC 뷰어  | 네임스페이스에 있는 대부분의 개체를 볼 수 있는 읽기 전용 권한을 허용합니다. <br> 역할 또는 역할 바인딩을 볼 수 없습니다.<br> `Secrets` 보기를 허용하지 않습니다. `Secrets` 콘텐츠를 읽으면 네임스페이스의 `ServiceAccount` 자격 증명에 액세스할 수 있으며, 이는 네임스페이스(권한 에스컬레이션의 형태)에서 모든 `ServiceAccount`로 API 액세스를 허용합니다.  |
| Azure Kubernetes Service RBAC 쓰기 권한자 | 네임스페이스의 대부분의 개체에 대한 읽기/쓰기 권한을 허용합니다. <br> 역할 또는 역할 바인딩을 보거나 수정할 수 없습니다. <br> `Secrets`에 액세스하고 네임스페이스의 모든 ServiceAccount로 Pod를 실행할 수 있으므로 네임스페이스에 있는 모든 ServiceAccount의 API 액세스 수준을 얻는 데 사용할 수 있습니다. |
| Azure Kubernetes Service RBAC 관리자  | 네임 스페이스 내에서 부여되는 관리자 액세스를 허용합니다. <br> 네임스페이스(또는 클러스터 범위)에 있는 대부분의 리소스에 대한 읽기/쓰기 권한을 허용하며, 여기에는 네임스페이스 내에서 역할 및 역할 바인딩을 만들 수 있는 권한이 포함됩니다. <br> 리소스 할당량 또는 네임스페이스 자체에 대한 쓰기 액세스를 허용하지 않습니다. |
| Azure Kubernetes Service RBAC 클러스터 관리자  | 슈퍼 사용자 액세스를 허용하여 모든 리소스에 대한 모든 작업을 수행할 수 있습니다. <br> 클러스터 및 모든 네임스페이스의 모든 리소스에 대한 모든 권한을 제공합니다. |


## <a name="summary"></a>요약

Azure AD 통합을 사용하는 경우 사용자가 Kubernetes에 인증할 수 있는 방법에 대한 간략한 요약 정보를 보려면 표를 참조하세요. 모든 경우에 사용자의 명령 시퀀스는 다음과 같습니다.
1. `az login`을 실행하여 Azure에 대해 인증합니다.
1. `az aks get-credentials`을 실행하여 클러스터에 대한 자격 증명을 `.kube/config`로 다운로드합니다.
1. `kubectl` 명령을 실행합니다. 
   * 첫 번째 명령은 다음 표에 설명된 대로 브라우저 기반 인증을 트리거하여 클러스터에 인증할 수 있습니다.

Azure Portal에서 다음을 찾을 수 있습니다.
* 두 번째 열에 언급된 *역할 부여*(Azure RBAC 역할 부여)는 **액세스 제어** 탭에 표시됩니다. 
* 클러스터 관리자 Azure AD 그룹은 **구성** 탭에 표시됩니다.
  * 또한 Azure CLI에서 매개 변수 이름 `--aad-admin-group-object-ids`가 있습니다.


| Description        | 역할 부여 필요| 클러스터 관리 Azure AD 그룹 | 사용 시기 |
| -------------------|------------|----------------------------|-------------|
| 클라이언트 인증서를 사용하는 레거시 관리자 로그인| **Azure Arc Kubernetes 관리자 역할**. 이 역할을 통해 `az aks get-credentials`을 `--admin` 플래그와 함께 사용할 수 있으며, [ 레거시(non-Azure AD) 클러스터 관리 인증서](control-kubeconfig-access.md)를 사용자의 `.kube/config`에 다운로드합니다. "Azure Kubernetes 관리자 역할"의 유일한 목적입니다.|해당 없음|클러스터에 대한 액세스 권한이 있는 유효한 Azure AD 그룹에 대한 액세스 권한이 없는 사용자가 영구적으로 차단되는 경우| 
| 수동 (클러스터) RoleBindings를 사용하는 Azure AD| **Azure Kubernetes 클러스터 사용자 역할**. "User" 역할을 사용하면 `--admin`플래그 없이 `az aks get-credentials`을 사용할 수 있습니다. "Azure Kubernetes 사용자 역할"의 유일한 목적입니다.) Azure AD를 사용하는 클러스터에 대한 결과는 `.kube/config`에 [빈 항목](control-kubeconfig-access.md)을 다운로드하는 것으로,이 항목은 `kubectl`에서 처음 사용하는 경우 브라우저 기반 인증을 트리거합니다.| 사용자가 이러한 그룹에 없습니다. 사용자가 클러스터 관리자 그룹에 있지 않기 때문에 해당 권한은 클러스터 관리자가 설정한 RoleBindings 또는 ClusterRoleBindings에 의해 완전히 제어됩니다. (Cluster) RoleBindings는 [Azure AD 사용자 또는 Azure AD 그룹](azure-ad-rbac.md)을 `subjects`으로 추천합니다. 이러한 바인딩을 설정하지 않으면 사용자가 아무 `kubectl` 명령도 실행할 수 없습니다.|세부적인 액세스 제어를 원하는 경우 Kubernetes 권한 부여를 위해 Azure RBAC를 사용하지 않습니다. 참고로 바인딩을 설정하는 사용자는 이 표에 나열된 다른 방법 중 하나를 사용하여 로그인해야 합니다.|
| 관리자 그룹의 구성원인 Azure AD| 위와 동일|사용자는 여기에 나열된 그룹 중 하나의 멤버입니다. AKS는 나열된 모든 그룹을 `cluster-admin`Kubernetes 역할에 바인딩하는 ClusterRoleBinding을 자동으로 생성합니다. 따라서 이러한 그룹의 사용자는 모든 `kubectl`명령을 `cluster-admin`로 실행할 수 있습니다.|편리하게 사용자에게 관리자 권한을 부여하고, Kubernetes 권한 부여를 위한 Azure RBAC를 사용하지 _않으려는_ 경우|
| Kubernetes 권한 부여를 위한 Azure AD 및 Azure RBAC|두 가지 역할: <br> 먼저, **Azure Kubernetes 사용자 역할**(위 참조). <br> 두 번째, 나열된 "Azure Kubernetes 서비스 **RBAC**..." 역할 중 하나, 또는 고유한 사용자 지정 대안입니다.|Kubernetes 권한 부여에 대한 Azure RBAC이 활성화된 경우 구성 탭의 관리자 역할 필드는 관련이 없습니다.|Kubernetes 권한 부여를 위한 Azure RBAC를 사용 중입니다. 이 방법은 RoleBindings 또는 ClusterRoleBindings를 설정하지 않고도 세분화된 제어를 제공합니다.|

## <a name="next-steps"></a>다음 단계

- Azure AD 및 Kubernetes RBAC를 시작하려면 [AKS와 Azure Active Directory 통합][aks-aad]을 참조하세요.
- 관련 모범 사례로 [AKS의 인증 및 인증 모범 사례][operator-best-practices-identity]를 참조하세요.
- Kubernetes 권한 부여에 대한 Azure RBAC를 시작하려면 [Azure Kubernetes Service(AKS) 클러스터 내에서 Azure RBAC를 사용한 액세스 권한 부여](manage-azure-rbac.md)를 참조하세요.
- `kubeconfig` 파일 보안을 시작하려면 [클러스터 구성 파일에 대한 액세스 제한](control-kubeconfig-access.md)을 참조하세요.

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 스토리지][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli

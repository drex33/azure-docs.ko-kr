---
title: Azure Kubernetes Service 소개
description: Azure에서 컨테이너 기반 애플리케이션을 배포 및 관리하는 Azure Kubernetes Service의 기능 및 이점을 알아봅니다.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 43313026bec06082b101629556b3b5b0f45ae2ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741644"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

AKS(Azure Kubernetes Service)는 운영 오버헤드를 Azure로 오프로드하여 Azure에서 관리되는 Kubernetes 클러스터 배포를 단순화합니다. 호스팅되는 Kubernetes 서비스인 Azure는 상태 모니터링 및 유지 관리 같은 중요 작업을 처리합니다. Kubernetes 마스터는 Azure에서 관리되므로 에이전트 노드만 관리하고 유지 관리합니다. 따라서 AKS는 무료입니다. 마스터가 아니라 클러스터 내의 에이전트 노드에 대해서만 지불합니다.  

다음 명령을 사용하여 AKS 클러스터를 만듭니다.
* [Azure CLI](kubernetes-walkthrough.md)
* [Azure Portal](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* [Azure Resource Manager 템플릿](kubernetes-walkthrough-rm-template.md) 및 Terraform과 같은 템플릿 기반 배포 옵션 사용 

AKS 클러스터를 배포할 때 Kubernetes 마스터 및 모든 노드가 배포되고 구성됩니다. 배포 프로세스 중에 고급 네트워킹, Azure AD(Azure Active Directory) 통합, 모니터링 및 기타 기능을 구성할 수 있습니다. 

Kubernetes 기본 사항에 대한 자세한 내용은 [AKS의 Kubernetes 핵심 개념][concepts-clusters-workloads]을 참조하세요.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS는 Windows Server 컨테이너도 지원합니다.

## <a name="access-security-and-monitoring"></a>액세스, 보안 및 모니터링

향상된 보안 및 관리를 위해 AKS를 사용하면 Azure AD와 통합하여 다음을 수행할 수 있습니다.
* Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)를 사용합니다. 
* 클러스터 및 리소스의 상태를 모니터링합니다.

### <a name="identity-and-security-management"></a>ID 및 보안 관리

#### <a name="kubernetes-rbac"></a>Kubernetes RBAC

클러스터 리소스에 대한 액세스를 제한하기 위해 AKS는 [Kubernetes RBAC][kubernetes-rbac]를 지원합니다. Kubernetes RBAC는 Kubernetes 리소스 및 네임스페이스에 대한 액세스 및 권한을 제어합니다.  

#### <a name="azure-ad"></a>Azure AD

Azure AD와 통합하도록 AKS 클러스터를 구성할 수 있습니다. Azure AD 통합을 사용하면 기존 ID 및 그룹 멤버 자격을 기반으로 하여 Kubernetes 액세스를 설정할 수 있습니다. AKS 리소스에 대한 통합 로그온 환경과 액세스를 기존 Azure AD 사용자 및 그룹에 제공할 수 있습니다.  

ID에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션][concepts-identity]을 참조하세요.

AKS 클러스터를 보호하려면 [AKS와 Azure Active Directory 통합][aks-aad]을 참조하세요.

### <a name="integrated-logging-and-monitoring"></a>통합된 로깅 및 모니터링

컨테이너 상태용 Azure Monitor는 AKS 클러스터 및 배포된 애플리케이션 내의 컨테이너, 노드 및 컨트롤러에서 메모리 및 프로세서 성능 메트릭을 수집합니다. 다음과 같은 컨테이너 로그와 [Kubernetes 마스터 로그][aks-master-logs]를 모두 검토할 수 있습니다.
* Azure Log Analytics 작업 영역에 저장됩니다.
* Azure Portal, Azure CLI 또는 REST 엔드포인트를 통해 사용할 수 있습니다.

자세한 내용은 [Azure Kubernetes Service 컨테이너 상태 모니터링][container-health]을 참조하세요.

## <a name="clusters-and-nodes"></a>클러스터 및 노드

AKS 노드는 Azure VM(가상 머신)에서 실행됩니다. AKS 노드를 사용하면 스토리지를 노드 및 Pod에 연결하고, 클러스터 구성 요소를 업그레이드하고, GPU를 사용할 수 있습니다. AKS는 혼합된 운영 체제 및 Windows Server 컨테이너를 지원하기 위해 여러 노드 풀을 실행하는 Kubernetes 클러스터를 지원합니다.  

Kubernetes 클러스터, 노드 및 노드 풀 기능에 대한 자세한 내용은 [AKS의 Kubernetes 핵심 개념][concepts-clusters-workloads]을 참조하세요.

### <a name="cluster-node-and-pod-scaling"></a>클러스터 노드 및 Pod 크기 조정

리소스 변경에 따라 서비스를 실행하는 클러스터 노드 또는 Pod 수를 자동으로 확대 또는 축소합니다. 수평 Pod 자동 크기 조정기 또는 클러스터 자동 크기 조정기 모두 수요에 맞게 조정하고 필요한 리소스만 실행할 수 있습니다.

자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터의 규모 조정][aks-scale]을 참조하세요.

### <a name="cluster-node-upgrades"></a>클러스터 노드 업그레이드

AKS는 여러 Kubernetes 버전을 제공합니다. AKS에서 새 버전을 사용할 수 있게 되면 Azure Portal 또는 Azure CLI를 사용하여 클러스터를 업그레이드할 수 있습니다. 업그레이드 프로세스 중에는 노드를 신중하게 통제하고 드레이닝하여 실행 중인 애플리케이션의 중단을 최소화합니다.  

수명 주기 버전에 대해 자세히 알아보려면 [AKS의 지원되는 Kubernetes 버전][aks-supported versions]을 참조하세요. 업그레이드하는 방법에 대한 단계는 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

### <a name="gpu-enabled-nodes"></a>GPU 지원 노드

AKS는 GPU 지원 노드 풀 생성을 지원합니다. Azure는 현재, 단일 또는 여러 GPU 지원 VM을 제공합니다. GPU 지원 VM은 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다.

자세한 내용은 [AKS에서 GPU 사용][aks-gpu]을 참조하세요.

### <a name="confidential-computing-nodes-public-preview"></a>기밀 컴퓨팅 노드(공개 미리 보기)

AKS는 Intel SGX 기반 기밀 컴퓨팅 노드 풀(DCSv2 VM) 만들기를 지원합니다. 기밀 컴퓨팅 노드를 통해 신뢰할 수 있는 하드웨어 기반 환경(enclave)에서 컨테이너를 실행할 수 있습니다. 증명을 통해 코드 무결성과 결합된 컨테이너 간의 격리는 심층 방어 컨테이너 보안 전략에 도움이 될 수 있습니다. 기밀 컴퓨팅 노드는 기밀 컨테이너(기존 Docker 앱)와 enclave 인식 컨테이너를 모두 지원합니다.

자세한 내용은 [AKS의 기밀 컴퓨팅 노드][conf-com-node]를 참조하세요.

### <a name="storage-volume-support"></a>스토리지 볼륨 지원

애플리케이션 워크로드를 지원하기 위해 영구 데이터에 대한 정적 또는 동적 스토리지 볼륨을 탑재할 수 있습니다. 스토리지 볼륨을 공유할 것으로 예상되는 연결된 포드 수에 따라 다음 중 하나가 지원하는 스토리지를 사용할 수 있습니다.
* 단일 Pod 액세스를 위한 Azure 디스크 또는 
* 여러 개의 동시 Pod 액세스를 위한 Azure Files.

자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

[Azure Disks][azure-disk] 또는 [Azure Files][azure-files]를 사용하여 동적 영구 볼륨을 시작합니다.

## <a name="virtual-networks-and-ingress"></a>가상 네트워크 및 수신

AKS 클러스터는 기존 가상 네트워크로 배포될 수 있습니다. 이 구성에서 클러스터의 모든 Pod에는 가상 네트워크의 IP 주소가 할당되며 다음과 직접 통신할 수 있습니다.
* 클러스터의 기타 Pod 
* 가상 네트워크의 기타 노드. 

또한 Pod는 피어링된 가상 네트워크에서 다른 서비스에 연결하고, ExpressRoute 또는 S2S(사이트 간) VPN 연결을 통해 온-프레미스 네트워크에 연결할 수 있습니다.  

자세한 내용은 [애플리케이션에 대한 AKS의 네트워크 개념][aks-networking]을 참조하세요.

### <a name="ingress-with-http-application-routing"></a>HTTP 애플리케이션 라우팅을 사용하여 수신

HTTP 애플리케이션 라우팅 추가 기능을 사용하면 AKS 클러스터에 배포된 애플리케이션에 쉽게 액세스할 수 있습니다. HTTP 애플리케이션 라우팅 솔루션이 설정되면 AKS 클러스터에 수신 컨트롤러를 구성합니다.  

애플리케이션이 배포되면 공개적으로 액세스할 수 있는 DNS 이름이 자동으로 구성됩니다. HTTP 애플리케이션 라우팅은 DNS 영역을 설정하고 이를 AKS 클러스터와 통합합니다. 그런 다음, 정상적으로 Kubernetes 수신 리소스를 배포할 수 있습니다.  

수신 트래픽을 시작하려면 [HTTP 애플리케이션 라우팅][aks-http-routing]을 참조하세요.

## <a name="development-tooling-integration"></a>개발 도구 통합

Kubernetes에는 AKS와 원활하게 작동하는 풍부한 개발 및 관리 도구 에코시스템이 있습니다. 이러한 도구에는 Helm과 Visual Studio Code용 Kubernetes 확장이 포함되어 있습니다. 

Azure는 DevOps Starter와 같이 Kubernetes를 간소화하는 데 도움이 되는 여러 도구를 제공합니다.  

### <a name="devops-starter"></a>DevOps Starter

DevOps Starter는 기존 코드 및 Git 리포지토리를 Azure로 가져오는 간단한 솔루션을 제공합니다. DevOps Starter는 자동으로 다음을 수행합니다.
* Azure 리소스(예: AKS)를 만듭니다. 
* Azure DevOps Services에서 CI용 빌드 파이프라인이 포함된 릴리스 파이프라인을 구성합니다. 
* CD용 릴리스 파이프라인을 설정합니다. 
* 모니터링을 위한 Azure Application Insights 리소스를 생성합니다. 

자세한 내용은 [DevOps Starter][azure-devops]를 참조하세요.

## <a name="docker-image-support-and-private-container-registry"></a>Docker 이미지 지원 및 프라이빗 컨테이너 레지스트리

AKS는 Docker 이미지 형식을 지원합니다. Docker 이미지의 프라이빗 스토리지의 경우 AKS를 ACR(Azure Container Registry)과 통합할 수 있습니다.

프라이빗 이미지 저장소를 만들려면 [Azure Container Registry][acr-docs]를 참조하세요.

## <a name="kubernetes-certification"></a>Kubernetes 인증

AKS는 CNCF에서 Kubernetes 규격을 준수하는 것으로 인증되었습니다.

## <a name="regulatory-compliance"></a>규정 준수

AKS는 SOC, ISO, PCI DSS 및 HIPAA를 준수합니다. 자세한 내용은 [Microsoft Azure 규정 준수 개요][compliance-doc]를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure CLI 빠른 시작을 사용하여 AKS를 배포하고 관리하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 AKS 클러스터 배포][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: /previous-versions/azure/dev-spaces/
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: monitor-aks-reference.md#resource-logs
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
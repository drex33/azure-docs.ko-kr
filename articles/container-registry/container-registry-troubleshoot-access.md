---
title: 레지스트리 관련 네트워크 문제 해결
description: 가상 네트워크에서 또는 방화벽 뒤에 있는 Azure 컨테이너 레지스트리에 액세스할 때 발생하는 일반적인 문제에 대한 증상, 원인 및 해결 방법
ms.topic: article
ms.date: 05/10/2021
ms.openlocfilehash: f5c51f14eb5ed5a950272e43e701ec7bcc44b901
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446489"
---
# <a name="troubleshoot-network-issues-with-registry"></a>레지스트리 관련 네트워크 문제 해결

이 문서는 가상 네트워크에서 또는 방화벽 또는 프록시 서버 뒤에 있는 Azure 컨테이너 레지스트리에 액세스할 때 발생할 수 있는 문제를 해결하는 데 도움이 됩니다. 

## <a name="symptoms"></a>증상

다음 중 하나 이상을 포함할 수 있습니다.

* 이미지를 푸시하거나 끌어올 수 없으며 오류 `dial tcp: lookup myregistry.azurecr.io`가 표시됩니다.
* 이미지를 푸시하거나 끌어올 수 없으며 오류 `Client.Timeout exceeded while awaiting headers`가 표시됩니다.
* 이미지를 푸시하거나 끌어올 수 없으며 Azure CLI 오류 `Could not connect to the registry login server`가 표시됩니다.
* 레지스트리에서 Azure Kubernetes Service 또는 다른 Azure 서비스로 이미지를 끌어올 수 없습니다.
* HTTPS 프록시 뒤에 있는 레지스트리에 액세스할 수 없으며 오류 `Error response from daemon: login attempt failed with status: 403 Forbidden` 또는 `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`가 표시됩니다.
* 가상 네트워크 설정을 구성할 수 없으며 오류 `Failed to save firewall and virtual network settings for container registry`가 표시됩니다.
* Azure Portal 또는 Azure CLI를 사용하여 레지스트리 설정을 확인하거나 볼 수 없습니다.
* 가상 네트워크 설정 또는 공용 액세스 규칙을 추가하거나 수정할 수 없습니다.
* ACR 작업에서 이미지를 푸시하거나 끌어올 수 없습니다.
* Azure Security Center의 레지스트리에서 이미지를 검색할 수 없거나 검색 결과가 Azure Security Center에 표시되지 않습니다.
* 프라이빗 엔드포인트로 구성된 레지스트리에 액세스하려고 할 때 `host is not reachable` 오류를 수신합니다.

## <a name="causes"></a>원인

* 클라이언트 방화벽 또는 프록시에서 액세스 방지 - [솔루션](#configure-client-firewall-access)
* 레지스트리의 공용 네트워크 액세스 규칙에서 액세스 방지 - [솔루션](#configure-public-access-to-registry)
* 가상 네트워크 또는 프라이빗 엔드포인트 구성에서 액세스 방지 - [솔루션](#configure-vnet-access)
* 프라이빗 엔드포인트, 서비스 엔드포인트 또는 공용 IP 액세스 규칙을 포함하는 레지스트리와 Azure Security Center 또는 다른 특정 Azure 서비스를 통합하려고 시도합니다. - [솔루션](#configure-service-access)

## <a name="further-diagnosis"></a>추가 진단 

[az acr check-health](/cli/azure/acr#az_acr_check_health) 명령을 실행하여 레지스트리 환경의 상태에 대한 자세한 정보를 얻고 선택적으로 대상 레지스트리에 액세스합니다. 예를 들어 특정 네트워크 연결 또는 구성 문제를 진단합니다. 

명령 예는 [Azure Container Registry 상태 확인](container-registry-check-health.md)을 참조하세요. 오류가 보고되면 [오류 참조](container-registry-health-error-reference.md) 및 다음 섹션에서 권장 솔루션을 검토하세요.

통합된 레지스트리와 함께 Azure Kubernetes Service를 사용하는 데 문제가 있는 경우 [az aks check-acr](/cli/azure/aks#az_aks_check_acr) 명령을 실행하여 AKS 클러스터에서 레지스트리에 도달할 수 있는지 확인합니다.

> [!NOTE]
> 일부 네트워크 연결 증상은 레지스트리 인증 또는 권한 부여에 문제가 있는 경우에도 발생할 수 있습니다. [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md)을 참조하세요.

## <a name="potential-solutions"></a>잠재적 해결 방법

### <a name="configure-client-firewall-access"></a>클라이언트 방화벽 액세스 구성

클라이언트 방화벽이나 프록시 서버 뒤에서 레지스트리에 액세스하려면 레지스트리의 공용 REST 및 데이터 엔드포인트에 액세스하는 방화벽 규칙을 구성합니다. [전용 데이터 엔드포인트](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)를 사용하는 경우 다음에 액세스할 수 있는 규칙이 필요합니다.

* REST 엔드포인트: `<registryname>.azurecr.io`
* 데이터 엔드포인트: `<registry-name>.<region>.data.azurecr.io`

지리적으로 복제된 레지스트리의 경우 각 지역 복제본의 데이터 엔드포인트에 대한 액세스를 구성합니다.

HTTPS 프록시 뒤에서 Docker 클라이언트와 Docker 데몬이 모두 프록시 동작에 대해 구성되어 있는지 확인합니다. Docker 디먼의 프록시 설정을 변경하는 경우 디먼을 다시 시작해야 합니다. 

ContainerRegistryLoginEvents 테이블의 레지스트리 리소스 로그는 차단된 연결 시도를 진단하는 데 도움이 될 수 있습니다.

관련 링크:

* [방화벽 뒤에서 Azure 컨테이너 레지스트리에 액세스하기 위한 규칙 구성](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS 프록시 구성](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Azure Container Registry의 지리적 복제](container-registry-geo-replication.md)
* [Azure Container Registry 모니터링](monitor-service.md)

### <a name="configure-public-access-to-registry"></a>레지스트리에 대한 공용 액세스 구성

인터넷을 통해 레지스트리에 액세스하는 경우 레지스트리가 클라이언트에서 공용 네트워크 액세스를 허용하는지 확인합니다. 기본적으로 Azure 컨테이너 레지스트리는 모든 네트워크에서 공용 레지스트리 엔드포인트에 대한 액세스를 허용합니다. 레지스트리는 선택한 네트워크 또는 선택한 IP 주소에 대한 액세스를 제한할 수 있습니다. 

레지스트리가 서비스 엔드포인트를 사용하여 가상 네트워크에 대해 구성된 경우 공용 네트워크 액세스를 사용하지 않도록 설정하면 서비스 엔드포인트에 대한 액세스도 비활성화됩니다. 레지스트리가 프라이빗 링크를 사용하여 가상 네트워크에 대해 구성된 경우 IP 네트워크 규칙은 레지스트리의 프라이빗 엔드포인트에 적용되지 않습니다. 

관련 링크:

* [공용 IP 네트워크 규칙 구성](container-registry-access-selected-networks.md)
* [Azure Private Link를 사용하여 Azure 컨테이너 레지스트리에 비공개로 연결합니다.](container-registry-private-link.md)
* [Azure Virtual Network의 서비스 엔드포인트를 사용하여 컨테이너 레지스트리에 대한 액세스 제한](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>VNet 액세스 구성

가상 네트워크가 프라이빗 링크 또는 서비스 엔드포인트(미리 보기)에 대한 프라이빗 엔드포인트로 구성되어 있는지 확인합니다. 현재 Azure Bastion 엔드포인트는 지원되지 않습니다.

프라이빗 엔드포인트가 구성된 경우 DNS가 레지스트리의 개인 IP 주소에 대한 *myregistry.azurecr.io* 와 같은 레지스트리의 공용 FQDN을 확인하는지 확인합니다.

  * `--vnet` 매개 변수와 함께 [az acr check-health](/cli/azure/acr#az_acr_check_health) 명령을 실행하여 가상 네트워크의 프라이빗 엔드포인트에 라우팅되는 DNS를 확인합니다.
  * DNS 조회를 위해 `dig` 또는 `nslookup`과 같은 네트워크 유틸리티를 사용합니다. 
  * 레지스트리 FQDN 및 각 데이터 엔드포인트 FQDN에 대해 [DNS 레코드가 구성](container-registry-private-link.md#dns-configuration-options)되었는지 확인합니다. 

네트워크의 다른 리소스에서 레지스트리로의 트래픽을 제한하는 데 사용되는 NSG 규칙 및 서비스 태그를 검토합니다. 

레지스트리에 대한 서비스 엔드포인트가 구성된 경우 해당 네트워크 서브넷의 액세스를 허용하는 네트워크 규칙이 레지스트리에 추가되어 있는지 확인합니다. 서비스 엔드포인트는 네트워크의 가상 머신 및 AKS 클러스터에서만 액세스할 수 있도록 지원합니다.

다른 Azure 구독에서 가상 네트워크를 사용하여 레지스트리 액세스를 제한하려면 해당 구독에서 `Microsoft.ContainerRegistry` 리소스 공급자를 등록해야 합니다. Azure Portal, Azure CLI 또는 다른 Azure 도구를 사용하여 Azure Container Registry에 대한 [리소스 공급자를 등록](../azure-resource-manager/management/resource-providers-and-types.md)합니다.

네트워크에서 Azure Firewall 또는 유사한 솔루션을 구성하는 경우 AKS 클러스터와 같은 다른 리소스의 송신 트래픽이 레지스트리 엔드포인트에 도달하도록 설정되었는지 확인합니다.

관련 링크:

* [Azure Private Link를 사용하여 Azure 컨테이너 레지스트리에 비공개로 연결합니다.](container-registry-private-link.md)
* [Azure 프라이빗 엔드포인트 연결 문제 해결](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Azure Virtual Network의 서비스 엔드포인트를 사용하여 컨테이너 레지스트리에 대한 액세스 제한](container-registry-vnet.md)
* [AKS 클러스터에 필요한 아웃바운드 네트워크 규칙 및 FQDN](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: DNS 확인 디버깅](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>서비스 액세스 구성

현재 여러 Azure 서비스에서 네트워크 제한이 있는 컨테이너 레지스트리에 대한 액세스를 허용하지 않습니다.

* Azure Security Center는 프라이빗 엔드포인트, 선택한 서브넷 또는 IP 주소에 대한 액세스를 제한하는 레지스트리에서 [이미지 취약성 검사](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json)를 수행할 수 없습니다. 
* 특정 Azure 서비스의 리소스는 Azure App Service 및 Azure Container Instances를 포함하여 네트워크 제한이 있는 컨테이너 레지스트리에 액세스할 수 없습니다.

이러한 Azure 서비스에 액세스하거나 컨테이너 레지스트리와 통합해야 하는 경우 네트워크 제한을 제거합니다. 예를 들어 레지스트리의 프라이빗 엔드포인트를 제거하거나 레지스트리의 공용 액세스 규칙을 제거하거나 수정합니다.

2021년 1월부터 신뢰할 수 있는 서비스 선택에 대한 [액세스를 허용](allow-access-trusted-services.md)하도록 네트워크 제한 레지스트리를 구성할 수 있습니다.

관련 링크:

* [Security Center에서 Azure Container Registry 이미지 검색](../security-center/defender-for-container-registries-introduction.md)
* [피드백](https://feedback.azure.com/d365community/idea/cbe6351a-0525-ec11-b6e6-000d3a4f07b8) 제공
* [신뢰할 수 있는 서비스에서 네트워크 제한 컨테이너 레지스트리에 안전하게 액세스하도록 허용](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>고급 문제 해결

레지스트리에서 [리소스 로그 수집](monitor-service.md)이 사용하도록 설정된 경우 ContainterRegistryLoginEvents 로그를 검토합니다. 이 로그는 들어오는 ID와 IP 주소를 포함하여 인증 이벤트와 상태를 저장합니다. 로그에서 [레지스트리 인증 실패](monitor-service.md#registry-authentication-failures)를 쿼리합니다. 

관련 링크:

* [진단 평가 및 감사를 위한 로그](./monitor-service.md)
* [컨테이너 레지스트리 FAQ](container-registry-faq.yml)
* [Azure Container Registry에 대한 Azure 보안 기준](security-baseline.md)
* [Azure Container Registry의 모범 사례](container-registry-best-practices.md)

## <a name="next-steps"></a>다음 단계

여기에서 문제를 해결하지 않으면 다음 옵션을 참조하세요.

* 기타 레지스트리 문제 해결 항목은 다음과 같습니다.
  * [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md) 
  * [쿼리 성능 문제 해결](container-registry-troubleshoot-performance.md)
* [커뮤니티 지원](https://azure.microsoft.com/support/community/) 옵션
* [Microsoft Q&A](/answers/products/)
* [지원 티켓 열기](https://azure.microsoft.com/support/create-ticket/)

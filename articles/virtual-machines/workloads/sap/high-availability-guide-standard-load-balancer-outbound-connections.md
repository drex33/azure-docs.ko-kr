---
title: SAP HA 시나리오에서 Azure VM 및 표준 ILB에 대한 퍼블릭 엔드포인트 연결
description: SAP 고가용성 시나리오에서 Azure Standard Load Balancer를 사용하는 가상 머신에 대한 퍼블릭 엔드포인트 연결
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 1b6ddb8412fab6e5b16a2c3fa30a96af08bc6e25
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132760716"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>SAP 고가용성 시나리오에서 Azure Standard Load Balancer를 사용하는 가상 머신에 대한 퍼블릭 엔드포인트 연결

이 문서의 범위는 퍼블릭 엔드포인트에 아웃바운드 연결을 사용하는 구성에 대해 설명하는 것입니다. 이 구성은 SUSE/RHEL용 Pacemaker를 사용하는 고가용성 컨텍스트에서 주로 사용됩니다.  

고가용성 솔루션에 Azure Fence 에이전트와 함께 Pacemaker를 사용하는 경우 VM에서 Azure 관리 API로 아웃바운드 연결이 설정되어야 합니다. 이 문서에서는 시나리오에 가장 적합한 옵션을 선택할 수 있도록 몇 가지 옵션을 제공합니다.  

## <a name="overview"></a>개요

클러스터링을 통해 SAP 솔루션의 고가용성을 구현할 때 필요한 구성 요소 중 하나는 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)입니다. Azure는 표준 및 기본의 두 가지 부하 분산 장치 SKU를 제공합니다.

표준 Azure 부하 분산 장치는 기본 부하 분산 장치에 비해 몇 가지 장점이 있습니다. 예를 들어 Azure 가용성 영역에서 작동하며, 모니터링 및 로깅 기능이 더 강력하기 때문에 보다 쉽게 문제를 해결하고 대기 시간을 줄일 수 있습니다. "HA 포트" 기능은 모든 포트에 적용됩니다. 즉, 더 이상 개별 포트를 일일이 나열할 필요가 없습니다.  

Azure 부하 분산 장치의 기본 SKU와 표준 SKU 간에는 몇 가지 중요한 차이점이 있습니다. 그 중 하나는 퍼블릭 엔드포인트에 대한 아웃바운드 트래픽 처리입니다. 기본과 표준 SKU 부하 분산 장치의 전체 비교는 [Load Balancer SKU 비교](../../../load-balancer/load-balancer-overview.md)를 참조하세요.  
 
공용 IP 주소가 없는 VM을 내부(공용 IP 주소 없는) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치하면 추가로 구성하지 않는 한 퍼블릭 엔드포인트에 대한 아웃바운드 연결이 없습니다.  

VM에 공용 IP 주소가 할당되거나 VM이 공용 IP 주소를 사용하는 부하 분산 장치의 백 엔드 풀에 있으면 퍼블릭 엔드포인트에 대한 아웃바운드 연결이 있습니다.  

SAP 시스템은 종종 중요한 비즈니스 데이터를 포함합니다. 공용 IP 주소를 통해 SAP 시스템을 호스트하는 VM에 액세스할 수 있도록 허용하는 경우는 거의 없습니다. 뿐만 아니라 VM에서 퍼블릭 엔드포인트로의 아웃바운드 연결이 필요한 시나리오가 있습니다.  

Azure 퍼블릭 엔드포인트에 대한 액세스가 필요한 시나리오의 예는 다음과 같습니다.  
- Azure Fence 에이전트에는 **management.azure.com** 및 **login.microsoftonline.com** 에 대한 액세스가 필요합니다.  
- [Azure Backup](../../../backup/backup-azure-sap-hana-database.md#establish-network-connectivity)
- [Azure Site Recovery](../../../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-for-urls)  
- 운영 체제 패치에 퍼블릭 리포지토리 사용
- SAP 애플리케이션 데이터 흐름에는 퍼블릭 엔드포인트에 대한 아웃바운드 연결이 필요할 수 있습니다.

SAP 배포 시 퍼블릭 엔드포인트에 대한 아웃바운드 연결이 필요 없는 경우에는 추가 구성을 구현할 필요가 없습니다. 퍼블릭 엔드포인트에서 인바운드로 연결할 필요도 없다면 고가용성 시나리오에 사용할 내부 표준 SKU Azure Load Balancer를 만드는 것만으로 충분합니다.  

> [!Note]
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 공용 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다.  
>VM이 공용 IP 주소를 사용하거나 공용 IP 주소를 사용하는 Azure Load Balancer의 백 엔드 풀에 이미 있는 경우에는 VM이 퍼블릭 엔드포인트에 대한 아웃바운드 연결을 이미 갖고 있습니다.


다음 문서를 먼저 읽어보세요.

* Azure 표준 Load Balancer
  * [Azure 표준 Load Balancer 개요](../../../load-balancer/load-balancer-overview.md) - Azure 표준 Load Balancer, 중요한 원칙, 개념 및 자습서에 대한 포괄적인 개요 
  * [Azure에서 아웃바운드 연결](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) - Azure에서 아웃바운드 연결을 설정하는 방법에 대한 시나리오
  * [부하 분산 장치 아웃바운드 규칙](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) - 부하 분산 장치 아웃바운드 규칙의 개념과 아웃바운드 규칙을 만드는 방법을 설명
* Azure Firewall
  * [Azure Firewall 개요](../../../firewall/overview.md)- Azure Firewall의 개요
  * [자습서: Azure Firewall 배포 및 구성](../../../firewall/tutorial-firewall-deploy-portal.md) - Azure Portal을 통해 Azure Firewall을 구성하는 방법에 대한 지침
* [가상 네트워크 - 사용자 정의 규칙](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) - Azure 라우팅 개념 및 규칙  
* [보안 그룹 서비스 태그](../../../virtual-network/network-security-groups-overview.md#service-tags) - 서비스 태그를 사용하여 네트워크 보안 그룹 및 방화벽 구성을 간소화하는 방법

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>옵션 1: 인터넷에 아웃바운드 연결을 설정할 수 있는 추가 외부 Azure 표준 Load Balancer

퍼블릭 엔드포인트에서 VM으로의 인바운드 연결을 허용하지 않고 퍼블릭 엔드포인트에 아웃바운드 연결을 설정하는 한 가지 옵션은 공용 IP 주소를 사용하여 두 번째 부하 분산 장치를 만들고, 두 번째 부하 분산 장치의 백 엔드 풀에 VM을 추가하고 [아웃바운드 규칙](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)만 정의하는 것입니다.  
[네트워크 보안 그룹](../../../virtual-network/network-security-groups-overview.md)을 사용하여 VM의 아웃바운드 호출에 액세스할 수 있는 퍼블릭 엔드포인트를 제어하세요.  
자세한 내용은 [아웃바운드 연결](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) 문서의 시나리오 2를 참조하세요.  
구성은 다음과 같습니다.  

![네트워크 보안 그룹을 사용하여 퍼블릭 엔드포인트에 대한 연결 제어](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>중요 고려 사항

- 같은 서브넷에 있는 여러 VM에 추가 퍼블릭 Load Balancer를 하나 사용하여 퍼블릭 엔드포인트에 대한 아웃바운드 연결을 설정하고 비용을 최적화할 수 있습니다.  
- [네트워크 보안 그룹](../../../virtual-network/network-security-groups-overview.md)을 사용하여 VM에서 액세스할 수 있는 퍼블릭 엔드포인트를 제어하세요. 서브넷 또는 각 VM에 네트워크 보안 그룹을 할당할 수 있습니다. 가능하다면 [서비스 태그](../../../virtual-network/network-security-groups-overview.md#service-tags)를 사용하여 보안 규칙의 복잡성을 줄이세요.  
- 공용 IP 주소와 아웃바운드 규칙을 사용하는 Azure 표준 Load balancer는 퍼블릭 엔드포인트에 대한 직접 액세스를 허용합니다. 회사의 보안 요구 사항에 따라 감사 및 로깅이 가능하도록 모든 아웃바운드 트래픽을 중앙의 회사 솔루션을 통해 전달해야 하는 경우에는 이 시나리오로 요구 사항을 충족하지 못할 수 있습니다.  

>[!TIP]
>가능하다면 [서비스 태그](../../../virtual-network/network-security-groups-overview.md#service-tags)를 사용하여 네트워크 보안 그룹의 복잡성을 줄이세요. 

### <a name="deployment-steps"></a>배포 단계

1. 부하 분산 장치 만들기  
   1. [Azure Portal](https://portal.azure.com)에서 [모든 리소스], [추가]를 차례로 클릭한 다음, **Load Balancer** 를 검색합니다.  
   1. **만들기** 를 클릭합니다. 
   1. Load Balancer 이름을 **MyPublicILB** 로 지정합니다.  
   1. 형식은 **퍼블릭**, SKU는 **표준** 을 선택합니다.  
   1. **공용 IP 주소 만들기** 를 선택하고 이름을 **MyPublicILBFrondEndIP** 로 지정합니다.  
   1. 가용성 영역으로 **영역 중복** 을 선택합니다.  
   1. [검토 및 만들기]를 클릭한 다음, [만들기]를 클릭합니다.  
2. 백 엔드 풀 **MyBackendPoolOfPublicILB** 를 만들고 VM을 추가합니다.  
   1. 가상 네트워크를 선택합니다.  
   1. VM과 IP 주소를 선택한 다음, VM을 백 엔드 풀에 추가합니다.  
3. [아웃바운드 규칙을 만듭니다](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). 현재는 Azure Portal에서 아웃바운드 규칙을 만들 수 없습니다. [Azure CLI](../../../cloud-shell/overview.md)를 사용하여 아웃바운드 규칙을 만들 수 있습니다.  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 특정 퍼블릭 엔드포인트에 대한 액세스를 제한하는 네트워크 보안 그룹 규칙을 만듭니다. 기존 네트워크 보안 그룹이 있다면 기존 그룹을 조정해도 됩니다. 아래 예제에서는 Azure 관리 API에 대한 액세스를 사용하도록 설정하는 방법을 보여줍니다. 
   1. 네트워크 보안 그룹으로 이동합니다.
   1. 아웃바운드 보안 규칙을 클릭합니다.
   1. **인터넷** 에 대한 모든 아웃바운드 액세스를 **거부** 하는 규칙을 추가합니다.
   1. **AzureCloud** 에 대한 액세스를 **허용** 하며 모든 인터넷 액세스를 거부하는 규칙의 우선 순위보다 우선 순위가 낮은 규칙을 추가합니다.


   아웃바운드 보안 규칙은 다음과 같습니다. 

   ![공용 IP를 사용하는 두 번째 Load Balancer에 대한 아웃바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Azure 네트워크 보안 그룹에 대한 자세한 내용은 [보안 그룹](../../../virtual-network/network-security-groups-overview.md)을 참조하세요. 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>옵션 2: 인터넷에 아웃바운드 연결을 위한 Azure Firewall

VM에서 퍼블릭 엔드포인트로의 인바운드 연결을 허용하지 않고 퍼블릭 엔드포인트에 대한 아웃바운드 연결을 설정하는 또 다른 옵션은 Azure Firewall을 사용하는 것입니다. Azure Firewall은 기본 제공되는 고가용성 관리형 서비스이며 여러 가용성 영역으로 확장할 수 있습니다.  
또한 VM과 Azure 부하 분산 장치가 배포된 서브넷과 연결되어 있고, Azure Firewall을 가리키고, Azure Firewall을 통해 트래픽을 라우팅하는 [사용자 정의 경로](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)를 배포해야 합니다.  
Azure Firewall을 배포하는 방법에 대한 자세한 내용은 [Azure Firewall 배포 및 구성](../../../firewall/tutorial-firewall-deploy-portal.md)을 참조하세요.  

아키텍처는 다음과 같습니다.

![Azure Firewall과 아웃바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>중요 고려 사항

- 클라우드 기본 서비스인 Azure Firewall은 기본 제공되는 고가용성 서비스이며 영역 배포를 지원합니다.
- AzureFirewallSubnet으로 이름을 지정해야 하는 추가 서브넷이 필요합니다. 
- SAP VM이 있는 가상 네트워크의 아웃바운드 데이터 세트를 다른 가상 네트워크에 있는 VM 또는 퍼블릭 엔드포인트에 대량으로 전송하는 솔루션은 비용 측면에서 효율적이지 않을 수 있습니다. 가상 네트워크 전체에서 백업을 대량으로 복사하는 솔루션을 예로 들 수 있습니다. 자세한 내용은 Azure Firewall 가격 책정을 참조하세요.  
- 회사 방화벽 솔루션이 Azure Firewall이 아니고, 중앙의 회사 솔루션을 통해 모든 아웃바운드 트래픽을 전달해야 하는 보안 요구 사항이 있는 경우에는 이 솔루션이 실용적이지 않을 수 있습니다.  

>[!TIP]
>가능하다면 [서비스 태그](../../../virtual-network/network-security-groups-overview.md#service-tags)를 사용하여 Azure Firewall 규칙의 복잡성을 줄이세요.  

### <a name="deployment-steps"></a>배포 단계

1. 배포 단계에서는 VM에 대한 가상 네트워크와 서브넷이 이미 정의되어 있다고 가정합니다.  
2. VM과 표준 Load Balancer가 배포되는 동일한 Virtual Network에 **AzureFirewallSubnet** 서브넷을 만듭니다.  
   1. Azure Portal에서 가상 네트워크로 이동합니다. [모든 리소스]를 클릭하고, Virtual Network를 검색하고, Virtual Network를 클릭하고, 서브넷을 선택합니다.  
   1. [서브넷 추가]를 클릭합니다. 이름으로 **AzureFirewallSubnet** 을 입력합니다. 적절한 주소 범위를 입력합니다. 저장합니다.  
3. Azure Firewall을 만듭니다.  
   1. Azure Portal에서 [모든 리소스]를 선택하고 [추가], [방화벽], [만들기]를 차례로 클릭합니다. 리소스 그룹을 선택합니다(Virtual Network가 있는 동일한 리소스 그룹을 선택).  
   1. Azure Firewall 리소스의 이름을 입력합니다. 예: **MyAzureFirewall**.  
   1. VM이 배포되는 가용성 영역에 맞게 지역을 선택하고 2개 이상의 가용성 영역을 선택합니다.  
   1. SAP VM과 Azure 표준 Load Balancer가 배포된 Virtual Network를 선택합니다.  
   1. 공용 IP 주소: [만들기]를 클릭하고 이름을 입력합니다. 예: **MyFirewallPublicIP**.  
4. 지정된 퍼블릭 엔드포인트에 대한 아웃바운드 연결을 허용하는 Azure Firewall 규칙을 만듭니다. 다음 예제에서는 Azure 관리 API 퍼블릭 엔드포인트에 대한 액세스를 허용하는 방법을 보여줍니다.  
   1. [규칙], [네트워크 규칙 컬렉션]을 차례로 선택하고 [네트워크 규칙 컬렉션 추가]를 클릭합니다.  
   1. 이름: 이름을 **MyOutboundRule** 로 지정하고, 우선 순위를 입력하고, 작업 **허용** 을 선택합니다.  
   1. 서비스: 이름을 **ToAzureAPI** 로 지정합니다.  프로토콜: **모두** 를 선택합니다. 원본 주소: VM과 표준 Load Balancer가 배포된 서브넷의 범위를 입력합니다. 예: **11.97.0.0/24**. 대상 포트: <b>*</b>를 입력합니다.  
   1. 저장
   1. Azure Firewall 내에서 [개요]를 선택합니다. Azure Firewall의 개인 IP 주소를 적어 둡니다.  
5. Azure Firewall의 경로를 만듭니다.  
   1. Azure Portal에서 [모든 리소스]를 선택하고 [추가], [경로 테이블], [만들기]를 차례로 클릭합니다.  
   1. 이름으로 MyRouteTable을 입력하고 구독, 리소스 그룹 및 위치(가상 네트워크 및 방화벽 위치와 일치하는)를 선택합니다.  
   1. 저장  

   방화벽 규칙은 다음과 같이 보입니다: ![방화벽의 모습을 보여 주는 다이어그램](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. VM의 서브넷에서 **MyAzureFirewall** 의 개인 IP로 이어지는 사용자 정의 경로를 만듭니다.
   1. [경로 테이블] 내에서 [경로]를 클릭합니다. [추가]를 선택합니다. 
   1. 경로 이름: ToMyAzureFirewall로 지정, 주소 접두사: **0.0.0.0/0**. 다음 홉 유형: 가상 어플라이언스를 선택합니다. 다음 홉 주소: 구성한 방화벽의 개인 IP 주소 **11.97.1.4** 를 입력합니다.  
   1. 저장

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>옵션 3: Azure 관리 API에 대한 Pacemaker 호출에 Proxy 사용

프록시를 사용하여 Azure 관리 API 퍼블릭 엔드포인트에 대한 Pacemaker 호출을 허용할 수 있습니다.  

### <a name="important-considerations"></a>중요 고려 사항

  - 이미 회사 프록시가 있는 경우 회사 프록시를 통해 아웃바운드 호출을 퍼블릭 엔드포인트로 라우팅할 수 있습니다. 퍼블릭 엔드포인트에 대한 아웃바운드 호출은 회사 제어점을 통과합니다.  
  - 프록시 구성이 Azure 관리 API `https://management.azure.com` 및 `https://login.microsoftonline.com`에 대한 아웃바운드 연결을 허용해야 합니다.  
  - VM에서 프록시로 이어지는 경로가 있어야 합니다.  
  - 프록시는 HTTP/HTTPS 호출만 처리합니다. 다른 프로토콜(예: RFC)을 통해 퍼블릭 엔드포인트에 대한 아웃바운드 호출을 추가로 수행해야 하는 경우 대체 솔루션이 필요합니다.  
  - Pacemaker 클러스터의 안정성을 확보하려면 프록시 솔루션의 고가용성이 필요합니다.  
  - 프록시 위치에 따라 Azure Fence 에이전트에서 Azure 관리 API로 호출할 때 대기 시간이 더 발생할 수 있습니다. 회사 프록시가 아직 온-프레미스에 있고 Pacemaker 클러스터가 Azure에 있으면 대기 시간을 측정하고 이 솔루션이 적합한지 생각해 봅니다.  
  - 아직 고가용성 회사 프록시가 없는 경우 고객 때문에 비용과 복잡성이 추가로 증가할 수 있으므로 이 옵션을 사용하지 않는 것이 좋습니다. 그럼에도 불구하고 Pacemaker에서 Azure Management 퍼블릭 API로의 아웃바운드 연결을 허용하기 위해 추가 프록시 솔루션을 배포하기로 결정하는 경우에는 프록시의 고가용성이 확보되어야 하고 VM에서 프록시로의 대기 시간이 짧아야 합니다.  

### <a name="pacemaker-configuration-with-proxy"></a>프록시를 사용하는 Pacemaker 구성 

업계에서 사용할 수 있는 다양한 프록시 옵션이 있습니다. 프록시 배포에 대한 단계별 지침은 이 문서의 범위를 벗어납니다. 아래 예제에서는 프록시가 **MyProxyService** 에 응답하고 **MyProxyPort** 포트에서 수신 대기하는 것으로 가정합니다.  
Pacemaker가 Azure 관리 API와 통신할 수 있도록 허용하려면 모든 클러스터 노드에서 다음 단계를 수행합니다.  

1. Pacemaker 구성 파일 /etc/sysconfig/pacemaker를 편집하고 다음 줄을 추가합니다(모든 클러스터 노드에서).

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. **모든** 클러스터 노드에서 Pacemaker 서비스를 다시 시작합니다.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-options"></a>기타 옵션

타사의 URL 기반 방화벽 프록시를 통해 아웃바운드 트래픽이 라우팅되는 경우:

- Azure Fence 에이전트를 사용한다면 해당 방화벽 구성이 다음 Azure 관리 API에 대한 아웃바운드 연결을 허용하는지 확인합니다. `https://management.azure.com` 및 `https://login.microsoftonline.com`   
- SUSE의 Azure 퍼블릭 클라우드 업데이트 인프라를 사용해 업데이트와 패치를 적용하는 경우, [Azure Public Cloud Update Infrastructure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)(Azure 퍼블릭 클라우드 업데이트 인프라에 관한 기본 정보)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 기반의 SUSE에서 Pacemaker를 구성하는 방법 알아보기](./high-availability-guide-suse-pacemaker.md)
* [Azure 기반의 Red Hat에서 Pacemaker를 구성하는 방법 알아보기](./high-availability-guide-rhel-pacemaker.md)
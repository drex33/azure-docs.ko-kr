---
title: Azure DDoS Protection 참조 아키텍처
description: Azure DDoS Protection 참조 아키텍처에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 429702adaccf5731292192f1b3cb6a7d42859a00
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867582"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection 참조 아키텍처

DDoS Protection Standard는 [가상 네트워크에 배포되는 서비스](../virtual-network/virtual-network-for-azure-services.md)를 위해 설계되었습니다. 다른 서비스에는 기본 DDoS Protection 기본 서비스가 적용됩니다. 다음과 같은 참조 아키텍처는 시나리오별로 정렬되어 있으며, 아키텍처 패턴이 함께 그룹화되어 있습니다.

## <a name="virtual-machine-windowslinux-workloads"></a>가상 머신(Windows/Linux) 워크로드

### <a name="application-running-on-load-balanced-vms"></a>부하 분산된 VM에서 실행되는 애플리케이션

이 참조 아키텍처에서는 확장성과 가용성을 개선하기 위해 부하 분산 장치 뒤에서 확장 집합에 포함된 복수의 Windows VM을 실행하는 검증된 모범 사례를 보여줍니다. 이 아키텍처는 웹 서버 같은 상태 비저장 워크로드에 사용할 수 있습니다.

![부하 분산된 VM에서 실행 중인 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/ddos-best-practices/image-9.png)

이 아키텍처에서 워크로드는 여러 VM 인스턴스에 배포됩니다. 공용 IP 주소는 한 개가 사용되며, 부하 분산 장치를 통해 인터넷 트래픽이 여러 VM으로 분산됩니다. DDoS Protection 표준은 공용 IP가 연결된 Azure(인터넷) 부하 분산 장치의 가상 네트워크에서 사용됩니다.

부하 분산 장치는 수신되는 인터넷 요청을 여러 VM 인스턴스로 분산합니다. 가상 머신 확장 집합을 사용하면 여러 VM을 수동 확장 또는 축소하거나 사전 정의된 규칙을 바탕으로 자동 확장 또는 축소할 수 있습니다. 이는 리소스가 DDoS 공격을 받고 있는 경우에 중요합니다. 이 참조 아키텍처에 대한 자세한 내용은 [이 아티클](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)을 참조하세요.

### <a name="application-running-on-windows-n-tier"></a>Windows N 계층에서 실행 중인 애플리케이션

N 계층 아키텍처를 구현하는 방법은 여러 가지가 있습니다. 다음 다이어그램은 일반적인 3계층 웹 애플리케이션을 보여줍니다. 이 아키텍처는 [부하가 분산된 VM을 실행하여 확장성 및 가용성 확보](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) 아티클을 기반으로 합니다. 웹 및 비즈니스 계층은 부하 분산된 VM을 사용합니다.

![Windows N 계층에서 실행 중인 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/ddos-best-practices/image-10.png)

이 아키텍처에서 DDoS Protection 표준은 가상 네트워크에서 사용됩니다. 가상 네트워크의 모든 공용 IP는 레이어 3 및 4에 대해 DDoS 보호를 받습니다. 레이어 7 보호의 경우 WAF SKU에 Application Gateway를 배포합니다. 이 참조 아키텍처에 대한 자세한 내용은 [이 아티클](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)을 참조하세요.

> [!NOTE]
> 단일 VM이 공용 IP 뒤에서 실행되는 시나리오는 지원되지 않습니다.

### <a name="paas-web-application"></a>PaaS 웹 애플리케이션

이 참조 아키텍처는 단일 지역에서 Azure App Service 애플리케이션을 실행하는 방법을 보여줍니다. 이 아키텍처는 [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) 및 [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)를 사용하는 웹 애플리케이션에 관한 일련의 검증된 사례를 보여줍니다.
대기 지역은 장애 조치(failover) 시나리오에 대해 설정되어 있습니다.

![PaaS 웹 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager는 들어오는 요청을 한 지역의 Application Gateway로 라우팅합니다. 정상 작동 중에는 요청을 활성 지역의 Application Gateway로 라우팅합니다. 해당 지역을 사용할 수 없게 되면 Traffic Manager가 대기 지역의 Application Gateway로 장애 조치(failover) 됩니다.

웹 애플리케이션을 대상으로 하는 인터넷의 모든 트래픽이 Traffic Manager를 통해 [Application Gateway 공용 IP 주소](../application-gateway/application-gateway-web-app-overview.md)로 라우팅됩니다. 이 시나리오에서 앱 서비스(웹앱) 자체는 외부와 직접 연결되지 않고 Application Gateway를 통해 보호됩니다. 

레이어 7(HTTP/HTTPS/WebSocket) 공격에 대해 보호할 수 있도록 Application Gateway WAF SKU(금지 모드)를 구성하는 것이 좋습니다. 또한 웹앱은 [Application Gateway IP 주소에서 오는 트래픽만 수락](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)하도록 구성됩니다.

이 참조 아키텍처에 대한 자세한 내용은 [이 아티클](/azure/architecture/reference-architectures/app-service-web-app/multi-region)을 참조하세요.

## <a name="protecting-on-premises-resources"></a>온-프레미스 리소스 보호

Azure에서 공용 IP 주소를 백 엔드 원본에 대한 트래픽을 온-프레미스 환경으로 리디렉션하여 Azure DDoS Protection 표준의 규모, 용량 및 효율성을 활용해 온-프레미스 리소스를 보호할 수 있습니다.

![온-프레미스 리소스 보호](./media/reference-architectures/ddos-on-prem.png)

인터넷에서 트래픽을 수신하는 웹 애플리케이션이 있는 경우 Application Gateway 뒤에 웹 애플리케이션을 호스트한 다음, WAF를 사용하여 SQL 삽입 같은 레이어 7 웹 공격으로부터 보호할 수 있습니다. 애플리케이션의 백 엔드 원본은 VPN을 통해 연결된 온-프레미스 환경에 있습니다. 

온-프레미스 환경의 백 엔드 리소스는 공용 인터넷에 노출되지 않습니다. AppGW/WAF 공용 IP만 인터넷에 노출되며 애플리케이션의 DNS 이름이 해당 공용 IP 주소에 매핑됩니다. 

AppGW/WAF가 포함된 가상 네트워크에서 DDoS Protection 표준이 활성화되면 DDoS Protection 표준은 잘못된 트래픽을 완화하고 정상적인 트래픽을 애플리케이션으로 라우팅하여 애플리케이션을 보호합니다. 

이 [문서](../azure-vmware/protect-azure-vmware-solution-with-application-gateway.md)에서는 Application Gateway와 함께 DDoS Protection 표준을 사용하여 Azure VMware Solution에서 실행되는 웹앱을 보호하는 방법을 보여줍니다.

## <a name="mitigation-for-non-web-paas-services"></a>비 웹 PaaS 서비스에 대한 완화

### <a name="hdinsight-on-azure"></a>Azure의 HDInsight

이 참조 아키텍처는 [Azure HDInsight 클러스터](../hdinsight/index.yml)에 대해 DDoS Protection 표준을 구성하는 방법을 보여줍니다. HDInsight 클러스터가 가상 네트워크에 연결되어 있고 DDoS Protection이 해당 가상 네트워크에서 사용되는지 확인해야 합니다.

![가상 네트워크 설정을 포함한 "HDInsight" 및 "고급 설정" 창](./media/ddos-best-practices/image-12.png)

![DDoS Protection을 사용하기 위한 선택](./media/ddos-best-practices/image-13.png)

이 아키텍처에서 인터넷의 HDInsight 클러스터를 대상으로 하는 트래픽은 HDInsight 게이트웨이 부하 분산 장치와 연결된 공용 IP 주소로 라우팅됩니다. 그러면 게이트웨이 부하 분산 장치가 트래픽을 헤드 노드 또는 작업자 노드로 직접 보냅니다. HDInsight 가상 네트워크에서 DDoS Protection 표준을 사용하기 때문에 가상 네트워크의 모든 공용 IP가 레이어3 및 4에 대해 DDoS Protection을 받습니다. 이 참조 아키텍처를 N 계층 및 다중 지역 참조 아키텍처와 결합할 수 있습니다.

이 참조 아키텍처에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 설명서를 참조하세요.


> [!NOTE]
> 공용 IP를 사용한 가상 네트워크에서의 Power Apps 또는 API 관리를 위한 Azure App Service Environment는 기본적으로 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 만드는](manage-ddos-protection.md) 방법을 알아봅니다.

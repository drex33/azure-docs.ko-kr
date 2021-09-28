---
title: Azure Private Link 연결 문제 해결
description: 프라이빗 링크 연결을 진단하는 단계별 지침
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 9aa0ac4334d50a7b3a55ff992fb3e5a984786384
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155024"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Azure Private Link 연결 문제 해결

이 문서에서는 Azure Private Link 설정에 대한 연결의 유효성을 검사하고 진단하는 단계별 지침에 관해 설명합니다.

Azure Private Link를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure Storage, Azure Cosmos DB, Azure SQL Database와 같은 Azure PaaS(Platform as a Service) 서비스와 Azure에서 호스트되는 고객 또는 파트너 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 퍼블릭 인터넷에서 노출을 제거합니다. 또한 가상 네트워크에서 고유한 프라이빗 링크 서비스를 만들어 고객에게 비공개로 제공할 수도 있습니다.

Private Link 액세스에 대한 Azure Load Balancer 표준 계층 뒤에서 실행되는 서비스를 사용하도록 설정할 수 있습니다. 서비스 소비자는 가상 네트워크 내에서 프라이빗 엔드포인트를 만든 다음 이 서비스에 매핑하여 개인적으로 액세스할 수 있습니다.

Private Link와 함께 사용할 수 있는 연결 시나리오는 다음과 같습니다.

- 동일한 지역의 가상 네트워크
- 지역적으로 피어링된 가상 네트워크
- 전역적으로 피어링된 가상 네트워크
- VPN 또는 Azure ExpressRoute 회로를 통한 온-프레미스 고객

## <a name="deployment-troubleshooting"></a>배포 문제 해결

프라이빗 링크 서비스에 대해 선택한 서브넷에서 원본 IP 주소를 선택할 수 없는 경우 문제 해결을 위해 [프라이빗 링크 서비스에 대해 네트워크 정책 사용 안 함](./disable-private-link-service-network-policy.md)의 정보를 검토합니다.

원본 IP 주소를 선택하는 서브넷에 대해 **privateLinkServiceNetworkPolicies** 설정을 사용하지 않도록 설정했는지 확인합니다.

## <a name="diagnose-connectivity-problems"></a>연결 문제 진단

프라이빗 링크 설정에 연결 문제가 발생하는 경우 이러한 단계를 검토하여 모든 일반적인 구성이 예상대로 작동하는지 확인합니다.

1. 리소스를 검색하여 프라이빗 링크 구성을 검토합니다.

    a. [Private Link 센터](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/overview)로 이동합니다.

      ![Private Link 센터](./media/private-link-tsg/private-link-center.png)

    b. 왼쪽 창에서 **프라이빗 링크 서비스** 를 선택합니다.

      ![프라이빗 링크 서비스](./media/private-link-tsg/private-link-service.png)

    다. 진단하려는 프라이빗 링크 서비스를 필터링하고 선택합니다.

    d. 프라이빗 엔드포인트 연결을 검토합니다.
     - 연결하려는 프라이빗 엔드포인트가 **승인** 된 연결 상태로 나열되어 있는지 확인합니다.
     - 상태가 **보류 중** 이면 해당 상태를 선택하고 승인합니다.

       ![프라이빗 엔드포인트 연결](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 이름을 선택하여 연결하는 프라이빗 엔드포인트로 이동합니다. 연결 상태가 **승인됨** 으로 표시되는지 확인합니다.

       ![프라이빗 엔드포인트 연결 개요](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 양측이 모두 승인되면 연결을 다시 시도합니다.

    e. **속성** 탭의 **개요** 탭과 **리소스 ID** 에서 **별칭** 을 검토합니다.
     - **별칭** 및 **리소스 ID** 정보가 이 서비스에 대한 프라이빗 엔드포인트를 만드는 데 사용하는 **별칭** 및 **리소스 ID** 와 일치하는지 확인합니다.

       ![별칭 정보 확인](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![리소스 ID 정보 확인](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. **개요** 탭에서 **표시 여부** 정보를 검토합니다.
     - 구독이 **표시 여부** 범위에 속하는지 확인합니다.

       ![표시 여부 정보 확인](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. **개요** 탭에서 **부하 분산 장치** 정보를 검토합니다.
     - 부하 분산 장치 링크를 선택하여 부하 분산 장치로 이동할 수 있습니다.

       ![부하 분산 장치 정보 확인](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 부하 분산 장치 설정이 예상대로 구성되었는지 확인합니다.
       - **프런트 엔드 IP 구성** 을 검토합니다.
       - **백 엔드 풀** 을 검토합니다.
       - **부하 분산 규칙** 을 검토합니다.

       ![부하 분산 장치 속성 확인](./media/private-link-tsg/pls-ilb-properties.png)

     - 이전 설정에 따라 부하 분산 장치가 작동하는지 확인합니다.
       - 부하 분산 장치 백 엔드 풀을 사용할 수 있는 서브넷 이외의 서브넷에서 VM을 선택합니다.
       - 이전 VM에서 부하 분산 장치 프런트 엔드에 액세스하세요.
       - 연결에서 부하 분산 규칙에 따라 백 엔드 풀에 연결하면 부하 분산 장치가 작동합니다.
       - Azure Monitor를 통해 부하 분산 장치 메트릭을 검토하여 부하 분산 장치를 통한 데이터의 흐름을 확인할 수도 있습니다.

1. [Azure Monitor](../azure-monitor/overview.md)를 사용하여 데이터 흐름을 확인합니다.

    a. 프라이빗 링크 서비스 리소스에서 **메트릭** 을 선택합니다.
     - **바이트 입력** 또는 **바이트 출력** 을 선택합니다.
     - 프라이빗 링크 서비스에 연결하려고 할 때 데이터의 흐름 여부를 확인합니다. 약 10분 정도 지연될 수 있습니다.

       ![프라이빗 링크 서비스 메트릭 확인](./media/private-link-tsg/pls-metrics.png)

1. Azure Monitor 사용 [- 인사이트를](../azure-monitor/insights/network-insights-overview.md#dependency-view) 위한 네트워크 및 다음으로 진행하여 리소스의 종속성 보기를 확인합니다.
     - Azure Monitor
     - 네트워크
     - Private Link 서비스
     - 종속성 보기 

![AzureMonitor](https://user-images.githubusercontent.com/20302679/135001735-56a9484b-f9b4-484b-a503-cfb9d20b264a.png)

![DependencyView](https://user-images.githubusercontent.com/20302679/135001741-8e848c52-d4bb-4646-b0d3-a85614ebe16c.png)

4. 문제가 여전히 해결되지 않고 연결 문제가 계속 발생하는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

 * [프라이빗 링크 서비스 만들기(CLI)](./create-private-link-service-cli.md)
 * [Azure 프라이빗 엔드포인트 문제 해결 가이드](troubleshoot-private-endpoint-connectivity.md)

---
title: Azure 프라이빗 엔드포인트 연결 문제 해결
description: 프라이빗 엔드포인트 연결을 진단하는 단계별 지침
services: private-endpoint
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
ms.openlocfilehash: c1599d9208759d881e1f036dc9cb881e1e9f635e
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133541055"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Azure 프라이빗 엔드포인트 연결 문제 해결

이 문서에서는 Azure 프라이빗 엔드포인트 연결 설정의 유효성을 검사하고 진단하는 단계별 지침을 제공합니다.

Azure 프라이빗 엔드포인트는 프라이빗 링크 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 이 솔루션은 가상 네트워크에서 Azure 서비스 리소스에 대한 프라이빗 연결을 제공하여 Azure에서 워크로드를 보호하는 데 도움이 됩니다. 이 솔루션은 이러한 서비스를 가상 네트워크에 효과적으로 제공합니다.

프라이빗 엔드포인트에서 사용할 수 있는 연결 시나리오는 다음과 같습니다.

- 동일한 지역의 가상 네트워크
- 지역적으로 피어링된 가상 네트워크
- 전역적으로 피어링된 가상 네트워크
- VPN 또는 Azure ExpressRoute 회로를 통한 온-프레미스 고객

## <a name="diagnose-connectivity-problems"></a>연결 문제 진단 

다음 단계를 검토하여 모든 일반적인 구성이 프라이빗 엔드포인트 설정에 대한 연결 문제를 해결하는 데 예상대로 작동하는지 확인합니다.

1. 리소스를 검색하여 프라이빗 엔드포인트 구성을 검토합니다.

    a. [Private Link 센터](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/overview)로 이동합니다.

      ![Private Link 센터](./media/private-endpoint-tsg/private-link-center.png)

    b. 왼쪽 창에서 **프라이빗 엔드포인트** 를 선택합니다.
    
      ![프라이빗 엔드포인트](./media/private-endpoint-tsg/private-endpoints.png)

    다. 진단하려는 프라이빗 엔드포인트를 필터링하고 선택합니다.

    d. 가상 네트워크 및 DNS 정보를 검토합니다.
     - 연결 상태가 **승인됨** 인지 유효성을 검사합니다.
     - VM이 프라이빗 엔드포인트를 호스트하는 가상 네트워크에 연결되어 있는지 확인합니다.
     - FQDN 정보(복사) 및 개인 IP 주소가 할당되었는지 확인합니다.
    
       ![가상 네트워크 및 DNS 구성](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. [Azure Monitor](../azure-monitor/overview.md)를 사용하여 데이터가 흐르는지 확인합니다.

    a. 프라이빗 엔드포인트 리소스에서 **메트릭을 선택합니다.**
     - **바이트 입력** 또는 **바이트 출력** 을 선택합니다. 
     - 프라이빗 엔드포인트에 연결하려고 할 때 데이터가 흐르는지 확인합니다. 약 10분 정도 지연될 수 있습니다.
    
       ![프라이빗 엔드포인트 원격 분석 확인](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Azure Network Watcher에서 **VM 연결 문제 해결** 을 사용합니다.

    a. 클라이언트 VM을 선택합니다.

    b. **연결 문제 해결** 을 선택한 다음 **아웃바운드 연결** 탭을 선택합니다.
    
      ![Network Watcher - 아웃바운드 연결 테스트](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    다. **자세한 연결 추적에 Network Watcher 사용** 을 선택합니다.
    
      ![Network Watcher - 연결 문제 해결](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. **FQDN으로 테스트** 를 선택합니다.
     - 프라이빗 엔드포인트 리소스에서 FQDN을 붙여넣습니다.
     - 포트를 제공합니다. 일반적으로 Azure Storage 또는 Azure Cosmos DB의 경우 443을 사용하고 SQL은 1336을 사용합니다.

    e. **테스트** 를 선택하고 테스트 결과를 확인합니다.
    
      ![Network Watcher - 테스트 결과](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. 테스트 결과의 DNS 확인에서는 프라이빗 엔드포인트에 할당된 것과 동일한 개인 IP 주소가 있어야 합니다.

    a. DNS 설정이 올바르지 않으면 다음 단계를 수행합니다.
     - 프라이빗 영역을 사용하는 경우: 
       - 클라이언트 VM 가상 네트워크가 프라이빗 영역과 연결되어 있는지 확인합니다.
       - 프라이빗 DNS 영역 레코드가 있는지 확인합니다. 이 레코드가 없으면 만듭니다.
     - 사용자 지정 DNS를 사용하는 경우:
       - 사용자 지정 DNS 설정을 검토하고 DNS 구성이 올바른지 확인합니다.
       지침은 [프라이빗 엔드포인트 개요: DNS 구성](./private-endpoint-overview.md#dns-configuration)을 참조하세요.

    b. NSG(네트워크 보안 그룹) 또는 사용자 정의 경로 때문에 연결에 실패한 경우:
     - NSG 아웃바운드 규칙을 검토하고 트래픽을 허용하는 적절한 아웃바운드 규칙을 만듭니다.
    
       ![NSG 아웃바운드 규칙](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. 원본 가상 머신에는 NIC 유효 경로에서 InterfaceEndpoints로 프라이빗 엔드포인트 IP 다음 홉에 대한 경로를 포함해야 합니다. 

    a. 원본 VM에서 프라이빗 엔드포인트 경로를 볼 수 없는 경우 다음을 확인합니다. 
     - 원본 VM 및 프라이빗 엔드포인트는 동일한 VNET의 일부입니다. 그렇다면 지원에 문의해야 합니다. 
     - 원본 VM과 프라이빗 엔드포인트는 서로 직접 피어되는 서로 다른 VNET의 일부입니다. 그렇다면 지원에 문의해야 합니다.
     - 원본 VM과 프라이빗 엔드포인트는 서로 직접 피어되지 않은 서로 다른 VNET의 일부인 다음, VNET 간의 IP 연결을 확인합니다.

1. 연결에서 검증된 결과가 있는 경우 연결 문제는 애플리케이션 계층의 비밀, 토큰 및 암호와 같은 다른 측면과 관련될 수 있습니다.
   - 이 경우 프라이빗 엔드포인트와 연결된 프라이빗 링크 리소스의 구성을 검토합니다. 자세한 내용은 [Azure Private Link 문제 해결 가이드](troubleshoot-private-link-connectivity.md)를 참조하세요.
   
1. 지원 티켓을 제기하기 전에 항상 범위를 좁히는 것이 좋습니다. 

    a. 원본이 온-프레미스에서 Azure의 프라이빗 엔드포인트에 연결하는 경우 문제가 발생하면 다음 연결을 시도합니다. 
      - 온-프레미스에서 다른 가상 머신으로 연결하고 온-프레미스에서 가상 네트워크에 대한 IP 연결이 있는지 확인합니다. 
      - 가상 네트워크의 가상 머신에서 프라이빗 엔드포인트로 연결합니다.
      
    b. 원본이 Azure이고 프라이빗 엔드포인트는 다른 가상 네트워크에 있는 경우 다음 연결을 시도합니다. 
      - 다른 원본에서 프라이빗 엔드포인트로 연결합니다. 이렇게 하면 가상 머신 관련 문제를 격리할 수 있습니다. 
      - 프라이빗 엔드포인트와 동일한 가상 네트워크의 일부인 가상 머신에 연결합니다.  

1. 프라이빗 엔드포인트가 Load Balancer 연결된 [Private Link 서비스에](./troubleshoot-private-link-connectivity.md) 연결된 경우 백 엔드 풀이 정상으로 보고되는지 확인합니다. Load Balancer 상태를 수정하면 프라이빗 엔드포인트에 연결하는 문제가 해결됩니다.

    - 다음으로 진행하여 관련 리소스, 메트릭 및 인사이트의 시각적 다이어그램 또는 [종속성 보기를](../azure-monitor/insights/network-insights-overview.md#dependency-view) 볼 수 있습니다.
        - Azure Monitor
        - 네트워크
        - 프라이빗 엔드포인트
        - 종속성 보기 

![Monitor-Networks](https://user-images.githubusercontent.com/20302679/134994620-0660b9e2-e2a3-4233-8953-d3e49b93e2f2.png)

![DependencyView](https://user-images.githubusercontent.com/20302679/134994637-fb8b4a1a-81d5-4723-b1c3-d7bdc72162f3.png)

9. 문제가 여전히 해결되지 않고 연결 문제가 계속 발생하는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

 * [업데이트된 서브넷(Azure Portal)에서 프라이빗 엔드포인트 만들기](./create-private-endpoint-portal.md)
 * [Azure Private Link 문제 해결 가이드](troubleshoot-private-link-connectivity.md)

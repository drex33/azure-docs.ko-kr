---
title: Azure DDoS Protection 표준에 대한 DDoS 보호 원격 분석 보기 및 구성
description: Azure DDoS Protection 표준에 대한 DDoS 보호 원격 분석을 보고 구성하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 6dc086aae55f3b35dbb7dc787df6a65b7ade108f
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674214"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 보기 및 구성

Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. DDoS 공격 기간 동안 상세 메트릭을 비롯한 다양한 원격 분석이 Azure Monitor를 통해 노출됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. Azure Monitor 진단 인터페이스를 통해 로깅을 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk(Azure Event Hubs), OMS Log Analytics 및 Azure Storage와 통합하면 고급 분석을 사용할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * DDoS 보호 원격 분석 보기
> * DDoS 완화 정책 보기
> * DDoS 보호 원격 분석 유효성 검사 및 테스트

### <a name="metrics"></a>메트릭

메트릭 이름은 다양한 패킷 유형과 바이트 및 패킷을 나타내며, 각 메트릭에서 태그 이름의 기본 구조는 다음과 같습니다.

- **Dropped 태그 이름**(예: **Inbound Packets Dropped DDoS**): DDoS 보호 시스템에서 삭제/스크럽한 패킷의 수입니다.
- **Forwarded 태그 이름**(예: **Inbound Packets Forwarded DDoS**): DDoS 시스템에서 대상 VIP로 전달한 패킷의 수입니다(필터링되지 않은 트래픽).
- **태그 이름 없음**(예: **Inbound Packets DDoS**): 스크럽 시스템으로 들어온 패킷의 총 수입니다(삭제 및 전달된 패킷의 합계를 나타냄).

> [!NOTE]
> **집계** 에 대한 여러 옵션이 Azure Portal에 표시되지만 아래 표에 나열된 집계 유형만 각 메트릭에 대해 지원됩니다. 이러한 혼란에 대해 사과드립니다. 이 문제를 해결하기 위해 노력하고 있습니다.

Azure DDoS Protection 표준에는 다음 [메트릭](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)을 사용할 수 있습니다. 이러한 메트릭은 진단 설정을 통해 내보낼 수도 있습니다([DDoS 진단 로깅 보기 및 구성](diagnostic-logging.md) 참조).


| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 형식 | Description |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | 인바운드 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대 | 인바운드 바이트가 삭제된 DDoS| 
| BytesForwardedDDoS | 인바운드 바이트가 전달된 DDoS | 초당 바이트 수 | 최대 | 인바운드 바이트가 전달된 DDoS |
| BytesInDDoS | 인바운드 바이트 DDoS | 초당 바이트 수 | 최대 | 인바운드 바이트 DDoS |
| DDoSTriggerSYNPackets | DDoS 완화를 트리거하는 인바운드 SYN 패킷 | 초당 개수 | 최대 | DDoS 완화를 트리거하는 인바운드 SYN 패킷 |
| DDoSTriggerTCPPackets | DDoS 완화를 트리거하는 인바운드 TCP 패킷 | 초당 개수 | 최대 | DDoS 완화를 트리거하는 인바운드 TCP 패킷 |
| DDoSTriggerUDPPackets | DDoS 완화를 트리거하는 인바운드 UDP 패킷 | 초당 개수 | 최대 | DDoS 완화를 트리거하는 인바운드 UDP 패킷 |
| IfUnderDDoSAttack | DDoS 공격 진행 여부 | 개수 | 최대 | DDoS 공격 진행 여부 |
| PacketsDroppedDDoS | 인바운드 패킷이 삭제된 DDoS | 초당 개수 | 최대 | 인바운드 패킷이 삭제된 DDoS |
| PacketsForwardedDDoS | 인바운드 패킷이 전달된 DDoS | 초당 개수 | 최대 | 인바운드 패킷이 전달된 DDoS |
| PacketsInDDoS | 인바운드 패킷 DDoS | 초당 개수 | 최대 | 인바운드 패킷 DDoS |
| TCPBytesDroppedDDoS | 인바운드 TCP 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대 | 인바운드 TCP 바이트가 삭제된 DDoS |
| TCPBytesForwardedDDoS | 인바운드 TCP 바이트가 전달된 DDoS | 초당 바이트 수 | 최대 | 인바운드 TCP 바이트가 전달된 DDoS |
| TCPBytesInDDoS | 인바운드 TCP 바이트 DDoS | 초당 바이트 수 | 최대 | 인바운드 TCP 바이트 DDoS |
| TCPPacketsDroppedDDoS | 인바운드 TCP 패킷이 삭제된 DDoS | 초당 개수 | 최대 | 인바운드 TCP 패킷이 삭제된 DDoS |
| TCPPacketsForwardedDDoS | 인바운드 TCP 패킷이 전달된 DDoS | 초당 개수 | 최대 | 인바운드 TCP 패킷이 전달된 DDoS |
| TCPPacketsInDDoS | 인바운드 TCP 패킷 DDoS | 초당 개수 | 최대 | 인바운드 TCP 패킷 DDoS |
| UDPBytesDroppedDDoS | 인바운드 UDP 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대 | 인바운드 UDP 바이트가 삭제된 DDoS |
| UDPBytesForwardedDDoS | 인바운드 UDP 바이트가 전달된 DDoS | 초당 바이트 수 | 최대 | 인바운드 UDP 바이트가 전달된 DDoS |
| UDPBytesInDDoS | 인바운드 UDP 바이트 DDoS | 초당 바이트 수 | 최대 | 인바운드 UDP 바이트 DDoS |
| UDPPacketsDroppedDDoS | 인바운드 UDP 패킷이 삭제된 DDoS | 초당 개수 | 최대 | 인바운드 UDP 패킷이 삭제된 DDoS |
| UDPPacketsForwardedDDoS | 인바운드 UDP 패킷이 전달된 DDoS | 초당 개수 | 최대 | 인바운드 UDP 패킷이 전달된 DDoS |
| UDPPacketsInDDoS | 인바운드 UDP 패킷 DDoS | 초당 개수 | 최대 | 인바운드 UDP 패킷 DDoS |

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 자습서의 단계를 완료하려면 먼저 [Azure DDoS 표준 보호 계획](manage-ddos-protection.md)을 만들고 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정해야 합니다.
- DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service Environment를 제외하고 [Azure 서비스용 가상 네트워크](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)(백 엔드 가상 머신이 가상 네트워크에 있는 Azure Load Balancer 포함)에 나열된 Resource Manager(클래식 아님)를 통해 배포된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.  

## <a name="view-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 보기

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. TCP SYN용 [완화 트리거](#view-ddos-mitigation-policies)인 TCP 및 UDP는 편한 시간에 사용할 수 있지만, 다른 원격 분석은 공용 IP 주소가 완화된 경우에만 사용할 수 있습니다. 

세 가지 리소스 종류인 DDoS 보호 계획, 가상 네트워크, 공용 IP 주소를 통해 보호된 공용 IP 주소에 대한 DDoS 원격 분석을 볼 수 있습니다.

### <a name="ddos-protection-plan"></a>DDoS 보호 계획
1. [Azure Portal](https://portal.azure.com/)에 로그인하고 DDoS 보호 계획으로 이동합니다.
2. **모니터링** 아래에서 **메트릭** 을 선택합니다.
3. **범위** 를 선택합니다. 로깅할 공용 IP 주소가 포함된 **구독** 을 선택하고 **리소스 종류** 로 **공용 IP 주소** 를 선택한 다음, 메트릭을 로깅할 특정 공용 IP 주소를 선택한 후, **적용** 을 선택합니다.
4. **집계** 유형을 **최대** 로 선택합니다. 

### <a name="virtual-network"></a>가상 네트워크
1. [Azure Portal](https://portal.azure.com/)에 로그인하고 DDoS 보호가 사용으로 설정된 가상 네트워크로 이동합니다.
2. **모니터링** 아래에서 **메트릭** 을 선택합니다.
3. **범위** 를 선택합니다. 로깅할 공용 IP 주소가 포함된 **구독** 을 선택하고 **리소스 종류** 로 **공용 IP 주소** 를 선택한 다음, 메트릭을 로깅할 특정 공용 IP 주소를 선택한 후, **적용** 을 선택합니다.
4. **집계** 유형을 **최대** 로 선택합니다.
5. **필터 추가** 를 선택합니다. **속성** 에서 **보호된 IP 주소** 를 선택하고 연산자를 **=** 로 설정해야 합니다. **값** 에는 DDoS 보호를 사용하여 보호되는 가상 네트워크와 연결된 공용 IP 주소의 드롭다운이 표시됩니다. 

![DDoS 진단 설정](./media/ddos-attack-telemetry/vnet-ddos-metrics.png)

### <a name="public-ip-address"></a>공용 IP 주소
1. [Azure Portal](https://portal.azure.com/)에 로그인하고 공용 IP 주소로 이동합니다.
2. **모니터링** 아래에서 **메트릭** 을 선택합니다.
3. **집계** 유형을 **최대** 로 선택합니다.

## <a name="view-ddos-mitigation-policies"></a>DDoS 완화 정책 보기

DDoS 보호 표준은 DDoS 보호가 사용하도록 설정된 가상 네트워크에서 보호되는 리소스의 각 공용 IP 주소에 대해 자동 조정된 세 가지 완화 정책(TCP SYN, TCP 및 UDP)을 적용합니다. 다음 그림과 같이 **DDoS 완화를 트리거하는 인바운드 TCP 패킷** 및 **집계** 유형이 '최대'인 **DDoS 완화를 트리거하는 인바운드 UDP 패킷** 메트릭을 선택하여 정책 임계값을 볼 수 있습니다.

![완화 정책 보기](./media/manage-ddos-protection/view-mitigation-policies.png)

## <a name="validate-and-test"></a>유효성 검사 및 테스트

DDoS 공격을 시뮬레이션하여 DDoS 보호 원격 분석의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](test-through-simulations.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

- DDoS 보호 메트릭에 대한 경고 구성
- DDoS 보호 원격 분석 보기
- DDoS 완화 정책 보기
- DDoS 보호 원격 분석 유효성 검사 및 테스트

공격 완화 보고서 및 흐름 로그를 구성하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [DDoS 진단 로깅 보기 및 구성](diagnostic-logging.md)
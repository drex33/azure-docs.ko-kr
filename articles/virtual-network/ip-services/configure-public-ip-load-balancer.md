---
title: 부하 분산 장치를 사용하여 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: Azure Load Balancer에서 공용 IP 주소를 사용하고 구성을 변경하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 68c400b2ec8ef85db494b85a04f8e70c32e0b6cb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373038"
---
# <a name="manage-a-public-ip-address-with-a-load-balancer"></a>부하 분산 장치를 사용하여 공용 IP 주소 관리

공용 부하 분산 장치는 TCP 및 UDP 트래픽을 백 엔드 풀에 분산하는 계층 4 솔루션입니다. 기본 및 표준 SKU를 부하 분산 장치에 사용할 수 있습니다. 이러한 SKU는 공용 IP 주소의 기본 및 표준 SKU에 해당합니다.

부하 분산 장치와 연결된 공용 IP는 인터넷 연결 프런트 엔드 IP 구성의 역할을 합니다. 프런트 엔드는 백 엔드 풀의 리소스에 액세스하는 데 사용됩니다. 프런트 엔드 IP는 백 엔드 풀의 멤버가 인터넷으로 송신하는 데 사용할 수 있습니다. 

기본 SKU Azure Load Balancer는 가용성 옵션 및 기능 세트가 제한되어 있습니다. 프로덕션 워크로드에는 표준 SKU 부하 분산 장치와 IP 주소의 조합을 배포하는 것이 좋습니다. 표준 SKU IP 주소는 지원되는 지역에서 가용성 영역을 지원합니다. 

이 문서에서는 구독에서 기존 공용 IP 주소를 사용하여 부하 분산 장치를 만드는 방법을 알아봅니다. 

부하 분산 장치에 연결된 현재 공용 IP를 변경하는 방법을 알아봅니다. 

아웃바운드 백 엔드 풀의 프런트 엔드 구성을 공용 IP 접두사로 변경하는 방법을 알아봅니다.  

마지막으로, 이 문서에서는 부하 분산 장치에서 공용 IP 및 공용 IP 접두사를 사용할 때의 고유한 특징을 검토합니다. 

> [!NOTE]
> 이 문서의 예제에서는 표준 SKU 부하 분산 장치와 공용 IP를 사용합니다. 기본 SKU 부하 분산 장치의 경우 부하 분산 장치와 공용 IP 리소스를 만들 때 선택하는 SKU를 제외한 나머지 절차는 동일합니다. 기본 부하 분산 장치는 아웃바운드 규칙 또는 공용 IP 접두사를 지원하지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독에 두 개의 표준 SKU 공용 IP 주소가 있습니다. IP 주소를 리소스와 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md)을 참조하세요.
    - 이 문서에 나오는 예제의 목적에 맞게 새 공용 IP 주소의 이름을 **myStandardPublicIP-1** 및 **myStandardPublicIP-2** 로 지정합니다.
- 구독의 공용 IP 접두사. 공용 IP 주소 접두사를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 공용 IP 주소 접두사 만들기](../../virtual-network/create-public-ip-prefix-portal.md)를 참조하세요.
    - 이 문서에 나오는 예제의 목적에 맞게 새 공용 IP 접두사를 **myPublicIPPrefixOutbound** 로 지정합니다.

## <a name="create-load-balancer-existing-public-ip"></a>부하 분산 장치 만들기 기존 공용 IP

이 섹션에서는 표준 SKU 부하 분산 장치를 만듭니다. 필수 구성 요소에서 만든 IP 주소를 부하 분산 장치의 프런트 엔드 IP로 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다.

3. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **부하 분산 장치 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **myResourceGroupIP** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 속성 | **myLoadBalancer** 를 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | Type | **공용**(기본값)을 그대로 둡니다. |
    | SKU | 기본값인 **표준** 을 그대로 둡니다. |
    | 계층 | **지역**(기본값)을 그대로 둡니다. |
    | **공용 IP 주소** |   |
    | 공용 IP 주소 | **기존 항목 사용** 을 선택합니다. |
    | 공용 IP 주소 선택 | **myStandardPublicIP-1** 을 선택합니다. |
    | 공용 IPv6 주소 추가 | **아니요**(기본값)를 그대로 둡니다. |

6. **검토 + 만들기** 탭을 선택하거나 파란색 **검토 + 만들기** 단추를 선택합니다. 

7. **만들기** 를 선택합니다.

> [!NOTE]
> 이 방법은 부하 분산 장치를 간단하게 배포하는 방법입니다. 고급 구성 및 설정에 대한 내용은 [빠른 시작: Azure Portal을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)를 참조하세요.
>
> Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](../../load-balancer/load-balancer-overview.md)을 참조하세요.

## <a name="change-or-remove-public-ip-address"></a>공용 IP 주소 변경 또는 제거

이 섹션에서는 Azure Portal에 로그인하고 부하 분산 장치의 IP 주소를 변경합니다. 

Azure Load Balancer의 IP 주소가 프런트 엔드와 연결되어 있어야 합니다. 수신 및 송신 트래픽에 대한 프런트 엔드로 별도의 공용 IP 주소를 사용할 수 있습니다. 

IP를 변경하려면 이전에 만든 새 공용 IP 주소를 부하 분산 장치 프런트 엔드와 연결합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다.

3. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

4. **Load Balancer** 에서 **myLoadBalancer** 또는 변경하려는 부하 분산 장치를 선택합니다.

5. **myLoadBalancer** 의 설정에서 **프런트 엔드 IP 구성** 을 선택합니다.

6. **프런트 엔드 IP 구성** 에서 **LoadBalancerFrontend** 또는 해당하는 부하 분산 장치 프런트 엔드를 선택합니다.

7. 부하 분산 장치 프런트 엔드 구성의 **공용 IP 주소** 에서 **myStandardPublicIP-2** 를 선택합니다.

8. **저장** 을 선택합니다.

9. 부하 분산 장치 프런트 엔드에 새 IP 주소 **myStandardPublicIP-2** 가 표시되는지 확인합니다.

    > [!NOTE]
    > 이러한 절차는 지역 간 부하 분산 장치에 유효합니다. 지역 간 부하 분산 장치에 대한 자세한 내용은 **[지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)** 를 참조하세요.


## <a name="add-public-ip-prefix"></a>공용 IP 접두사 추가

표준 부하 분산 장치는 SNAT(Source Network Address Translation)에 대한 아웃바운드 규칙을 지원합니다. SNAT는 백 엔드 풀의 멤버에 대한 인터넷 송신을 허용합니다. 공용 IP 접두사는 아웃바운드 연결에 여러 IP 주소를 허용하여 SNAT의 확장성을 넓힙니다. 

여러 IP를 허용하면 SNAT 포트 고갈이 방지됩니다. 각 프런트 엔드 IP는 부하 분산 장치에서 사용할 수 있는 64,000개의 임시 포트를 제공합니다. 자세한 내용은 [아웃바운드 규칙](../../load-balancer/outbound-rules.md)을 참조하세요.

이 섹션에서는 아웃바운드 연결에 사용되는 프런트 엔드 구성을 공용 IP 접두사로 변경합니다.

> [!IMPORTANT]
> 이 섹션을 완료하려면 아웃바운드 프런트 엔드 구성 및 아웃바운드 규칙이 배포된 부하 분산 장치가 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다.

3. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

4. **Load Balancer** 에서 **myLoadBalancer** 또는 변경하려는 부하 분산 장치를 선택합니다.

5. **myLoadBalancer** 의 설정에서 **프런트 엔드 IP 구성** 을 선택합니다.

6. **프런트 엔드 IP 구성** 에서 **LoadBalancerFrontendOutbound** 또는 해당하는 부하 분산 장치 아웃바운드 프런트 엔드를 선택합니다.

7. **IP 유형** 으로 **공용 IP 접두사** 를 선택합니다.

8. **공용 IP 접두사** 에서 이전에 만든 공용 IP 접두사 **myPublicIPPrefixOutbound** 를 선택합니다.

9. **저장** 을 선택합니다.

10. **프런트 엔드 IP 구성** 에서 IP 접두사가 아웃바운드 프런트 엔드 구성에 추가되었는지 확인합니다.

## <a name="more-information"></a>자세한 정보

* 영역 간 부하 분산 장치는 여러 지역에 걸쳐 존재할 수 있는 특수 한 유형의 표준 공용 부하 분산 장치입니다. 지역 간 부하 분산 장치의 프런트 엔드는 전역 계층 옵션인 표준 SKU 공용 IP에만 사용할 수 있습니다. 지역 간 부하 분산 장치의 프런트 엔드 IP로 전송된 트래픽은 지역 공용 부하 분산 장치에 분산됩니다. 지역 프런트 엔드 IP는 지역 간 부하 분산 장치의 백 엔드 풀에 포함됩니다. 자세한 내용은 [지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)를 참조하세요.

* 기본적으로 공용 부하 분산 장치는 동일한 백 엔드 포트에 여러 부하 분산 규칙을 사용하는 것을 허용하지 않습니다. 동일한 백 엔드 포트에 여러 규칙 구성이 필요한 경우에는 부동 IP 옵션을 부하 분산 규칙에 사용하도록 설정합니다. 이 설정은 백 엔드 풀로 전송된 트래픽의 대상 IP 주소를 덮어씁니다. 부동 IP를 사용하지 않으면 대상은 백 엔드 풀 개인 IP가 됩니다. 부동 IP를 사용하면 대상 IP는 부하 분산 장치 프런트 엔드 공용 IP가 됩니다. 이 트래픽을 올바르게 받으려면 백 엔드 인스턴스의 네트워크 구성에서 이 공용 IP를 구성해야 합니다. 인스턴스에 프런트 엔드 IP 주소가 구성된 루프백 인터페이스입니다. 자세한 내용은 [Azure Load Balancer 부동 IP 구성](../../load-balancer/load-balancer-floating-ip.md)을 참조하세요.

* 부하 분산 장치가 설정되었으면 백 엔드 풀의 멤버에게 종종 인스턴스 수준 공용 IP를 할당할 수 있습니다. 이 아키텍처를 사용하는 경우 이러한 IP에 직접 트래픽을 전송하면 부하 분산 장치가 무시됩니다. 

* 표준 공용 부하 분산 장치와 공용 IP 주소에는 keepalive를 수신할 때까지 연결을 열어 놓을 수 있는 시간에 대한 TCP 시간 제한 값이 할당될 수 있습니다. 공용 IP가 부하 분산 장치 프런트 엔드로 할당되면 IP의 시간 제한 값이 우선적으로 적용됩니다. 이 설정은 부하 분산 장치에 대한 인바운드 연결에만 적용됩니다. 자세한 내용은 [Load Balancer TCP 초기화](../../load-balancer/load-balancer-tcp-reset.md)를 참조하세요.

## <a name="caveats"></a>제한 사항

* 표준 퍼블릭 부하 분산 장치는 표준 SKU 고정 IPv6 주소를 프런트 엔드 공용 IP 또는 공용 IP 접두사로 사용할 수 있습니다.  모든 배포는 IPv4 및 IPv6 프런트 엔드가 모두 포함된 이중 스택이어야 합니다. NAT64 변환은 사용할 수 없습니다. 자세한 내용은 [Azure에서 IPv6 이중 스택 애플리케이션 배포 - PowerShell](../../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)을 참조하세요(기본 퍼블릭 부하 분산 장치는 기본 SKU 동적 IPv6 주소를 프런트 엔드 공용 IP로 사용할 수 있습니다.)

* 공용 부하 분산 장치에 여러 프런트 엔드가 할당된 경우 특정 IP에서 송신하도록 특정 백 엔드 인스턴스의 흐름을 할당하는 방법이 없습니다.  자세한 내용은 [Azure Load Balancer의 다중 프런트 엔드](../../load-balancer/load-balancer-multivip-overview.md)를 참조하세요.
## <a name="next-steps"></a>다음 단계

이 문서에서는 부하 분산 장치를 만들고 기존 공용 IP를 사용하는 방법을 알아봤습니다. 

부하 분산 장치 프런트 엔드 구성에서 IP 주소를 바꿨습니다. 

마지막으로, 공용 IP 접두사를 사용하도록 아웃바운드 프런트 엔드 구성을 변경했습니다.

- Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](../../load-balancer/load-balancer-overview.md)를 참조하세요.
- Auzre의 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소](../../virtual-network/public-ip-addresses.md)를 참조하세요.

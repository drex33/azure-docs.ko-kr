---
title: NAT 게이트웨이를 사용하여 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: Azure Virtual Network NAT에서 공용 IP 주소를 사용하고 구성을 변경하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 2fd41f3459469474c100657747c4873ee99dc057
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373037"
---
# <a name="manage-a-public-ip-address-with-a-nat-gateway"></a>NAT 게이트웨이를 사용하여 공용 IP 주소 관리

Azure NAT Gateway 리소스는 가상 네트워크의 서브넷에서 아웃바운드 인터넷 연결을 사용합니다. NAT 게이트웨이 가상 네트워크 서브넷에 배포된 리소스는 표준 SKU여야 합니다. 기본 SKU 리소스를 사용하여 가상 네트워크 서브넷에 NAT 게이트웨이를 배포할 수 없습니다. 

NAT 게이트웨이는 NAT 게이트웨이를 사용하여 리소스에서 SNAT(Source Network Address Translation) 연결을 사용합니다. NAT 게이트웨이는 표준 SKU 공용 IP 주소와 공용 IP 접두사를 지원합니다. 모든 조합이 지원되지만 지정된 IP 수는 16개를 초과할 수 없습니다. IP 주소나 IP 접두사를 추가하면 NAT 게이트웨이를 사용하여 리소스에서 SNAT 연결을 확장할 수 있습니다. 

이 문서에서는 구독의 기존 공용 IP를 사용하여 NAT 게이트웨이를 만드는 방법을 알아봅니다. 

NAT 게이트웨이에 연결된 현재 공용 IP를 변경하는 방법을 알아봅니다. 

마지막으로, IP 구성을 공용 IP 주소에서 IP 접두사로 변경합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독에 두 개의 표준 SKU 공용 IP 주소가 있습니다. IP 주소를 리소스와 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md)을 참조하세요.
    - 이 문서에 나오는 예제의 목적에 맞게 새 공용 IP 주소의 이름을 **myStandardPublicIP-1** 및 **myStandardPublicIP-2** 로 지정합니다.
- 구독의 공용 IP 접두사. 공용 IP 주소 접두사를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 공용 IP 주소 접두사 만들기](../../virtual-network/create-public-ip-prefix-portal.md)를 참조하세요.
    - 이 문서에 나오는 예의 목적에 맞게 새 공용 IP 접두사 이름을 **myPublicIPPrefixNAT** 로 지정합니다.

## <a name="create-nat-gateway-existing-public-ip"></a>공용 IP가 있는 NAT 게이트웨이 만들기

이 섹션에서는 NAT 게이트웨이 리소스를 만듭니다. 필수 구성 요소에서 만든 IP 주소를 NAT 게이트웨이의 공용 IP로 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **NAT 게이트웨이** 를 입력합니다.

3. 검색 결과에서 **NAT 게이트웨이** 를 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **NAT(Network Address Translation) 게이트웨이 만들기** 에 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **myResourceGroupNAT** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 이름 | **myNATgateway** 를 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 가용성 영역 | **없음** 의 기본값을 그대로 둡니다. |
    | 유휴 상태 시간 제한 | 기본값 **4** 를 그대로 둡니다. |

6. **아웃바운드 IP** 탭을 선택하거나 **다음: 아웃바운드 IP** 를 선택합니다.

7. **아웃바운드 IP** 탭의 **공용 IP 주소** 에 **myStandardPublicIP-1** 을 선택합니다.

6. **검토 + 만들기** 탭을 선택하거나 파란색 **검토 + 만들기** 단추를 선택합니다. 

7. **만들기** 를 선택합니다.

> [!NOTE]
> NAT 게이트웨이의 간단한 배포입니다. 고급 구성과 설정은 [자습서: Azure Portal을 사용하여 NAT 게이트웨이 만들기](../nat-gateway/tutorial-create-nat-gateway-portal.md)를 참조하세요.
>
> Azure Virtual Network NAT에 대한 자세한 내용은 [Azure Virtual Network NAT란?](../nat-gateway/nat-overview.md)을 참조하세요.

## <a name="change-or-remove-public-ip-address"></a>공용 IP 주소 변경 또는 제거

이 섹션에서는 Azure Portal에 로그인하고 NAT 게이트웨이의 IP 주소를 변경합니다. 

IP를 변경하려면 이전에 만든 새 공용 IP 주소를 NAT 게이트웨이와 연결합니다. NAT 게이트웨이에 IP 주소가 최소 하나 이상 할당되어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **NAT 게이트웨이** 를 입력합니다.

3. 검색 결과에서 **NAT 게이트웨이** 를 선택합니다.

4. **NAT 게이트웨이** 에서 **myNATgateway** 또는 변경하려는 NAT 게이트웨이를 선택합니다.

5. **myNATgateway** 설정에서 **아웃바운드 IP** 를 선택합니다.

6. **아웃바운드 IP** 에서 **공용 IP 주소** 옆에 있는 **변경** 을 선택합니다.

7. 현재 IP 주소를 바꾸거나 기존 주소를 추가할 수 있습니다. **공용 IP 주소** 옆에 있는 **공용 IP 주소 및 접두사 관리** 에서 **myStandardPublicIP-2** 를 선택합니다.

8. **확인** 을 선택합니다.

9. **myStandardPublicIP-2** 가 공용 IP 주소에 추가되었는지 확인합니다. 필요한 경우 휴지통을 선택하여 이미 할당된 IP 주소를 삭제할 수 있습니다.

10. **저장** 을 선택합니다.

## <a name="add-public-ip-prefix"></a>공용 IP 접두사 추가

공용 IP 접두사는 NAT 게이트웨이에서 아웃바운드 연결에 사용되는 SNAT의 확장성을 확장합니다. 공용 IP 접두사는 SNAT 포트 소모를 방지합니다. 각 IP에서 사용할 수 있는 사용 후 삭제 포트를 64,000개 제공합니다.

> [!NOTE] 
> NAT 게이트웨이에 공용 IP 접두사를 할당할 경우 전체 범위가 사용됩니다. 

이 섹션에서는 이전에 만든 공용 IP 접두사를 사용하도록 아웃바운드 IP 구성을 변경합니다.

> [!NOTE]
> NAT 게이트웨이와 연결된 단일 IP 주소를 제거하고 다시 사용하거나 NAT 게이트웨이에 연결하여 아웃바운드 SNAT 포트를 늘릴 수 있습니다. NAT 게이트웨이는 아웃바운드 IP 구성에서 공용 IP와 접두사 조합을 지원합니다. 주소가 16개 있는 공용 IP 접두사를 만든 경우 접두사를 추가하기 전에 단일 공용 IP를 제거하고 구성을 저장합니다. 할당된 IP 수는 16개를 초과할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **NAT 게이트웨이** 를 입력합니다.

3. 검색 결과에서 **NAT 게이트웨이** 를 선택합니다.

4. **NAT 게이트웨이** 에서 **myNATgateway** 또는 변경하려는 NAT 게이트웨이를 선택합니다.

5. **myNATgateway** 설정에서 **아웃바운드 IP** 를 선택합니다.

6. **아웃바운드 IP** 에서 **공용 IP 접두사** 옆에 있는 **변경** 을 선택합니다.

7. **myPublicIPPrefixNAT** 또는 사용 중인 접두사를 선택합니다.

8. **확인** 을 선택합니다.

9. **myPublicIPPrefixNAT** 가 공용 IP 접두사에 추가되었는지 확인합니다.

10. **저장** 을 선택합니다.

## <a name="more-information"></a>자세한 정보

* NAT 게이트웨이를 사용하여 가상 머신을 가상 네트워크에 배포할 경우 NAT 게이트웨이로 전송된 모든 수신 트래픽은 NAT 게이트웨이를 통해 송신됩니다. 표준 공용 부하 분산 장치와 함께 NAT Gateway를 사용하는 경우 NAT 게이트웨이 공용 IP 주소로 전송된 모든 수신 트래픽은 NAT Gateway를 통해 송신됩니다. 

    > [!NOTE] 
    > 부하 분산 규칙 또는 아웃바운드 규칙의 모든 아웃바운드 구성이 NAT 게이트웨이로 대체됩니다. 또한 부하 분산 장치 백 엔드 풀의 멤버는 아웃바운드 연결에 NAT 게이트웨이를 사용합니다. 자세한 내용은 [NAT 게이트웨이 리소스를 사용하여 가상 네트워크 설계](../nat-gateway/nat-gateway-resource.md)를 참조하세요.

* NAT 게이트웨이와 공용 IP 주소에는 keepalive를 수신할 때까지 연결을 열어 놓을 수 있는 시간에 대한 TCP 시간 제한 값이 할당될 수 있습니다.  공용 IP가 NAT Gateway에 할당된 경우 IP의 시간 제한 값이 우선적으로 적용됩니다.  자세한 내용은 [NAT 게이트웨이 리소스를 사용하여 가상 네트워크 설계](../nat-gateway/nat-gateway-resource.md#timers)를 참조하세요.

## <a name="caveats"></a>제한 사항

* 현재 공용 IPv6 주소와 공용 IPv6 접두사는 NAT 게이트웨이에서 지원되지 않습니다. IPv6 접두사를 사용하여 NAT 게이트웨이를 가상 네트워크 서브넷에 배포할 수 없습니다. 자세한 내용은 [Azure Virtual Network NAT 연결 문제 해결](../nat-gateway/troubleshoot-nat.md)을 참조하세요.
## <a name="next-steps"></a>다음 단계

이 문서에서 부하 NAT 게이트웨이를 만들고 기존 공용 IP를 사용하는 방법을 알아봤습니다. NAT 게이트웨이 아웃바운드 IP 구성에서 IP 주소를 대체했습니다. 마지막으로, 공용 IP 접두사를 사용하도록 아웃바운드 IP 구성을 변경했습니다.

- Azure Virtual Network NAT에 대한 자세한 내용은 [Azure Virtual Network NAT란?](../nat-gateway/nat-overview.md)을 참조하세요.
- Azure의 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소](../../virtual-network/public-ip-addresses.md)를 참조하세요.
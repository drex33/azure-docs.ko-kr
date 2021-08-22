---
title: 아웃바운드 전용 부하 분산 장치 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 아웃바운드 NAT를 사용하여 내부 부하 분산 장치를 만드는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: 482effeb373b17bb100e012a72d2c1a60cbfc2a4
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033176"
---
# <a name="outbound-only-load-balancer-configuration"></a>아웃바운드 전용 부하 분산 장치 구성

내부 및 외부 표준 부하 분산 장치의 조합을 사용하여 내부 부하 분산 장치 뒤에 있는 VM용 아웃바운드 연결을 만듭니다. 

이 구성은 내부 부하 분산 장치 시나리오용 아웃바운드 NAT를 제공하여 백 엔드 풀에 관한 "송신 전용" 설정을 생성합니다.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*사진: 송신 전용 부하 분산 장치 구성*

필요한 단계:

1. 베스천 호스트를 포함하는 가상 네트워크 만들기.
2. 개인 IP를 사용하여 가상 머신 만들기.
3. 내부 및 퍼블릭 표준 부하 분산 장치를 만듭니다.
4. 두 부하 분산 장치에 백 엔드 풀을 추가하고 각 풀에 VM을 넣습니다.
5. 베스천 호스트를 통해 VM에 연결하고 다음을 수행합니다.
    1. 아웃바운드 연결 테스트, 
    2. 퍼블릭 부하 분산 장치에서 아웃바운드 규칙을 구성합니다.
    3. 아웃바운드 연결을 다시 테스트합니다.

## <a name="create-virtual-network-and-virtual-machine"></a>가상 네트워크 및 가상 머신 만들기

두 서브넷이 있는 가상 네트워크 만들기:

* 기본 서브넷
* 베스천 서브넷

새 가상 네트워크에서 가상 머신을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal에 [로그인](https://portal.azure.com)합니다.

2. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크** 를 차례로 선택하거나, 검색 상자에서 **가상 네트워크** 를 검색합니다.

2. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | 구독     | Azure 구독 선택                                  |
    | 리소스 그룹   | **새로 만들기** 를 선택합니다. </br> **myResourceGroupLB** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **myVNet** 입력                                    |
    | 지역           | **미국 동부 2** 선택 |

3. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

4. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **10.1.0.0/16** 입력 |

5. **서브넷 이름** 아래에서 **기본값** 이라는 단어를 선택합니다.

6. **서브넷 편집** 에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **myBackendSubnet** 입력 |
    | 서브넷 주소 범위 | **10.1.0.0/24** 입력 |

7. **저장** 을 선택합니다.

8. **보안** 탭을 선택합니다.

9. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.1.1.0/24** 입력 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |


8. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

9. **만들기** 를 선택합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 차례로 선택합니다. 
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | 구독 | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroupLB** 를 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **미국 동부 2** 선택 |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 를 선택합니다. |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-----|------------|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVNet** |
    | 서브넷 | **myBackendSubnet** |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **없음** 을 선택합니다.|
    | 기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치하시겠습니까? | **아니요** 를 선택합니다. |
   
5. **관리** 탭을 선택하거나 **다음** > **관리** 를 선택합니다.

6. **관리** 탭에서 다음을 선택하거나 입력합니다.
    
    | 설정 | 값 |
    |-|-|
    | **Monitoring** |  |
    | 부트 진단 | **끄기** 를 선택합니다. |
   
7. **검토 + 만들기** 를 선택합니다. 
  
8. 설정을 검토한 다음, **만들기** 를 선택합니다.

## <a name="create-load-balancers-and-test-connectivity"></a>부하 분산 장치 만들기 및 연결 테스트

Azure Portal을 사용하여 다음을 만듭니다.

* 내부 부하 분산 장치
* 공용 부하 분산 장치
 
만든 VM을 각각의 백 엔드 풀에 추가합니다.  그런 다음 VM에서의 아웃바운드 연결만 허용하도록 구성을 설정하고 이전과 그 이후에 테스트합니다.

### <a name="create-internal-load-balancer"></a>내부 부하 분산 장치 만들기

1. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Load Balancer** 를 선택합니다.

2. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Subscription               | 구독을 선택합니다.    |    
    | Resource group         | 이전 단계에서 만든 **myResourceGroupLB** 를 선택합니다.|
    | Name                   | **myInternalLoadBalancer** 를 입력합니다.                                   |
    | 지역         | **미국 동부 2** 를 선택합니다.                                        |
    | Type          | **내부** 를 선택합니다.                                        |
    | SKU           | **표준** 선택 |
    | 가상 네트워크 | 이전 단계에서 만든 **myVNet** 을 선택합니다. |
    | 서브넷  | 이전 단계에서 만든 **myBackendSubnet** 을 선택합니다. |
    | IP 주소 할당 | **동적** 을 선택합니다. |

3. 나머지 설정에는 기본값을 적용한 다음, **검토 + 만들기** 를 선택합니다.

4. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.   

### <a name="create-public-load-balancer"></a>퍼블릭 부하 분산 장치 만들기

1. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Load Balancer** 를 선택합니다.

2. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Subscription               | 구독을 선택합니다.    |    
    | Resource group         | **새로 만들기** 를 선택하고, 텍스트 상자에서 **myResourceGroupLB** 를 입력합니다.|
    | Name                   | **myPublicLoadBalancer** 를 입력합니다.                                   |
    | 지역         | **미국 동부 2** 를 선택합니다.                                        |
    | Type          | **공용** 을 선택합니다.                                        |
    | SKU           | **표준** 선택 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. |
    | 공용 IP 주소 이름 | 텍스트 상자에 **myFrontendIP** 를 입력합니다.|
    | 가용성 영역 | **영역 중복** 을 선택합니다. |
    | 공용 IPv6 주소 추가 | **아니오** 를 선택합니다. |

3. 나머지 설정에는 기본값을 적용한 다음, **검토 + 만들기** 를 선택합니다.

4. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.   
   
### <a name="create-internal-backend-address-pool"></a>내부 백 엔드 주소 풀 만들기

백 엔드 주소 풀 **myInternalBackendPool** 을 만듭니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myInternalLoadBalancer** 를 선택합니다.

2. **설정** 에서 **백 엔드 풀** 을 선택한 다음, **추가** 를 선택합니다.

3. **백 엔드 풀 추가** 페이지에서 이름에 백 엔드 풀의 이름인 **myInternalBackendPool** 을 입력합니다.
 
4. **가상 네트워크** 에서 **myVNet** 을 선택합니다.

5. **가상 머신** 에서 **추가** 를 선택하고 **myVM** 을 선택합니다.
 
6. **추가** 를 선택합니다.

### <a name="create-public-backend-address-pool"></a>퍼블릭 백 엔드 주소 풀 만들기

백 엔드 주소 풀 **myPublicBackendPool** 을 만듭니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myPublicLoadBalancer** 를 선택합니다.

2. **설정** 에서 **백 엔드 풀** 을 선택한 다음, **추가** 를 선택합니다.

3. **백 엔드 풀 추가** 페이지에서 이름에 백 엔드 풀의 이름인 **myPublicBackendPool** 을 입력합니다.

4. **가상 네트워크** 에서 **myVNet** 을 선택합니다.
 
5. **가상 머신** 에서 **추가** 를 선택하고 **myVM** 을 선택합니다.
 
6. **추가** 를 선택합니다.

### <a name="test-connectivity-before-outbound-rule"></a>아웃바운드 규칙 전 연결 테스트

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고, **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myResourceGroupLB** 리소스 그룹에 있는 **myVM** 을 선택합니다.

2. **개요** 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

4. VM을 만드는 동안 입력한 사용자 이름과 암호를 입력합니다.

5. **연결** 을 선택합니다.

6. Internet Explorer를 엽니다.

7. 주소 표시줄에 **https://whatsmyip.org** 을 입력합니다.

8. 연결이 실패합니다. 기본적으로 표준 퍼블릭 부하 분산 장치는 [정의된 아웃바운드 규칙이 없는 아웃바운드 트래픽을 허용하지 않습니다](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>퍼블릭 부하 분산 장치 아웃바운드 규칙 만들기

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myPublicLoadBalancer** 를 선택합니다.

2. **설정** 아래에서 **아웃바운드 규칙** 을 선택한 다음 **추가** 를 선택합니다.

3. 다음 값을 사용하여 아웃바운드 규칙을 구성합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | **myOutboundRule** 을 입력합니다. |
    | 프런트 엔드 IP 주소 | **LoadBalancerFrontEnd** 를 선택합니다.|
    | 유휴 제한 시간(분) | 슬라이더를 **15분** 으로 이동합니다.|
    | TCP 재설정 | **사용** 을 선택합니다.|
    | 백 엔드 풀 | **myPublicBackendPool** 를 선택합니다.|
    | 포트 할당 -> 포트 할당 | **기본 아웃바운드 포트 수 사용** 을 선택합니다. |

4. **추가** 를 선택합니다.

### <a name="test-connectivity-after-outbound-rule"></a>아웃바운드 규칙 후 연결 테스트

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고, **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myResourceGroupLB** 리소스 그룹에 있는 **myVM** 을 선택합니다.

2. **개요** 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

4. VM을 만드는 동안 입력한 사용자 이름과 암호를 입력합니다.

5. **연결** 을 선택합니다.

6. Internet Explorer를 엽니다.

7. 주소 표시줄에 **https://whatsmyip.org** 을 입력합니다.

8. 연결이 성공합니다.

9. 표시되는 IP 주소는 **myPublicLoadBalancer** 의 프런트 엔드 IP 주소여야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, 부하 분산 장치, VM, 모든 관련 리소스를 삭제합니다. 

이렇게 하려면 집합의 리소스 그룹 **myResourceGroupLB** 를 선택하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 퍼블릭 및 내부 부하 분산 장치의 조합을 사용하여 "송신 전용" 구성을 만들었습니다.  

이 구성을 사용하면 퍼블릭 인바운드 연결을 계속 방지하면서도 들어오는 내부 트래픽을 백 엔드 풀로 부하 분산할 수 있습니다.

- [Azure Load Balancer](load-balancer-overview.md)에 관한 자세한 정보.
- [Azure에서 아웃바운드 연결](load-balancer-outbound-connections.md)에 관한 자세한 정보.
- 부하 분산 장치 [FAQ](load-balancer-faqs.yml).
- [Azure Bastion](../bastion/bastion-overview.md)에 관한 자세한 정보

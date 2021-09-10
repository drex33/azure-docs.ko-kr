---
title: Azure Load Balancer에 대한 규칙 관리-Azure Portal
description: 이 문서에서는 Azure Portal를 사용하여 Azure Load Balancer에 대한 규칙을 관리하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e94978fa61c144ced0b405717eefc8ffecc47873
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832358"
---
# <a name="manage-rules-for-azure-load-balancer-using-the-azure-portal"></a>Azure Portal를 사용하여 Azure Load Balancer에 대한 규칙 관리

Azure Load Balancer는 백 엔드 풀에 대한 트래픽을 구성하는 규칙을 지원합니다.  이 문서에서는 Azure Load Balancer에 대한 규칙을 관리하는 방법을 알아봅니다.

로그인에는 네 가지 규칙이 있습니다.

* **부하 분산 규칙** - 부하 분산 장치 규칙은 수신 트래픽이 백 엔드 풀 내의 **모든** 인스턴스에 배포되는 방식을 정의하는 데 사용됩니다. 부하 분산 규칙은 지정된 프런트 엔드 IP 구성 및 포트를 여러 백 엔드 IP 주소 및 포트에 매핑합니다. 예를 들어 웹 트래픽의 부하를 분산하기 위해 포트 80에서 만든 규칙이 있습니다.

* **고가용성 포트** - **protocol - all** and **port - 0** 로 구성된 부하 분산 장치 규칙입니다. 이 규칙을 사용하면 단일 규칙이 내부 표준 부하 분산 장치의 모든 포트에 도달하는 모든 TCP 및 UDP 트래픽을 단일 규칙으로 부하 분산할 수 있습니다. HA 포트 부하 분산 규칙은 가상 네트워크 내 NVA(네트워크 가상 어플라이언스)의 고가용성 및 확장과 같은 시나리오를 지원합니다. 이 기능은 많은 수의 포트에서 부하를 분산시켜야 할 때 도움이 될 수 있습니다.

* **인바운드 NAT 규칙** - 인바운드 NAT 규칙은 프런트 엔드 IP 주소 및 포트 조합으로 전송되는 수신 트래픽을 전달합니다. 트래픽은 백 엔드 풀의 **특정** 가상 머신 또는 인스턴스로 전송됩니다. 포트 전달은 부하 분산과 동일한 해시 기반 배포를 통해 수행됩니다.

* **아웃바운드 규칙** - 아웃바운드 규칙은 백 엔드풀에 의해 식별되는 **모든** 가상 머신 또는 인스턴스에 대해 NAT(Network Address Translation)를 구성합니다. 이 규칙을 사용하면 백 엔드의 인스턴스가 인터넷 또는 다른 엔드포인트로 통신(아웃바운드)할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- 구독의 표준 공용 부하 분산 장치. Azure Load Balancer 만들기에 대한 자세한 정보는 [빠른 시작: Azure Portal을 사용하여 VM의 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요. 이 문서의 예제에서 부하 분산 장치 이름은 **myLoadBalancer** 입니다.

- 구독의 표준 내부 부하 분산 장치. Azure Load Balancer 만들기에 대한 자세한 정보는 [빠른 시작: Azure Portal을 사용하여 VM의 부하를 분산하는 내부 부하 분산 장치 만들기](quickstart-load-balancer-standard-internal-portal.md)를 참조하세요. 이 문서의 예제에서 부하 분산 장치 이름은 **myLoadBalancer** 입니다.

## <a name="load-balancing-rules"></a>부하 분산 규칙

이 섹션에서는 부하 분산 규칙을 추가하고 제거하는 방법을 알아봅니다. 예제에서는 공용 부하 분산 장치를 사용합니다.

### <a name="add-a-load-balancing-rule"></a>부하 분산 규칙 추가

이 예제에서는 80 포트의 부하를 분산하는 규칙을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

3. **myLoadBalancer** 또는 사용자의 부하 분산 장치 선택

4. 부하 분산 장치 페이지의 **설정** 에서 **부하 분산 규칙** 을 선택합니다.

5. **부하 분산 규칙** 에서 **+ 추가** 를 선택하여 규칙을 추가합니다.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="표준 부하 분산 장치의 부하 분산 규칙 페이지 스크린샷" border="true":::

6. **부하 분산 장치 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 속성 | **myHTTPRule** |
    | IP 버전 | **IPv4** 또는 **IPv6** 을 선택합니다. |
    | 프런트 엔드 IP 주소 | 부하 분산 장치의 프런트 엔드 IP 주소를 선택합니다. <br> 이 예제에서는 **myFrontendIP** 입니다. |
    | 프로토콜 | **TCP** 의 기본값을 그대로 둡니다. |
    | 포트 | **80** 을 입력합니다. |
    | 백 엔드 포트 | **80** 을 입력합니다. |
    | 백 엔드 풀 | 부하 분산 장치의 백 엔드 풀을 선택합니다. </br> 이 예제에서는 **myBackendPool** 입니다. |
    | 상태 프로브 | **새로 만들기** 를 선택합니다. </br> **이름** 에서 **myHealthProbe** 를 입력합니다. </br> **프로토콜** 에서 **HTTP** 를 선택합니다. </br> 나머지는 기본값을 유지하거나 요구 사항에 맞게 조정합니다. </br> **확인** 을 선택합니다. |
    | 세션 지속성 | **없음** 또는 필요한 지속성을 선택합니다. </br> 배포 모드에 대한 자세한 정보는 [Azure Load Balancer 배포 모드](load-balancer-distribution-mode.md)를 참조하세요. | 
    | 유휴 제한 시간(분) | 기본값을 **4** 로 유지하거나 슬라이더를 필요한 유휴 시간 제한으로 이동합니다. |
    | TCP 재설정 | **사용** 을 선택합니다. </br> TCP 초기화에 대한 자세한 정보는 [Load Balancer TCP 초기화 및 유휴 시간 제한](load-balancer-tcp-reset.md)을 참조하세요. |
    | 부동 IP | 기본값은 **사용 안 함** 으로 두거나 배포에 부동 IP가 필요한 경우 또는 사용하도록 설정합니다. </br> 부동 IP에 대한 자세한 정보는 [Azure Load Balancer 부동 IP 구성](load-balancer-floating-ip.md)을 참조하세요. |
    | 아웃바운드 SNAT(Source Network Address Translation) | **(권장) 아웃바운드 규칙을 사용하여 백 엔드 풀 멤버에 인터넷 액세스 권한을 제공합니다.** (기본값)를 그대로 둡니다. </br> 아웃바운드 규칙 및 (SNAT)에 대한 자세한 정보는 [아웃바운드 규칙 Azure Load Balancer](outbound-rules.md) 및 [아웃바운드 연결에 SNAT(Source Network Address Translation) 사용](load-balancer-outbound-connections.md)을 참조하세요.|

7. **추가** 를 선택합니다.

    :::image type="content" source="./media/manage-rules-how-to/add-load-balancing-rule.png" alt-text="부하 분산 장치 규칙 추가 페이지의 스크린샷" border="true":::

### <a name="remove-a-load-balancing-rule"></a>부하 분산 규칙 제거

이 예제에서는 부하 분산 규칙을 제거합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

3. **myLoadBalancer** 또는 사용자의 부하 분산 장치 선택

4. 부하 분산 장치 페이지의 **설정** 에서 **부하 분산 규칙** 을 선택합니다.

5. 제거하려는 규칙 옆에 있는 세 개의 점을 선택합니다.

6. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage-rules-how-to/remove-load-balancing-rule.png" alt-text="부하 분산 규칙을 제거하는 스크린샷" border="true":::

## <a name="high-availability-ports"></a>고가용성 포트

이 섹션에서는 고가용성 포트 규칙을 추가하고 제거하는 방법을 알아봅니다. 이 예제에서는 내부 부하 분산 장치를 사용합니다. 

HA 포트 규칙은 표준 내부 부하 분산 장치에서 지원됩니다.

### <a name="add-high-availability-ports-rule"></a>고가용성 포트 규칙 추가

이 예제에서는 고가용성 포트 규칙을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

3. **myLoadBalancer** 또는 사용자의 부하 분산 장치 선택

4. 부하 분산 장치 페이지의 **설정** 에서 **부하 분산 규칙** 을 선택합니다.

5. **부하 분산 규칙** 에서 **+ 추가** 를 선택하여 규칙을 추가합니다.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="표준 부하 분산 장치의 부하 분산 규칙 페이지 스크린샷" border="true":::

6. **부하 분산 장치 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | **myHARule** 을 입력합니다. |
    | IP 버전 | **IPv4** 또는 **IPv6** 을 선택합니다. |
    | 프런트 엔드 IP 주소 | 부하 분산 장치의 프런트 엔드 IP 주소를 선택합니다. <br> 이 예제에서는 **myFrontendIP** 입니다. </br> **HA 포트** 옆에 있는 상자를 선택합니다. |
    | 백 엔드 풀 | 부하 분산 장치의 백 엔드 풀을 선택합니다. </br> 이 예제에서는 **myBackendPool** 입니다. |
    | 상태 프로브 | **새로 만들기** 를 선택합니다. </br> **이름** 에서 **myHealthProbe** 를 입력합니다. </br> **프로토콜** 에서 **TCP** 를 선택합니다. </br> **포트** 에 TCP 포트 번호를 입력합니다. 이 예제에서는 포트 **80** 입니다. 사용자의 요구 사항을 충족하는 포트를 입력합니다. </br> 나머지는 기본값을 유지하거나 요구 사항에 맞게 조정합니다. </br> **확인** 을 선택합니다. |
    | 세션 지속성 | **없음** 또는 필요한 지속성을 선택합니다. </br> 배포 모드에 대한 자세한 정보는 [Azure Load Balancer 배포 모드](load-balancer-distribution-mode.md)를 참조하세요. | 
    | 유휴 제한 시간(분) | 기본값을 **4** 로 유지하거나 슬라이더를 필요한 유휴 시간 제한으로 이동합니다. |
    | TCP 재설정 | **사용** 을 선택합니다. </br> TCP 초기화에 대한 자세한 정보는 [Load Balancer TCP 초기화 및 유휴 시간 제한](load-balancer-tcp-reset.md)을 참조하세요. |
    | 부동 IP | 기본값은 **사용 안 함** 으로 두거나 배포에 부동 IP가 필요한 경우 또는 사용하도록 설정합니다. </br> 부동 IP에 대한 자세한 정보는 [Azure Load Balancer 부동 IP 구성](load-balancer-floating-ip.md)을 참조하세요. |

    HA 포트 규칙 구성에 대한 자세한 정보는 **[고가용성 포트 개요](load-balancer-ha-ports-overview.md)** 를 참조하세요.

7. **추가** 를 선택합니다.

    :::image type="content" source="./media/manage-rules-how-to/add-ha-ports-load-balancing-rule.png" alt-text="부하 분산 장치 HA 포트 규칙 추가 페이지의 스크린샷" border="true":::

### <a name="remove-a-high-availability-ports-rule"></a>고가용성 포트 규칙 제거

이 예제에서는 부하 분산 규칙을 제거합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

3. **myLoadBalancer** 또는 사용자의 부하 분산 장치 선택

4. 부하 분산 장치 페이지의 **설정** 에서 **부하 분산 규칙** 을 선택합니다.

5. 제거하려는 규칙 옆에 있는 세 개의 점을 선택합니다.

6. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage-rules-how-to/remove-ha-ports-load-balancing-rule.png" alt-text="HA 포트 부하 분산 규칙을 제거하는 스크린샷" border="true":::

## <a name="inbound-nat-rule"></a>인바운드 NAT 규칙

인바운드 NAT 규칙은 백 엔드 풀의 특정 VM으로 연결을 라우팅하는 데 사용됩니다. 인바운드 NAT 규칙 구성 및 테스트에 대한 정보 및 자세한 자습서는 [자습서: 포털을 사용하여 Azure Load Balancer 포트 전달 구성](tutorial-load-balancer-port-forwarding-portal.md)을 참조하세요.

## <a name="outbound-rule"></a>아웃바운드 규칙

이 섹션에서는 아웃바운드 규칙을 추가하고 제거하는 방법을 알아봅니다. 이 예제에서는 공용 부하 분산 장치를 사용합니다. 

아웃바운드 규칙은 표준 공용 부하 분산 장치에서 지원됩니다.

### <a name="add-outbound-rule"></a>아웃바운드 규칙 추가

이 예제에서는 아웃바운드 규칙을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

3. **myLoadBalancer** 또는 사용자의 부하 분산 장치 선택

4. 부하 분산 장치 페이지의 **설정** 에서 **아웃바운드 규칙** 을 선택합니다.

5. **아웃바운드 규칙** 에서 **+ 추가** 를 선택하여 규칙을 추가합니다.

    :::image type="content" source="./media/manage-rules-how-to/outbound-rules.png" alt-text="표준 부하 분산 장치의 아웃바운드 규칙 페이지 스크린샷" border="true":::

6. **아웃바운드 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | **myOutboundRule** 을 입력합니다. |
    | 프런트 엔드 IP 주소 | 부하 분산 장치의 프런트 엔드 IP 주소를 선택합니다. <br> 이 예제에서는 **myFrontendIP** 입니다. | 
    | 프로토콜 | 기본값을 **모두** 로 유지합니다. |
    | 유휴 제한 시간(분) | 기본값인 **4** 를 유지하거나 사용자의 요구 사항에 맞게 슬라이더를 이동합니다. |
    | TCP 재설정 | 기본값인 **사용** 을 유지합니다. |
    | 백 엔드 풀 | 부하 분산 장치의 백 엔드 풀을 선택합니다. </br> 이 예제에서는 **myBackendPool** 입니다. |
    | **포트 할당** |   |
    | 포트 할당 | **수동으로 아웃바운드 포트 수 선택** 을 선택합니다. |
    | **아웃바운드 포트** |  |
    | 선택 기준 | **인스턴스당 포트** 를 선택합니다. |
    | 인스턴스당 포트 수 | **10000** 을 입력합니다. |

7. **추가** 를 선택합니다.

    :::image type="content" source="./media/manage-rules-how-to/add-outbound-rule.png" alt-text="아웃바운드 규칙 추가 페이지의 스크린샷" border="true":::

### <a name="remove-an-outbound-rule"></a>아웃바운드 규칙 제거

이 예제에서는 아웃바운드 규칙을 제거합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

3. **myLoadBalancer** 또는 사용자의 부하 분산 장치 선택

4. 부하 분산 장치 페이지의 **설정** 에서 **아웃바운드 규칙** 을 선택합니다.

5. 제거하려는 규칙 옆에 있는 세 개의 점을 선택합니다.

6. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage-rules-how-to/remove-outbound-rule.png" alt-text="아웃바운드 규칙을 제거하는 스크린샷" border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Load Balancer에 대한 부하 분산 규칙을 관리하는 방법을 배웠습니다.

Azure Load Balancer에 대한 자세한 정보는 다음을 참조하세요.
- [Azure Load Balancer란?](load-balancer-overview.md)
- [질문과 대답 - Azure Load Balancer](load-balancer-faqs.yml)

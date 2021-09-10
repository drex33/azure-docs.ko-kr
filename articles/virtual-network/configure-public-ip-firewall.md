---
title: Azure Firewall을 사용하여 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 Azure Firewall에서 사용하는 방법과 구성을 변경하는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 1ffb1242dfded6af9c869a53367016d30dd61f49
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439368"
---
# <a name="manage-a-public-ip-address-with-azure-firewall"></a>Azure Firewall을 사용하여 공용 IP 주소 관리

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 클라우드 기반 네트워크 보안 서비스입니다. Azure Firewall을 사용하려면 하나 이상의 공용 고정 IP 주소가 구성되어야 합니다. 이 IP 또는 IP 집합은 방화벽에 대한 외부 연결 지점으로 사용됩니다. Azure Firewall은 표준 SKU 공용 IP 주소를 지원합니다. 기본 SKU 공용 IP 주소 및 공용 IP 접두사는 지원되지 않습니다. 

이 문서에서는 구독의 기존 공용 IP를 사용해서 Azure Firewall을 만드는 방법을 알아봅니다. 방화벽의 IP 구성을 변경합니다. 마지막으로 방화벽에 IP 구성을 추가합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독에 3개의 표준 SKU 공용 IP 주소가 있습니다. IP 주소를 리소스에 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](create-public-ip-portal.md)을 참조하세요.
    - 이 문서에 나오는 예제의 목적에 맞게 새 공용 IP 주소의 이름을 **myStandardPublicIP-1**, **myStandardPublicIP-2** 및 **myStandardPublicIP-3** 으로 지정합니다.

## <a name="create-azure-firewall-existing-public-ip"></a>Azure Firewall 기존 공용 IP 만들기

이 섹션에서는 Azure Firewall을 만듭니다. 필수 구성 요소에서 만든 IP 주소를 방화벽의 공용 IP로 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 상단에 있는 검색 상자에 **Firewall** 을 입력합니다.

3. 검색 결과에서 **Firewall** 을 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **방화벽 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **myResourceGroupFW** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 이름 | **myFirewall** 을 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 가용성 영역 | **없음** 의 기본값을 그대로 둡니다. |
    | 방화벽 계층  | 기본값인 **표준** 을 그대로 둡니다. |
    | 방화벽 관리 | 기본값인 **Firewall Policy를 사용하여 이 방화벽 관리** 를 그대로 둡니다.|
    | 방화벽 정책 | **새로 추가** 를 선택합니다. </br> **정책 이름** 에 **myFirewallPolicy** 를 입력합니다. </br> **지역** 에서 **미국 서부 2** 를 선택합니다. </br> **Yes** 를 선택합니다. |
    | 가상 네트워크 이름 | **myVNet** 을 입력합니다. |
    | 주소 공간 | **10.0.0.0/16** 을 입력합니다. |
    | 서브넷 주소 공간 | **10.0.0.0/26** 을 입력합니다. |
    | 공용 IP 주소 | **myStandardPublicIP-1** 또는 공용 IP를 선택합니다. |
    | 강제 터널링 | **사용 안 함**(기본값)을 그대로 둡니다. |
    
 
6. **검토 + 만들기** 탭을 선택하거나, 파란색 **검토 + 만들기** 단추를 선택합니다.

7. **만들기** 를 선택합니다.

> [!NOTE]
> Azure Firewall의 간단한 배포입니다. 고급 구성 및 설정은 [자습서: Azure Portal을 사용하여 Azure Firewall 및 정책 배포 및 구성](../firewall/tutorial-firewall-deploy-portal-policy.md)을 참조하세요.
>
> Azure Firewall에 대한 자세한 내용은 [Azure Firewall이란?](../firewall/overview.md)을 참조하세요.

## <a name="change-public-ip-address"></a>공용 IP 주소 변경

이 섹션에서는 방화벽과 연결된 공용 IP 주소를 변경합니다. 방화벽에는 해당 구성과 연결된 공용 IP 주소가 하나 이상 있어야 합니다. 

1. 포털 상단에 있는 검색 상자에 **Firewall** 을 입력합니다.

2. 검색 결과에서 **Firewall** 을 선택합니다.

3. **방화벽** 에서 **myFirewall** 을 선택합니다.

4. **myFirewall** 의 **설정** 에서 **공용 IP 구성** 을 선택합니다.

5. **공용 IP 구성** 에서 **myStandardPublicIP-1** 또는 IP 주소를 선택합니다.

6. **공용 IP 구성 편집** 의 **공용 IP 주소** 에서 **myStandardPublicIP-2** 를 선택합니다.

7. **저장** 을 선택합니다.

## <a name="add-public-ip-configuration"></a>공용 IP 구성 추가

이 섹션에서는 Azure Firewall에 공용 IP 구성을 추가합니다. 여러 IP에 대한 자세한 내용은 [여러 공용 IP 주소](../firewall/features.md#multiple-public-ip-addresses)를 참조하세요.  

1. 포털 상단에 있는 검색 상자에 **Firewall** 을 입력합니다.

2. 검색 결과에서 **Firewall** 을 선택합니다.

3. **방화벽** 에서 **myFirewall** 을 선택합니다.

4. **myFirewall** 의 **설정** 에서 **공용 IP 구성** 을 선택합니다.

5. **+ 공용 IP 구성 추가** 를 선택합니다.

6. **이름** 에 **myNewPublicIPconfig** 를 입력합니다.

7. **공용 IP 주소** 에서 **myStandardPublicIP-3** 를 선택합니다.

8. **추가** 를 선택합니다.

## <a name="more-information"></a>자세한 정보

* Azure Firewall을 표준 SKU 부하 분산 장치와 통합하여 백 엔드 풀 리소스를 보호할 수 있습니다.  방화벽을 공용 부하 분산 장치와 연결하는 경우 방화벽 공용 IP 주소로 전달되도록 수신 트래픽을 구성합니다. 사용자 정의 경로를 통해 방화벽 공용 IP 주소에 대한 송신을 구성합니다.  자세한 내용 및 설치 지침은 [Azure Standard Load Balancer와 Azure Firewall 통합](../firewall/integrate-lb.md)을 참조하세요. 

* Azure Firewall을 NAT 게이트웨이와 연결하여 SNAT(원본 네트워크 주소 변환)의 확장성을 확장할 수도 있습니다. NAT 게이트웨이는 방화벽과 연결된 다수의 공용 IP에서 오는 트래픽을 허용하는 구성을 방지합니다. 이 구성을 사용하면 모든 인바운드 트래픽이 NAT 게이트웨이의 공용 IP 주소를 사용합니다. 트래픽은 Azure Firewall 공용 IP 주소를 통해 송신됩니다.  자세한 내용은 [Azure NAT 게이트웨이를 사용하여 SNAT 포트 크기 조정](../firewall/integrate-with-nat-gateway.md)을 참조하세요.

## <a name="caveats"></a>제한 사항

* Azure Firewall은 표준 SKU 부하 분산 장치를 사용합니다. 네트워크 필터 규칙에서 TCP 및 UDP 이외의 프로토콜은 방화벽의 공용 IP에 대한 SNAT에서 지원되지 않습니다. 
## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Firewall을 만들고 기존 공용 IP를 사용하는 방법을 알아보았습니다. 기본 IP 구성의 공용 IP를 변경했습니다. 마지막으로 방화벽에 공용 IP 구성을 추가했습니다.

- Auzre의 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소](public-ip-addresses.md)를 참조하세요.
- Azure Firewall에 대한 자세한 내용은 [Azure Firewall이란?](../firewall/overview.md)을 참조하세요.

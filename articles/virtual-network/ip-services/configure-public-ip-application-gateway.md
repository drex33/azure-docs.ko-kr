---
title: Azure Application Gateway를 사용하여 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: Azure Application Gateway에서 공용 IP 주소를 사용하는 방법과 구성을 변경하고 관리하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 8139d6c6309bf971ed9a955f5ef02f0c674b152b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372887"
---
# <a name="manage-a-public-ip-address-with-an-azure-application-gateway"></a>Azure Application Gateway를 사용하여 공용 IP 주소 관리

Azure Application Gateway는 웹 애플리케이션에 대한 트래픽을 관리하는 웹 트래픽 부하 분산 장치입니다. Application Gateway는 HTTP 요청의 특성을 기반으로 라우팅 결정을 내립니다. URI 경로 또는 호스트 헤더와 같은 특성의 예입니다.  Application Gateway의 프런트 엔드는 백 엔드 풀의 애플리케이션에 대한 연결 지점입니다. 

Application Gateway 프런트 엔드는 개인 IP 주소, 공용 IP 주소 또는 둘 다 일 수 있습니다.  Application Gateway의 V1 SKU는 정적 또는 동적 기본 공용 IP를 지원합니다.  V2 SKU는 정적 전용 표준 SKU 공용 IP를 지원합니다. Application Gateway V2 SKU는 프런트 엔드일 뿐이므로 내부 IP 주소를 지원하지 않습니다.  자세한 내용은 [Application Gateway 프런트 엔드 IP 주소 구성](../../application-gateway/configuration-front-end-ip.md)을 참조하세요.  

이 문서에서는 구독에서 기존 공용 IP를 사용하여 Application Gateway를 만드는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독에 두 개의 표준 SKU 공용 IP 주소가 있습니다. IP 주소를 리소스와 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md)을 참조하세요.
    - 이 문서에 나오는 예제의 목적에 맞게 새 공용 IP 주소의 이름을 **myStandardPublicIP-1** 및 **myStandardPublicIP-2** 로 지정합니다.

## <a name="create-application-gateway-existing-public-ip"></a>기존 공용 IP를 사용하여 Application Gateway 만들기

이 섹션에서는 Application Gateway 리소스를 만듭니다. 필수 구성 요소에서 만든 IP 주소를 Application Gateway의 공용 IP로 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Portal 맨 위에 있는 검색 상자에 **Application Gateway** 를 입력합니다.

3. 검색 결과에서 **Application Gateway** 를 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **Application Gateway 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다.<ul><li>**myResourceGroupAppGW** 를 입력합니다.</li></ul>**확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | Application Gateway 이름 | **myAppGateway** 를 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 서비스 계층 | **표준 V2**(기본값)를 그대로 둡니다. |
    | 자동 크기 조정 사용 | **예**(기본값)를 그대로 둡니다. |
    | 최소 인스턴스 수 | **0**(기본값)을 그대로 둡니다. |
    | 최대 인스턴스 수 | **10**(기본값)을 그대로 둡니다. |
    | 가용성 영역 | **없음** 의 기본값을 그대로 둡니다. |
    | HTTP2 | **사용 안 함**(기본값)을 그대로 둡니다. |
    | 가상 네트워크 | **새로 만들기** 를 선택합니다. <ul><li>**가상 네트워크 만들기** 에서 이름에 **myVNet** 을 입력합니다.</li><li>**주소 공간** 에 기본 주소 공간을 그대로 둡니다.</li><li>**서브넷** 에서 **기본값** 을 **myAGSubnet** 으로 변경합니다.</li><li>두 번째 서브넷 이름에 **myBackendSubnet** 을 입력합니다.</li><li>**주소 범위** 에 기본 주소 공간 내의 범위를 입력합니다.</li></ul>**확인** 을 선택합니다.|

6. **다음: 프런트 엔드** 를 선택합니다.

7. **프런트 엔드** 탭의 **공용 IP 주소** 에 **myStandardPublicIP-1** 을 선택하거나 공용 IP 주소를 선택합니다.

8. 완료되면 **다음: 백 엔드** 를 선택합니다. 

9. **백 엔드 풀 추가** 를 선택합니다.

10. **백 엔드 풀 추가** 에서 이름에 **myBackendPool** 을 입력합니다.

11. 완료되면 **다음: 구성** 을 선택합니다.

12. **+ 라우팅 규칙 추가** 를 선택합니다. 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 규칙 이름 | **myRoutingRule** 을 입력합니다. |
    | **수신기** |    |
    | 수신기 이름 | **myListener** 를 입력합니다. |
    | 프런트 엔드 IP | **공용** 을 선택합니다. |
    | 프런트 엔드 IP 프로토콜 | **HTTP**(기본값)를 그대로 둡니다. |
    | 포트 | **80**(기본값)을 그대로 둡니다. |
    | **추가 설정** |   |
    | 수신기 유형 | **기본**(기본값)을 그대로 둡니다. |
    | 오류 페이지 URL | **아니요**(기본값)를 그대로 둡니다. |
    | **백 엔드 대상** |    |
    | 대상 형식 | **백 엔드 풀**(기본값)을 그대로 둡니다. |
    | 백 엔드 대상 | **myBackendPool** 을 선택합니다. |
    | HTTP 설정 | **새로 추가** 를 선택합니다.<ul><li>**이름** 에 **myHTTPsetting** 을 입력합니다.</li><li>다른 설정은 기본값으로 둡니다.</li></ul>**추가** 를 선택합니다.|

13. **추가** 를 선택합니다.

14. **다음: 태그** 를 선택하고 **다음: 검토 + 만들기** 를 선택합니다.

15. **만들기** 를 선택합니다.

> [!NOTE]
> 이는 Application Gateway의 간단한 배포입니다. 고급 구성 및 설정은 [빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal](../../application-gateway/quick-create-portal.md)을 참조하세요.
>
> Azure Application Gateway에 대한 자세한 내용은 [Azure Application Gateway란?](../../application-gateway/overview.md)을 참조하세요.

## <a name="change-or-remove-public-ip-address"></a>공용 IP 주소 변경 또는 제거

Application Gateway는 만든 후 공용 IP 주소 변경을 지원하지 않습니다.

## <a name="more-information"></a>자세한 정보

* 동적 기본 SKU IP가 Application Gateway 프런트 엔드와 연결된 경우 게이트웨이가 중지되거나 시작될 때만 변경됩니다. Application Gateway 프런트 엔드와 연결된 DNS 이름은 변경되지 않습니다. 

## <a name="caveats"></a>제한 사항

* 지금은 Application Gateway에 대해 공용 IPv6 주소가 지원되지 않습니다.  

## <a name="next-steps"></a>다음 단계

이 문서에서는 Application Gateway를 만들고 기존 공용 IP를 사용하는 방법을 알아보았습니다. 

- Azure Virtual Network NAT 대한 자세한 내용은 [Azure Virtual Network NAT 무엇인가요?를 참조하세요.](../nat-gateway/nat-overview.md)
- Azure의 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소](../../virtual-network/public-ip-addresses.md)를 참조하세요.
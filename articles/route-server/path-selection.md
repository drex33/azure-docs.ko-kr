---
title: Azure Route Server를 사용하여 경로 선택
description: Azure Route Server에서 네트워크 가상 어플라이언스에 대한 경로 선택을 사용하도록 설정하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: duau
ms.openlocfilehash: 011156427bfaed238679d4fc4b4e6d51e566ec6e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352711"
---
# <a name="path-selection-with-azure-route-server"></a>Azure Route Server를 사용하여 경로 선택

Azure Route Server를 사용하면 인터넷을 통해 SDWAN NVA를 통해 온-프레미스 네트워크로 라우팅하는 트래픽을 제어할 수 있습니다. 이 문서에서는 Azure Route Server가 온-프레미스 네트워크와 통신할 때 [라우팅 기본 설정을](../virtual-network/ip-services/routing-preference-overview.md) 갖도록 SNWAN NVA를 구성할 수 있는 경로 선택을 사용하도록 설정하는 방법에 대해 살펴보겠습니다.

## <a name="how-does-it-work"></a>작동 원리

:::image type="content" source="./media/path-selection/routing-preference.png" alt-text="인터넷 라우팅 IP가 있는 Azure Route Server 및 SDWAN의 다이어그램.":::

### <a name="cold-potato-routing"></a>콜드 라우트 라우팅

Azure Route Server와 동일한 가상 네트워크에 SDWAN NVA를 배포하는 경우 Microsoft 네트워크 IP 주소로 구성됩니다. 온-프레미스에 대한 트래픽 경로는 Microsoft 글로벌 네트워크를 사용하고 대상에 가장 가까운 Microsoft 네트워크를 종료합니다. 온-프레미스에서 라우팅하면 반환 경로에서 사용자에게 가장 가까운 Microsoft 네트워크가 입력됩니다. 이 라우팅 방법은 성능 최적화이므로 비용상 최상의 환경을 제공합니다. 

### <a name="hot-potato-routing"></a>핫 핫 라우트 라우팅

비용을 최적화하는 방법으로 인터넷 IP를 사용하여 SDWAN NVA를 할당하여 두 번째 라우팅 방법을 도입합니다. 트래픽이 온-프레미스로 라우팅되면 서비스가 호스트되는 동일한 지역에서 Microsoft 네트워크가 종료됩니다. 그런 다음 ISP의 네트워크를 사용하여 인터넷을 통해 라우팅합니다. 온-프레미스에서 라우팅하면 호스팅된 서비스 지역에 가장 가까운 Microsoft 네트워크가 시작됩니다. 이 라우팅 방법은 대량의 데이터 전송과 같은 작업을 완료할 때 최상의 전반적인 가격을 제공합니다.

## <a name="how-to-enable-routing-preference"></a>라우팅 기본 설정을 사용하도록 설정하는 방법

새 공용 IP 주소를 만들 때 *라우팅 기본 설정으로* **인터넷을** 선택합니다. 그런 다음 SDWAN NVA에 공용 IP 주소를 할당합니다.

:::image type="content" source="./media/path-selection/internet-ip.png" alt-text="공용 IP 주소에 대한 인터넷 라우팅 기본 설정의 스크린샷.":::

Microsoft는 사용자 환경에 추가 복원력 계층을 제공하기 위해 Microsoft 네트워크와 인터넷을 모두 사용하여 연결 솔루션을 구현하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Route Server](route-server-faq.md)에 대한 자세한 정보 알아보기
- [Azure Route Server 구성](quickstart-configure-route-server-powershell.md) 방법 알아보기

---
title: Azure Static Web Apps에서 프라이빗 엔드포인트 구성
description: Azure Static Web Apps에 대한 프라이빗 엔드포인트 액세스 구성 알아보기
services: static-web-apps
author: burkeholland
ms.author: buhollan
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 7/28/2021
ms.openlocfilehash: 8d6f5e019852200068d4db342ef4ab533d8779b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535926"
---
# <a name="configure-private-endpoint-in-azure-static-web-apps"></a>Azure Static Web Apps에서 프라이빗 엔드포인트 구성

개인 네트워크에서만 액세스할 수 있도록 프라이빗 엔드포인트(프라이빗 링크라고도 함)를 사용하여 정적 웹앱에 대한 액세스를 제한할 수 있습니다.

> [!NOTE]
> Static Web Apps의 프라이빗 엔드포인트 지원은 현재 미리 보기 상태입니다.

## <a name="how-it-works"></a>작동 방식

VNet(Azure Virtual Network)은 기존 데이터 센터에 있는 것처럼 네트워크이지만 VNet 내의 리소스는 Microsoft 백본 네트워크에서 서로 안전하게 통신합니다.

프라이빗 엔드포인트를 사용하여 Static Web Apps를 구성하면 VNet의 개인 IP 주소를 사용할 수 있습니다. 이 링크를 만들면 정적 웹앱이 VNet에 통합됩니다. 따라서 정적 웹앱은 공용 인터넷에서 더 이상 사용할 수 없으며 Azure VNet 내의 머신에서만 액세스할 수 있습니다.

> [!NOTE]
> 애플리케이션을 프라이빗 엔드포인트 뒤에 배치하는 것은 VNet이 있는 지역에서만 앱을 사용할 수 있음을 의미합니다. 따라서 애플리케이션을 여러 개의 현재 위치에서 더 이상 사용할 수 없습니다.

> [!WARNING]
> 현재 프라이빗 엔드포인트는 프로덕션 환경만 보호합니다. 스테이징 환경에 대한 지원은 예정된 서비스 업데이트에 추가될 예정입니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정.
  - [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure VNet](../virtual-network/quick-create-portal.md)
- 표준 호스팅 계획을 사용하는 [Azure Static Web Apps](./get-started-portal.md)로 배포된 애플리케이션

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 정적 웹앱에 대한 프라이빗 엔드포인트를 만듭니다.

> [!IMPORTANT]
> 프라이빗 엔드포인트를 사용하려면 표준 호스팅 계획에 정적 웹앱을 배포해야 합니다. 측면 메뉴의 **호스팅 계획** 옵션에서 호스팅 계획을 변경할 수 있습니다.

1. 포털에서 정적 웹앱을 엽니다.

1. 측면 메뉴에서 **프라이빗 엔드포인트** 옵션을 선택합니다.

1. **추가** 단추를 클릭합니다.

1. "프라이빗 엔드포인트 추가" 대화 상자에서 다음 정보를 입력합니다.

   | 설정                         | 값                         |
   | ------------------------------- | ----------------------------- |
   | Name                            | **myPrivateEndpoint** 를 입력합니다.  |
   | Subscription                    | 구독을 선택합니다.     |
   | Virtual Network                 | 가상 네트워크를 선택합니다.  |
   | 서브넷                          | 서브넷을 선택합니다.           |
   | 프라이빗 DNS 영역과 통합 | **예**(기본값)를 그대로 둡니다. |

   :::image type="content" source="media/create-private-link-dialog.png" alt-text="./media/create-private-link-dialog.png":::

1. **확인** 을 선택합니다.

## <a name="testing-your-private-endpoint"></a>프라이빗 엔드포인트 테스트

애플리케이션을 더 이상 공개적으로 사용할 수 없으므로 가상 네트워크 내에서 액세스할 수 있습니다. 테스트하려면 가상 네트워크 내에서 가상 머신을 설정하고 사이트로 이동합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프라이빗 엔드포인트에 대한 자세한 정보](../private-link/private-endpoint-overview.md)

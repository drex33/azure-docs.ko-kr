---
title: Azure API Management의 가용성 영역 지원
description: 영역 중복을 사용하도록 설정하여 지역에서 Azure API Management 서비스 인스턴스의 복원력을 개선하는 방법을 알아봅니다.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/11/2021
ms.author: danlep
ms.custom: references_regions
ms.openlocfilehash: d43d6e7b04e117e4144356ac22ea2858408405df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582845"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Azure API Management의 가용성 영역 지원 

이 문서에서는 Azure Portal을 사용하여 API Management 인스턴스에 대해 영역 중복을 사용하는 방법을 보여 줍니다. [영역 중복](../availability-zones/az-overview.md#availability-zones)은 특정 Azure 지역(위치)에서 서비스 인스턴스에 대한 복원력 및 고가용성을 제공합니다. 영역 중복을 사용하면 API Management 인스턴스의 게이트웨이 및 컨트롤 플레인(관리 API, 개발자 포털, Git 구성)이 물리적으로 분리된 영역에 있는 데이터 센터 간에 복제되어 해당 인스턴스가 영역 오류를 복원할 수 있습니다. 

또한 API Management는 지리적으로 분산된 API 소비자가 인식하는 요청 대기 시간을 줄이고 한 지역이 오프라인으로 전환되는 경우 게이트웨이 구성 요소의 가용성을 향상하는 데 도움이 되는 [다중 지역 배포](api-management-howto-deploy-multi-region.md)를 지원합니다. 지역 내 중복을 위한 가용성 영역 및 지역 중단이 있는 경우 게이트웨이 가용성 향상을 위한 다중 지역 배포를 조합하면 API Management 인스턴스의 안정성과 성능을 둘 다 개선할 수 있습니다.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>지원되는 지역

영역 중복을 위한 API Management 구성은 현재 다음 Azure 지역에서 지원됩니다.

* 오스트레일리아 동부
* 브라질 남부
* 캐나다 중부
* 인도 중부(*)
* 미국 중부
* 미국 동부
* 미국 동부 2
* 프랑스 중부
* 독일 중서부
* 일본 동부
* 한국 중부(*)
* 북유럽
* 노르웨이 동부(*)
* 남아프리카 북부(*)
* 미국 중남부
* 동남아시아
* 영국 남부
* 서유럽
* 미국 서부 2
* 미국 서부 3

> [!IMPORTANT]
> *가 있는 지역은 Azure 구독에서 가용성 영역 지원을 사용하도록 설정하기 위해 제한된 액세스 권한을 갖습니다. Microsoft 영업 또는 고객 담당자와 협력합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요. 프리미엄 서비스 계층을 선택합니다.
* API Management 인스턴스가 [가상 네트워크](api-management-using-with-vnet.md)에 배포된 경우 영역 중복을 사용하도록 설정할 새 위치에서 가상 네트워크, 서브넷 및 공용 IP 주소를 설정해야 합니다.

## <a name="enable-zone-redundancy---portal"></a>영역 중복 사용 - 포털

포털에서 필요에 따라 API Management 서비스에 위치를 추가하거나 기존 위치의 구성을 업데이트할 때 영역 중복을 사용하도록 설정합니다.

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API** 를 선택합니다.
1. 기존 위치를 선택하거나 위쪽 표시줄에서 **+ 추가** 를 선택합니다. 해당 위치는 [가용성 영역을 지원](#supported-regions)해야 합니다.
1. 해당 위치에서 배율 **[단위](upgrade-and-scale.md)** 수를 선택합니다.
1. **가용성 영역** 에서 하나 이상의 영역을 선택합니다. 선택한 단위 수는 가용성 영역 전체에 균등하게 분산되어야 합니다. 예를 들어, 3개 단위를 선택한 경우 각 영역이 하나의 단위를 호스트하도록 3개 영역을 선택합니다.
1. API Management 인스턴스가 [가상 네트워크에](api-management-using-with-vnet.md)배포된 경우 해당 위치에서 사용할 수 있는 기존 가상 네트워크, 서브넷 및 공용 IP 주소를 선택하여 해당 위치에서 가상 네트워크 설정을 구성합니다. 기존 위치의 경우 Virtual Network 블레이드에서 가상 네트워크 및 서브넷을 구성해야 합니다.
1. **적용** 을 선택한 다음, **저장** 을 선택합니다.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="영역 중복성 사용":::

> [!IMPORTANT]
> 가용성 영역을 사용, 추가 또는 제거하는 경우 위치의 공용 IP 주소가 변경됩니다. 네트워크 설정을 사용하여 지역에서 가용성 영역을 업데이트할 때 이전에 설정한 것과 다른 공용 IP 주소 리소스를 구성해야 합니다.

> [!NOTE]
> API Management 인스턴스에 변경 내용을 적용하는 데 15~45분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포](api-management-howto-deploy-multi-region.md)하는 방법에 관해 알아봅니다.
* [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-simple-zones)을 사용하여 영역 중복을 사용하도록 설정할 수도 있습니다.
* [가용성 영역을 지원하는 Azure 서비스](../availability-zones/az-region.md)에 관해 자세히 알아봅니다.
* Azure에서 [안정성](/azure/architecture/framework/resiliency/app-design)을 보장하도록 빌드하는 방법을 자세히 알아봅니다.

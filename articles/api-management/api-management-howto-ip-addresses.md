---
title: Azure API Management 서비스의 IP 주소 | Microsoft Docs
description: Azure API Management 서비스의 IP 주소를 검색하는 방법 및 변경되는 시점에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: danlep
ms.openlocfilehash: d0fe9c305b9592aad7b08437d6e9d14150748ed6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680740"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API Management의 IP 주소

이 문서에서는 Azure API Management 서비스의 IP 주소를 검색하는 방법을 설명합니다. 서비스가 가상 네트워크에 있는 경우 IP 주소는 공용 또는 개인일 수 있습니다.

IP 주소를 사용하여 방화벽 규칙을 만들거나 백 엔드 서비스로 들어오는 트래픽을 필터링하거나 아웃바운드 트래픽을 제한할 수 있습니다.

## <a name="ip-addresses-of-api-management-service"></a>API Management 서비스의 IP 주소

개발자, 기본, 표준 또는 프리미엄 계층의 모든 API Management 서비스 인스턴스에는 공용 IP 주소가 있으며 이 주소는 해당 서비스 인스턴스에만 독점적으로 사용되며 다른 리소스와 공유되지 않습니다. 

Azure Portal의 리소스 개요 대시보드에서 IP 주소를 검색할 수 있습니다.

![API Management IP 주소](media/api-management-howto-ip-addresses/public-ip.png)

다음 API 호출을 사용하여 프로그래밍 방식으로 가져올 수도 있습니다.

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

공용 IP 주소는 응답의 일부입니다.

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

[다중 지역 배포](api-management-howto-deploy-multi-region.md)의 경우 각 지역 배포에는 하나의 공용 IP 주소가 있습니다.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet의 API Management 서비스에 대한 IP 주소

API Management 서비스가 가상 네트워크 내에 있는 경우 공용 및 개인이라는 두 가지 유형의 IP 주소를 가지게 됩니다.

공용 IP 주소는 구성을 관리하기 위해 포트 `3443`에 대한 내부 통신에 사용됩니다(예: Azure Resource Manager를 통해). 외부 VNet 구성에서는 런타임 API 트래픽에도 사용됩니다. API Management에서 퍼블릭(인터넷 연결) 백 엔드로 요청이 전송되면 공용 IP 주소가 요청의 원본으로 표시됩니다.

[내부 VNet 모드](api-management-using-with-internal-vnet.md)**에서만** 사용 가능한 VIP(개인 가상 IP) 주소는 네트워크 내에서 API Management 엔드포인트-게이트웨이, 개발자 포털, 직접 API 액세스를 위한 관리 평면에 연결하는 데 사용됩니다. 네트워크 내에서 DNS 레코드를 설정하는 데 이를 사용할 수 있습니다.

Azure Portal 및 API 호출의 응답에 두 형식의 주소가 모두 표시됩니다.

![VNet IP 주소의 API Management](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management는 VNet 외부의 연결에 대한 공용 IP 주소와 VNet 내의 연결에 대한 개인 IP 주소를 사용합니다.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>소비 계층 API Management 서비스의 IP 주소

API Management 서비스가 소비 계층 서비스인 경우 전용 IP 주소가 없습니다. 소비 계층 서비스는 결정적 IP 주소 없이 공유 인프라에서 실행됩니다. 

트래픽 제한 목적으로 Azure 데이터 센터의 IP 주소 범위를 사용할 수 있습니다. 정확한 단계는 [Azure Functions 설명서 문서](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)를 참조하세요.

## <a name="changes-to-the-ip-addresses"></a>IP 주소에 대한 변경 내용

API Management의 개발자, 기본, 표준 및 프리미엄 계층에서 VIP(공용 IP 주소)는 서비스의 수명 주기 동안 정적이며 다음과 같은 경우는 예외입니다.

* 서비스가 삭제된 다음 다시 생성되었습니다.
* 서비스 구독이 [일시 중단](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)되거나 [경고](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)된 다음(예: 미지불) 복원되었습니다.
* Azure Virtual Network는 서비스에서 추가되거나 제거됩니다.
* API Management 서비스가 외부 및 내부 VNet 배포 모드 사이에서 전환됩니다.

[다중 지역 배포](api-management-howto-deploy-multi-region.md)에서는 지역이 비워진 후 복구되는 경우 지역 IP 주소가 변경됩니다. [가용성 영역](zone-redundancy.md)을 활성화, 추가 또는 제거하면 지역 IP 주소도 변경됩니다.

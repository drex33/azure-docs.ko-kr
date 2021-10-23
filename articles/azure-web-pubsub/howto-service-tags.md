---
title: 서비스 태그 사용
titleSuffix: Azure Web PubSub service
description: 서비스 태그를 사용하여 Azure Web PubSub 서비스에 대한 아웃바운드 트래픽 허용
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/21/2021
ms.author: dayshen
ms.openlocfilehash: 2422fb24fd3608d035fe374bd08dcae49321f4f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273952"
---
# <a name="use-service-tags-for-azure-web-pubsub-service"></a>Azure Web PubSub 서비스에 대한 서비스 태그 사용

[네트워크 보안 그룹을](../virtual-network/network-security-groups-overview.md#network-security-groups)구성할 때 Azure Web PubSub 서비스에 대한 서비스 [태그를](../virtual-network/network-security-groups-overview.md#service-tags) 사용할 수 있습니다. IP 주소를 하드 코딩할 필요 없이 Azure Web PubSub 서비스 엔드포인트에 대한 인바운드/아웃바운드 네트워크 보안 규칙을 정의할 수 있습니다.

Azure Web PubSub Service는 이러한 서비스 태그를 관리합니다. 사용자 고유의 서비스 태그를 만들거나 기존 태그를 수정할 수는 없습니다. Microsoft는 서비스 태그에 맞는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

> [!Note]
> 2021년 8월 15일부터 Azure Web PubSub Service는 인바운드 및 아웃바운드 트래픽 모두에 양방향 서비스 태그를 지원합니다.

## <a name="use-service-tag-via-azure-cli"></a>Azure CLI 통해 서비스 태그 사용

### <a name="configure-outbound-traffic"></a>아웃바운드 트래픽 구성

새 아웃바운드 네트워크 보안 규칙을 추가하여 Azure Web PubSub Service에 대한 아웃바운드 트래픽을 허용할 수 있습니다.

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Outbound --destination-address-prefixes AzureWebPubSub
```

### <a name="configure-inbound-traffic"></a>인바운드 트래픽 구성

[이벤트 처리기](concept-service-internals.md#event_handler)를 사용하는 경우 새 인바운드 네트워크 보안 규칙을 추가하여 Azure Web PubSub Service의 인바운드 트래픽을 허용할 수도 있습니다.

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Inbound --source-address-prefixes AzureWebPubSub
```

## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹: 서비스 태그](../virtual-network/network-security-groups-overview.md#security-rules)
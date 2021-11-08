---
title: 서비스 태그 사용
titleSuffix: Azure Web PubSub service
description: 서비스 태그를 사용 하 여 Azure 웹 PubSub 서비스에 대 한 아웃 바운드 트래픽 허용
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 11/08/2021
ms.author: dayshen
ms.openlocfilehash: ef420efa87d80ddbadc103ad1fadece81e47e9f4
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997933"
---
# <a name="use-service-tags-for-azure-web-pubsub-service"></a>Azure 웹 PubSub 서비스에 서비스 태그 사용

[네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#network-security-groups)을 구성할 때 Azure 웹 pubsub 서비스에 대 한 [서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags) 를 사용할 수 있습니다. IP 주소를 하드 코딩 하지 않고도 Azure 웹 PubSub 서비스 끝점에 대 한 인바운드/아웃 바운드 네트워크 보안 규칙을 정의할 수 있습니다.

Azure 웹 PubSub 서비스는 이러한 서비스 태그를 관리 합니다. 사용자 고유의 서비스 태그를 만들거나 기존 태그를 수정할 수는 없습니다. Microsoft는 서비스 태그에 맞는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

> [!Note]
> 2021 년 8 월 15 일부 터 Azure 웹 PubSub 서비스는 인바운드 및 아웃 바운드 트래픽 모두에 양방향 서비스 태그를 지원 합니다.

## <a name="use-service-tag-via-azure-cli"></a>Azure CLI를 통해 서비스 태그 사용

### <a name="configure-outbound-traffic"></a>아웃바운드 트래픽 구성

새 아웃 바운드 네트워크 보안 규칙을 추가 하 여 Azure 웹 PubSub 서비스에 대 한 아웃 바운드 트래픽을 허용할 수 있습니다.

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Outbound --destination-address-prefixes AzureWebPubSub
```

### <a name="configure-inbound-traffic"></a>인바운드 트래픽 구성

[이벤트 처리기](concept-service-internals.md#event_handler)를 사용 하는 경우 새 인바운드 네트워크 보안 규칙을 추가 하 여 Azure 웹 Pubsub 서비스에서 인바운드 트래픽을 허용할 수도 있습니다.

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Inbound --source-address-prefixes AzureWebPubSub
```

## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹: 서비스 태그](../virtual-network/network-security-groups-overview.md#security-rules)
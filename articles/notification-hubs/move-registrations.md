---
title: Azure Notification Hubs 리소스를 한 지역에서 다른 지역으로 이동
description: Azure Notification Hubs 리소스를 다른 Azure 지역으로 이동하는 방법을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: 0de15ab1f16ab5bc234ad6430eae5f65e608753e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634199"
---
# <a name="move-resources-between-azure-regions"></a>Azure 지역 간에 리소스 이동

이 문서에서는 Azure Notification Hubs 리소스를 다른 Azure 지역으로 이동하는 방법을 설명합니다. 높은 수준에서 프로세스는 다음과 같습니다.

1. 다른 이름으로 대상 네임스페이스를 만듭니다.
1. 이전 네임스페이스에서 등록을 내보냅니다.
1. 원하는 지역의 새 네임스페이스로 등록을 가져옵니다.

## <a name="overview"></a>개요

일부 시나리오에서는 다양한 비즈니스 이유로 Azure 지역 간에 서비스 리소스를 이동해야 할 수 있습니다. 새로 사용 가능한 지역으로 이동하거나, 특정 지역에서만 사용할 수 있는 기능 또는 서비스를 배포하거나, 내부 정책 또는 규정 준수 요구 사항으로 인해 이동하거나, 용량 문제를 해결할 수 있습니다.

Azure Notification Hubs 네임스페이스 이름은 고유하며 등록은 허브별로 수행되므로 이러한 이동을 수행하려면 원하는 지역에 새 허브를 만든 다음 등록을 다른 모든 관련 데이터와 함께 새로 만든 네임스페이스로 이동해야 합니다.

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>다른 이름의 Notification Hubs 네임스페이스 만들기

다음 단계에 따라 새 Notification Hubs 네임스페이스를 만듭니다. 네임스페이스에 대해 원하는 대상 지역을 포함하여 **기본 사항** 탭에 필요한 모든 정보를 입력합니다.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

새 네임스페이스가 만들어지면 새 네임스페이스에서 PNS 자격 증명을 설정하고 새 네임스페이스에서 동등한 정책을 만들어야 합니다.

## <a name="exportimport-registrations"></a>등록 내보내기/가져오기

리소스를 이동하려는 지역에 새 네임스페이스가 만들어지면 모든 등록을 대량으로 내보내고 새 네임스페이스로 가져옵니다. 이렇게 하려면 [Azure Notification Hubs 등록을 대량으로 내보내기 및 가져오기를 참조하세요.](export-modify-registrations-bulk.md)

## <a name="delete-the-previous-namespace-optional"></a>이전 네임스페이스 삭제(선택 사항)

이전 네임스페이스에서 새 네임스페이스로 등록 내보내기를 완료한 후 원하는 경우 이전 네임스페이스를 삭제할 수 있습니다.

1. 이전 지역의 기존 네임스페이스로 이동합니다.

2. **삭제를** 클릭한 다음 네임스페이스 삭제 창에 **네임스페이스** 이름을 다시 입력합니다.

3. **네임스페이스** **삭제** 창의 맨 아래에서 삭제를 클릭합니다.

## <a name="next-steps"></a>다음 단계

다음 문서는 지역 이동 문서가 있는 다른 서비스의 예입니다.

- [NSG를 다른 지역으로 이동](/azure/virtual-network/move-across-regions-nsg-portal)
- [공용 IP 주소를 다른 지역으로 이동](/azure/virtual-network/move-across-regions-publicip-portal)
- [스토리지 계정을 다른 지역으로 이동](/azure/storage/common/storage-account-move?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
- [영역 간 이동(리소스 그룹에서)](/azure/resource-mover/move-region-within-resource-group#:~:text=1%20In%20the%20Azure%20portal%2C%20open%20the%20relevant,you%20want%20to%20move.%20...%20More%20items...%20)

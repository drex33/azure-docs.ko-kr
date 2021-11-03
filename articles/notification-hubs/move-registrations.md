---
title: 한 지역에서 다른 지역으로 Azure Notification Hubs 리소스 이동
description: Azure Notification Hubs 리소스를 다른 Azure 지역으로 이동 하는 방법을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: ae1f52ea007746ab162f13edf77f34fe8c8157c1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004989"
---
# <a name="move-resources-between-azure-regions"></a>Azure 지역 간에 리소스 이동

이 문서에서는 Azure Notification Hubs 리소스를 다른 Azure 지역으로 이동 하는 방법을 설명 합니다. 높은 수준에서 프로세스는 다음과 같습니다.

1. 다른 이름을 사용 하 여 대상 네임 스페이스를 만듭니다.
1. 이전 네임 스페이스에서 등록을 내보냅니다.
1. 원하는 지역의 새 네임 스페이스로 등록을 가져옵니다.

## <a name="overview"></a>개요

일부 시나리오에서는 다양 한 비즈니스 이유로 Azure 지역 간에 서비스 리소스를 이동 해야 할 수 있습니다. 새로 사용 가능한 지역으로 이동할 수 있으며, 특정 지역 에서만 사용할 수 있는 기능 또는 서비스를 배포 하거나, 내부 정책 또는 준수 요구 사항으로 인해 이동 하거나, 용량 문제를 해결할 수 있습니다.

Azure Notification Hubs 네임 스페이스 이름은 고유 하 고 등록은 허브 당 이므로 이러한 이동을 수행 하려면 원하는 지역에 새 허브를 만든 후 다른 모든 관련 데이터와 함께 등록을 새로 만든 네임 스페이스로 이동 해야 합니다.

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>다른 이름을 사용 하 여 Notification Hubs 네임 스페이스 만들기

새 Notification Hubs 네임 스페이스를 만들려면 다음 단계를 수행 합니다. **기본** 정보 탭에서 네임 스페이스에 대 한 원하는 대상 지역을 포함 하 여 필요한 모든 정보를 입력 합니다.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

새 네임 스페이스를 만든 후 새 네임 스페이스에 PNS 자격 증명을 설정 하 고 새 네임 스페이스에 동등한 정책을 만들어야 합니다.

## <a name="exportimport-registrations"></a>등록 내보내기/가져오기

리소스를 이동 하려는 지역에서 새 네임 스페이스를 만들었으면 모든 등록을 대량으로 내보내고 새 네임 스페이스로 가져옵니다. 이렇게 하려면 [Azure Notification Hubs 등록을 대량으로 내보내기 및 가져오기](export-modify-registrations-bulk.md)를 참조 하세요.

## <a name="delete-the-previous-namespace-optional"></a>이전 네임 스페이스를 삭제 합니다 (선택 사항).

이전 네임 스페이스에서 새 네임 스페이스로 등록 내보내기를 완료 한 후 필요한 경우 이전 네임 스페이스를 삭제할 수 있습니다.

1. 이전 지역의 기존 네임 스페이스로 이동 합니다.

2. **삭제** 를 클릭 한 다음 **네임 스페이스 삭제** 창에서 네임 스페이스 이름을 다시 입력 합니다.

3. **네임 스페이스 삭제** 창의 맨 아래에서 **삭제** 를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서는 지역 이동 문서를 포함 하는 다른 서비스의 예입니다.

- [다른 지역으로 NSGs 이동](../virtual-network/move-across-regions-nsg-portal.md)
- [공용 IP 주소를 다른 지역으로 이동](../virtual-network/move-across-regions-publicip-portal.md)
- [스토리지 계정을 다른 지역으로 이동](../storage/common/storage-account-move.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [영역 간 이동(리소스 그룹에서)](../resource-mover/move-region-within-resource-group.md)

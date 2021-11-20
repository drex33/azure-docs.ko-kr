---
title: Azure Notification Hubs의 가용성 영역
description: Azure Notification Hubs을 사용 하 여 가용성 영역 및 고가용성에 대해 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: conceptual
ms.date: 11/19/2021
ms.custom: template-concept
ms.openlocfilehash: 8c1ac6d15aeef7aa54459cffa09d28b8bb2f7a50
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871792"
---
# <a name="availability-zones"></a>가용성 영역

이제 azure Notification Hubs는 [가용성 영역](/azure/availability-zones/az-overview)을 지원 하 여 동일한 Azure 지역 내에서 오류 격리 위치를 제공 합니다. 복원 력을 보장 하기 위해 모든 가용성 영역 사용 지역에는 세 가지 별도의 가용성 영역이 있습니다. 가용성 영역을 사용 하는 경우 데이터와 메타 데이터가 모두 가용성 영역의 데이터 센터에 복제 됩니다.

## <a name="feature-availability"></a>기능 가용성

가용성 영역 지원은 예정 된 Azure Notification Hubs Premium SKU의 일부로 포함 될 예정입니다. 가용성 영역이 있는 [Azure 지역](/azure/availability-zones/az-region) 에서만 사용할 수 있습니다.

> [!NOTE]
> Azure Notification Hubs Premium 릴리스될 때까지 가용성 영역은 초대만을 기준으로 합니다. 이 기능을 사용 하는 데 관심이 있는 경우 Microsoft의 고객 성공 관리자에 게 문의 하거나 지원 팀에서 심사 될 Azure 지원 티켓을 만드세요.

## <a name="enable-availability-zones"></a>가용성 영역 사용

이번에는 새 네임 스페이스에 대해서만 가용성 영역을 사용할 수 있습니다. Notification Hubs는 기존 네임 스페이스의 마이그레이션을 지원 하지 않습니다. 네임스페이스를 사용하도록 설정한 후에는 영역 중복성을 사용하지 않도록 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](/azure/availability-zones/az-overview)
- [가용성 영역을 지 원하는 Azure 서비스](/azure/availability-zones/az-region)

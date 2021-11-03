---
title: 여러 작업 영역에서 한 번에 Azure Sentinel 인시던트 사용 | Microsoft Docs
description: Azure Sentinel에서 동시에 여러 작업 영역에 있는 인시던트를 확인하는 방법입니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c2063356b62a3b91bf3bf04453ea05f28788cab5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009380"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>한 번에 여러 작업 영역에서 인시던트 작업 

 [!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel의 기능을 최대한 활용하려면 단일 작업 영역 환경을 사용하는 것이 좋습니다. 그러나 일부 경우에는 여러 테넌트에 걸친 [MSSP(관리형 보안 서비스 공급자)](./multiple-tenants-service-providers.md) 및 고객의 경우와 같이 여러 작업 영역을 포함해야 하는 몇 가지 사용 사례가 있습니다. **여러 작업 영역 보기** 를 사용하면 테넌트 간에도 여러 작업 영역에서 동시에 보안 인시던트를 보고 사용하여 조직의 보안 응답성을 완벽하게 보고 제어할 수 있습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="entering-multiple-workspace-view"></a>여러 작업 영역 보기 입력

Azure Sentinel을 열면 선택한 모든 테넌트 및 구독에서 액세스 권한이 있는 모든 작업 영역 목록이 표시됩니다. 각 작업 영역 이름의 왼쪽에는 확인란이 있습니다. 단일 작업 영역의 이름을 클릭하면 해당 작업 영역으로 이동합니다. 여러 작업 영역을 선택하려면 해당 확인란을 모두 클릭한 다음 페이지 맨 위에 있는 **여러 작업 영역 보기** 단추를 클릭합니다.

> [!IMPORTANT]
> 현재 여러 작업 영역 보기에서는 동시에 표시되는 최대 10개의 작업 영역을 지원합니다. 
> 
> 작업 영역을 10개 이상 선택하면 경고 메시지가 표시됩니다.

작업 영역 목록에서 각 작업 영역과 연결된 디렉터리, 구독, 위치, 리소스 그룹을 볼 수 있습니다. 디렉터리는 테넌트에 해당합니다.

   ![여러 작업 영역 선택](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>인시던트 작업

**여러 작업 영역 보기** 에서는 현재 **인시던트** 화면만 사용할 수 있습니다. 일반적인 **인시던트** 화면과 마찬가지로 대부분의 방법으로 표시되고 기능합니다. 그러나 다음과 같은 몇 가지 중요한 차이점이 있습니다.

   ![여러 작업 영역에서 인시던트 보기](./media/multiple-workspace-view/incidents.png)

- *인시던트 열기*, *새 인시던트*, *진행 중* 등과 같이 페이지 위에 있는 카운터에 선택한 모든 작업 영역에 대한 수를 통칭하여 표시합니다.

- 선택한 모든 작업 영역 및 디렉터리(테넌트)의 인시던트를 하나의 통합된 목록에서 볼 수 있습니다. 일반 **인시던트** 화면의 필터 외에도 작업 영역 및 디렉터리를 기준으로 목록을 필터링할 수 있습니다.

- 인시던트를 선택한 모든 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다. 일부 작업 영역에 대한 읽기 권한만 있는 경우 해당 작업 영역에서 인시던트를 선택하면 경고 메시지가 표시됩니다. 해당 인시던트 또는 이와 함께 선택한 다른 모든 대상(권한이 있는 경우라도)을 수정할 수 없습니다.

- 단일 인시던트를 선택하고 **전체 세부 정보 보기** 또는 **작업** > **조사** 를 클릭하는 경우에는 해당 인시던트의 작업 영역의 데이터 컨텍스트에서 있으며 다른 사용자가 없는 것입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 여러 Azure Sentinel 작업 영역에서 동시에 인시던트를 보고 사용하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.

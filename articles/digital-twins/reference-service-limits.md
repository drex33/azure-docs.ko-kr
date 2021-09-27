---
title: 서비스 제한
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 서비스의 한도를 보여 주는 차트입니다.
author: baanders
ms.author: baanders
ms.date: 09/02/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ab7e10b32d3cf99b17151bea61b8cb5542e0ef3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763598"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins 서비스 제한

다음 섹션에서는 Azure Digital Twins의 서비스 제한에 대해 설명합니다.

> [!NOTE]
> 이 서비스의 일부 영역에는 조정 가능한 한도가 있습니다. 아래 표에 조정 가능? 열과 함께 표시되어 있습니다. 제한을 조정할 수 있는 경우 조정 가능? 값은 예입니다.
>
> 비즈니스에서 조정 가능한 제한 또는 기본 제한보다 높은 할당량이 필요한 경우 [지원 티켓을 열어](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 추가 리소스를 요청할 수 있습니다.

## <a name="limits-by-type"></a>유형별 한도

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>제한 작업

제한에 도달하면 서비스에서 제한을 초과한 모든 요청이 제한되며, 이로 인해 이러한 요청에서 429 오류 응답이 발생합니다.

제한을 관리하기 위해 제한 작업에 대한 몇 가지 권장 사항은 다음과 같습니다.
* **재시도 논리 사용.** [Azure Digital Twins SDK](concepts-apis-sdks.md)는 실패한 요청에 대한 재시도 논리를 구현하므로 제공된 SDK로 작업하는 경우 이 기능이 이미 기본 제공됩니다. 그렇지 않으면 자체 애플리케이션에서 재시도 논리를 구현하는 것이 좋습니다. 서비스는 다시 시도하기 전에 대기하는 시간을 결정하는 데 사용할 수 있는 오류 응답의 `Retry-After` 헤더를 다시 보냅니다.
* **임계값 및 알림을 사용하여 근접 한도에 대해 경고합니다.** Azure Digital Twins에 대한 서비스 제한 중 일부에는 이러한 영역에서 사용량을 추적하는 데 사용할 수 있는 해당 [메트릭](troubleshoot-metrics.md)이 있습니다. 임계값에 도달했을 때 임계값을 구성하고 메트릭에 대한 경고를 설정하려면 [문제 해결: 경고 설정](troubleshoot-alerts.md)의 지침을 참조하세요. 메트릭이 제공되지 않는 기타 제한에 대한 알림을 설정하려면 고유한 애플리케이션 코드에서 이 논리를 구현하는 것이 좋습니다.
* **여러 인스턴스에 대규모로 배포합니다.** 단일 실패 지점이 없도록 해야 합니다. 전체 배포에 대한 하나의 큰 그래프 대신 여러 인스턴스에서 트윈의 하위 집합을 논리적으로(예: 지역 또는 테넌트별) 분할하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

서비스 개요에서 Azure Digital Twins의 현재 릴리스에 대해 알아봅니다.
* [Azure Digital Twins란?](overview.md)

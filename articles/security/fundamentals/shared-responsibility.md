---
title: 클라우드의 공동 책임 - Microsoft Azure
description: 공유 책임 모델 및 클라우드 공급자가 처리하는 보안 작업과 고객이 처리하는 보안 작업을 이해합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2021
ms.author: terrylan
ms.openlocfilehash: 14d53d3f58c1bbdb03a5ac510f6a2e44157906f9
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272116"
---
# <a name="shared-responsibility-in-the-cloud"></a>클라우드의 공동 책임

공용 클라우드 서비스를 고려하고 평가할 때 책임 분담 모델과 클라우드 공급자가 처리하는 보안 작업과 사용자 본인이 처리하는 작업을 이해하는 것이 중요합니다. 워크로드 책임은 워크로드가 SaaS(Software as a service), PaaS(Platform as a service), IaaS(Infrastructure as a service) 또는 온-프레미스 데이터 센터 중 어디에 호스트되는지에 따라 달라집니다.

## <a name="division-of-responsibility"></a>책임 분담
온-프레미스 데이터 센터에서는 전체 스택을 소유합니다. 클라우드로 이동하면 일부 책임이 Microsoft로 이전됩니다. 다음 다이어그램에서는 스택의 배포 유형에 따라 사용자와 Microsoft 간의 책임 영역을 보여 줍니다.

:::image type="content" source="media/shared-responsibility/shared-responsibility.svg" alt-text="책임 영역을 보여주는 다이어그램" border="false":::

모든 클라우드 배포 유형에서 사용자는 고유의 데이터와 ID를 소유합니다. 사용자는 자신이 제어하는 데이터 및 ID 보안, 온-프레미스 리소스 및 클라우드 구성 요소(서비스 유형에 따라 다름)의 보안을 보호하는 데 책임이 있습니다.

배포 형식에 관계없이 다음 책임은 항상 유지됩니다.

- 데이터
- 엔드포인트
- 계정
- 액세스 관리

## <a name="cloud-security-advantages"></a>클라우드 보안 이점
클라우드는 장기적인 정보 보안 문제를 해결하기 위한 상당한 이점을 제공합니다. 온-프레미스 환경에서 조직은 충족되지 않은 책임과 제한된 리소스를 보안에 투자할 가능성이 높으며, 이로 인해 공격자가 모든 계층의 취약점을 악용할 수 있는 환경이 만들어집니다.

다음 다이어그램에서는 제한된 리소스 때문에 많은 보안 책임이 충족되지 않는 기존의 접근 방식을 보여 줍니다. 클라우드를 사용하는 방법에서는 클라우드 공급자에게 일상적인 보안 책임을 이동하고 리소스를 다시 할당할 수 있습니다.

:::image type="content" source="media/shared-responsibility/cloud-enabled-security.svg" alt-text="클라우드 시대 보안 이점을 보여주는 다이어그램" border="false":::

클라우드를 사용하는 방법에서는 클라우드 기반 보안 기능을 활용하여 더 많은 효과를 얻을 수 있으며, 클라우드 인텔리전스를 사용하여 위협 감지 및 응답 시간을 개선할 수도 있습니다. 조직은 클라우드 공급자에 책임을 전가함으로써 보안 적용 범위를 더 넓힐 수 있으며, 보안 리소스와 예산을 다른 비즈니스 우선 순위에 다시 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계
SaaS, PaaS 및 IaaS 배포에서 사용자와 Microsoft 간의 책임 분할에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공동 책임](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/)을 참조하세요.

---
title: Azure Communication Services에 대 한 Azure Advisor 활용
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services에 대 한 Azure Advisor 제품에 대해 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 6c229f4afddc77e9b85f0dbd7fb4669facf8788e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983991"
---
# <a name="azure-advisor-for-azure-communication-services"></a>Azure Communication Services에 대 한 Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) 는 Azure 배포를 최적화하기 위한 모범 사례를 따르는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. Azure 통신 서비스는 Azure Advisor 등록 되며, 통신 리소스를 최적화 하는 방법에 대 한 권장 사항을 게시 합니다. [Advisor 블레이드의](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview) [Azure Portal](https://portal.azure.com) 에서 이러한 권장 사항을 볼 수 있습니다. 권장 사항는 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)에 저장 되며 [ARM 템플릿](../../advisor/advisor-alerts-arm.md) 또는 [포털](../../advisor/advisor-alerts-portal.md)을 통해 이러한 권장 사항에 대 한 경고를 구성할 수 있습니다. 

## <a name="install-the-latest-sdks"></a>최신 Sdk 설치

최신 수정 및 업데이트를 확인 하려면 항상 최신 Sdk를 사용 하 여 최신 상태로 유지 하는 것이 좋습니다. 사용 중인 SDK의 최신 버전이 있는 경우 최신 SDK로 업데이트 하기 위한 권장 사항이 **성능** 범주에 표시 됩니다.

![채팅 SDK를 업데이트 하는 권장 사항을 보여 주는 예제를 Azure Advisor 합니다.](./media/advisor-chat-sdk-update-example.png)

지원 되는 모든 언어와 함께이 기능에 대해 지원 되는 Sdk는 다음과 같습니다. 이 기능은 일반적으로 사용할 수 있는 최신 버전의 Sdk에 대 한 권장 사항만을 보냅니다. 베타 또는 미리 보기 버전은 권장 사항이 나 경고를 트리거하지 않습니다. 사용 가능한 [SDK 옵션](./sdk-options.md) 에 대해 자세히 알아볼 수 있습니다.

* 호출 (클라이언트)
* 채팅
* SMS
* ID
* 전화 번호
* 관리
* Network Traversal
* 통화 자동화

## <a name="next-steps"></a>다음 단계

다음 문서는 사용자에게 유용할 수 있습니다.

- [로깅 및 진단](./logging-and-diagnostics.md)
- [메트릭](./metrics.md)

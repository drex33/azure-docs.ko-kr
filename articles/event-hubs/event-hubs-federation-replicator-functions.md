---
title: 이벤트 복제 작업 및 애플리케이션 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서 Azure Functions를 사용하여 이벤트 복제 작업 및 애플리케이션을 빌드하는 방법에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 92c6357e5c4302232bddabdd1e1818e2f325246b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217337"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Azure Functions를 사용하는 이벤트 복제 작업 및 애플리케이션

> [!TIP]
> 이벤트의 페이로드를 고려하고 변환, 집계, 보강하거나 줄이는 데 필요한 모든 상태 저장 복제 작업의 경우 Azure Functions 대신 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)를 사용합니다.

[이벤트 복제 및 지역 간 페더레이션](event-hubs-federation-overview.md) 문서에 설명된 대로, Event Hubs 쌍 사이 및 Event Hubs 사이에 발생하는 이벤트 스트림의 상태 비저장 복제와 기타 이벤트 스트림 원본 및 대상은 Azure Functions를 사용합니다.

[Azure Functions](../azure-functions/functions-overview.md)는 이벤트 복제 및 페더레이션 작업을 포함하여 서버리스 애플리케이션을 구성하고 실행하기 위한 스케일링 가능하고 신뢰할 수 있는 실행 환경입니다.

이 개요에서는 해당 애플리케이션에 대한 Azure Functions의 기본 제공 기능, 변환 작업에 맞게 조정하고 수정할 수 있는 코드 블록, Event Hubs 및 다른 Azure Messaging 서비스와 이상적으로 통합되도록 Azure Functions 애플리케이션을 구성하는 방법에 관해 알아봅니다. 자세한 내용은 이 문서에서 가리키는 Azure Functions 설명서를 참조하세요.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]










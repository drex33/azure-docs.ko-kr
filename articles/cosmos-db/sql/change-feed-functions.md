---
title: Azure Functions에서 Azure Cosmos DB 변경 피드를 사용하는 방법
description: Azure Functions를 사용하여 Azure Cosmos DB 변경 피드에 연결합니다. 나중에 모든 새 이벤트에서 트리거되는 반응형 Azure 함수를 만들 수 있습니다.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 01fb12957a9dce42a53861daebab796047cb6f84
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116476"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB 및 Azure Functions를 사용한 서버리스 이벤트 기반 아키텍처
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Functions는 [변경 피드](../change-feed.md)에 연결하는 가장 간단한 방법을 제공합니다. Azure Cosmos 컨테이너의 변경 피드에서 새 이벤트마다 자동으로 트리거되는 작은 반응형 Azure Functions를 만들 수 있습니다.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Cosmos DB용 Azure Functions 트리거를 사용하는 서버리스 이벤트 기반 함수" border="false":::

[Cosmos DB용 Azure Functions 트리거](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)를 사용하면 [작업자 인프라](change-feed-processor.md)를 유지 관리할 필요 없이 [변경 피드 프로세서](change-feed-processor.md)의 확장 및 안정적인 이벤트 감지 기능을 활용할 수 있습니다. 나머지 이벤트 소싱 파이프라인에 대해 걱정하지 않고 Azure 함수의 논리에만 집중하세요. 트리거를 다른 [Azure Functions 바인딩](../../azure-functions/functions-triggers-bindings.md#supported-bindings)과 혼합할 수도 있습니다.

> [!NOTE]
> 현재 Cosmos DB용 Azure Functions 트리거는 Core(SQL) API에서만 사용하도록 지원됩니다.

## <a name="requirements"></a>요구 사항

서버리스 이벤트 기반 흐름을 구현하려면 다음이 필요합니다.

* **모니터링된 컨테이너**: 모니터링된 컨테이너는 모니터링된 Azure Cosmos 컨테이너이며 변경 피드가 생성되는 데이터를 저장합니다. 모니터링된 컨테이너에 대한 모든 삽입 및 업데이트는 컨테이너의 변경 피드에 반영됩니다.
* **임대 컨테이너**: 임대 컨테이너는 여러 동적 서버리스 Azure Function 인스턴스에서 상태를 유지하고 동적 크기 조정을 사용합니다. 이 임대 컨테이너는 Cosmos DB용 Azure Functions 트리거에 의해 수동 또는 자동으로 생성될 수 있습니다. 임대 컨테이너를 자동으로 만들려면 [구성](../../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)에서 *CreateLeaseCollectionIfNotExists* 플래그를 설정합니다. 분할된 임대 컨테이너에는 `/id` 파티션 키 정의가 필요합니다.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB용 Azure Functions 트리거 만들기

이제 모든 Azure Functions IDE 및 CLI 통합에서 Cosmos DB용 Azure Functions 트리거를 사용하여 Azure 함수 만들기가 지원됩니다.

* Visual Studio 사용자를 위한 [Visual Studio 확장](../../azure-functions/functions-develop-vs.md).
* Visual Studio Code 사용자를 위한 [Visual Studio Code 확장](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
* 마지막으로 플랫폼 간 IDE에 독립적인 환경을 위한 [핵심 CLI 도구](../../azure-functions/functions-run-local.md#create-func).

## <a name="run-your-trigger-locally"></a>로컬로 트리거 실행

[Azure Cosmos DB 에뮬레이터](../local-emulator.md)를 사용하여 [Azure 함수를 로컬에서](../../azure-functions/functions-develop-local.md) 실행하여 Azure 구독이나 비용 발생 없이 서버리스 이벤트 기반 흐름을 만들고 개발할 수 있습니다.

클라우드에서 라이브 시나리오를 테스트하려는 경우 신용 카드나 Azure 구독 없이 [Cosmos DB를 무료로 사용해 보세요](https://azure.microsoft.com/try/cosmosdb/).

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](../change-feed.md)
* [변경 피드를 읽는 방법](read-change-feed.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
* [변경 피드 프로세서 라이브러리를 사용하는 방법](change-feed-processor.md)
* [Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md)

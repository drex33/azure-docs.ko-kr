---
title: 변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET V3 SDK로 마이그레이션
description: 변경 피드 프로세서 라이브러리를 사용하여 Azure Cosmos DB SDK V3로 애플리케이션을 마이그레이션하는 방법 알아보기
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 498b304e5b0e1deeca2b923e9f1312ea0cebf6bf
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117086"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET V3 SDK로 마이그레이션
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 [변경 피드 프로세서 라이브러리](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet)를 사용하는 기존 애플리케이션 코드를 최신 버전의 .NET SDK(.NET V3 SDK라고도 함)의 [변경 피드](../change-feed.md) 기능으로 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="required-code-changes"></a>필요한 코드 변경

.NET V3 SDK에는 몇 가지 주요 변경 내용이 포함되어 있으며, 애플리케이션을 마이그레이션하는 주요 단계는 다음과 같습니다.

1. `DocumentCollectionInfo` 인스턴스를 모니터링 대상 및 임대 컨테이너에 대한 `Container` 참조로 변환합니다.
1. `WithProcessorOptions`를 사용하는 사용자 지정은 간격으로 `WithLeaseConfiguration` 및 `WithPollInterval`을 사용하고, [시작 시간](./change-feed-processor.md#starting-time)으로 `WithStartTime`를 사용하고 `WithMaxItems`를 사용하여 최대 항목 개수를 정의하도록 업데이트해야 합니다.
1. `ChangeFeedProcessorOptions.LeasePrefix`에 구성된 값과 일치하도록 `GetChangeFeedProcessorBuilder`에 대해 `processorName`을 설정하고, 그렇지 않은 경우 `string.Empty`를 사용합니다.
1. 변경 내용은 더 이상 `IReadOnlyList<Document>`로 전달되지 않습니다. 대신 `IReadOnlyCollection<T>`입니다. 여기서 `T`는 정의해야 하는 형식이며, 기본 항목 클래스는 더 이상 없습니다.
1. 변경 내용을 처리하기 위해 더 이상 구현이 필요하지 않으며 대신 [대리자를 정의](change-feed-processor.md#implementing-the-change-feed-processor)해야 합니다. 대리자는 고정 함수이거나 실행 간에 상태를 유지해야 하는 경우 고유한 클래스를 만들고 인스턴스 메서드를 대리자로 전달할 수 있습니다.

예를 들어, 변경 피드 프로세서를 빌드하는 원래 코드는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

마이그레이션된 코드는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

또한 대리자는 정적 메서드일 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>상태 및 임대 컨테이너

변경 피드 프로세서 라이브러리와 마찬가지로 .NET V3 SDK의 변경 피드 기능은 [임대 컨테이너](change-feed-processor.md#components-of-the-change-feed-processor)를 사용하여 상태를 저장합니다. 그러나 스키마는 다릅니다.

SDK V3 변경 피드 프로세서는 마이그레이션된 애플리케이션 코드를 처음 실행할 때 모든 이전 라이브러리 상태를 검색하고 새 스키마로 자동으로 마이그레이션합니다. 

이전 코드를 사용하여 애플리케이션을 안전하게 중지하고, 코드를 새 버전으로 마이그레이션하고, 마이그레이션된 애플리케이션을 시작하고, 애플리케이션이 중지되는 동안 발생한 모든 변경 내용을 새 버전에서 선택하여 처리합니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 프로세서 개요](change-feed-processor.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
* [변경 피드 프로세서 시작 시간](./change-feed-processor.md#starting-time)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
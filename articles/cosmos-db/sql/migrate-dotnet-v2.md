---
title: Azure Cosmos DB .NET SDK 2.0(Microsoft.Azure.Cosmos)을 사용하도록 애플리케이션 마이그레이션
description: 기존 .NET 애플리케이션을 v1 SDK에서 .NET SDK v2 for Core(SQL) API로 업그레이드하는 방법을 알아봅니다.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: ab7abdedd05c0b7b392279b793b572cf6bd248c5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116367"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Azure Cosmos DB .NET SDK v2를 사용하도록 애플리케이션 마이그레이션
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> 현재 .NET SDK의 v3을 사용할 수 있으며 v2에서 v3으로의 마이그레이션 계획은 [여기](migrate-dotnet-v3.md)에서 확인할 수 있습니다. Azure Cosmos DB .NET SDK v2에 대한 자세한 내용은 [릴리스 정보](sql-api-sdk-dotnet.md), [.NET GitHub 리포지토리](https://github.com/Azure/azure-cosmos-dotnet-v2), .NET SDK v2 [성능 팁](performance-tips.md) 및 [문제 해결 가이드](troubleshoot-dot-net-sdk.md)를 참조하세요.
>

이 문서에서는 기존 v1 .NET 애플리케이션을 Azure Cosmos DB .NET SDK v2 for Core(SQL) API로 업그레이드하기 위한 몇 가지 고려 사항을 강조합니다. Azure Cosmos DB .NET SDK v2는 `Microsoft.Azure.DocumentDB` 네임스페이스에 해당합니다. 다음 Azure Cosmos DB .NET 플랫폼에서 애플리케이션을 마이그레이션하여 v2 SDK `Microsoft.Azure.Cosmos`를 사용하는 경우 이 문서에 제공된 정보를 사용할 수 있습니다.

* SQL API용 Azure Cosmos DB .NET Framework v1 SDK
* Azure Cosmos DB .NET Core SDK v1 for SQL API

## <a name="whats-available-in-the-net-v2-sdk"></a>.NET v2 SDK에서 사용할 수 있는 기능

v2 SDK에는 다음을 비롯한 많은 사용성 및 성능 개선 사항이 포함되어 있습니다.

* 비 Windows 클라이언트에 대한 TCP 직접 모드 지원
* 다중 지역 쓰기 지원
* 쿼리 성능 향상
* 지리 공간/기하학 컬렉션 및 인덱싱 지원
* 직접/TCP 전송 진단에 대한 향상된 개선 사항
* 설정된 연결 수를 줄이기 위한 직접 TCP 전송 스택 업데이트
* RequestTimeout의 대기 시간 감소 개선

SDK의 대부분의 재시도 논리와 하위 수준은 크게 변경되지 않은 상태로 유지됩니다.

## <a name="why-migrate-to-the-net-v2-sdk"></a>.NET v2 SDK로 마이그레이션하는 이유

수많은 성능 향상 외에도 최신 SDK에 대한 새로운 기능 투자는 이전 버전으로 다시 포팅되지 않습니다.

또한 이전 SDK는 최신 버전으로 대체되고 v1 SDK는 [유지 관리 모드](sql-api-sdk-dotnet.md)로 전환됩니다. 최상의 개발 환경을 위해 애플리케이션을 최신 버전의 SDK로 마이그레이션하는 것이 좋습니다.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>v1 SDK에서 v2 SDK로의 주요 변경 사항

### <a name="direct-mode--tcp"></a>직접 모드 + TCP

.NET v2 SDK는 이제 직접 모드와 게이트웨이 모드를 모두 지원합니다. 직접 모드는 TCP 프로토콜을 통한 연결을 지원하고 더 적은 수의 네트워크 홉으로 백 엔드 복제본에 직접 연결하므로 더 나은 성능을 제공합니다.

자세한 내용은 [Azure Cosmos DB SQL SDK 연결 모드 가이드](sql-sdk-connection-modes.md)를 참조하세요.

### <a name="session-token-formatting"></a>세션 토큰 형식

v2 SDK는 더 이상 v1에서 사용되는 *단순* 세션 토큰 형식을 사용하지 않고 대신 SDK에서 *벡터* 형식을 사용합니다. 형식은 상호 교환할 수 없으므로 버전이 다른 클라이언트 애플리케이션에 전달할 때 형식을 변환해야 합니다.

자세한 내용은 [.NET SDK에서 세션 토큰 형식 변환](how-to-convert-session-token.md)을 참조하세요.

### <a name="using-the-net-change-feed-processor-sdk"></a>.NET 변경 피드 프로세서 SDK 사용

.NET 변경 피드 프로세서 라이브러리 2.1.x에는 `Microsoft.Azure.DocumentDB` 2.0 이상이 필요합니다.

2\.1.x 라이브러리에는 다음과 같은 주요 변경 사항이 있습니다.

* 안정성 및 진단 기능 향상
* 오류 및 예외 처리 개선
* 분할된 임대 컬렉션에 대한 추가 지원
* 추가 오류 처리 및 추적을 위해 `ChangeFeedDocument` 인터페이스 및 클래스를 구현하는 고급 확장
* 파티션당 연속 토큰을 유지하기 위해 사용자 지정 저장소를 사용하기 위한 지원이 추가됨

자세한 내용은 변경 피드 프로세서 라이브러리 [릴리스 정보](sql-api-sdk-dotnet-changefeed.md)를 참조하세요.

### <a name="using-the-bulk-executor-library"></a>대량 실행기 라이브러리 사용

v2 대량 실행기 라이브러리는 현재 Azure Cosmos DB .NET SDK 2.5.1 이상에서만 사용됩니다.

자세한 내용은 [Azure Cosmos DB 대량 실행기 라이브러리 개요](../bulk-executor-overview.md) 및 .NET 대량 실행기 라이브러리 [릴리스 정보](sql-api-sdk-bulk-executor-dot-net.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 최대 성능을 달성하기 위해 애플리케이션을 최적화하기 위해 Azure Cosmos DB for SQL API v2를 사용하는 [추가 성능 팁](sql-api-get-started.md)에 대해 읽어보세요.
* [v2 SDK로 수행할 수 있는 작업](sql-api-dotnet-samples.md)에 대해 자세히 알아보기
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
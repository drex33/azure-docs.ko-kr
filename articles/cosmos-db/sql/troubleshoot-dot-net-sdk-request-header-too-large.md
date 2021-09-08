---
title: Azure Cosmos DB에서 "요청 헤더가 너무 큼" 메시지 또는 400 잘못된 요청의 문제 해결
description: 요청 헤더가 너무 큼 예외를 진단하고 수정하는 방법에 대해 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: d180e955fda4074fa2115b26f363b2c1350dab98
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117114"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Azure Cosmos DB "요청 헤더 너무 큼" 메시지 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 오류 코드 400인 "요청 헤더가 너무 큼" 메시지가 발생했습니다. 이 오류는 요청 헤더의 크기가 최대 허용 크기를 초과할 정도로 커졌을 경우 발생합니다. 최신 버전의 SDK를 사용하는 것이 좋습니다. 예외 메시지에 헤더 크기 추적을 추가하기 때문에 최신 3.x 또는 2.x 버전을 사용합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
세션 또는 연속 토큰이 너무 클 경우 "요청 헤더가 너무 큼" 메시지가 발생합니다. 다음 섹션에서는 각 범주의 문제 원인과 해당 솔루션을 설명합니다.

### <a name="session-token-is-too-large"></a>세션 토큰이 너무 큼

#### <a name="cause"></a>원인:
400 잘못된 요청은 세션 토큰이 너무 커서 발생할 가능성이 높습니다. 다음 문이 true일 경우 세션 토큰이 너무 깁니다.

* 이 오류는 연속 토큰이 없는 만들기, 읽기 및 업데이트와 같은 지점 작업에서 발생합니다.
* 애플리케이션을 변경하지 않고 예외가 시작되었습니다. 세션 토큰은 컨테이너에서 파티션 수가 증가함에 따라 증가합니다. 파티션 수는 데이터 양이 증가하거나 처리량이 증가함에 따라 증가합니다.

#### <a name="temporary-mitigation"></a>임시 완화: 
클라이언트 애플리케이션을 다시 시작하여 모든 세션 토큰을 다시 설정합니다. 결국 세션 토큰은 문제를 발생시킨 이전 크기로 다시 증가하게 됩니다. 이 문제를 완전히 방지하려면 다음 섹션의 솔루션을 사용합니다.

#### <a name="solution"></a>해결책:
> [!IMPORTANT]
> .NET [v3.20.1](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) 또는 [v2.15.0](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md) 이상으로 업그레이드합니다. 이러한 부 버전에는 헤더가 증가하여 크기 제한에 도달하는 것을 방지하기 위해 세션 토큰 크기를 줄이는 최적화가 포함되어 있습니다.
1. [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 또는 [.NET v2](performance-tips.md) 성능 팁 문서의 지침을 따릅니다. TCP(Transmission Control Protocol)를 통해 직접 연결 모드를 사용하도록 애플리케이션을 변환합니다. TCP 프로토콜을 사용하는 직접 연결 모드에는 HTTP 프로토콜과 같은 헤더 크기 제한이 없으므로 이 문제를 방지할 수 있습니다. 서비스 상호 운용성을 사용할 수 없는 경우 쿼리 작업에 대한 수정이 포함된 최신 버전의 SDK를 사용해야 합니다.
1. TCP 프로토콜을 사용하는 직접 연결 모드가 워크로드에 대한 옵션이 아닌 경우 [클라이언트 일관성 수준](how-to-manage-consistency.md)을 변경하여 이를 완화합니다. 세션 토큰은 Azure Cosmos DB의 기본 일관성 수준인 세션 일관성에만 사용됩니다. 다른 일관성 수준은 세션 토큰을 사용하지 않습니다.

### <a name="continuation-token-is-too-large"></a>연속 토큰이 너무 큼

#### <a name="cause"></a>원인:
연속 토큰이 너무 크거나 서로 다른 쿼리의 연속 토큰 크기가 다른 경우, 연속 토큰이 사용되는 쿼리 작업에서 400 잘못된 요청이 발생합니다.
    
#### <a name="solution"></a>해결책:
1. [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 또는 [.NET v2](performance-tips.md) 성능 팁 문서의 지침을 따릅니다. TCP 프로토콜을 사용하는 직접 연결 모드를 사용하도록 애플리케이션을 변환합니다. TCP 프로토콜을 사용하는 직접 연결 모드에는 HTTP 프로토콜과 같은 헤더 크기 제한이 없으므로 이 문제를 방지할 수 있습니다. 
1. TCP 프로토콜을 사용하는 직접 연결 모드가 워크로드에 대한 옵션이 아닌 경우 `ResponseContinuationTokenLimitInKb` 옵션을 설정합니다. 이 옵션은 v2의 경우 `FeedOptions` 또는 v3의경우 `QueryRequestOptions`에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.

---
title: .NET SDK에서 세션 토큰 형식을 변환하는 방법 - Azure Cosmos DB
description: 세션 토큰 형식을 변환하여 서로 다른 .NET SDK 버전 간의 호환성을 보장하는 방법을 알아봅니다.
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8d1e3ece8515734f262382c4ef3a5eb444183f96
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117011"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>.NET SDK에서 세션 토큰 형식 변환
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 SDK 버전 간의 호환성을 보장하기 위해 서로 다른 세션 토큰 형식 간에 변환하는 방법을 설명합니다.

> [!NOTE]
> 기본적으로 SDK는 세션 토큰을 자동으로 추적하고 가장 최근의 세션 토큰을 사용합니다.  자세한 내용은 [세션 토큰 사용](how-to-manage-consistency.md#utilize-session-tokens)을 방문하세요. 이 문서의 지침은 다음 조건에만 적용됩니다.
> * Azure Cosmos DB 계정은 세션 일관성을 사용합니다.
> * 세션 토큰은 수동으로 관리합니다.
> * 여러 버전의 SDK를 동시에 사용하고 있습니다.

## <a name="session-token-formats"></a>세션 토큰 형식

**단순** 및 **벡터** 의 두 가지 세션 토큰 형식이 있습니다.  이러한 두 형식은 서로 교환할 수 없으므로 서로 다른 버전으로 클라이언트 애플리케이션에 전달할 때는 형식을 변환해야 합니다.
- **단순** 세션 토큰 형식은 .NET SDK V1(Microsoft.Azure.DocumentDB -version 1.x)에서 사용됩니다.
- **벡터** 세션 토큰 형식은 .NET SDK V2(Microsoft.Azure.DocumentDB -version 2.x)에서 사용됩니다.

### <a name="simple-session-token"></a>단순 세션 토큰

단순 세션 토큰의 형식은 다음과 같습니다. `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>벡터 세션 토큰

벡터 세션 토큰의 형식은 다음과 같습니다. `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>단순 세션 토큰으로 변환

.NET SDK V1을 사용하여 클라이언트에 세션 토큰을 전달하려면 **단순** 세션 토큰 형식을 사용합니다.  예를 들어 다음 샘플 코드를 사용하여 변환합니다.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>벡터 세션 토큰으로 변환

.NET SDK V2를 사용하여 세션 토큰을 클라이언트에 전달하려면 **벡터** 세션 토큰 형식을 사용합니다.  예를 들어 다음 샘플 코드를 사용하여 변환합니다.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>다음 단계

다음 문서를 읽어보세요.

* [Azure Cosmos DB에서 세션 토큰을 사용하여 일관성 관리](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB에서 올바른 일관성 수준 선택](../consistency-levels.md)
* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](../consistency-levels.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](../consistency-levels.md)
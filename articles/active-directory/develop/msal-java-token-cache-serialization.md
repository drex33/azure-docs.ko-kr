---
title: 사용자 지정 토큰 캐시 serialization(MSAL4j)
titleSuffix: Microsoft identity platform
description: Java용 MSAL에 대한 토큰 캐시를 직렬화하는 방법 알아보기
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 4a8ea864acaba138819abe900433f01ff125ec09
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616786"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Java용 MSAL의 사용자 지정 토큰 캐시 serialization

애플리케이션 인스턴스 간에 토큰 캐시를 유지하려면 serialization을 사용자 지정해야 합니다. 토큰 캐시 serialization에 사용되는 Java 클래스와 인터페이스는 다음과 같습니다.

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): 보안 토큰 캐시를 나타내는 인터페이스입니다.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): 액세스 전후에 코드를 실행하는 작업을 나타내는 인터페이스입니다. 캐시 직렬화와 역직렬화를 처리하는 논리를 사용하여 *beforeCacheAccess* 및 *afterCacheAccess* 를 @Override합니다.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): 토큰 캐시에 액세스하는 컨텍스트를 나타내는 인터페이스입니다. 

토큰 캐시 serialization/deserialization의 사용자 지정 serialization에 대한 단순한 구현은 다음과 같습니다. 이 구현을 복사하여 프로덕션 환경에 붙여넣지 마세요.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>자세한 정보

[Java용 MSAL을 사용하여 토큰 캐시에서 계정 가져오기 및 제거](msal-java-get-remove-accounts-token-cache.md)에 관해 알아봅니다.

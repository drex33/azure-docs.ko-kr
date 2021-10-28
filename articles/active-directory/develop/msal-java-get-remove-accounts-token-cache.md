---
title: 토큰 캐시에서 계정 가져오기 및 제거(MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Java용 Microsoft 인증 라이브러리를 사용하여 토큰 캐시에서 계정을 보고 제거하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 8ea51bfd356e7479f421a6db804ec94bf656284a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612532"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Java용 MSAL을 사용하여 토큰 캐시에서 계정 가져오기 및 제거

Java용 MSAL은 기본적으로 메모리 내 토큰 캐시를 제공합니다. 메모리 내 토큰 캐시는 애플리케이션 인스턴스 기간 동안 지속됩니다.

## <a name="see-which-accounts-are-in-the-cache"></a>캐시에 있는 계정 확인

다음 예제와 같이 `PublicClientApplication.getAccounts()`를 호출하여 캐시에 어떤 계정이 있는지 확인할 수 있습니다.

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>캐시에서 계정 제거

캐시에서 계정을 제거하려면 제거해야 하는 계정을 찾은 후 다음 예제와 같이 `PublicClientApplication.removeAccount()`를 호출합니다.

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>자세한 정보

Java용 MSAL을 사용하는 경우 [Java용 MSAL의 사용자 지정 토큰 캐시 serialization](msal-java-token-cache-serialization.md)에 대해 알아보세요.

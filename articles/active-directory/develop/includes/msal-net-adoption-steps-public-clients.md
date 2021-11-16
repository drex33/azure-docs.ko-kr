---
title: MSAL로의 퍼블릭 클라이언트 마이그레이션을 위한 일반적인 단계
description: ADAL에서 MSAL로 마이그레이션할 때 모든 퍼블릭 클라이언트 앱에 대해 수행해야 하는 일반적인 단계를 설명하는 파일을 포함합니다.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 09/08/2021
ms.author: sahmalik
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 419b038f89df0d7fb5185fe2ef8ae43c76db7f7f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838721"
---
코드를 업데이트하기 위한 다음 단계는 모든 기밀 클라이언트 시나리오에 적용됩니다.

1. 소스 코드 `using Microsoft.Identity.Client;`에 MSAL.NET 네임스페이스를 추가합니다.
2. `AuthenticationContext`를 인스턴스화하는 대신 `PublicClientApplicationBuilder.Create`를 사용하여 `IPublicClientApplication`을 인스턴스화합니다.
3. `resourceId` 문자열 대신 MSAL.NET은 범위를 사용합니다. ADAL.NET을 사용하는 애플리케이션은 사전 인증되므로 항상 `new string[] { $"{resourceId}/.default" }` 범위를 사용할 수 있습니다.
4. `AuthenticationContext.AcquireTokenAsync`에 대한 호출 대신 `IPublicClientApplication.AcquireTokenXXX`에 대한 호출로 교체됩니다. 여기서 *XXX* 는 시니리오에 따라 다릅니다.
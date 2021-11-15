---
title: MSAL.NET에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: MSAL.NET에서 오류 및 예외를 로그하는 방법 알아보기
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 518fc85aff920ffece511e383b2322d8e81266ee
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091866"
---
# <a name="logging-in-msalnet"></a>MSAL.NET의 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>MSAL.NET에서 로깅 구성

MSAL에서 로깅은 `.WithLogging` 작성기 한정자를 사용하여 애플리케이션 생성 시 설정됩니다. 이 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- `Level`을 사용하면 원하는 로깅 수준을 결정할 수 있습니다. Errors로 설정하면 오류만 발생합니다.
- `PiiLoggingEnabled`를 사용하여 true로 설정하면 개인 및 조직 데이터(PII)를 기록할 수 있습니다. 애플리케이션에서 개인 데이터를 기록하지 않도록 기본적으로 false로 설정됩니다.
- `LogCallback`은 로깅을 수행하는 대리자로 설정됩니다. `PiiLoggingEnabled`가 true이면 이 메서드는 PII를 포함할 수 있는 메시지를 받습니다. 이 경우 `containsPii` 플래그가 true로 설정됩니다.
- `DefaultLoggingEnabled`는 플랫폼에 대한 기본 로깅을 사용하도록 설정합니다. 기본적으로 false입니다. true로 설정하면 데스크톱/UWP 애플리케이션의 이벤트 추적, iOS의 NSLog, Android의 logcat을 사용합니다.

```csharp
class Program
{
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
}
```

> [!TIP]
 > MSAL.NET 로깅 등에 대한 샘플은 [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)를 참조하세요.

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요.

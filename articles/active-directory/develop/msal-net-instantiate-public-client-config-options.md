---
title: 공용 클라이언트 앱 인스턴스화(MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 구성 옵션으로 공용 클라이언트 애플리케이션을 인스턴스화하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 57d7cba072e51483732d103d358910ba443a525a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059417"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET을 사용하여 구성 옵션으로 공용 클라이언트 애플리케이션 인스턴스화

.NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 구성 옵션으로 [공용 클라이언트 애플리케이션](msal-client-applications.md)을 인스턴스화하는 방법을 알아봅니다.  애플리케이션은 설정 파일에 정의된 구성 옵션으로 인스턴스화됩니다.

애플리케이션을 초기화하려면 먼저 앱이 Microsoft ID 플랫폼과 통합될 수 있도록 해당 애플리케이션을 [등록](quickstart-register-app.md)해야 합니다. 등록 후에는 다음 정보가 필요할 수 있으며 해당 정보는 Azure Portal에서 찾을 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- ID 공급자 URL(인스턴스 이름) 및 애플리케이션의 로그인 대상. 이러한 두 매개 변수를 통칭하여 권한이라고 합니다.
- 조직 전용 사업 부문 애플리케이션(단일 테넌트 애플리케이션이라고도 함)을 작성하는 경우 테넌트 ID.
- 웹앱의 경우와 때로 공용 클라이언트 앱의 경우(특히 앱에서 broker를 사용해야 하는 경우) ID 공급자가 애플리케이션에 보안 토큰을 다시 연결할 redirectUri도 설정해야 합니다.

## <a name="default-reply-uri"></a>기본 응답 URI

MSAL.NET 4.1 이상에서는 이제 기본 리디렉션 URI(회신 URI)를 `public PublicClientApplicationBuilder WithDefaultRedirectUri()` 메서드로 설정할 수 있습니다. 이 메서드는 공용 클라이언트 애플리케이션의 리디렉션 URI 속성을 권장 기본값으로 설정합니다.

이 메서드의 동작은 당시 사용 중인 플랫폼에 따라 다릅니다. 다음은 특정 플랫폼에서 설정된 리디렉션 URI를 설명하는 표입니다.

플랫폼  | 리디렉션 URI  
---------  | --------------
데스크톱 앱(.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`의 값입니다.
.NET Core | `http://localhost`

UWP 플랫폼의 경우 값을 `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`의 결과로 설정하여 브라우저에서 SSO를 사용하도록 설정하여 환경이 향상되었습니다. 

.NET Core의 경우 MSAL.Net은 사용자가 대화형 인증에 시스템 브라우저를 사용할 수 있도록 값을 로컬 호스트로 설정합니다.

> [!NOTE]
> 데스크톱 시나리오의 포함된 브라우저의 경우 사용된 리디렉션 URI가 MSAL에서 가로채어 인증 코드가 반환되었다는 응답이 ID 공급자로부터 반환되었음을 탐지합니다. 따라서 이 URI는 해당 URI에 대한 실제 리디렉션을 보지 않고 모든 클라우드에서 사용할 수 있습니다. 즉, 모든 클라우드에서 `https://login.microsoftonline.com/common/oauth2/nativeclient`를 사용할 수 있고 사용해야 합니다. 원하는 경우 MSAL 및 앱 등록에서 리디렉션 URI를 올바르게 구성하는 한 다른 URI를 사용할 수도 있습니다. 애플리케이션 등록에서 기본 URI를 지정한다는 것은 MSAL에서 가장 적은 양의 설정이 있음을 의미합니다.


.NET Core 콘솔 애플리케이션에는 다음과 같은 *appsettings.json* 구성 파일이 있을 수 있습니다.

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

다음 코드는 .NET 구성 프레임워크를 사용하여 이 파일을 읽습니다.

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

다음 코드는 설정 파일의 구성을 사용하여 애플리케이션을 만듭니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

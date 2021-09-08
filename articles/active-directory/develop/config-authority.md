---
title: ID 공급자 구성(MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: iOS 및 macOS용 MSAL와 함께 B2C, 소버린 클라우드 및 게스트 사용자와 같은 다양한 인증 기관을 사용하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: af729767467d2dc950fb7a559fec4d0a81d07cab
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029186"
---
# <a name="configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>다른 ID 공급자를 사용하도록 iOS 및 macOS용 MSAL 구성

이 문서에서는 Azure AD(Azure Active Directory ), B2C(Business-to-Consumer), 소버린 클라우드 및 게스트 사용자와 같은 다양한 인증 기관에 대해 iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리) 앱을 구성하는 방법을 보여줍니다.  이 문서 전체에서 인증 기관을 일반적으로 ID 공급자로 간주해도 무방합니다.

## <a name="default-authority-configuration"></a>기본 인증 기관 구성

`MSALPublicClientApplication`는 대부분의 AAD(Azure Active Directory) 시나리오에 적합한 기본 인증 기관 URL`https://login.microsoftonline.com/common`을 사용하여 구성됩니다. 국가별 클라우드와 같은 고급 시나리오를 구현하거나 B2C를 사용하지 않는 경우에는 변경할 필요가 없습니다.

> [!NOTE]
> ADFS(Active Directory Federation Services)를 ID 공급자로 사용하는 최신 인증은 지원되지 않습니다(세부 정보는 [개발자용 ADFS](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) 참조). ADFS는 페더레이션을 통해 지원됩니다.

## <a name="change-the-default-authority"></a>기본 인증 기관 변경

B2C(business-to-consumer)와 같은 일부 시나리오에서는 기본 기관을 변경해야 할 수 있습니다.

### <a name="b2c"></a>B2C

B2C를 사용하려면 [MSAL(Microsoft 인증 라이브러리)](reference-v2-libraries.md)에 다른 기관 구성이 필요합니다. MSAL은 하나의 기관 URL 형식을 자체적으로 B2C로 인식합니다. 예를 들어, 인식할 수 있는 B2C 인증 기관 형식은 `https://<host>/tfp/<tenant>/<policy>`이며, 예를 들면 `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`입니다. 그러나 인증 기관을 B2C 인증 기관으로 명시적으로 선언하여 지원되는 기타 B2C 인증 기관 URL을 사용할 수도 있습니다.

B2C에 대한 임의의 URL 형식을 지원하기 위해 다음과 같은 임의 URL을 사용하여 `MSALB2CAuthority`를 설정할 수 있습니다.

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

기본 B2C 인증 기관 형식을 사용하지 않는 모든 B2C 기관은 알려진 기관으로 선언되어야 합니다.

인증 기관이 정책만 다른 경우에도 각 B2C 인증 기관을 알려진 인증 기관 목록에 추가합니다.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

앱에서 새 정책을 요청하는 경우 인증 기관 URL이 정책마다 다르므로 인증 기관 URL을 변경해야 합니다. 

B2C 애플리케이션을 구성하려면 다음과 같이 `MSALPublicClientApplication`를 만들기 전에 `MSALPublicClientApplicationConfig`에서 `@property MSALAuthority *authority`을 `MSALB2CAuthority` 인스턴스로 설정합니다.

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>소버린 클라우드

앱이 소버린 클라우드에서 실행되는 경우 `MSALPublicClientApplication`에서 기관 URL을 변경해야 할 수 있습니다. 다음 예제는 기관 URL이 독일 AAD 클라우드에서 작동하도록 설정합니다.

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

각 소버린 클라우드에 서로 다른 범위를 전달해야 할 수도 있습니다. 보낼 범위는 사용 중인 리소스에 따라 달라집니다. 예를 들어 전 세계 클라우드에서는 `"https://graph.microsoft.com/user.read"`를 사용하고 독일 클라우드에서는 `"https://graph.microsoft.de/user.read"`를 사용할 수 있습니다.

### <a name="signing-a-user-into-a-specific-tenant"></a>특정 테넌트에서 사용자 서명

기관 URL이 `"login.microsoftonline.com/common"`로 설정되면 사용자가 홈 테넌트에서 로그인됩니다. 그러나 일부 앱은 사용자를 다른 테넌트로 로그인해야 하고 일부 앱은 단일 테넌트에서만 작동합니다.

사용자를 특정 테넌트로 로그인하려면 특정 인증 기관으로 `MSALPublicClientApplication`를 구성합니다. 예를 들면 다음과 같습니다.

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

다음은 특정 테넌트에서 사용자를 로그인하는 방법을 보여줍니다.

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>지원되는 인증 기관

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority` 클래스는 MSAL 인증 기관 클래스의 베이스 추상 클래스입니다. `alloc` 또는 `new`를 사용하여 인스턴스를 만들면 안 됩니다. 대신 해당 서브클래스 중 하나를 직접(`MSALAADAuthority`, `MSALB2CAuthority`) 만들거나 팩터리 메서드 `authorityWithURL:error:`를 사용해 인증 기관 URL을 사용하는 서브클래스를 만듭니다.

`url` 속성으로 정규화된 인증 기관 URL을 가져옵니다. 인증 기관의 일부가 아닌 추가 매개 변수 및 경로 구성 요소 또는 조각은 반환된 정규화 인증 기관 URL에 없습니다.

다음은 사용하려는 인증 기관에 따라 인스턴스화할 수 있는 `MSALAuthority`의 서브클래스입니다.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`는 AAD 인증 기관을 나타냅니다. 인증 기관 URL은 다음과 같은 형식이어야 하며, 여기서 `<port>`은 선택 사항입니다: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`은 B2C 인증 기관을 나타냅니다. 기본값으로 B2C 인증 기관 URL은 다음과 같은 형식이어야 하며, 여기서 `<port>`은 선택 사항입니다: `https://<host>:<port>/tfp/<tenant>/<policy>` 그러나 MSAL은 기타 임의의 B2C 인증 기관 형식도 지원합니다.

## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
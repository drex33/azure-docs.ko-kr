---
title: 사용자 지정 클레임 요청 (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: 사용자 지정 클레임을 요청하는 방법을 알아보세요.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 70a883fc1c2cd78124c9efe44a54cc528ee5426c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039921"
---
# <a name="request-custom-claims-using-msal-for-ios-and-macos"></a>OS 및 macOS용 MSAL을 통한 사용자 지정 클레임 요청

OpenID Connect를 통해 필요에 따라 사용자 정보 엔드포인트 및/또는 ID 토큰에서 개별 클레임 반환을 요청할 수 있습니다. 클레임 요청은 요청된 클레임 목록을 포함 하는 JSON 개체로 표시됩니다. 자세한 내용은 [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)을 참조하세요.

iOS 및 MacOS용 Microsoft 인증 라이브러리(MSAL)를 사용하면 대화형 및 자동 토큰 획득 시나리오에서 특정 클레임을 요청할 수 있습니다. `claimsRequest`매개 변수를 통해 이 작업을 수행합니다.

이 경우 여러가지 시나리오가 필요합니다. 예를 들면 다음과 같습니다.

- 애플리케이션의 표준 세트 외부에서 클레임을 요청합니다.
- 애플리케이션의 범위를 사용하여 지정할 수 없는 표준 클레임의 특정 조합 요청 예를 들어 클레임 누락으로 인해 액세스 토큰이 거부된 경우 애플리케이션은 MSAL을 사용하여 누락된 클레임을 요청할 수 있습니다.

> [!NOTE]
> MSAL은 클레임 요청이 지정될 때마다 액세스 토큰 캐시를 건너뜁니다. 추가 클레임이 필요한 경우 `claimsRequest` 매개 변수를 제공하는 것이 중요합니다(각 MSAL API 호출에서 항상 동일한 `claimsRequest` 매개 변수를 제공하는 것과 반대)

`claimsRequest`은 `MSALSilentTokenParameters` 및 `MSALInteractiveTokenParameters`에서 지정될 수 있습니다.

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`은 JSON 클레임 요청의 NSString 표현으로 구성될 수 있습니다. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



또한 추가적으로 특정 클레임을 요청하여 수정할 수 있습니다.

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



그런 다음 `MSALClaimsRequest`을 토큰 매개 변수에 설정하고 MSAL 토큰 획득 API 중 하나에 제공해야 합니다.

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기

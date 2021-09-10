---
title: 웹 API를 호출하는 데스크톱 앱을 프로덕션으로 이동 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 데스크톱 앱을 프로덕션으로 이동하는 방법 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 759dcafe3d1c98b73d9ddcaf079cba952b9a95ca
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777903"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>웹 API를 호출하는 데스크톱 앱 - 프로덕션으로 이동

이 문서에서는 웹 API를 호출하는 데스크톱 앱을 프로덕션으로 이동하는 방법에 대해 알아봅니다.

## <a name="handle-errors-in-desktop-applications"></a>데스크톱 애플리케이션의 오류 처리

다른 흐름에서는 코드 조각에 표시된 것처럼 자동 흐름에 대한 오류를 처리하는 방법을 알아보았습니다. 증분 동의 및 조건부 액세스와 같이 상호 작용이 필요한 경우도 있습니다.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>여러 리소스에 대한 사용자 사전 동의 받기

> [!NOTE]
> 여러 리소스에 대한 동의를 얻는 것은 Microsoft ID 플랫폼에서는 작동하지만 Azure AD(Azure Active Directory) B2C에서는 작동하지 않습니다. Azure AD B2C는 사용자 동의가 아닌 관리자 동의만 지원합니다.

Microsoft ID 플랫폼을 사용하여 한 번에 여러 리소스에 대한 토큰을 가져올 수 없습니다. `scopes` 매개 변수는 단일 리소스에 대한 범위만 포함할 수 있습니다. `extraScopesToConsent` 매개 변수를 사용하여 사용자가 여러 리소스에 사전 동의했는지 확인할 수 있습니다.

예를 들어, 각각 두 개의 범위가 포함된 두 개의 리소스가 있을 수 있습니다.

- `customer.read` 및 `customer.write` 범위가 있는 `https://mytenant.onmicrosoft.com/customerapi`
- `vendor.read` 및 `vendor.write` 범위가 있는 `https://mytenant.onmicrosoft.com/vendorapi`

이 예제에서는 `.WithExtraScopesToConsent` 매개 변수가 있는 `extraScopesToConsent` 한정자를 사용합니다.

예를 들면 다음과 같습니다.

### <a name="in-msalnet"></a>MSAL.NET에서

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

이 호출은 첫 번째 웹 API에 대한 액세스 토큰을 가져옵니다.

두 번째 웹 API를 호출하는 경우 `AcquireTokenSilent` API를 호출합니다.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Microsoft 개인 계정은 앱이 실행될 때마다 다시 동의해야 합니다.

Microsoft 개인 계정 사용자의 경우 권한 부여를 위해 각 네이티브 클라이언트(데스크톱 또는 모바일 앱) 호출에 대한 동의를 다시 요청하는 것은 의도된 동작입니다. 네이티브 클라이언트 ID는 기밀 클라이언트 애플리케이션 ID와 달리 본질적으로 안전하지 않습니다. 기밀 클라이언트 애플리케이션은 Microsoft ID 플랫폼과 비밀을 교환하여 해당 ID를 증명합니다. Microsoft ID 플랫폼은 애플리케이션에 권한이 부여될 때마다 사용자에게 동의를 요청하여 소비자 서비스에 대한 이러한 불안정을 완화하도록 선택했습니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

추가 샘플을 시도하려면 [데스크톱 퍼블릭 클라이언트 애플리케이션](sample-v2-code.md#desktop)을 참조하세요.




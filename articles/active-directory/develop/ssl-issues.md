---
title: TLS/SSL 문제 해결(MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: MSAL.Objective-C 라이브러리에서 TLS/SSL 인증서를 사용하여 다양한 문제에 관해 수행할 작업을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: aceddf008979453383a1460da4342fd2a066d6b1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039836"
---
# <a name="troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>iOS 및 macOS TLS/SSL 문제에 대한 MSAL 문제 해결

이 문서에서는 [iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리)](reference-v2-libraries.md)을 사용하는 동안 발생할 수 있는 문제를 해결하는 데 도움이 되는 정보를 제공합니다.

## <a name="network-issues"></a>네트워크 문제

**오류 - 1200**: “SSL 오류가 발생했고 서버에 연결할 수 없습니다.”

이 오류는 연결이 안전하지 않음을 의미합니다. 인증서가 유효하지 않은 경우 발생합니다. TLS 확인에 실패하는 서버를 포함하여 자세한 내용은 error 개체의 `userInfo` 사전에서 `NSURLErrorFailingURLErrorKey`를 참조하세요.

이 오류는 Apple의 네트워킹 라이브러리에서 발생합니다. NSURL 오류 코드의 전체 목록은 macOS 및 iOS SDK의 NSURLError.h에 있습니다. 이 오류에 관한 자세한 내용은 [URL 로딩 시스템 오류 코드](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)를 참조하세요.

## <a name="certificate-issues"></a>인증서 문제

잘못된 인증서를 제공하는 URL이 인증 흐름의 일부로 사용하려는 서버에 연결되는 경우 문제를 진단하는 좋은 방법은 [SSL 서버 테스트](https://www.ssllabs.com/ssltest/analyze.html)와 같은 SSL 유효성 검사 서비스를 사용하여 URL을 테스트하는 것입니다. 많은 시나리오와 브라우저에 대해 서버를 테스트하고 알려진 많은 취약성을 확인합니다.

기본적으로 Apple의 새로운 [ATS(App Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 기능은 TLS/SSL 인증서를 사용하는 앱에 더 엄격한 보안 정책을 적용합니다. 일부 운영 체제와 웹 브라우저는 기본적으로 이러한 정책 중 일부를 적용하기 시작했습니다. 보안상 이유로 ATS를 사용하지 않도록 설정하는 것이 좋습니다.

SHA-1 해시를 사용하는 인증서에는 알려진 취약성이 있습니다. 대부분의 최신 웹 브라우저는 SHA-1 해시를 사용하는 인증서를 허용하지 않습니다.

## <a name="captive-portals"></a>종속 포털

종속 포털은 사용자가 Wi-Fi 네트워크에 처음 액세스할 때 해당 네트워크에 대한 액세스 권한이 아직 부여되지 않은 경우 사용자에게 웹 페이지를 제공합니다. 사용자가 포털의 요구 사항을 충족할 때까지 인터넷 트래픽을 가로챕니다. 사용자가 네트워크 리소스에 연결할 수 없기 때문에 사용자가 포털을 통해 연결할 때까지 네트워크 오류가 예상됩니다.

## <a name="next-steps"></a>다음 단계

[종속 포털](https://en.wikipedia.org/wiki/Captive_portal) 및 Apple의 새로운 [ATS(앱 전송 보안)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 기능에 관해 알아봅니다.

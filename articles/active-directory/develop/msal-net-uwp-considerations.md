---
title: UWP 고려 사항(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET용 Microsoft 인증 라이브러리(MSAL.NET)와 함께 UWP(유니버설 Windows 플랫폼)를 사용하기 위한 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: f3acdfb2944a763f8601ff8448fcb79f5fc2e073
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052734"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>MSAL.NET과 함께 유니버설 Windows 플랫폼 사용 시 고려 사항
MSAL.NET과 함께 UWP(유니버설 Windows 플랫폼)를 사용하는 애플리케이션 개발자는 이 문서에서 제시하는 개념을 고려해야 합니다.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 속성
Windows 런타임(WinRT) 플랫폼에서 `PublicClientApplication`에는 부울 속성 `UseCorporateNetwork`가 있습니다. 이 속성을 사용하면 사용자가 페더레이션된 Azure AD(Azure Active Directory) 테넌트가 있는 계정에 로그인한 경우 Windows 10 애플리케이션 및 UWP 애플리케이션에서 IWA(Windows 통합 인증)의 이점을 활용할 수 있습니다. 운영 체제에 로그인한 사용자는 SSO(Single Sign-On)도 사용할 수 있습니다. `UseCorporateNetwork` 속성을 설정하면 MSAL.NET은 WAB(웹 인증 브로커)를 사용합니다.

> [!IMPORTANT]
> `UseCorporateNetwork` 속성을 true로 설정하면 애플리케이션 개발자가 애플리케이션에서 IWA를 사용하도록 설정했다고 가정합니다. IWA를 사용하려면:
> - UWP 애플리케이션의 `Package.appxmanifest`에 있는 **기능** 탭에서 다음 기능을 사용하도록 설정합니다.
>   - **엔터프라이즈 인증**
>   - **개인 네트워크(클라이언트 및 서버)**
>   - **공유 사용자 인증서**

Microsoft Store에서 엔터프라이즈 인증 또는 공유 사용자 인증서 기능을 요청하는 애플리케이션을 수락하기 전에 높은 수준의 확인이 필요하기 때문에 IWA는 기본적으로 사용하도록 설정되어 있지 않습니다. 모든 개발자가 이 수준의 검증을 원하는 것은 아닙니다.

UWP 플랫폼에서 기본 WAB 구현은 조건부 액세스가 사용하도록 설정된 엔터프라이즈 시나리오에서 제대로 작동하지 않습니다. 사용자가 Windows Hello를 사용하여 로그인하려고 할 때 이 문제의 증상이 나타납니다. 사용자에게 인증서를 선택하라는 메시지가 표시되는 경우:

- PIN에 대한 인증서를 찾을 수 없습니다.
- 사용자가 인증서를 선택한 후에는 PIN을 입력하라는 메시지가 표시되지 않습니다.

사용자 이름-암호 및 전화 인증과 같은 대체 방법을 사용하여 이 문제를 방지할 수 있지만 환경이 좋지 않습니다.

## <a name="troubleshooting"></a>문제 해결

일부 고객은 인터넷에 연결되어 있고 공용 네트워크에서 연결이 작동한다는 것을 알고 있는 특정 엔터프라이즈 환경에서 다음과 같은 로그인 오류를 보고했습니다.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

WAB(기본 Windows 구성 요소)가 개인 네트워크를 허용하도록 하면 이 문제를 피할 수 있습니다. 레지스트리 키를 설정하여 이를 수행할 수 있습니다.

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

자세한 내용은 [웹 인증 브로커 - Fiddler](/windows/uwp/security/web-authentication-broker#fiddler)를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 샘플은 자세한 정보를 제공합니다.

샘플 | 플랫폼 | 설명 
|------ | -------- | -----------|
|[`active-directory-dotnet-native-uwp-v2`](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | MSAL.NET을 사용하는 UWP 클라이언트 애플리케이션입니다. Azure AD 2.0 엔드포인트를 사용하여 인증하는 사용자의 Microsoft Graph에 액세스합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[`active-directory-xamarin-native-v2`](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | MSAL을 사용하여 Microsoft ID 플랫폼을 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 Xamarin Forms 앱입니다. 또한 Microsoft Graph에 액세스하는 방법과 결과 토큰을 보여 줍니다. <br>![MSAL을 사용하여 Microsoft ID 플랫폼을 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 다이어그램입니다.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

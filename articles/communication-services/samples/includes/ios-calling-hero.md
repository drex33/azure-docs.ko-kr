---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 2cf3875eef9dcad7ba85bcf12c52f03aa7103aca
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288074"
---
Azure Communication Services **iOS용 그룹 통화 주인공 샘플** 은 Communication Services 통화 iOS SDK를 사용하여 음성 및 비디오가 포함된 그룹 통화 환경을 구축하는 방법을 보여 줍니다. 이 샘플 빠른 시작에서는 샘플을 설정하고 실행하는 방법에 대해 알아봅니다. 상황에 맞는 샘플 개요가 제공됩니다.

## <a name="download-code"></a>코드 다운로드

[GitHub](https://github.com/Azure-Samples/communication-services-ios-calling-hero)에서 이 샘플에 대한 프로젝트를 찾습니다. [Teams 상호 운용성](../../concepts/teams-interop.md)을 사용하는 샘플 버전은 별도의 [분기](https://github.com/Azure-Samples/communication-services-ios-calling-hero/tree/feature/teams_interop)에서 찾을 수 있습니다.

## <a name="overview"></a>개요

이 샘플은 Azure Communication Services iOS SDK를 사용하여 음성 및 영상 통화를 모두 제공하는 통화 환경을 구축하는 네이티브 iOS 애플리케이션입니다. 애플리케이션에서 서버 쪽 구성 요소를 사용하여 Azure Communication Services SDK를 초기화하는 데 사용되는 액세스 토큰을 프로비저닝합니다. 이 서버 쪽 구성 요소를 구성하려면 언제든지 [Azure Functions를 사용하여 신뢰할 수 있는 서비스 빌드](../../tutorials/trusted-service-tutorial.md) 자습서를 따르세요.

샘플은 다음과 같습니다.

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="샘플 애플리케이션의 방문 페이지를 보여 주는 스크린샷":::

"새 통화 시작" 단추를 누르면 iOS 애플리케이션에서 새 통화를 만들고 해당 통화에 참가합니다. 애플리케이션을 사용하면 기존 통화의 ID를 지정하여 기존 Azure Communication Services 통화에 참가할 수 있습니다.

통화에 참가하면 카메라와 마이크에 액세스할 수 있는 권한을 애플리케이션에 부여하라는 메시지가 표시됩니다. 표시 이름도 입력하라는 메시지가 표시됩니다.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="샘플 애플리케이션의 통화 전 화면을 보여 주는 스크린샷":::

표시 이름 및 디바이스가 구성되면 통화에 참가할 수 있습니다. 핵심 통화 환경이 있는 기본 통화 캔버스가 표시됩니다.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="샘플 애플리케이션의 기본 화면을 보여 주는 스크린샷":::

기본 통화 화면의 구성 요소:

- **미디어 갤러리**: 참가자가 표시되는 기본 스테이지입니다. 참가자가 카메라를 사용하도록 설정한 경우 비디오 피드가 여기에 표시됩니다. 각 참가자에게는 표시 이름 및 비디오 스트림(있는 경우)을 보여주는 개별 타일이 있습니다. 갤러리는 여러 참가자를 지원하며, 참가자가 통화에 추가되거나 제거될 때 업데이트됩니다.
- **작업 모음**: 기본 통화 컨트롤이 있는 위치입니다. 이러한 컨트롤을 사용하면 비디오와 마이크를 설정/해제하고, 화면을 공유하고, 통화를 종료할 수 있습니다.

아래에는 샘플을 설정하기 위한 필수 구성 요소 및 설정 단계가 자세히 설명되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)를 실행하는 Mac
- Azure Communication Services 리소스. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../quickstarts/create-communication-resource.md)를 참조하세요.
- 액세스 토큰을 가져오기 위해 [인증 엔드포인트](../../tutorials/trusted-service-tutorial.md)를 실행하는 Azure Function

## <a name="running-sample-locally"></a>로컬로 샘플 실행

그룹 통화 샘플은 XCode를 사용하여 로컬로 실행할 수 있습니다. 개발자는 물리적 디바이스 또는 에뮬레이터를 사용하여 애플리케이션을 테스트할 수 있습니다.

### <a name="before-running-the-sample-for-the-first-time"></a>처음으로 샘플을 실행하기 전에

1. `pod install`을 실행하여 종속성을 설치합니다.
2. XCode에서 `AzureCalling.xcworkspace`를 엽니다.
3. `AppSettings.plist`를 업데이트합니다. `communicationTokenFetchUrl` 키의 값을 인증 엔드포인트에 대한 URL로 설정합니다.

### <a name="run-sample"></a>샘플 실행

XCode에서 샘플을 빌드하고 실행합니다.

## <a name="optional-securing-an-authentication-endpoint"></a>(선택 사항) 인증 엔드포인트 보안

데모를 위해 이 샘플은 기본적으로 공개적으로 액세스할 수 있는 엔드포인트를 사용하여 Azure Communication Services 액세스 토큰을 가져옵니다. 프로덕션 시나리오의 경우 사용자 고유의 보안 엔드포인트를 사용하여 사용자 고유의 토큰을 프로비저닝하는 것이 좋습니다.

추가 구성을 사용하는 경우 이 샘플은 **Azure AD**(Azure Active Directory) 보호 엔드포인트에 대한 연결을 지원하므로 앱에서 Azure Communication Services 액세스 토큰을 가져오려면 사용자 로그인이 필요합니다. 아래 단계를 참조하세요.

1. 앱에서 Azure Active Directory 인증을 사용하도록 설정합니다.  
   - [Azure Active Directory에서 앱 등록(iOS/macOS 플랫폼 설정 사용)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Azure AD 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성](../../../app-service/configure-authentication-provider-aad.md)
2. Azure Active Directory 앱 등록에서 등록된 앱 개요 페이지로 이동합니다. `Application (client) ID`, `Directory (tenant) ID`, `Application ID URI`를 적어 두세요.

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Portal의 Azure Active Directory 구성":::

3. Xcode에서 `AppSettings.plist`를 열고, 다음 키 값을 추가합니다.
   - `communicationTokenFetchUrl`: Azure Communication Services 토큰을 요청하는 URL 
   - `isAADAuthEnabled`: Azure Communication Services 토큰 인증이 필요한지 여부를 나타내는 부울 값
   - `aadClientId`: 사용자의 애플리케이션(클라이언트) ID
   - `aadTenantId`: 사용자의 디렉터리(테넌트) ID
   - `aadRedirectURI`: 리디렉션 URI는 `msauth.<app_bundle_id>://auth` 형식이어야 합니다.
   - `aadScopes`: 권한 부여를 위해 사용자가 요청한 권한 범위의 배열. `<Application ID URI>/user_impersonation`을 배열에 추가하여 인증 엔드포인트에 대한 액세스 권한을 부여합니다.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../../quickstarts/create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[GitHub에서 샘플 다운로드](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

자세한 내용은 다음 문서를 참조하세요.

- [통화 SDK 사용법](../../quickstarts/voice-video-calling/getting-started-with-calling.md) 숙지
- [통화 작동 방식](../../concepts/voice-video-calling/about-call-types.md)에 대한 자세한 정보

### <a name="additional-reading"></a>추가 자료

- [Azure Communication GitHub](https://github.com/Azure/communication) - 공식 GitHub 페이지에서 더 많은 예제 및 정보 찾기
- [샘플](./../overview.md) - 샘플 개요 페이지에서 추가 샘플과 예제를 찾아보세요.
- [Azure Communication 기능 호출](../../concepts/voice-video-calling/calling-sdk-features.md) - iOS sdk 호출에 대한 자세한 내용은 [Azure Communication iOS 호출 SDK](https://github.com/Azure/Communication/releases/)를 참조하세요.
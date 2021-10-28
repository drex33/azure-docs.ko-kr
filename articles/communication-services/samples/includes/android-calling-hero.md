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
ms.openlocfilehash: 0c93d09618eca9b5f43b0b52c80607875b5c73cb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287572"
---
Azure Communication Services **Android용 그룹 통화 주인공 샘플** 은 Communication Services 통화 Android SDK를 사용하여 음성 및 비디오가 포함된 그룹 통화 환경을 구축하는 방법을 보여줍니다. 이 샘플 빠른 시작에서는 샘플을 설정하고 실행하는 방법에 대해 알아봅니다. 상황에 맞는 샘플 개요가 제공됩니다.

## <a name="download-code"></a>코드 다운로드

[GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)에서 이 샘플에 대한 프로젝트를 찾습니다. [Teams 상호 운용성](../../concepts/teams-interop.md)을 사용하는 샘플 버전은 별도의 [분기](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop)에서 찾을 수 있습니다.

## <a name="overview"></a>개요

이 샘플은 Azure Communication Services Android SDK를 사용하여 음성 및 영상 통화를 모두 제공하는 통화 환경을 구축하는 네이티브 Android 애플리케이션입니다. 애플리케이션에서 서버 쪽 구성 요소를 사용하여 Azure Communication Services SDK를 초기화하는 데 사용되는 액세스 토큰을 프로비저닝합니다. 이 서버 쪽 구성 요소를 구성하려면 언제든지 [Azure Functions를 사용하여 신뢰할 수 있는 서비스 빌드](../../tutorials/trusted-service-tutorial.md) 자습서를 따르세요.

샘플은 다음과 같습니다.

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="샘플 애플리케이션의 방문 페이지를 보여 주는 스크린샷":::

"새 통화 시작" 단추를 누르면 Android 애플리케이션에서 새 통화를 만들고 해당 통화에 참가합니다. 또한 애플리케이션을 사용하면 기존 통화의 ID를 지정하여 기존 Azure Communication Services 통화에 참가할 수 있습니다.

통화에 참가하면 카메라와 마이크에 액세스할 수 있는 권한을 애플리케이션에 부여하라는 메시지가 표시됩니다. 표시 이름도 입력하라는 메시지가 표시됩니다.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="샘플 애플리케이션의 통화 전 화면을 보여 주는 스크린샷":::

표시 이름 및 디바이스가 구성되면 통화에 참가할 수 있습니다. 핵심 통화 환경이 있는 기본 통화 캔버스가 표시됩니다.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="샘플 애플리케이션의 기본 화면을 보여 주는 스크린샷":::

기본 통화 화면의 구성 요소:

- **미디어 갤러리**: 참가자가 표시되는 기본 스테이지입니다. 참가자가 카메라를 사용하도록 설정한 경우 비디오 피드가 여기에 표시됩니다. 각 참가자에게는 표시 이름 및 비디오 스트림(있는 경우)을 보여주는 개별 타일이 있습니다. 갤러리는 여러 참가자를 지원하며, 참가자가 통화에 추가되거나 제거될 때 업데이트됩니다.
- **작업 모음**: 기본 통화 컨트롤이 있는 위치입니다. 이러한 컨트롤을 사용하면 비디오와 마이크를 설정/해제하고, 화면을 공유하고, 통화를 종료할 수 있습니다.

아래에는 샘플을 설정하기 위한 필수 구성 요소 및 설정 단계가 자세히 설명되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- 컴퓨터에서 실행 중인 [Android Studio](https://developer.android.com/studio)
- Azure Communication Services 리소스. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../quickstarts/create-communication-resource.md)를 참조하세요.
- 액세스 토큰을 가져오기 위해 [인증 엔드포인트](../../tutorials/trusted-service-tutorial.md)를 실행하는 Azure Function

## <a name="running-sample-locally"></a>로컬로 샘플 실행

그룹 통화 샘플은 Android Studio를 사용하여 로컬로 실행할 수 있습니다. 개발자는 물리적 디바이스 또는 에뮬레이터를 사용하여 애플리케이션을 테스트할 수 있습니다.

### <a name="before-running-the-sample-for-the-first-time"></a>처음으로 샘플을 실행하기 전에

1. Android Studio를 열고 `Open an Existing Project`를 선택합니다.
2. 샘플을 받기 위해 다운로드한 릴리스 내에서 `AzureCalling` 폴더를 엽니다.
3. `appSettings.properties`를 업데이트할 앱/자산을 확장합니다. `communicationTokenFetchUrl` 키의 값을 필수 구성 요소로 설정한 인증 엔드포인트의 URL로 설정합니다.

### <a name="run-sample"></a>샘플 실행

Android Studio에서 샘플을 빌드하고 실행합니다.

## <a name="optional-securing-an-authentication-endpoint"></a>(선택 사항) 인증 엔드포인트 보안

데모를 위해 이 샘플은 기본적으로 공개적으로 액세스할 수 있는 엔드포인트를 사용하여 Azure Communication Services 토큰을 가져옵니다. 프로덕션 시나리오의 경우 사용자 고유의 보안 엔드포인트를 사용하여 사용자 고유의 토큰을 프로비저닝하는 것이 좋습니다.

추가 구성을 사용하는 경우 이 샘플은 **Azure AD**(Azure Active Directory) 보호 엔드포인트에 대한 연결을 지원하므로 앱에서 Azure Communication Services 토큰을 가져오려면 사용자 로그인이 필요합니다. 아래 단계를 참조하세요.

1. 앱에서 Azure Active Directory 인증을 사용하도록 설정합니다.  
   - [Azure Active Directory에서 앱 등록(Android 플랫폼 설정 사용)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Azure AD 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성](../../../app-service/configure-authentication-provider-aad.md)

2. Azure Active Directory 앱 등록에서 등록된 앱 개요 페이지로 이동합니다. `Package name`, `Signature hash`, `MSAL Configutaion`를 적어 두세요.

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Portal의 Azure Active Directory 구성":::

3. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`을 편집하고 Azure Active Directory를 사용하도록 `isAADAuthEnabled` 설정
4. `AndroidManifest.xml`을 편집하고 `android:path`를 키 저장소 서명 해시로 설정합니다. (선택 사항. 현재 값은 debug.keystore 번들의 해시를 사용합니다. 다른 키 저장소를 사용하는 경우 이 부분을 업데이트해야 합니다.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Azure Portal에서 MSAL Android 구성을 복사하여 `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`에 붙여넣습니다. "account_mode" : "SINGLE" 포함
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`을 편집하고 `communicationTokenFetchUrl` 키의 값을 보안 인증 엔드포인트의 URL로 설정합니다.
7. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`을 편집하고 `aadScopes` 키의 값을 `Azure Active Directory` `Expose an API` 범위에서 설정합니다.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../../quickstarts/create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[GitHub에서 샘플 다운로드](https://github.com/Azure-Samples/communication-services-android-calling-hero)

자세한 내용은 다음 문서를 참조하세요.

- [통화 SDK 사용법](../../quickstarts/voice-video-calling/getting-started-with-calling.md) 숙지
- [통화 작동 방식](../../concepts/voice-video-calling/about-call-types.md)에 대한 자세한 정보

### <a name="additional-reading"></a>추가 자료

- [Azure Communication GitHub](https://github.com/Azure/communication) - 공식 GitHub 페이지에서 더 많은 예제 및 정보 찾기
- [샘플](./../overview.md) - 샘플 개요 페이지에서 추가 샘플과 예제를 찾아보세요.
- [Azure Communication 기능 호출](../../concepts/voice-video-calling/calling-sdk-features.md) - Android sdk 호출에 대한 자세한 내용은 [Azure Communication Android 호출 SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)를 참조하세요.
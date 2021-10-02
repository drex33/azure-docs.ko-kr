---
title: iOS 앱 만들기
description: Azure 지도 계정 및 첫 번째 iOS 앱을 만드는 단계입니다.
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef19d6d542fc0e8cd44b73a7cf3c9fae63915bbc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372903"
---
#  <a name="create-an-ios-app-public-preview"></a>iOS 앱 만들기(공개 미리 보기)

이 문서에서는 iOS 앱에 Azure 지도 추가하는 방법을 보여줍니다. 여기서는 다음 기본 단계를 단계별로 안내합니다.

* 개발 환경을 설정합니다.
* 사용자 고유의 Azure Maps 계정을 만듭니다.
* 앱에서 사용할 기본 Azure Maps 키를 가져옵니다.
* 프로젝트에서 Azure Maps 라이브러리를 참조합니다.
* 앱에 Azure Maps 컨트롤을 추가합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Portal 로그인하여 Azure [지도](https://portal.azure.com/) 계정을 만듭니다. Azure 구독이 없는 경우 시작하기 전에  [체험 계정을](https://azure.microsoft.com/free/)  만듭니다.
* [Azure Maps 계정을 만듭니다](https://docs.microsoft.com//azure/azure-maps/quick-demo-map-app#create-an-azure-maps-account).
* [기본 키](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account) 또는 구독 키라고도 하는 기본 구독 키를 얻습니다. Azure 지도 인증에 대한 자세한 내용은 [Azure 지도 인증 관리를](https://docs.microsoft.com//azure/azure-maps/how-to-manage-authentication) 참조하세요.
* [Mac App Store Xcode를](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) 무료로 다운로드합니다.

## <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

다음 단계에 따라 새 Azure Maps 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com/) 왼쪽 위 모서리에서 **리소스 만들기를** 클릭합니다.
2. _Marketplace 검색_ 상자에서 **Azure Maps** 를 입력합니다.
3. _결과_ 에서 **Azure Maps** 를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다.
4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
    * 이 계정에 사용하려는 _구독_.
    * 이 계정에 대한 _리소스 그룹_ 이름. _새로 만들기_ 또는 _기존_ 리소스 그룹 사용을 선택할 수도 있습니다.
    * 새 계정의 _이름_.
    * 이 계정에 대한 _가격 책정 계층_ 입니다.
    * _라이선스_ 및 _개인정보처리방침_ 을 읽고 조건에 동의하는 확인란을 선택합니다.
    * **만들기** 단추를 클릭합니다.

![Azure Maps ccont를 만듭니다.](./media/quick-ios-app/create-account.png)

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Maps 계정이 성공적으로 만들어지면 Maps API를 쿼리할 수 있는 기본 키를 검색합니다.

1. 포털에서 Maps 계정을 엽니다.
2. 설정 섹션에서 **인증** 을 선택합니다.
3. **기본 키** 를 클립보드로 복사합니다. 이 자습서의 뒷부분에서 사용하기 위해 로컬로 저장합니다.

> [!Note]
> Azure Maps 기본 키 대신 Azure 구독 키를 사용하면 맵이 제대로 렌더링되지 않습니다. 또한 보안을 위해 기본 키와 보조 키 사이를 회전하는 것이 좋습니다. 키를 회전하려면 보조 키를 사용하도록 앱을 업데이트하고 배포한 다음, 기본 키 옆에 있는 주기/새로 고침 단추를 눌러 새 기본 키를 생성합니다. 이전 기본 키는 사용할 수 없습니다. 키 회전에 대한 자세한 내용은 [키 회전 및 감사를 사용하여 Azure Key Vault 설정](https://docs.microsoft.com/azure/key-vault/secrets/tutorial-rotation-dual)을 참조하세요.

![구독 키를 얻습니다.](./media/quick-ios-app/get-key.png)

## <a name="create-a-project-in-xcode"></a>Xcode에서 프로젝트 만들기

먼저 새 iOS 앱 프로젝트를 만듭니다. Xcode 프로젝트를 만들려면 다음 단계를 완료합니다.

1. **파일에서** **새**  ->  **Project** 선택합니다.
2. **iOS** 탭에서 **앱** 을 선택한 후 **다음을** 선택합니다.
3. 앱 이름, 번들 ID를 입력하고 **다음을** 선택합니다.

새 프로젝트를 만드는 데 도움이 되는 자세한 내용은 [앱에 대한 Xcode Project](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app) 만들기를 참조하세요.

![첫 번째 iOS 애플리케이션을 만듭니다.](./media/quick-ios-app/create-app.png)

## <a name="install-the-azure-maps-ios-sdk"></a>Azure 지도 iOS SDK 설치

애플리케이션을 빌드하는 다음 단계는 Azure 지도 iOS SDK를 설치하는 것입니다. SDK를 설치하려면 다음 단계를 완료합니다.

1. **Project 탐색기에서** 프로젝트 파일 선택
2. 열린 **Project 편집기에서** 프로젝트 선택
3. Swift **패키지** 탭으로 전환
4. Azure 지도 iOS SDK 추가:`{link goes here}`

![iOS 프로젝트 추가](./media/quick-ios-app/add-project.png)

## <a name="add-mapcontrol-view"></a>MapControl 보기 추가

1. 컨트롤러를 보려면 사용자 지정 추가 `UIView`
2. `MapControl`모듈에서 클래스 선택 `AzureMapsControl`

![auzre maps contro.l 추가](./media/quick-ios-app/add-map-control.png)

3. **AppDelegate.swift** 파일에서 다음을 수행해야 합니다.
    * Azure 지도 SDK에 대한 가져오기 추가
    * Azure 지도 인증 정보 설정 또는 메서드를 사용하여 전역적으로 AzureMaps 클래스에 `AzureMaps.configure(subscriptionKey:)` `AzureMaps.configure(aadClient:aadAppId:aadTenant:)` 인증 정보를 설정하면 모든 보기에 인증 정보를 추가할 필요가 없습니다.

4. 다음 그래픽과 같이 실행 단추를 선택하거나 를 눌러 `CMD`  +  `R` 애플리케이션을 빌드합니다.

![ios 애플리케이션을 실행합니다.](./media/quick-ios-app/run.png)

Xcode는 애플리케이션을 빌드하는 데 몇 초 정도 걸립니다. 빌드가 완료되면 시뮬레이션된 iOS 디바이스에서 애플리케이션을 테스트할 수 있습니다. 다음과 같은 맵이 표시됩니다.

![iOS 애플리케이션의 첫 번째 맵입니다.](./media/quick-ios-app/example.png)

## <a name="clean-up-resources"></a>리소스 정리

> [!WARNING]
> [다음 단계](#next-steps) 섹션에 나열된 이 자습서에서는 계정에서 Azure Maps를 사용하고 구성하는 방법에 대해 자세히 설명되어 있습니다. 자습서를 계속 진행하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요.

자습서를 계속 진행하지 않으려면 다음 단계에 따라 리소스를 정리합니다.

1. Xcode를 닫고 만든 프로젝트를 삭제합니다.
2. 외부 디바이스에서 애플리케이션을 테스트한 경우 해당 디바이스에서 애플리케이션을 제거합니다.

Azure 지도 iOS SDK를 사용하여 계속 개발할 계획이 없는 경우:

1. Azure Portal 페이지로 이동합니다. 기본 포털 페이지에서 **모든 리소스** 를 선택합니다. 또는 왼쪽 위 모서리에 있는 메뉴 아이콘을 클릭합니다. **모든 리소스** 를 선택합니다.
2. Azure Maps 계정을 클릭합니다. 페이지 위쪽에서 **삭제** 를 클릭합니다.
3. 필요에 따라 iOS 앱을 계속 개발할 계획이 없는 경우 Xcode를 제거합니다.

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Maps 계정을 만들고 데모 애플리케이션을 만들었습니다. Azure 지도 대해 자세히 알아보려면 다음 자습서를 살펴보세요. 이 빠른 시작에서는 Azure 지도 계정을 만들고 데모 애플리케이션을 만들었습니다. Azure Maps에 대해 자세히 알아보려면 다음 자습서를 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Maps에 GeoJSON 데이터 로드](tutorial-load-geojson-file-android.md)


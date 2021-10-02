---
title: iOS 앱 만들기
description: Azure Maps 계정 및 첫 번째 iOS 앱을 만드는 단계입니다.
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 46e5ec41c0cea9a417b920e8441ff1129040c209
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389611"
---
#  <a name="create-an-ios-app-public-preview"></a>IOS 앱 만들기 (공개 미리 보기)

이 문서에서는 iOS 앱에 Azure Maps를 추가 하는 방법을 보여 줍니다. 여기서는 다음 기본 단계를 단계별로 안내합니다.

* 개발 환경을 설정합니다.
* 사용자 고유의 Azure Maps 계정을 만듭니다.
* 앱에서 사용할 기본 Azure Maps 키를 가져옵니다.
* 프로젝트에서 Azure Maps 라이브러리를 참조합니다.
* 앱에 Azure Maps 컨트롤을 추가합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 Azure Maps 계정을 만듭니다. Azure 구독이 없는 경우 시작 하기 전에  [무료 계정](https://azure.microsoft.com/free/)  을 만듭니다.
* [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
* 기본 키 또는 구독 키 라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account) . Azure Maps의 인증에 대 한 자세한 내용은 [Azure Maps 인증 관리](how-to-manage-authentication.md)를 참조 하세요.
* [Mac 앱 스토어의 Xcode](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) 을 무료로 다운로드 하세요.

## <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

다음 단계에 따라 새 Azure Maps 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com/) 의 왼쪽 위 모서리에서 **리소스 만들기** 를 클릭 합니다.

2. _Marketplace 검색_ 상자에서 **Azure Maps** 를 입력합니다.

3. _결과_ 에서 **Azure Maps** 를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다.

4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
   - 이 계정에 사용하려는 _구독_.
   - 이 계정에 대한 _리소스 그룹_ 이름. _새로 만들기_ 또는 _기존_ 리소스 그룹 사용을 선택할 수도 있습니다.
   - 새 계정의 _이름_.
   - 이 계정에 대한 _가격 책정 계층_ 입니다.
   - _라이선스_ 및 _개인정보처리방침_ 을 읽고 조건에 동의하는 확인란을 선택합니다.
   - **만들기** 단추를 클릭합니다.

   :::image source="./media/quick-ios-app/create-account.png" alt-text="Azure Maps 계정을 만드는 스크린샷":::

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Maps 계정이 성공적으로 만들어지면 Maps API를 쿼리할 수 있는 기본 키를 검색합니다.

1. 포털에서 Maps 계정을 엽니다.

2. 설정 섹션에서 **인증** 을 선택합니다.

3. **기본 키** 를 클립보드로 복사합니다. 이 자습서의 뒷부분에서 사용하기 위해 로컬로 저장합니다.

   > [!Note]
   > Azure Maps 기본 키 대신 Azure 구독 키를 사용하면 맵이 제대로 렌더링되지 않습니다. 또한 보안을 위해 기본 키와 보조 키 사이를 회전하는 것이 좋습니다. 키를 회전하려면 보조 키를 사용하도록 앱을 업데이트하고 배포한 다음, 기본 키 옆에 있는 주기/새로 고침 단추를 눌러 새 기본 키를 생성합니다. 이전 기본 키는 사용할 수 없습니다. 키 회전에 대한 자세한 내용은 [키 회전 및 감사를 사용하여 Azure Key Vault 설정](../key-vault/secrets/tutorial-rotation-dual.md)을 참조하세요.

   :::image source="./media/quick-ios-app/get-key.png" alt-text="구독 키를 가져오는 스크린샷.":::

## <a name="create-a-project-in-xcode"></a>Xcode에서 프로젝트 만들기

먼저 새 iOS 앱 프로젝트를 만듭니다. 다음 단계를 완료 하 여 Xcode 프로젝트를 만듭니다.

1. **파일** 에서 **새로 만들기**  ->  **Project** 를 선택 합니다.

2. **IOS** 탭에서 **앱** 을 선택 하 고 **다음** 을 선택 합니다.

3. 앱 이름, 번들 ID를 입력 하 고 **다음** 을 선택 합니다.

   새 프로젝트를 만드는 방법에 대 한 자세한 내용은 [앱에 대 한 Xcode Project 만들기](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app) 를 참조 하세요.

:::image source="./media/quick-ios-app/create-app.png" alt-text="첫 번째 iOS 응용 프로그램을 만듭니다.":::

## <a name="install-the-azure-maps-ios-sdk"></a>Azure Maps iOS SDK 설치

응용 프로그램을 빌드하는 다음 단계는 Azure Maps iOS SDK를 설치 하는 것입니다. SDK를 설치하려면 다음 단계를 완료합니다.

1. **Project 탐색기** 에서 프로젝트 파일을 선택 합니다.

2. 열린 **Project 편집기** 에서 프로젝트를 선택 합니다.

3. **Swift 패키지** 탭으로 전환

4. IOS SDK Azure Maps 추가:`{link goes here}`

   :::image source="./media/quick-ios-app/add-project.png" alt-text="IOS 프로젝트를 추가 하는 스크린샷":::
  
## <a name="add-mapcontrol-view"></a>없습니다 view 추가

1. `UIView`보기 컨트롤러에 사용자 지정 추가

2. `MapControl`모듈에서 클래스 `AzureMapsControl` 선택

   :::image source="./media/quick-ios-app/add-map-control.png" alt-text="Azure Maps 컨트롤을 추가 하는 스크린샷.":::

3. **AppDelegate swift** 파일에서 다음을 수행 해야 합니다.

   - Azure Maps SDK에 대 한 가져오기를 추가 합니다.
   - Azure Maps 인증 정보를 설정 합니다.
   
   또는 메서드를 사용 하 여 AzureMaps 클래스의 인증 정보를 전역적으로 설정 하면 `AzureMaps.configure(subscriptionKey:)` `AzureMaps.configure(aadClient:aadAppId:aadTenant:)` 모든 보기에 인증 정보를 추가할 필요가 없습니다.

4. 다음 그래픽에 표시 된 대로 실행 단추를 선택 하거나 (누르기 `CMD`  +  `R` ) 응용 프로그램을 빌드합니다.

   :::image source="./media/quick-ios-app/run.png" alt-text="IOS 응용 프로그램을 실행 하는 스크린샷":::

   Xcode는 응용 프로그램을 빌드하는 데 몇 초 정도 걸립니다. 빌드가 완료 된 후 시뮬레이션 된 iOS 장치에서 응용 프로그램을 테스트할 수 있습니다. 다음과 같은 맵이 표시됩니다.

   :::image source="./media/quick-ios-app/example.png" alt-text="IOS 응용 프로그램에 대 한 첫 번째 지도의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

> [!WARNING]
> [다음 단계](#next-steps) 섹션에 나열된 이 자습서에서는 계정에서 Azure Maps를 사용하고 구성하는 방법에 대해 자세히 설명되어 있습니다. 자습서를 계속 진행하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요.

자습서를 계속 진행하지 않으려면 다음 단계에 따라 리소스를 정리합니다.

1. Xcode를 닫고 만든 프로젝트를 삭제 합니다.
2. 외부 디바이스에서 애플리케이션을 테스트한 경우 해당 디바이스에서 애플리케이션을 제거합니다.

Azure Maps iOS SDK를 사용 하 여 계속 개발 하려는 경우:

1. Azure Portal 페이지로 이동합니다. 기본 포털 페이지에서 **모든 리소스** 를 선택합니다. 또는 왼쪽 위 모서리에 있는 메뉴 아이콘을 클릭합니다. **모든 리소스** 를 선택합니다.
2. Azure Maps 계정을 클릭합니다. 페이지 위쪽에서 **삭제** 를 클릭합니다.
3. 필요에 따라 iOS 앱을 계속 개발 하지 않으려는 경우 Xcode을 제거 합니다.

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](how-to-manage-authentication.md)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Maps 계정을 만들고 데모 애플리케이션을 만들었습니다. Azure Maps에 대해 자세히 알아보려면 다음 자습서를 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Maps에 GeoJSON 데이터 로드](tutorial-load-geojson-file-android.md)

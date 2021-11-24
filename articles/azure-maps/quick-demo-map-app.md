---
title: '빠른 시작: Azure Maps를 사용하여 대화형 맵 검색'
description: '빠른 시작: 검색 가능한 대화형 맵을 만드는 방법을 알아봅니다. Azure Maps 계정을 만들고, 기본 키를 가져오고, 웹 SDK를 사용하여 맵 애플리케이션을 설정하는 방법을 참조하세요.'
author: stevemunk
ms.author: v-munksteve
ms.date: 09/30/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, mode-other
ms.openlocfilehash: 9074b210438c5f79743a9bed1f10f639cc6cf732
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133062518"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>빠른 시작: Azure Maps를 사용하여 대화형 검색 맵 만들기

이 빠른 시작에서는 Azure Maps를 사용하여 사용자에게 대화형 검색 환경을 제공하는 맵을 만드는 방법을 알아봅니다. 여기서는 다음 기본 단계를 단계별로 안내합니다.

* 사용자 고유의 Azure Maps 계정을 만듭니다.
* 데모 웹 애플리케이션에서 사용할 기본 키를 가져옵니다.
* 데모 맵 애플리케이션을 다운로드하여 엽니다.

이 빠른 시작에서는 Azure Maps Web SDK를 사용하지만 Azure Maps 서비스는 Azure Maps 팀에서 플러그 인을 만들 때 사용하는 인기 있는 [오픈 소스 맵 컨트롤](open-source-projects.md#third-part-map-control-plugins)과 같은 모든 맵 컨트롤에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* [Azure Portal](https://portal.azure.com)에 로그인합니다.

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

다음 단계에 따라 새 Azure Maps 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기** 를 클릭합니다.
2. *Marketplace 검색* 상자에서 **Azure Maps** 를 입력합니다.
3. *결과* 에서 **Azure Maps** 를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다.
4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
    * 이 계정에 사용하려는 *구독*.
    * 이 계정에 대한 *리소스 그룹* 이름. *새로 만들기* 또는 *기존* 리소스 그룹 사용을 선택할 수도 있습니다.
    * 새 계정의 *이름*.
    * 이 계정에 대한 *가격 책정 계층* 입니다.
    * *라이선스* 및 *개인정보처리방침* 을 읽고 조건에 동의하는 확인란을 선택합니다.
    * **만들기** 단추를 클릭합니다.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="포털에서 Maps 계정 만들기" lightbox="./media/quick-demo-map-app/create-account.png":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Maps 계정이 성공적으로 만들어지면 Maps API를 쿼리할 수 있는 기본 키를 검색합니다.

1. 포털에서 Maps 계정을 엽니다.
2. 설정 섹션에서 **인증** 을 선택합니다.
3. **기본 키** 를 클립보드로 복사합니다. 이 자습서의 뒷부분에서 사용하기 위해 로컬로 저장합니다.

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Azure Portal에서 기본 Key Azure Maps 키 가져오기" lightbox="./media/quick-demo-map-app/get-key.png":::

>[!NOTE]
> 이 빠른 시작에서는 데모용 [공유 키](azure-maps-authentication.md#shared-key-authentication) 인증 방법을 사용하지만, 프로덕션 환경에 사용되는 기본 방법은 [Azure Active Directory](azure-maps-authentication.md#azure-ad-authentication) 인증입니다.

## <a name="download-and-update-the-azure-maps-demo"></a>Azure Maps 데모 다운로드 및 업데이트

1. [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html)로 이동합니다. 파일 콘텐츠를 복사합니다.
2. 이 파일의 콘텐츠를 **AzureMapDemo.html** 에 로컬로 저장합니다. 텍스트 편집기에서 엽니다.
3. 이전 섹션에서 가져온 **기본 키** 값을 추가합니다.
    1. `authOptions` 함수의 모든 코드를 주석으로 처리합니다. 이 코드는 Azure Active Directory 인증에 사용됩니다.
    1. `authOptions` 함수에서 마지막 두 줄의 주석 처리를 제거합니다. 이 코드는 이 빠른 시작에서 사용되는 방식인 공유 키 인증에 사용됩니다.
    1. `<Your Azure Maps Key>`를 이전 섹션의 **기본 키** 값으로 바꿉니다.

## <a name="open-the-demo-application"></a>데모 애플리케이션 열기

1. 원하는 브라우저에서 **AzureMapDemo.html** 파일을 엽니다.
2. 로스앤젤레스 시의 지도를 관찰합니다. 확대 및 축소하여 확대/축소 수준에 따라 지도가 더 많거나 더 적은 정보로 자동으로 렌더링되도록 하는 방법을 확인합니다.
3. 지도의 기본 센터를 변경합니다. **AzureMapDemo.html** 파일에서 **center** 라는 변수를 검색합니다. 이 변수에 대한 위도, 경도 쌍 값을 새 값인 **[-74.0060, 40.7128]** 로 바꿉니다. 파일을 저장하고 브라우저를 새로 고칩니다.
4. 대화형 검색 환경을 사용해 봅니다. 웹 애플리케이션 데모의 왼쪽 위 모서리에 있는 검색 상자에서 **식당** 을 검색합니다.
5. 검색 상자 아래에 표시되는 주소와 위치의 목록 위로 마우스를 이동합니다. 지도의 해당 핀이 해당 위치에 대한 정보를 팝업하는 상태를 확인합니다. 프라이빗 비즈니스의 정보 보호를 위해 가상의 이름과 주소가 표시됩니다.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="대화형 맵 검색 웹 애플리케이션" lightbox="./media/quick-demo-map-app/interactive-search.png":::

## <a name="clean-up-resources"></a>리소스 정리

>[!WARNING]
>[다음 단계](#next-steps) 섹션에 나열된 이 자습서에서는 계정에서 Azure Maps를 사용하고 구성하는 방법에 대해 자세히 설명되어 있습니다. 자습서를 계속 진행하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요.

자습서를 계속 진행하지 않으려면 다음 단계에 따라 리소스를 정리합니다.

1. **AzureMapDemo.html** 웹 애플리케이션을 실행하는 브라우저를 닫습니다.
2. Azure Portal 페이지로 이동합니다. 기본 포털 페이지에서 **모든 리소스** 를 선택합니다. 또는 왼쪽 위 모서리에 있는 메뉴 아이콘을 클릭합니다. **모든 리소스** 를 선택합니다.
3. Azure Maps 계정을 클릭합니다. 페이지 위쪽에서 **삭제** 를 클릭합니다.

더 많은 코드 예제 및 대화형 코딩 환경은 다음 가이드를 참조하세요.

* [Azure Maps 검색 서비스를 사용하여 주소 찾기](how-to-search-for-address.md)
* [Azure Maps 맵 컨트롤 사용](how-to-use-map-control.md)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Maps 계정을 만들고 데모 애플리케이션을 만들었습니다. Azure Maps에 대해 자세히 알아보려면 다음 자습서를 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Maps를 사용하여 주변 관심 지점 검색](tutorial-search-location.md)

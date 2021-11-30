---
title: iOS 맵 컨트롤 | 시작 Microsoft Azure 지도
description: Azure 지도 iOS SDK에 익숙해집니다. SDK를 설치하고 대화형 맵을 만드는 방법을 참조하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 8b6eab451552a7e18bd527abe616cab885be00ba
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218770"
---
# <a name="getting-started-with-azure-maps-ios-sdk-preview"></a>Azure 지도 iOS SDK 시작(미리 보기)

Azure 지도 iOS SDK는 iOS용 벡터 맵 라이브러리입니다. 이 문서에서는 Azure 지도 iOS SDK를 설치하고 맵을 로드하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료해야 합니다.

## <a name="localizing-the-map"></a>맵 지역화

Azure 지도 iOS SDK는 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 다음 코드에서는 *언어를* 프랑스어("fr-FR")로 설정하고 *지역 보기를* "Auto"로 설정하는 다양한 방법을 보여 있습니다.

1. 정적 및 속성을 사용하여 언어 및 지역별 뷰 정보를 `AzureMaps` 클래스에 `language` `view` 전달합니다. 그러면 앱의 기본 언어 및 지역 보기 속성이 설정됩니다.

    ```swift
    // Alternatively use Azure Active Directory authenticate.
    AzureMaps.configure(aadClient: "<Your aad clientId>", aadAppId: "<Your aad AppId>", aadTenant: "<Your aad Tenant>")
    
    // Set your Azure Maps Key.
    // AzureMaps.configure(subscriptionKey: "<Your Azure Maps Key>")
    
    // Set the language to be used by Azure Maps.
    AzureMaps.language = "fr-FR"
    
    // Set the regional view to be used by Azure Maps.
    AzureMaps.view = "Auto"
    ```

1. 언어 및 지역 보기 정보를 지도 컨트롤 init에 전달할 수도 있습니다.

    ```swift
    MapControl(options: [
        StyleOptions.language("fr-FR"),
        StyleOptions.view("Auto")
    ])
    ```

1. 언어 및 지역별 보기 속성을 프로그래밍 방식으로 설정하는 마지막 방법은 maps `setStyle` 메서드를 사용합니다. 맵의 언어 및 지역 보기를 변경해야 할 때마다 이 작업을 수행합니다.

    ```swift
    mapControl.getMapAsync { map in
        map.setStyle([
            StyleOptions.language("fr-FR"),
            StyleOptions.view("Auto")
        ])
    }
    ```

다음은 언어가 "fr-FR"로 설정되고 지역 보기가 "Auto"로 설정된 Azure 지도 애플리케이션의 예입니다.

:::image type="content" source="media/ios-sdk/how-to-use-ios-map-control-library/fr-borderless.png" alt-text="프랑스어로 레이블을 보여주는 지도 이미지입니다.":::

지원되는 언어 및 지역 보기의 전체 목록은 [Azure 지도 지역화 지원을](supported-languages.md)참조하세요.

## <a name="navigating-the-map"></a>맵 탐색

이 섹션에서는 Azure 지도 프로그램에서 탐색하는 다양한 방법을 자세히 설명합니다.

### <a name="zoom-the-map"></a>맵 확대/축소

* 두 손가락으로 맵을 터치하고 두 손가락을 모으면 축소되고 두 손가락을 넓히면 확대됩니다.
* 한 수준 확대하려면 맵을 두 번 탭합니다.
* 두 손가락을 두 번 탭하여 맵을 한 단계 축소합니다.
* 두 번 탭할 때 두 번째 탭에서 손가락을 맵에 고정한 채로 위나 아래로 끌면 확대 또는 축소됩니다.

### <a name="pan-the-map"></a>맵 이동

* 맵을 터치하고 원하는 방향으로 끕니다.

### <a name="rotate-the-map"></a>맵 회전

* 두 손가락으로 맵을 터치하고 회전합니다.

### <a name="pitch-the-map"></a>맵 피치

* 두 손가락으로 맵을 터치하고 위나 아래로 함께 끕니다.

## <a name="azure-government-cloud-support"></a>Azure Government 클라우드 지원

Azure 지도 iOS SDK는 Azure Government 클라우드 사용을 지원합니다. Azure 지도 인증 세부 정보가 지정된 다음 코드 줄을 추가하여 Azure 지도 정부 클라우드 도메인 사용을 지정합니다.

```
AzureMaps.domain = "atlas.azure.us"
```

맵과 서비스를 인증하는 경우 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용해야 합니다.

## <a name="additional-information"></a>추가 정보

추가 코드 예제는 다음 문서를 참조하세요.

* [빠른 시작: iOS 앱 만들기](quick-ios-app.md)
* [iOS 맵에서 지도 스타일 변경](set-map-style-ios-sdk.md)
* [기호 계층 추가](add-symbol-layer-ios.md)
* [선 계층 추가](add-line-layer-map-ios.md)
* [다각형 계층 추가](add-polygon-layer-map-ios.md)

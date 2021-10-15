---
title: IOS 맵 컨트롤 시작 | Microsoft Azure 지도
description: Azure Maps iOS SDK에 대해 잘 알고 있어야 합니다. SDK를 설치 하 고 대화형 지도를 만드는 방법을 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/08/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 53d5b70f62d3ccea3d881a4e91a4b16287e1aa49
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043342"
---
# <a name="getting-started-with-azure-maps-ios-sdk-public-preview"></a>IOS SDK Azure Maps 시작 (공개 미리 보기)

Ios SDK Azure Maps는 iOS 용 벡터 맵 라이브러리입니다. 이 문서에서는 Azure Maps iOS SDK를 설치 하 고 지도를 로드 하는 프로세스를 안내 합니다.

## <a name="prerequisites"></a>필수 조건

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료 해야 합니다.

## <a name="localizing-the-map"></a>맵 지역화

IOS SDK Azure Maps은 지도의 언어 및 지역별 보기를 설정 하는 세 가지 방법을 제공 합니다. 다음 코드에서는 *언어* 를 프랑스어 ("fr-fr")로 설정 하는 여러 가지 방법과 *지역 보기* 를 "자동"으로 설정 하는 방법을 보여 줍니다.

1. `AzureMaps`정적 및 속성을 사용 하 여 언어 및 지역 뷰 정보를 클래스에 전달 합니다 `language` `view` . 그러면 앱에서 기본 언어 및 국가별 보기 속성이 설정 됩니다.

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

1. 언어 및 지역 보기 정보를 지도 컨트롤 init로 전달할 수도 있습니다.

    ```swift
    MapControl(options: [
        StyleOptions.language("fr-FR"),
        StyleOptions.view("Auto")
    ])
    ```

1. 언어 및 지역 보기 속성을 프로그래밍 방식으로 설정 하는 마지막 방법은 maps 메서드를 사용 하는 것입니다 `setStyle` . 이 작업을 수행 하려면 언제 든 지 지도의 언어 및 지역 보기를 변경 해야 합니다.

    ```swift
    mapControl.getMapAsync { map in
        map.setStyle([
            StyleOptions.language("fr-FR"),
            StyleOptions.view("Auto")
        ])
    }
    ```

언어가 "fr-fr"로 설정 되 고 지역 보기가 "Auto"로 설정 된 Azure Maps 응용 프로그램의 예는 다음과 같습니다.

:::image type="content" source="media/ios-sdk/how-to-use-ios-map-control-library/fr-borderless.png" alt-text="프랑스어로 레이블을 표시 하는 지도 이미지입니다.":::

지원 되는 언어 및 지역 보기의 전체 목록은 [Azure Maps의 지역화 지원](supported-languages.md)을 참조 하세요.

## <a name="navigating-the-map"></a>맵 탐색

이 섹션에서는 Azure Maps 프로그램에서 탐색 하는 다양 한 방법에 대해 자세히 설명 합니다.

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

Azure Maps iOS SDK는 Azure Government 클라우드의 사용을 지원 합니다. Azure Maps 인증 세부 정보를 지정 하는 다음 코드 줄을 추가 하 여 Azure Maps 정부 클라우드 도메인을 사용 하도록 지정 합니다.

```
AzureMaps.domain = "atlas.azure.us"
```

맵과 서비스를 인증하는 경우 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용해야 합니다.
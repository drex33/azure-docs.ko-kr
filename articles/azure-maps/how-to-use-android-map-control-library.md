---
title: Android 맵 컨트롤 | 시작 Microsoft Azure 지도
description: Azure Maps Android SDK에 대해 알아봅니다. Android Studio에서 프로젝트를 만들고, SDK를 설치하고, 대화형 맵을 만드는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1e9a8fac2dc12d2dcb3eb80b5cca01c8c3b19a19
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483005"
---
# <a name="get-started-with-azure-maps-android-sdk"></a>Azure 지도 Android SDK 시작

Azure Maps Android SDK는 Android용 벡터 지도 라이브러리입니다. 이 문서에서는 Azure Maps Android SDK를 설치하고 맵을 로드하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료해야 합니다.

## <a name="localizing-the-map"></a>맵 지역화

Azure 지도 Android SDK 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 다음 코드에서는 언어를 프랑스어(“fr-FR”)로 설정하고 지역 보기를 “Auto”로 설정하는 방법을 보여 줍니다.

1. 정적 및 속성을 사용하여 언어 및 지역별 뷰 정보를 `AzureMaps` 클래스에 `setLanguage` `setView` 전달합니다. 그러면 앱에서 기본 언어 및 지역 보기 속성이 설정됩니다.

    ::: zone pivot="programming-language-java-android"

    ```java
    static {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
    ```

    ::: zone-end

    ::: zone pivot="programming-language-kotlin"

    ```kotlin
    companion object {
        init {
            //Alternatively use Azure Active Directory authenticate.
            AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
            //Set your Azure Maps Key.
            //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
        
            //Set the language to be used by Azure Maps.
            AzureMaps.setLanguage("fr-FR");
        
            //Set the regional view to be used by Azure Maps.
            AzureMaps.setView("Auto");
        }
    }
    ```

    ::: zone-end

1. 언어 및 지역 보기 정보를 지도 컨트롤 XML에 전달할 수도 있습니다.

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        />
    ```

1. 언어 및 지역별 보기 속성을 프로그래밍 방식으로 설정하는 마지막 방법은 maps `setStyle` 메서드를 사용합니다. 언제든지 이 작업을 수행하여 맵의 언어 및 지역 보기를 변경할 수 있습니다.

    ::: zone pivot="programming-language-java-android"

    ```java
    mapControl.onReady(map -> {
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        );
    });
    ```

    ::: zone-end

    ::: zone pivot="programming-language-kotlin"

    ```kotlin
    mapControl.onReady(OnReady { map: AzureMap ->
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        )
    })
    ```

    ::: zone-end

다음은 언어가 “fr-FR”로 설정되고 지역 보기가 “Auto”로 설정된 Azure Maps의 예시입니다.

![Azure Maps, 프랑스어로 레이블을 표시하는 맵 이미지](media/how-to-use-android-map-control-library/android-localization.png)

지원되는 언어 및 지역 보기의 전체 목록은 [Azure 지도 지역화 지원을](supported-languages.md)참조하세요.

## <a name="navigating-the-map"></a>맵 탐색

여러 가지 방식으로 맵을 확대/축소, 이동, 회전 및 피치할 수 있습니다. 다음에서 맵을 탐색하는 다양한 방법을 자세히 설명합니다.

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

Azure Maps Android SDK는 Azure Government 클라우드를 지원합니다. Azure Maps Android SDK는 동일한 Maven 리포지토리에서 액세스됩니다. Azure Maps 플랫폼의 Azure Government 클라우드 버전에 연결하려면 다음 작업을 수행해야 합니다.

Azure Maps 인증 세부 정보가 지정된 위치에서 다음 코드 줄을 추가하여 맵에 Azure Maps 정부 클라우드 도메인을 사용하도록 지시합니다.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

맵과 서비스를 인증하는 경우 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용해야 합니다.

## <a name="migrating-from-a-preview-version"></a>미리 보기 버전에서 마이그레이션

미리 보기에서 일반 공급으로 이동하면서 몇 가지 호환성이 손상되는 변경이 Azure Maps Android SDK에 도입되었습니다. 주요 세부 정보는 다음과 같습니다.

* Maven 식별자가 `"com.microsoft.azure.maps:mapcontrol:0.7"`에서 `"com.azure.android:azure-maps-control:1.0.0"`으로 변경되었습니다. 네임스페이스 및 주 버전 번호가 변경되었습니다.
* 가져오기 네임스페이스가 `com.microsoft.azure.maps.mapcontrol`에서 `com.azure.android.maps.control`로 변경되었습니다.
* XML 옵션, 색 리소스 및 이미지 리소스의 리소스 이름에서 텍스트 `mapcontrol_`이 `azure_maps_`로 바뀌었습니다.

    **이전:**

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
    />
    ```

    **이후:**

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        app:azure_maps_centerLat="47.602806"
        app:azure_maps_centerLng="-122.329330"
        app:azure_maps_zoom="12"
    />
    ```

## <a name="next-steps"></a>다음 단계

맵에 오버레이 데이터를 추가하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps의 인증 관리](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Android 맵에서 맵 스타일 변경](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)

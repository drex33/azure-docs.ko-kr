---
title: 자습서 - Android 앱 마이그레이션 | Microsoft Azure Maps
description: Android 앱을 Google Maps에서 Microsoft Azure Maps로 마이그레이션하는 방법에 대한 자습서
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 50958765e0ff582e630d5a78b3d17f871a0c41aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072487"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>자습서: Google Maps에서 Android 앱 마이그레이션

Azure Maps Android SDK의 API 인터페이스는 웹 SDK와 비슷합니다. 이러한 SDK 중 하나로 앱을 개발하신 분들은 개념, 모범 사례 및 아키텍처가 상당히 많이 겹친다는 것을 알 수 있습니다. 이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 맵 로드
> * 맵 지역화
> * 표식, 폴리라인 및 다각형 추가
> * 타일 레이어 오버레이
> * 트래픽 데이터 표시

모든 예제는 Java로 제공되지만, Azure Maps Android SDK에서는 Kotlin을 사용할 수 있습니다.

Azure Maps에서 제공하는 Android SDK로 앱을 개발하는 방법에 대한 자세한 내용은 [Azure Maps Android SDK에 대한 방법 가이드](how-to-use-android-map-control-library.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

1. [Azure Portal](https://portal.azure.com)에 로그인하여 Azure Maps 계정을 만듭니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
2. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

## <a name="load-a-map"></a>맵 로드

Google Maps 또는 Azure Maps를 사용하여 Android 앱에서 맵을 로드하는 작업은 비슷한 단계로 구성됩니다. 두 SDK 중 하나를 사용하는 경우 다음을 수행해야 합니다.

* 두 플랫폼 중 하나에 액세스하기 위한 API 또는 구독 키를 가져옵니다.
* 작업에 XML을 추가하여 맵을 렌더링할 위치 및 레이아웃을 지정합니다.
* 맵 보기를 포함하는 활동에서 맵 클래스의 해당 메서드로 모든 수명 주기 메서드를 재정의합니다. 특히 다음 메서드를 재정의해야 합니다.
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* 맵이 준비될 때까지 기다린 후에 맵에 액세스하여 프로그래밍합니다.

### <a name="before-google-maps"></a>이전: Google Maps

Android용 Google Maps SDK를 사용하여 맵을 표시하려면 다음 단계를 수행합니다.

1. Google Play 서비스가 설치되어 있는지 확인합니다.
2. 모듈의 **gradle.build** 파일에 Google Maps 서비스에 대한 종속성을 추가합니다.

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. **google\_maps\_api.xml** 파일의 애플리케이션 섹션 안에 Google Maps API 키를 추가합니다.

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. 다음과 같이 기본 작업에 맵 조각을 추가합니다.

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

::: zone pivot="programming-language-java-android"

5. **MainActivity.java** 파일에서 Google Maps SDK를 가져와야 합니다. 맵 보기를 포함하고 있는 작업의 모든 수명 주기 메서드를 map 클래스의 해당 항목으로 전달합니다. `getMapAsync(OnMapReadyCallback)` 메서드를 사용하여 맵 조각에서 `MapView` 인스턴스를 검색합니다. `MapView`는 맵 시스템과 보기를 자동으로 초기화합니다. **MainActivity.java** 파일을 다음과 같이 편집합니다.

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

5. **MainActivity.kt** 파일에서 Google Maps SDK를 가져와야 합니다. 맵 보기를 포함하고 있는 작업의 모든 수명 주기 메서드를 map 클래스의 해당 항목으로 전달합니다. `getMapAsync(OnMapReadyCallback)` 메서드를 사용하여 맵 조각에서 `MapView` 인스턴스를 검색합니다. `MapView`는 맵 시스템과 보기를 자동으로 초기화합니다. **MainActivity.kt** 파일을 다음과 같이 편집합니다.

    ```kotlin
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle

    class MainActivity : AppCompatActivity() implements OnMapReadyCallback {
    
        var mapView: MapView? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapView = findViewById(R.id.myMap)
    
            mapView?.onCreate(savedInstanceState)
            mapView?.getMapAsync(this)
        }

        public fun onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        public override fun onStart() {
            super.onStart()
            mapView?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapView?.onResume()
        }
    
        public override fun onPause() {
            mapView?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapView?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapView?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapView?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapView?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

애플리케이션을 실행하면 다음 이미지와 같이 지도 컨트롤이 로드됩니다.

![간단한 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>이후: Azure Maps

Android용 Azure Maps SDK를 사용하여 맵을 표시하려면 다음 단계를 수행합니다.

1. 최상위 **build.gradle** 파일을 열고, **all projects** 블록 섹션에 다음 코드를 추가합니다.

    ```gradel
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle** 을 업데이트하고 다음 코드를 추가합니다.

    1. 프로젝트의 **minSdkVersion** 이 API 21 이상인지 확인합니다.

    2. Android 섹션에 다음 코드를 추가합니다.

        ```gradel
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. 종속성 블록을 업데이트합니다. 최신 Azure Maps Android SDK에 대한 새 구현 종속성 줄을 추가합니다.

        ```gradel
        implementation "com.azure.android:azure-maps-control:1.0.0"
        ```

        > [!NOTE]
        > 버전 번호를 "0+"로 설정하여 코드가 항상 최신 버전을 가리키게 할 수 있습니다.

    4. 도구 모음에서 **파일** 로 이동한 다음, **Gradle 파일과 프로젝트 동기화** 를 클릭합니다.

3. 다음과 같이 기본 작업에 맵 조각을 추가합니다(리소스 pwd\> 레이아웃 \> 작업\_main.xml):

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.azure.android.maps.control.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

::: zone pivot="programming-language-java-android"

4. **MainActivity.java** 파일에서 다음을 수행해야 합니다.

    * Azure Maps SDK 가져오기
    * Azure Maps 인증 정보 설정
    * **onCreate** 메서드에서 지도 컨트롤 인스턴스 가져오기

     `setSubscriptionKey` 또는 `setAadProperties` 메서드를 사용하여 `AzureMaps` 클래스에서 인증 정보를 설정합니다. 이 글로벌 업데이트의 경우 인증 정보를 모든 보기에 추가해야 합니다.

    지도 컨트롤에는 Android의 OpenGL 수명 주기를 관리하는 자체 수명 주기 메서드가 포함되어 있습니다. 이러한 메서드는 포함된 작업에서 직접 호출해야 합니다. 지도 컨트롤의 수명 주기 메서드를 올바르게 호출하려면 지도 컨트롤이 포함된 작업에서 다음 수명 주기 메서드를 재정의해야 합니다. 각 지도 컨트롤 메서드를 호출합니다.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    **MainActivity.java** 파일을 다음과 같이 편집합니다.

    ```java
    package com.example.myapplication;
    
    import androidx.appcompat.app.AppCompatActivity;
    import com.azure.android.maps.control.AzureMaps;
    import com.azure.android.maps.control.MapControl;
    import com.azure.android.maps.control.layer.SymbolLayer;
    import com.azure.android.maps.control.options.MapStyle;
    import com.azure.android.maps.control.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. **MainActivity.kt** 파일에서 다음을 수행해야 합니다.

    * Azure Maps SDK 가져오기
    * Azure Maps 인증 정보 설정
    * **onCreate** 메서드에서 지도 컨트롤 인스턴스 가져오기

     `setSubscriptionKey` 또는 `setAadProperties` 메서드를 사용하여 `AzureMaps` 클래스에서 인증 정보를 설정합니다. 이 글로벌 업데이트의 경우 인증 정보를 모든 보기에 추가해야 합니다.

    지도 컨트롤에는 Android의 OpenGL 수명 주기를 관리하는 자체 수명 주기 메서드가 포함되어 있습니다. 이러한 메서드는 포함된 작업에서 직접 호출해야 합니다. 지도 컨트롤의 수명 주기 메서드를 올바르게 호출하려면 지도 컨트롤이 포함된 작업에서 다음 수명 주기 메서드를 재정의해야 합니다. 각 지도 컨트롤 메서드를 호출합니다.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    **MainActivity.kt** 파일을 다음과 같이 편집합니다.

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.azure.android.maps.control.AzureMap
    import com.azure.android.maps.control.AzureMaps
    import com.azure.android.maps.control.MapControl
    import com.azure.android.maps.control.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

애플리케이션을 실행하면 다음 이미지와 같이 지도 컨트롤이 로드됩니다.

![간단한 Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Azure Maps 컨트롤은 보다 강력한 축소를 지원하고 더 많은 월드 보기를 제공합니다.

> [!TIP]
> Windows 머신에서 Android 에뮬레이터를 사용하는 경우 OpenGL 및 소프트웨어 가속 그래픽 렌더링과의 충돌로 인해 맵이 렌더링되지 않을 수 있습니다. 일부 사용자는 이 문제를 해결하기 위해 다음 작업을 수행했습니다. AVD 관리자를 열고 편집할 가상 디바이스를 선택합니다. **구성 확인** 패널에서 아래로 스크롤합니다. **에뮬레이트된 성능** 섹션에서 **그래픽** 옵션을 **하드웨어** 로 설정합니다.

## <a name="localizing-the-map"></a>맵 지역화

대상 그룹이 여러 국가/지역에 분산되어 있거나 다른 언어를 사용하는 경우 지역화가 중요합니다.

### <a name="before-google-maps"></a>이전: Google Maps

다음 코드를 `onCreate` 메서드에 추가하여 맵의 언어를 설정합니다. 맵의 컨텍스트 보기를 설정하기 전에 코드를 추가해야 합니다. "fr" 언어 코드는 언어를 프랑스어로 제한합니다.

::: zone pivot="programming-language-java-android"

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);

Configuration config = new Configuration();
config.locale = locale;

getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val languageToLoad = "fr"
val locale = Locale(languageToLoad)
Locale.setDefault(locale)

val config = Configuration()
config.locale = locale

baseContext.resources.updateConfiguration(
    config,
    baseContext.resources.displayMetrics
)
```

::: zone-end

다음은 언어가 "fr"로 설정된 Google Maps의 예입니다.

![Google Maps 지역화](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps&quot;></a>이후: Azure Maps

Azure Maps는 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 첫 번째 옵션은 언어 및 지역 보기 정보를 `AzureMaps` 클래스에 전달하는 것입니다. 이 옵션은 정적 `setLanguage` 및 `setView` 메서드를 전역적으로 사용합니다. 즉, 앱에 로드된 모든 Azure Maps 컨트롤에서 기본 언어 및 지역 보기가 설정됩니다. 다음 예제에서는 &quot;fr-FR&quot; 언어 코드를 사용하여 프랑스어를 설정합니다.

::: zone pivot=&quot;programming-language-java-android&quot;

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey(&quot;<Your Azure Maps Key>");

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
            //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

두 번째 옵션은 언어 및 보기 정보를 지도 컨트롤 XML 코드에 전달하는 것입니다.

```xml
<com.azure.android.maps.control.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_language="fr-FR"
    app:azure_maps_view="Auto"
    />
```

세 번째 옵션은 맵 `setStyle` 메서드를 사용하여 언어 및 지역 지도 보기를 프로그래밍하는 것입니다. 이 옵션은 코드가 실행될 때마다 언어 및 지역 보기를 업데이트합니다.

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
mapControl!!.onReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
}
```

::: zone-end

다음은 언어가 "fr-FR"로 설정된 Azure Maps의 예입니다.

![Azure Maps 지역화](media/migrate-google-maps-android-app/azure-maps-localization.png)

[지원되는 언어](supported-languages.md)의 전체 목록을 검토합니다.

## <a name="setting-the-map-view"></a>맵 보기 설정

Azure Maps 및 Google Maps의 동적 맵은 적절한 메서드를 호출하여 프로그래밍 방식으로 새 지리적 위치로 이동할 수 있습니다. 맵에서 위성 항공 이미지를 표시하고, 맴의 중심을 좌표의 위치에 맞추고, 확대/축소 수준을 변경해 보겠습니다. 다음 예제에서는 위도 35.0272, 경도 -111.0225 및 확대/축소 수준 15를 사용합니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps 지도 컨트롤의 카메라는 `moveCamera` 메서드를 사용하여 프로그래밍 방식으로 이동할 수 있습니다. `moveCamera` 메서드를 사용하면 맵의 중심과 확대/축소 수준을 지정할 수 있습니다. `setMapType` 메서드는 맵 유형을 표시됨으로 변경합니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(LatLng(35.0272, -111.0225), 15))
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE)
}
```

::: zone-end

![Google Maps 보기 설정](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google Maps는 크기가 256픽셀인 타일을 사용하고, Azure Maps는 이보다 큰 512픽셀 타일을 사용합니다. 따라서 Azure Maps에서 Google Maps와 동일한 맵 영역을 로드하는 데 필요한 네트워크 요청 수가 줄어듭니다. Google Maps에서 맵과 동일한 보기 가능 영역을 얻으려면 Azure Maps를 사용할 때 사용되는 Google Maps 확대/축소 수준에서 1을 빼야 합니다.

### <a name="after-azure-maps"></a>이후: Azure Maps

앞에서 설명한 대로 Azure Maps에서 동일한 보기 가능 영역을 얻으려면 사용되는 Google Maps 확대/축소 수준에서 1을 빼야 합니다. 이 경우 확대/축소 수준으로 14를 사용합니다.

초기 맵 보기는 지도 컨트롤의 XML 특성에서 설정할 수 있습니다.

```xml
<com.azure.android.maps.control.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_cameraLat="35.0272"
    app:azure_maps_cameraLng="-111.0225"
    app:azure_maps_zoom="14"
    app:azure_maps_style="satellite"
    />
```

맵 보기는 맵 `setCamera` 및 `setStyle` 메서드를 사용하여 프로그래밍할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE))
}
```

::: zone-end

![Azure Maps 보기 설정](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**추가 리소스:**

* [지원되는 맵 스타일](supported-map-styles.md)

## <a name="adding-a-marker"></a>표식 추가

지점 데이터는 맵의 이미지를 사용하여 렌더링되는 경우가 많습니다. 이러한 이미지를 표식, 압정, 핀 또는 기호라고 합니다. 다음 예제에서는 맵에서 위도 51.5, 경도 -0.2로 표시됩니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps를 사용하면 맵 `addMarker` 메서드를 사용하여 표식이 추가됩니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33)))
}
```

::: zone-end

![Google Maps 표식](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>이후: Azure Maps

Azure Maps에서 먼저 데이터를 데이터 원본에 추가하여 지점 데이터를 맵에 렌더링할 수 있습니다. 그런 다음, 해당 데이터 원본을 기호 레이어에 연결합니다. 데이터 원본은 지도 컨트롤의 공간 데이터 관리를 최적화합니다. 기호 레이어는 이미지 또는 텍스트를 사용하여 지점 데이터를 렌더링하는 방법을 지정합니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(source));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = new DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Create a symbol layer and add it to the map.
    map.layers.add(SymbolLayer(source))
}
```

::: zone-end

![Azure Maps 표식](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>사용자 지정 표식 추가

사용자 지정 이미지를 사용하여 맵에 지점을 나타낼 수 있습니다. 아래 예제의 맵은 사용자 지정 이미지를 사용하여 지점을 맵에 표시합니다. 지점은 위도 51.5, 경도 -0.2입니다. 앵커는 표식의 위치를 오프셋하여 압정 아이콘의 지점이 맵의 올바른 위치와 맞춰집니다.

![노란색 압정 이미지](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

위의 두 예제에서 이미지는 앱 리소스의 drawable 폴더에 추가됩니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps를 사용하면 표식에 사용자 지정 이미지를 사용할 수 있습니다. 표식의 `icon` 옵션을 사용하여 사용자 지정 이미지를 로드합니다. 이미지의 지점을 좌표에 맞추려면 `anchor` 옵션을 사용합니다. 앵커는 이미지의 크기를 기준으로 합니다. 이 경우 앵커의 너비는 0.2 단위이고, 높이는 1단위입니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f))
}
```

::: zone-end

![Google Maps 사용자 지정 표식](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>이후: Azure Maps

Azure Maps의 기호 레이어는 사용자 지정 이미지를 지원하지만, 먼저 이미지를 맵 리소스에 로드한 다음, 고유 ID를 할당해야 합니다. 그런 다음, 기호 레이어에서 이 ID를 참조해야 합니다. `iconOffset` 옵션을 사용하여 이미지의 올바른 지점과 일치하도록 기호를 오프셋합니다. 아이콘 오프셋은 픽셀 단위입니다. 기본적으로 오프셋은 이미지의 아래쪽 중심을 기준으로 하지만, `iconAnchor` 옵션을 사용하여 이 오프셋 값을 조정할 수 있습니다. 다음 예제에서는 `iconAnchor` 옵션을 `"center"`로 설정합니다. 압정 이미지의 지점에 맞추기 위해 아이콘 오프셋을 사용하여 이미지를 오른쪽으로 5픽셀, 위쪽으로 15픽셀씩 이동합니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(new Float[]{5f, -15f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin)

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(arrayOf(0f, -1.5f))))
}
```

::: zone-end

![Azure Maps 사용자 지정 표식](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>폴리라인 추가

폴리라인은 맵에 선 또는 경로를 나타내는 데 사용됩니다. 다음 예제에서는 맵에 파선 폴리라인을 만드는 방법을 보여줍니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps를 사용하면 `PolylineOptions` 클래스를 사용하여 폴리라인을 렌더링합니다. `addPolyline` 메서드를 사용하여 폴리라인을 맵에 추가합니다. `color` 옵션을 사용하여 스트로크 색을 설정합니다. `width` 옵션을 사용하여 스트로크 너비를 설정합니다. `pattern` 옵션을 사용하여 스트로크 파선 배열을 추가합니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    //Create the options for the polyline.
    val lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)))

    //Add the polyline to the map.
    val polyline = mapView.addPolyline(lineOptions)
}
```

::: zone-end

![Google Maps 폴리라인](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>이후: Azure Maps

Azure Maps에서 폴리라인을 `LineString` 또는 `MultiLineString` 개체라고 합니다. 이러한 개체를 데이터 원본에 추가하고, 선 계층을 사용하여 렌더링합니다. `strokeWidth` 옵션을 사용하여 스트로크 너비를 설정합니다. `strokeDashArray` 옵션을 사용하여 스트로크 파선 배열을 추가합니다.

Azure Maps 웹 SDK의 스트로크 너비와 파선 배열 "픽셀" 단위는 Google Maps 서비스와 동일합니다. 둘 모두는 동일한 값을 적용하여 동일한 결과를 생성합니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of points.
    val points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46))

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)))

    //Create a line layer and add it to the map.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})))
}
```

::: zone-end

![Azure Maps 폴리라인](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>다각형 추가

다각형은 맵의 영역을 나타내는 데 사용됩니다. 다음 예제에서는 다각형을 만드는 방법을 보여 줍니다. 이 다각형은 맵의 중심 좌표를 기준으로 삼각형을 만듭니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps를 사용하면 `PolygonOptions` 클래스를 사용하여 다각형을 렌더링합니다. `addPolygon` 메서드를 사용하여 다각형을 맵에 추가합니다. `fillColor` 및 `strokeColor` 옵션을 각각 사용하여 채우기 및 스트로크 색을 설정합니다. `strokeWidth` 옵션을 사용하여 스트로크 너비를 설정합니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    val polygonOptions = PolygonOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .add(new LatLng(46, -123))  //Close the polygon.
        .fillColor(Color.argb(128, 0, 128, 0))
        .strokeColor(Color.RED)
        .strokeWidth(5f)

    //valAdd the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions)
}
```

::: zone-end

![Google Maps 다각형](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>이후: Azure Maps

Azure Maps에서는 `Polygon` 및 `MultiPolygon` 개체를 데이터 원본에 추가하고, 레이어를 사용하여 맵에 렌더링합니다. 다각형 계층에서 다각형 영역을 렌더링합니다. 선 계층을 사용하여 다각형의 윤곽선을 렌더링합니다. `strokeColor` 및 `strokeWidth` 옵션을 사용하여 스트로크 색과 너비를 설정합니다.

Azure Maps 웹 SDK의 스트로크 너비 및 파선 배열 "픽셀" 단위는 Google Maps의 해당 단위에 맞춥니다. 둘 모두는 동일한 값을 적용하고 동일한 결과를 생성합니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of point arrays to create polygon rings.
    val rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)))

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)))

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)))

    //Add a line layer for rendering the polygon outline.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)))
}
```

::: zone-end

![Azure Maps 다각형](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>타일 레이어 오버레이

 타일 계층을 사용하여 맵 바둑판식 배열 시스템에 맞추는 더 작은 바둑판식 이미지로 분할된 레이어 이미지를 오버레이합니다. 이 방법은 레이어 이미지 또는 큰 데이터 세트를 오버레이하는 일반적인 방법입니다. Google Maps에서는 타일 레이어를 이미지 오버레이라고 합니다.

다음 예제에서는 아이오와 주립 대학교의 Iowa Environmental Mesonet에서 받은 날씨 레이더 타일 레이어를 오버레이합니다. 타일의 크기는 256픽셀입니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps를 사용하면 타일 계층을 맵 위에 오버레이할 수 있습니다. `TileOverlayOptions` 클래스를 사용합니다. `addTileLayer` 메서드를 사용하여 타일 계층을 맵에 추가합니다. 타일을 반투명하게 만들기 위해 `transparency` 옵션이 0.2 또는 20% 투명으로 설정됩니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap
    //Create the options for the tile layer.
    val tileLayer: TileOverlayOptions = TileOverlayOptions()
        .tileProvider(object : UrlTileProvider(256, 256) {
            fun getTileUrl(x: Int, y: Int, zoom: Int): URL? {
                return try { //Define the URL pattern for the tile images.
                    URL(
                        String.format(
                            "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png",
                            zoom,
                            x,
                            y
                        )
                    )
                } catch (e: MalformedURLException) {
                    throw AssertionError(e)
                }
            }
        }).transparency(0.2f)
    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer)
}
```

::: zone-end

![Google Maps 타일 레이어](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps&quot;></a>이후: Azure Maps

타일 레이어를 다른 레이어와 비슷한 방식으로 맵에 추가할 수 있습니다. x, y 및 확대/축소 자리 표시자가 있는 형식이 지정된 URL `{x}`, `{y}`, `{z}`는 각각 타일에 액세스할 위치를 레이어에 알리는 데 사용됩니다. 또한 Azure Maps의 타일 계층은 `{quadkey}`, `{bbox-epsg-3857}` 및 `{subdomain}` 자리 표시자를 지원합니다. 타일 레이어를 반투명하게 만들기 위해 불투명도 값 0.8이 사용됩니다. 불투명도와 투명도는 비슷하지만 반전된 값을 사용합니다. 두 옵션 사이를 변환하려면 숫자 1에서 해당 값을 뺍니다.

> [!TIP]
> Azure Maps에서는 기본 지도 계층을 포함하여 다른 레이어 아래에 레이어를 렌더링하는 것이 편리합니다. 또한 쉽게 읽을 수 있도록 맵 레이블 아래에 타일 레이어를 렌더링하는 것이 좋은 경우가 많습니다. `map.layers.add` 메서드는 아래에 새 레이어를 삽입할 레이어의 ID인 두 번째 매개 변수를 사용합니다. 타일 계층을 맵 레이블 아래에 삽입하려면 `map.layers.add(myTileLayer, &quot;labels");` 코드를 사용할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Add a tile layer to the map, below the map labels.
    map.layers.add(TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels")
}
```

::: zone-end

![Azure Maps 타일 레이어](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>트래픽 표시

Azure Maps와 Google Maps에는 모두 트래픽 데이터를 오버레이하는 옵션이 있습니다.

### <a name="before-google-maps"></a>이전: Google Maps

Google Maps를 사용하면 true를 맵의 `setTrafficEnabled` 메서드에 전달하여 트래픽 흐름 데이터를 맵 위쪽에 오버레이할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.setTrafficEnabled(true)
}
```

::: zone-end

![Google Maps 트래픽](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>이후: Azure Maps

Azure Maps는 트래픽을 표시하는 여러 가지 옵션을 제공합니다. 도로 폐쇄나 사고 같은 트래픽 인시던트를 맵에 아이콘으로 표시할 수 있습니다. 트래픽 흐름과 색으로 구분된 도로를 맵에 오버레이할 수 있습니다. 게시된 속도 제한, 일반적인 예상 지연 시간 또는 절대 지연 시간을 기준으로 표시되도록 색을 수정할 수 있습니다. Azure Maps의 인시던트 데이터는 1분마다 업데이트되고 흐름 데이터는 2분마다 업데이트됩니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE))
}
```

::: zone-end

![Azure Maps 트래픽](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

Azure Maps Android SDK에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Maps Android SDK 시작](how-to-use-android-map-control-library.md)

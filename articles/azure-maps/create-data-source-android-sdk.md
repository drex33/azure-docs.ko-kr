---
title: Android maps에 대 한 데이터 원본 만들기 | Microsoft Azure 지도
description: 지도에 대한 데이터 원본을 만드는 방법을 알아봅니다. Azure Maps Android SDK에서 사용하는 데이터 원본(GeoJSON 원본 및 벡터 타일)에 대해 알아보세요.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 9ea96613425ec3802080277da9ac674af4e87c52
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669092"
---
# <a name="create-a-data-source-android-sdk"></a>데이터 원본 만들기 (Android SDK)

Azure Maps Android SDK 데이터 원본에 데이터를 저장합니다. 데이터 원본을 사용하면 쿼리 및 렌더링을 위해 데이터 작업이 최적화됩니다. 현재 다음 두 가지 유형의 데이터 원본이 지원됩니다.

- **GeoJSON 원본**: GeoJSON 형식의 원시 위치 데이터를 로컬로 관리합니다. 중소 규모의 데이터 세트에 적합합니다(수십만 개 이상의 형태).
- **벡터 타일 원본**: 지도 바둑판식 배열 시스템을 기반으로 현재 지도 보기의 벡터 타일 형식의 데이터를 로드합니다. 대규모 데이터 세트에 이상적입니다(수백만 또는 수십억 개의 형태).

## <a name="geojson-data-source"></a>GeoJSON 데이터 원본

Azure Maps은 기본 데이터 모델 중 하나로 GeoJSON를 사용합니다. GeoJSON는 지리 공간적 데이터를 JSON 형식으로 나타내는 데 사용할 수 있는 지리 공간적 표준 방식입니다. Azure Maps Android SDK에서 GeoJSON 클래스를 사용하여 GeoJSON 데이터를 쉽게 만들고 직렬화할 수 있습니다. `DataSource` 클래스에 GeoJSON 데이터를 로드 및 저장하고 레이어를 사용하여 렌더링합니다. 다음 코드는 Azure Maps에서 GeoJSON 개체를 만드는 방법을 보여줍니다.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

> [!TIP]
> GeoJSON 데이터는 세 가지 메서드 중 하나를 사용하여 `DataSource` 인스턴스에 추가할 수 있습니다. `add`, `importDataFromUrl` 및 `setShapes`. `setShapes` 메서드는 데이터 소스의 모든 데이터를 덮어쓰는 효율적인 방법을 제공합니다. `clear` 와 `add` 메서드를 호출하여 데이터 소스의 모든 데이터를 바꾸면 지도에 대해 두 번의 렌더링 호출이 수행됩니다. `setShape` 메서드는 지도에 대한 단일 렌더링 호출로 데이터를 지우고 데이터 소스에 추가합니다.

::: zone-end

또는 아래와 같이 속성을 먼저 JsonObject에 로드한 다음 생성할 때 기능에 전달할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

GeoJSON 기능을 만든 후에는 지도의 속성을 통해 데이터 원본을 맵에 추가할 수 `sources` 있습니다. 다음 코드는 `DataSource`을 만들고 지도에 추가하고 데이터 소스에 지형지물을 추가하는 방법을 보여줍니다.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

다음 코드는 GeoJSON 기능, FeatureCollection 및 지오메트리를 생성하는 여러 방법을 보여줍니다.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>GeoJSON 직렬화 및 역직렬화

지형지물 컬렉션, 지형지물 및 기하학 클래스에는 모두 직렬화에 도움이 되는 `fromJson()` 및 `toJson()` 정적 메서드가 있습니다. `fromJson()` 메서드를 통해 전달된 형식이 지정된 유효한 JSON 문자열은 기하학 객체를 생성합니다. 또한 이 `fromJson()` 메서드는 Gson 또는 기타 직렬화/역직렬화 전략을 사용할 수 있음을 의미합니다. 다음 코드는 문자열화된 GeoJSON 기능을 가져와 Feature 클래스로 역직렬화한 다음 다시 GeoJSON 문자열로 직렬화하는 방법을 보여줍니다.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>웹 또는 자산 폴더에서 GeoJSON 데이터 가져오기

대부분의 GeoJSON 파일에는 FeatureCollection이 포함되어 있습니다. GeoJSON 파일을 문자열로 읽고 `FeatureCollection.fromJson` 메서드를 사용하여 역직렬화했습니다.

`DataSource` 클래스에는 웹 또는 자산 폴더의 파일에 대한 URL을 사용하여 GeoJSON 파일에서 로드할 수 있는 `importDataFromUrl`라는 내장 메서드가 있습니다. 이 메소드는 데이터 소스가 지도에 추가되기 전에 **반드시** 호출되어야 합니다.

zone_pivot_groups: azure-maps-android

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data");

//Examples:
//source.importDataFromUrl("asset://sample_file.json");
//source.importDataFromUrl("https://example.com/sample_file.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
var source = new DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data")

//Examples:
//source.importDataFromUrl("asset://sample_file.json")
//source.importDataFromUrl("https://example.com/sample_file.json")

//Add data source to the map.
map.sources.add(source)
```

::: zone-end

`importDataFromUrl` 메서드는 GeoJSON 피드를 데이터 소스에 쉽게 로드할 수 있는 방법을 제공하지만 데이터가 로드되는 방식과 로드된 후 발생하는 일에 대한 제한된 제어를 제공합니다. 다음 코드는 웹 또는 자산 폴더에서 데이터를 가져오고 콜백 함수를 통해 UI 스레드로 반환하기 위한 재사용 가능한 클래스입니다. 그런 다음 콜백에서 추가 포스트 로드 로직을 추가하여 데이터를 처리하고, 지도에 추가하고, 경계 상자를 계산하고, 지도 카메라를 업데이트할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

아래 코드에서는이 유틸리티를 사용 하 여 GeoJSON 데이터를 문자열로 가져오고 콜백을 통해 UI 스레드에 반환 하는 방법을 보여 줍니다. 콜백에서 문자열 데이터는 GeoJSON 기능 컬렉션으로 직렬화되고 데이터 소스에 추가될 수 있습니다. 필요에 따라 지도 카메라를 업데이트 하 여 데이터에 초점을 맞출 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

### <a name="update-a-feature"></a>기능 상태 업데이트

`DataSource` 클래스를 사용하면 기능을 쉽게 추가 및 제거할 수 있습니다. 기능의 기하 도형 또는 속성을 업데이트 하려면 데이터 원본의 기능을 바꾸어야 합니다. 다음 두 가지 방법을 사용하여 기능을 업데이트할 수 있습니다.

1. 원하는 업데이트로 새 기능을 만들고 `setShapes` 메서드를 사용하여 데이터 소스의 모든 기능을 바꿉니다. 이 방법은 데이터 소스의 모든 기능을 업데이트하려는 경우에 적합합니다.

::: zone pivot="programming-language-java-android"

``` java
DataSource source;

private void onReady(AzureMap map) {
    //Create a data source and add it to the map.
    source = new DataSource();
    map.sources.add(source);

    //Create a feature and add it to the data source.
    Feature myFeature = Feature.fromGeometry(Point.fromLngLat(0,0));
    myFeature.addStringProperty("Name", "Original value");

    source.add(myFeature);
}

private void updateFeature(){
    //Create a new replacement feature with an updated geometry and property value.
    Feature myNewFeature = Feature.fromGeometry(Point.fromLngLat(-10, 10));
    myNewFeature.addStringProperty("Name", "New value");

    //Replace all features to the data source with the new one.
    source.setShapes(myNewFeature);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
var source: DataSource? = null

private fun onReady(map: AzureMap) {
    //Create a data source and add it to the map.
    source = DataSource()
    map.sources.add(source)

    //Create a feature and add it to the data source.
    val myFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
    myFeature.addStringProperty("Name", "Original value")
    source!!.add(myFeature)
}

private fun updateFeature() {
    //Create a new replacement feature with an updated geometry and property value.
    val myNewFeature = Feature.fromGeometry(Point.fromLngLat(-10.0, 10.0))
    myNewFeature.addStringProperty("Name", "New value")

    //Replace all features to the data source with the new one.
    source!!.setShapes(myNewFeature)
}
```

::: zone-end

2. 변수의 기능 인스턴스를 추적하고 데이터 소스 `remove` 메서드에 전달하여 제거합니다. 원하는 업데이트로 새 기능을 만들고 변수 참조를 업데이트한 다음 `add` 메서드를 사용하여 데이터 소스에 추가합니다.

::: zone pivot="programming-language-java-android"

``` java
DataSource source;
Feature myFeature;

private void onReady(AzureMap map) {
    //Create a data source and add it to the map.
    source = new DataSource();
    map.sources.add(source);

    //Create a feature and add it to the data source.
    myFeature = Feature.fromGeometry(Point.fromLngLat(0,0));
    myFeature.addStringProperty("Name", "Original value");

    source.add(myFeature);
}

private void updateFeature(){
    //Remove the feature instance from the data source.
    source.remove(myFeature);

    //Get properties from original feature.
    JsonObject props = myFeature.properties();

    //Update a property.
    props.addProperty("Name", "New value");

    //Create a new replacement feature with an updated geometry.
    myFeature = Feature.fromGeometry(Point.fromLngLat(-10, 10), props);

    //Re-add the feature to the data source.
    source.add(myFeature);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
var source: DataSource? = null
var myFeature: Feature? = null

private fun onReady(map: AzureMap) {
    //Create a data source and add it to the map.
    source = DataSource()
    map.sources.add(source)

    //Create a feature and add it to the data source.
    myFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
    myFeature.addStringProperty("Name", "Original value")
    source!!.add(myFeature)
}

private fun updateFeature() {
    //Remove the feature instance from the data source.
    source!!.remove(myFeature)

    //Get properties from original feature.
    val props = myFeature!!.properties()

    //Update a property.
    props!!.addProperty("Name", "New value")

    //Create a new replacement feature with an updated geometry.
    myFeature = Feature.fromGeometry(Point.fromLngLat(-10.0, 10.0), props)

    //Re-add the feature to the data source.
    source!!.add(myFeature)
}
```

::: zone-end

> [!TIP]
> 정기적으로 업데이트될 일부 데이터와 거의 변경되지 않을 다른 데이터가 있는 경우 이러한 데이터를 별도의 데이터 원본 인스턴스로 분할하는 것이 가장 좋습니다. 데이터 소스에서 업데이트가 발생하면 맵이 데이터 소스의 모든 기능을 다시 그리도록 합니다. 이 데이터를 분할 하면 해당 데이터 원본에서 업데이트가 발생 하는 동안 다른 데이터 원본의 기능을 다시 그릴 필요가 없는 경우 정기적으로 업데이트 되는 기능만 다시 그려집니다. 따라서 성능 향상에 도움이 됩니다.

## <a name="vector-tile-source"></a>벡터 타일 원본

벡터 타일 원본은 벡터 타일 레이어에 액세스하는 방법을 설명합니다. `VectorTileSource` 클래스를 사용하여 벡터 타일 소스를 인스턴스화합니다. 벡터 타일 레이어는 타일 레이어와 비슷하지만 동일하지는 않습니다. 타일 레이어는 래스터 이미지입니다. 벡터 타일 레이어는 **PBF** 형식의 압축 파일입니다. 이 압축 파일은 벡터 지도 데이터 및 하나 이상의 레이어를 포함합니다. 각 레이어의 스타일에 따라 클라이언트에서 파일을 렌더링하고 스타일을 지정할 수 있습니다. 벡터 타일의 데이터에는 점, 선 및 다각형 형식의 지리적 피처가 포함되어 있습니다. 래스터 타일 레이어 대신 벡터 타일 레이어를 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 벡터 타일의 파일 크기는 일반적으로 해당하는 래스터 타일보다 훨씬 작습니다. 따라서 더 적은 대역폭이 사용됩니다. 이것은 낮은 대기 시간, 더 빠른 지도 및 더 나은 사용자 환경을 의미합니다.
- 벡터 타일이 클라이언트에서 렌더링된 후에는 표시되는 디바이스의 해상도에 맞게 조정됩니다. 따라서 렌더링된 지도는 명확한 레이블을 포함하여 잘 정의된 것으로 나타납니다.
- 클라이언트에 새 스타일을 적용할 수 있으므로 벡터 지도에서 데이터의 스타일을 변경하려면 데이터를 다시 다운로드할 필요가 없습니다. 반면, 래스터 타일 레이어의 스타일을 변경하려면 일반적으로 서버에서 타일을 로드한 다음, 새 스타일을 적용해야 합니다.
- 데이터가 벡터 형식으로 전달되므로 데이터를 준비하는 데 필요한 서버 쪽 처리가 줄어듭니다. 따라서 최신 데이터를 더 빨리 사용할 수 있습니다.

Azure Maps는 [Mapbox Vector 타일 사양](https://github.com/mapbox/vector-tile-spec)(개방형 표준)을 준수합니다. Azure Maps는 플랫폼의 일부로 다음 벡터 타일 서비스를 제공합니다.

- 도로 타일 [설명서](/rest/api/maps/render-v2/get-map-tile) | [데이터 형식 세부 정보](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 트래픽 인시던트 [설명서](/rest/api/maps/traffic/gettrafficincidenttile) | [데이터 형식 세부 정보](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 트래픽 흐름 [설명서](/rest/api/maps/traffic/gettrafficflowtile) | [데이터 형식 세부 정보](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps Creator를 사용하면 [Render V2-Get Map Tile API](/rest/api/maps/render-v2/get-map-tile)를 통해 사용자 지정 벡터 타일을 만들고 액세스할 수도 있습니다.

> [!TIP]
> Web SDK를 사용하여 Azure Maps 렌더링 서비스에서 벡터 또는 래스터 이미지 타일을 사용하는 경우 `atlas.microsoft.com`을 자리 표시자 `azmapsdomain.invalid`르로 바꿀 수 있습니다. 이 자리 표시자는 지도에서 사용하는 동일한 도메인으로 대체되고 동일한 인증 세부 정보도 자동으로 추가됩니다. 이를 통해 Azure Active Directory 인증을 사용할 때 렌더링 서비스에 대한 인증이 크게 간소화됩니다.

지도에 벡터 타일 원본의 데이터를 표시하려면 원본을 데이터 렌더링 레이어 중 하나에 연결합니다. 벡터 원본을 사용하는 모든 레이어는 옵션에서 `sourceLayer` 값을 지정해야 합니다. 다음 코드는 Azure Maps 트래픽 흐름 벡터 타일 서비스를 벡터 타일 원본으로 로드한 다음, 선 레이어를 사용하여 지도에 표시합니다. 이 벡터 타일 원본에는 "트래픽 흐름"이라는 원본 레이어에 단일 데이터 세트가 있습니다. 이 데이터 세트의 선 데이터에는 이 코드에서 선 색을 선택하고 스케일링하는 데 사용되는 `traffic_level`이라는 속성이 있습니다.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![트래픽 흐름 수준을 표시 하는 색으로 구분 된 이동 선이 있는 지도](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>레이어에 데이터 원본 연결

데이터는 렌더링 레이어를 사용하여 지도에 렌더링됩니다. 단일 데이터 원본이 하나 이상의 렌더링 레이어에서 참조될 수 있습니다. 다음 렌더링 레이어에는 데이터 원본이 필요합니다.

- [거품형 레이어](map-add-bubble-layer-android.md) - 지도에서 점 데이터를 스케일링된 원으로 렌더링합니다.
- [기호 레이어](how-to-add-symbol-to-android-map.md) - 점 데이터를 아이콘 또는 텍스트로 렌더링합니다.
- [열 지도 레이어](map-add-heat-map-layer-android.md) - 점 데이터를 고밀도 열 지도로 렌더링합니다.
- [선 레이어](android-map-add-line-layer.md) - 선을 렌더링하거나 다각형의 윤곽선을 렌더링합니다.
- [다각형 레이어](how-to-add-shapes-to-android-map.md) - 단색 또는 이미지 패턴으로 다각형 영역을 채웁니다.

다음 코드에서는 데이터 원본을 만들고 지도에 추가한 다음, 거품형 레이어에 연결하는 방법을 보여 줍니다. 그런 다음, 원격 위치에서 데이터 원본으로 GeoJSON 점 데이터를 가져옵니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data");

//Add data source to the map.
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data")

//Add data source to the map.
map.sources.add(source)
```

::: zone-end

이러한 데이터 원본에 연결되지 않지만 렌더링을 위해 데이터를 직접 로드하는 추가 렌더링 레이어가 있습니다.

- [타일 레이어](how-to-add-tile-layer-android-map.md) - 지도 위에 래스터 타일 레이어를 겹쳐 놓습니다.

## <a name="one-data-source-with-multiple-layers"></a>여러 레이어가 있는 하나의 데이터 원본

여러 레이어를 단일 데이터 원본에 연결할 수 있습니다. 이 옵션이 유용할 수 있는 여러 가지 시나리오가 있습니다. 예를 들어, 사용자가 다각형을 그리는 시나리오를 생각해 보겠습니다. 사용자가 지도에 점이 추가할 때 다각형 영역을 렌더링하고 채워야 합니다. 스타일이 지정된 선을 추가하여 다각형의 윤곽을 만들면 사용자가 그릴 때 다각형의 가장자리를 더 쉽게 볼 수 있습니다. 다각형의 개별 위치를 편리하게 편집하기 위해 각 위치 위에 핀이나 표식 같은 핸들을 추가할 수 있습니다.

![단일 데이터 원본에서 데이터를 렌더링하는 여러 레이어를 보여 주는 지도](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

대부분의 매핑 플랫폼에서 다각형 개체, 선 개체 및 다각형의 각 위치에 대한 핀이 필요합니다. 다각형이 수정될 때 선과 핀을 수동으로 업데이트해야 하며, 이 작업은 빠르게 복잡해질 수 있습니다.

Azure Maps를 사용하는 경우 아래 코드와 같이 데이터 원본에 단일 다각형이 필요합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> `map.layers.add` 메서드를 사용하여 지도에 레이어를 추가할 때 기존 레이어의 ID 또는 인스턴스를 두 번째 매개변수로 전달할 수 있습니다. 그러면 지도에 기존 레이어 아래에 새 레이어를 삽입하도록 지시됩니다. 레이어 ID를 전달하는 것 외에도 이 방법은 다음 값을 지원합니다.
>
> - `"labels"` - 지도 레이블 레이어 아래에 새 레이어를 삽입합니다.
> - `"transit"` - 지도 도로 및 교통 레이어 아래에 새 레이어를 삽입합니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [클러스터 지점 데이터](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [웹 SDK 코드 샘플](/samples/browse/?products=azure-maps)

---
title: '자습서: Microsoft Azure Maps Creator를 사용하여 실내 맵 만들기'
description: Microsoft Azure Maps Creator를 사용하여 실내 맵을 만드는 방법에 대한 자습서입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 5/19/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: db5bebce41e8fd13cf0796e02fc0bfe91d63a6c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060019"
---
# <a name="tutorial-use-creator-to-create-indoor-maps"></a>자습서: Creator를 사용하여 실내 맵 만들기

이 자습서에서는 실내 맵을 만드는 방법에 대해 설명합니다. 이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 실내 맵 그리기 패키지를 업로드합니다.
> * 그리기 패키지를 맵 데이터로 변환합니다.
> * 맵 데이터에서 데이터 세트를 만듭니다.
> * 데이터 세트의 데이터에서 타일 세트를 만듭니다.
> * Azure Maps WFS(Web Feature Service) API를 쿼리하여 맵 기능에 대해 알아봅니다.
> * 맵 기능 및 데이터 세트의 데이터를 사용하여 기능 상태 세트를 만듭니다.
> * 기능 상태 세트를 업데이트합니다.

## <a name="prerequisites"></a>필수 조건

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Creator 리소스를 만듭니다](how-to-manage-creator.md).
4. [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)을 다운로드합니다.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만, 다른 API 개발 환경을 사용할 수 있습니다.

>[!IMPORTANT]
> 이 자습서에서는 `us.atlas.microsoft.com` 지리적 URL을 사용합니다. Creator 서비스가 미국에서 만들어지지 않은 경우 다른 지리적 URL을 사용해야 합니다.  자세한 내용은 [Creator 서비스에 대한 액세스](how-to-manage-creator.md#access-to-creator-services)를 참조하세요. 지리적 위치에 대한 지역의 매핑을 보려면 [Creator 서비스 지리적 범위](creator-geographic-scope.md)를 참조하세요.

## <a name="upload-a-drawing-package"></a>그리기 패키지 업로드

[데이터 업로드 API](/rest/api/maps/data-v2/upload-preview)를 사용하여 그리기 패키지를 Azure Maps 리소스에 업로드합니다.

데이터 업로드 API는 [Creator 장기 실행 작업 API V2](creator-long-running-operation-v2.md)에 정의된 패턴을 구현하는 장기 실행 트랜잭션입니다.

그리기 패키지를 업로드하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *POST 데이터 업로드*)을 입력합니다.

4. **POST** HTTP 메서드를 선택합니다.

5. [데이터 업로드 API](/rest/api/maps/data-v2/upload-preview)에 대한 다음 URL을 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿈).

    ```http
    https://us.atlas.microsoft.com/mapData?api-version=2.0&dataFormat=dwgzippackage&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **Headers** 탭을 선택합니다.

7. **KEY** 필드에서 `Content-Type`을 선택합니다. 

8. **VALUE** 필드에서 `application/octet-stream`을 선택합니다.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-header.png"alt-text="데이터 업로드에 대한 헤더 탭 정보":::

9. **본문** 탭을 선택합니다.

10. 드롭다운 목록에서 **이진** 을 선택합니다.

11. **파일 선택** 을 선택한 다음, 그리기 패키지를 선택합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-body.png" alt-text="그리기 패키지 선택":::

12. **보내기** 를 선택합니다.

13. 응답 창에서 **헤더** 탭을 선택합니다.

14. **Operation-Location** 키의 값인 `status URL`을 복사합니다. `status URL`을 사용하여 그리기 패키지 업로드 상태를 확인합니다.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-response-header.png" alt-text="위치 키의 상태 URL 복사":::

### <a name="check-the-drawing-package-upload-status"></a>그리기 패키지 업로드 상태 확인

그리기 패키지의 상태를 확인하고 해당 고유 ID(`udid`)를 검색하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 데이터 업로드 상태*)을 입력합니다.

4. **GET** HTTP 메서드를 선택합니다.

5. [그리기 패키지 업로드](#upload-a-drawing-package)에서 복사한 `status URL`을 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿈).

    ```http
    https://us.atlas.microsoft.com/mapData/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다.

8. **Resource-Location** 키의 값인 `resource location URL`을 복사합니다. `resource location URL`에는 그리기 패키지 리소스의 고유 식별자(`udid`)가 포함됩니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/resource-location-url.png" alt-text="리소스 위치 URL을 복사합니다.":::

### <a name="optional-retrieve-drawing-package-metadata"></a>(선택 사항) 그리기 패키지 메타데이터 검색

그리기 패키지 리소스에서 메타데이터를 검색할 수 있습니다. 메타데이터에는 리소스 위치 URL, 만든 날짜, 업데이트한 날짜, 크기 및 업로드 상태와 같은 정보가 포함됩니다.

콘텐츠 메타데이터를 검색하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 데이터 업로드 메타데이터*)을 입력합니다.

4. . **GET** HTTP 메서드를 선택합니다.

5. [그리기 패키지 업로드 상태 확인](#check-the-drawing-package-upload-status)에서 복사한 `resource Location URL`을 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿈).

    ```http
    https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **본문** 탭을 선택합니다. 메타데이터는 다음 JSON 조각과 같습니다.

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>그리기 패키지 변환

이제 그리기 패키지가 업로드되었으므로 업로드된 패키지에 대한 `udid`를 사용하여 패키지를 맵 데이터로 변환합니다. 변환 API는 [여기](creator-long-running-operation-v2.md)에 정의된 패턴을 구현하는 장기 실행 트랜잭션을 사용합니다.

그리기 패키지를 변환하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *POST 그리기 패키지 변환*)을 입력합니다.

4. **POST** HTTP 메서드를 선택합니다.

5. [변환 서비스](/rest/api/maps/v2/conversion/convert)에 대한 다음 URL을 입력합니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고, `udid`를 업로드된 패키지의 `udid`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/conversions?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&udid={udid}&inputType=DWG&outputOntology=facility-2.0
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다. 

8. **Operation-Location** 키의 값인 `status URL`을 복사합니다. `status URL`을 사용하여 변환 상태를 확인합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-convert-location-url.png" border="true" alt-text="그리기 패키지에 대한 위치 키 값 복사":::

### <a name="check-the-drawing-package-conversion-status"></a>그리기 패키지 변환 상태 확인

변환 작업이 완료되면`conversionId`가 반환됩니다. 그리기 패키지 변환 프로세스의 상태를 확인하여 `conversionId`에 액세스할 수 있습니다. 그런 다음, `conversionId`를 사용하여 변환된 데이터에 액세스할 수 있습니다.

변환 프로세스의 상태를 확인하고 `conversionId`를 검색하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 변환 상태*)을 입력합니다.

4. **GET** HTTP 메서드를 선택합니다.

5. [그리기 패키지 변환](#convert-a-drawing-package)에서 복사한 `status URL`을 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿈).

    ```http
    https://us.atlas.microsoft.com/conversions/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다. 

8. **Resource-Location** 키의 값인 `resource location URL`을 복사합니다. `resource location URL`에는 변환된 맵 데이터에 액세스하기 위해 다른 API에서 사용할 수 있는 고유 식별자(`conversionId`)가 포함되어 있습니다.

      :::image type="content" source="./media/tutorial-creator-indoor-maps/data-conversion-id.png" alt-text="변환 ID 복사":::

그리기 패키지 샘플은 오류 또는 경고 없이 변환되어야 합니다. 그러나 사용자 고유의 그리기 패키지에서 오류 또는 경고를 받는 경우 JSON 응답에는 [그리기 오류 시각화 도우미](drawing-error-visualizer.md)에 대한 링크가 포함됩니다. 그리기 오류 시각화 도우미를 사용하여 오류 및 경고에 대한 세부 정보를 검사할 수 있습니다. 변환 오류 및 경고를 해결하기 위한 추천 사항을 받으려면 [그리기 변환 오류 및 경고](drawing-conversion-error-codes.md)를 참조하세요.

다음 JSON 조각에서는 샘플 변환 경고를 표시합니다.

```json
{
    "operationId": "<operationId>",
    "created": "2021-05-19T18:24:28.7922905+00:00",
    "status": "Succeeded",
     "warning": {
        "code": "dwgConversionProblem",
        "details": [
            {
                "code": "warning",
                "details": [
                    {
                        "code": "manifestWarning",
                        "message": "Ignoring unexpected JSON property: unitProperties[0].nonWheelchairAccessible with value False"
                    }
                ]
            }
        ]
    },
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>데이터 세트 만들기

데이터 세트는 건물, 수준 및 방과 같은 맵 기능의 컬렉션입니다. 데이터 세트를 만들려면 [데이터 세트 만들기 API](/rest/api/maps/v2/dataset/create)를 사용합니다. 데이터 세트 만들기 API는 변환된 그리기 패키지에 대한 `conversionId`를 사용하고, 만든 데이터 세트의 `datasetId`를 반환합니다.

데이터 세트를 만들려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *POST 데이터 세트 만들기*)을 입력합니다.

4. **POST** HTTP 메서드를 선택합니다.

5. 다음 URL을 [데이터 세트 API](/rest/api/maps/v2/dataset)에 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고 `{conversionId`}를 [그리기 패키지 변환 상태 확인](#check-the-drawing-package-conversion-status)에서 얻은 `conversionId`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/datasets?api-version=2.0&conversionId={conversionId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다. 

8. **Operation-Location** 키의 값인 `status URL`을 복사합니다. `status URL`을 사용하여 데이터 세트 상태를 확인합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-dataset-location-url.png" border="true" alt-text="데이터 세트에 대한 위치 키 값 복사":::

### <a name="check-the-dataset-creation-status"></a>데이터 세트 만들기 상태 확인

데이터 세트 만들기 프로세스의 상태를 확인하고 `datasetId`를 검색하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 데이터 세트 상태*)을 입력합니다.

4. **GET** HTTP 메서드를 선택합니다.

5. [데이터 세트 만들기](#create-a-dataset)에서 복사한 `status URL`을 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿈).

    ```http
    https://us.atlas.microsoft.com/datasets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다. **Resource-Location** 키의 값은 `resource location URL`입니다. `resource location URL`에는 데이터 세트의 고유 식별자(`datasetId`)가 포함됩니다. 

8. 이 자습서의 다음 섹션에서 사용하므로 `datasetId`를 복사합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/dataset-id.png" alt-text="데이터 세트 ID 복사":::

## <a name="create-a-tileset"></a>타일 세트 만들기

타일 세트는 맵에서 렌더링하는 벡터 타일 세트입니다. 타일 세트는 기존 데이터 세트에서 만들어집니다. 그러나 타일 세트는 원본 데이터 세트와 독립적입니다. 데이터 세트를 삭제하더라도 타일 세트는 계속 존재합니다.

타일 세트를 만들려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *POST 타일 세트 만들기*)을 입력합니다.

4. **POST** HTTP 메서드를 선택합니다.

5. 다음 URL을 [타일 세트 API](/rest/api/maps/v2/tileset)에 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고 `{datasetId`}를 [데이터 세트 만들기 상태 확인](#check-the-dataset-creation-status)에서 얻은 `datasetId`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/tilesets?api-version=2.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다. 

8. **Operation-Location** 키의 값인 `status URL`을 복사합니다. `status URL`을 사용하여 타일 세트 상태를 확인합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-tileset-location-url.png" border="true" alt-text="타일 세트 상태 URL 값 복사":::

### <a name="check-the-tileset-creation-status"></a>타일 세트 만들기 상태 확인

데이터 세트 만들기 프로세스의 상태를 확인하고 `tilesetId`를 검색하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 타일 세트 상태*)을 입력합니다.

4. **GET** HTTP 메서드를 선택합니다.

5. [타일 세트 만들기](#create-a-tileset)에서 복사한 `status URL`을 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿈).

    ```http
    https://us.atlas.microsoft.com/tilesets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **보내기** 를 선택합니다.

7. 응답 창에서 **헤더** 탭을 선택합니다. **Resource-Location** 키의 값은 `resource location URL`입니다.  `resource location URL`에는 데이터 세트의 고유 식별자(`tilesetId`)가 포함됩니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/tileset-id.png" alt-text="타일 세트 ID 복사":::

## <a name="query-datasets-with-wfs-api"></a>WFS API를 사용하여 데이터 세트 쿼리

데이터 세트는 [WFS API](/rest/api/maps/v2/wfs)를 사용하여 쿼리할 수 있습니다. WFS API를 사용하여 모든 기능 컬렉션 또는 특정 컬렉션을 쿼리할 수 있습니다. 자습서의 이 섹션에서는 두 작업을 모두 수행합니다. 먼저 모든 컬렉션을 쿼리한 다음, `unit` 컬렉션에 대해 쿼리합니다.

### <a name="query-for-feature-collections"></a>기능 컬렉션 쿼리

데이터 세트의 모든 컬렉션을 쿼리하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 데이터 세트 컬렉션*)을 입력합니다.

4. **GET** HTTP 메서드를 선택합니다.

5. 다음 URL을 [WFS API](/rest/api/maps/v2/wfs)에 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고 `{datasetId`}를 [데이터 세트 만들기 상태 확인](#check-the-dataset-creation-status)에서 얻은 `datasetId`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. **보내기** 를 선택합니다.

7. 응답 본문은 GeoJSON 형식으로 반환되며, 데이터 세트의 모든 컬렉션을 포함합니다. 간단히 하기 위해 다음 예제에서는 `unit` 컬렉션만 보여 줍니다. 모든 컬렉션이 포함된 예제를 보려면 [WFS 컬렉션 설명 API](/rest/api/maps/v2/wfs/collection-description)를 참조하세요. 컬렉션에 대해 자세히 알아보려면 `link` 요소 내의 URL을 선택하면 됩니다.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

### <a name="query-for-unit-feature-collection"></a>단위 기능 컬렉션에 대한 쿼리

이 섹션에서는 `unit` 기능 컬렉션에 대해 [WFS API](/rest/api/maps/v2/wfs)를 쿼리합니다.

데이터 세트의 단위 컬렉션을 쿼리하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *GET 단위 컬렉션*)을 입력합니다.

4. **GET** HTTP 메서드를 선택합니다.

5. 다음 URL을 입력합니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고 `{datasetId`}를 [데이터 세트 만들기 상태 확인](#check-the-dataset-creation-status)에서 얻은 `datasetId`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. **보내기** 를 선택합니다.

7. 응답이 반환되면 `unit` 기능 중 하나에 대한 기능 `id`를 복사합니다. 다음 예제에서 기능 `id`는 "UNIT26"입니다. 이 자습서에서는 다음 섹션에서 "UNIT26"을 기능 `id`로 사용합니다.

    ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>기능 상태 세트 만들기

기능 상태 세트는 이를 지원하는 특정 기능에 대한 동적 속성과 값을 정의합니다. 이 섹션에서는 **occupancy** 속성에 대한 부울 값과 해당 스타일을 정의하는 상태 세트를 만듭니다.

상태 세트를 만들려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *POST 상태 세트 만들기*)을 입력합니다.

4. **POST** HTTP 메서드를 선택합니다.

5. 다음 URL을 [상태 세트 API](/rest/api/maps/v2/feature-state/create-stateset)에 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고 `{datasetId`}를 [데이터 세트 만들기 상태 확인](#check-the-dataset-creation-status)에서 얻은 `datasetId`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/featurestatesets?api-version=2.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **Headers** 탭을 선택합니다.

7. **KEY** 필드에서 `Content-Type`을 선택합니다. 

8. **VALUE** 필드에서 `application/json`을 선택합니다.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="상태 집합 만들기에 대한 머리글 탭 정보.":::

9. **본문** 탭을 선택합니다.

10. 드롭다운 목록에서 **원시** 및 **JSON** 을 선택합니다.

11. 다음 JSON 스타일을 복사한 다음, **본문** 창에 붙여넣습니다.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          }
       ]
    }
    ```

12. **보내기** 를 선택합니다.

13. 응답이 성공적으로 반환되면 응답 본문에서 `statesetId`를 복사합니다. 다음 섹션에서는 `statesetId`를 사용하여 기능 `id`가 "UNIT26"인 단위의 `occupancy` 속성 상태를 변경합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/response-stateset-id.png"alt-text="상태 세트 ID 응답":::

### <a name="update-a-feature-state"></a>기능 상태 업데이트

기능 `id`가 "UNIT26"인 단위의 `occupied` 상태를 업데이트하려면,

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름**(예: *PUT 상태 세트 설정*)을 입력합니다.

4. **PUT** HTTP 메서드를 선택합니다.

5. 다음 URL을 [기능 상태 세트 API](/rest/api/maps/v2/feature-state/create-stateset)에 입력합니다. 요청은 다음 URL과 같습니다(`{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고 `{statesetId`}를 [기능 상태 세트 만들기](#create-a-feature-stateset)에서 얻은 `statesetId`로 바꿈).

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **Headers** 탭을 선택합니다.

7. **KEY** 필드에서 `Content-Type`을 선택합니다. 

8. **VALUE** 필드에서 `application/json`을 선택합니다.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="상태 집합 만들기에 대한 머리글 탭 정보.":::

9. **본문** 탭을 선택합니다.

10. 드롭다운 목록에서 **원시** 및 **JSON** 을 선택합니다.

11. 다음 JSON 스타일을 복사한 다음, **본문** 창에 붙여넣습니다.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > 게시된 타임스탬프가 이전 요청의 타임스탬프 이후인 경우에만 업데이트가 저장됩니다.

12. **보내기** 를 선택합니다.

13. 업데이트가 완료되면 `200 OK` HTTP 상태 코드를 받습니다. 실내 맵에 대한 [동적 스타일 지정](indoor-map-dynamic-styling.md)을 구현한 경우 업데이트는 렌더링된 맵의 지정된 타임스탬프에 표시됩니다.

[기능 가져오기 상태 세트 API](/rest/api/maps/v2/feature-state/get-states)를 사용하여 해당 `id` 기능을 사용하는 기능의 상태를 검색할 수 있습니다. 또한 [기능 상태 삭제 상태 API](/rest/api/maps/v2/feature-state/delete-stateset)를 사용하여 상태 세트 및 해당 리소스를 삭제할 수 있습니다.

이 문서에서 설명하는 다양한 Azure Maps Creator 서비스에 대한 자세한 내용은 [실내 맵용 Creator](creator-indoor-maps.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

정리가 필요한 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

실내 맵 모듈을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Indoor Maps 모듈 사용](how-to-use-indoor-module.md)

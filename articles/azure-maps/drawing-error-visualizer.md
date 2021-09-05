---
title: Azure Maps 그리기 오류 시각화 도우미 사용
description: 이 문서에서는 Creator Conversion API에서 반환되는 경고 및 오류를 시각화하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: c541a35bf2ef79fd058a58713afd927413ec8fcf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536222"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator"></a>Creator와 함께 Azure Maps 그리기 오류 시각화 도우미 사용


그리기 오류 시각화 도우미는 변환 프로세스 중에 감지된 [그리기 패키지 경고 및 오류](drawing-conversion-error-codes.md)를 표시하는 독립 실행형 웹 애플리케이션입니다. 오류 시각화 도우미 웹 애플리케이션은 인터넷에 연결하지 않고 사용할 수 있는 정적 페이지로 구성됩니다.  오류 시각화 도우미를 사용하여 [그리기 패키지 요구 사항](drawing-requirements.md)에 따라 오류 및 경고를 해결할 수 있습니다. [Azure Maps 변환 API](/rest/api/maps/v2/conversion)는 오류가 감지된 경우에만 오류 시각화 도우미에 대한 링크가 있는 응답을 반환합니다.

## <a name="prerequisites"></a>사전 요구 사항

그리기 오류 시각화 도우미를 다운로드하려면 먼저 다음을 수행해야 합니다.

1. [Azure Maps 계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Creator 리소스 만들기](how-to-manage-creator.md)

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="download"></a>다운로드

1. Azure Maps Creator 서비스에 그리기 패키지를 업로드하여 업로드된 패키지에 대한 `udid`를 가져옵니다. 패키지를 업로드하는 자세한 방법은 [그리기 패키지 업로드](tutorial-creator-indoor-maps.md#upload-a-drawing-package)를 참조하세요.

2. 이제 그리기 패키지가 업로드되었으므로 업로드된 패키지에 대해 `udid`를 사용하여 패키지를 지도 데이터로 변환합니다. 패키지를 변환하는 자세한 방법은 [그리기 패키지 변환](tutorial-creator-indoor-maps.md#convert-a-drawing-package)을 참조하세요.

    >[!NOTE]
    >변환 프로세스가 성공하면 오류 시각화 도우미 도구에 대한 링크가 수신되지 않습니다.

3. Postman 애플리케이션의 응답 **헤더** 탭 아래에서 변환 API가 반환한 `diagnosticPackageLocation` 속성을 찾습니다. 다음 JSON과 유사한 응답이 표시됩니다.

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "properties": {
            "diagnosticPackageLocation": "https://us.atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=2.0"
        }
    }
    ```

4. `diagnosticPackageLocation` URL에 대한 `HTTP-GET` 요청을 만들어 그리기 패키지 오류 시각화 도우미를 다운로드합니다.

## <a name="setup"></a>설치 프로그램

`diagnosticPackageLocation` 링크에서 다운로드된 압축 패키지 내에서 두 개의 파일을 찾을 수 있습니다.

* _VisualizationTool.zip_: 그리기 오류 시각화 도우미의 소스 코드, 미디어 및 웹 페이지가 포함되어 있습니다.
* _ConversionWarningsAndErrors.json_: 그리기 오류 시각화 도우미에서 사용하는 경고, 오류 및 기타 세부 정보의 서식이 지정된 목록을 포함합니다.

_VisualizationTool.zip_ 폴더의 압축을 풉니다. 여기에는 다음 항목이 포함됩니다.

* _assets_ 폴더: 이미지 및 미디어 파일 포함
* _static_ 폴더: 소스 코드
* _index.html_ 파일: 웹 애플리케이션

아래 브라우저 중 하나의 해당 버전 번호를 사용하여 _index.html_ 파일을 엽니다. 버전이 나열된 버전과 동일하게 호환되는 동작을 제공하는 경우 다른 버전을 사용할 수 있습니다.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>그리기 오류 시각화 도우미 도구 사용

그리기 오류 시각화 도우미 도구를 시작하면 업로드 페이지가 표시됩니다. 업로드 페이지에는 끌어서 놓기 상자가 포함됩니다. 끌어서 놓기 상자는 파일 탐색기 대화 상자를 시작하는 단추로도 작동합니다.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="그리기 오류 시각화 도우미 앱 - 시작 페이지":::

_ConversionWarningsAndErrors.json_ 파일은 다운로드된 디렉터리의 루트에 배치되었습니다. _ConversionWarningsAndErrors.json_ 을 로드하려면 파일을 상자로 끌어 놓습니다. 또는 상자를 클릭하고 `File Explorer dialogue`에서 파일을 찾아 업로드합니다.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="그리기 오류 시각화 도우미 앱 - 끌어서 놓기로 데이터 로드":::

_ConversionWarningsAndErrors.json_ 파일이 로드되면 그리기 패키지 오류 및 경고 목록이 표시됩니다. 각 오류 또는 경고에는 레이어, 수준 및 자세한 메시지가 명시됩니다. 오류 또는 경고에 대한 자세한 정보를 보려면 **세부 정보** 링크를 클릭합니다. 그러면 intractable 섹션이 목록 아래에 표시됩니다. 이제 각 오류로 이동하여 오류를 해결하는 방법에 대한 자세한 정보를 확인할 수 있습니다.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="그리기 오류 시각화 도우미 앱 - 오류 및 경고":::

## <a name="next-steps"></a>다음 단계

[그리기 패키지 요구 사항이 충족되면](drawing-requirements.md) [Azure Maps Dataset 서비스](/rest/api/maps/v2/conversion)를 사용하여 그리기 패키지를 데이터 세트로 변환할 수 있습니다. 그런 다음, Indoor Maps 웹 모듈을 사용하여 애플리케이션을 개발할 수 있습니다. 자세히 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [그리기 변환 오류 코드](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [그리기 패키지 가이드](drawing-package-guide.md)

> [!div class="nextstepaction"]
> [실내 지도용 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Indoor Maps 모듈 사용](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [실내 지도 동적 스타일 구현](indoor-map-dynamic-styling.md)
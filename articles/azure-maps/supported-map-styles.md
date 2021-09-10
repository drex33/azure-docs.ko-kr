---
title: 지원되는 기본 제공 Azure Maps 지도 스타일
description: 도로, blank_accessible, 위성, satellite_road_labels, road_shaded_relief, 야간 등 Azure Maps에서 지원하는 기본 제공 지도 스타일에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 4032dec715fc12b4e8144e6d9eebd51faceaaaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566751"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps에서 지원하는 기본 제공 지도 스타일

Azure Maps는 아래 설명된 대로 여러 가지 기본 제공 지도 스타일을 지원합니다.

>[!important]
>이 섹션의 절차에는 Gen 1 또는 Gen 2 가격 책정 계층의 Azure Maps 계정이 필요합니다. 가격 책정 계층에 대한 자세한 내용은 [Azure Maps에서 적절한 가격 책정 계층 선택](choose-pricing-tier.md)을 참조하세요.

## <a name="road"></a>도로

**도로** 지도는 도로를 표시하는 표준 지도입니다. 또한 자연 및 인공 기능과 해당 기능의 레이블을 표시합니다.

![도로 지도 스타일](./media/supported-map-styles/road.png)

**적용 가능한 API:**

* [지도 이미지](/rest/api/maps/render/getmapimage)
* [지도 타일](/rest/api/maps/render/getmaptile)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="blank-and-blank_accessible"></a>blank 및 blank_accessible

**blank** 지도와 **blank_accessible** 지도 스타일은 데이터 시각화를 위한 빈 캔버스를 제공합니다. **blank_accessible** 스타일은 기본 지도가 표시되지 않더라도 지도의 위치 세부 정보와 함께 화면 읽기 프로그램 업데이트를 계속 제공합니다.

> [!Note]
> 웹 SDK에서 지도 DIV 요소의 CSS `background-color` 스타일을 설정하여 지도의 배경색을 변경할 수 있습니다.

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤

## <a name="satellite"></a>satellite

**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성 타일 지도 스타일](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**

* [위성 타일](/rest/api/maps/render/getmapimagerytilepreview)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="satellite_road_labels"></a>satellite_road_labels

이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels 지도 스타일](./media/supported-map-styles/satellite-road-labels.png)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="grayscale_dark"></a>grayscale_dark

**짙은 회색조** 는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale 지도 스타일](./media/supported-map-styles/grayscale-dark.png)

**적용 가능한 API:**

* [지도 이미지](/rest/api/maps/render/getmapimage)
* [지도 타일](/rest/api/maps/render/getmaptile)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="grayscale_light"></a>grayscale_light

**밝은 회색조** 는 도로 지도 스타일의 밝은 버전입니다.

![밝은 회색조 지도 스타일](./media/supported-map-styles/grayscale-light.jpg)

**적용 가능한 API:**
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="night"></a>야간

**야간** 은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간 지도 스타일](./media/supported-map-styles/night.png)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="road_shaded_relief"></a>road_shaded_relief

**도로 음영 입체** 는 지구의 등고선으로 채워진 Azure Maps 주요 스타일입니다.

![음영 입체 지도 스타일](./media/supported-map-styles/shaded-relief.png)

**적용 가능한 API:**

* [지도 타일](/rest/api/maps/render/getmaptile)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** 는 다른 스타일보다 더 고대비의 어두운 지도 스타일입니다.

![고대비 어두운 지도 스타일](./media/supported-map-styles/high-contrast-dark.png)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="high_contrast_light"></a>high_contrast_light

**high_contrast_light** 는 다른 스타일보다 더 고대비의 밝은 맵 스타일입니다.

![고대비 밝은 맵 스타일](./media/supported-map-styles/high-contrast-light.jpg)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="map-style-accessibility"></a>맵 스타일 접근성

대화형 Azure Maps 맵 컨트롤은 맵 스타일의 벡터 타일을 사용하여 맵이 표시되는 영역을 설명하는 화면 읽기 프로그램을 제공합니다. 여러 맵 스타일은 색상 대비와 관련하여 완전히 액세스할 수 있도록 디자인되었습니다. 다음 표에는 각 맵 스타일에서 지원하는 접근성 기능에 대한 세부 정보가 나와 있습니다.

| 맵 스타일  | 색 대비 | 화면 읽기 프로그램 | 참고 |
|------------|----------------|---------------|-------|
| `blank` | 해당 없음 | 아니요 | 빈 캔버스는 타일을 자신의 기본 맵으로 사용하거나 배경 없이 데이터를 보려는 개발자에게 유용합니다. 화면 읽기 프로그램은 설명을 위해 벡터 타일을 사용하지 않습니다.  |
| `blank_accessible` | 해당 없음  | 예 | 내부적으로 이 맵 스타일은 맵을 렌더링하는 데 사용되는 벡터 타일을 계속 로드하지만 해당 데이터를 투명하게 만듭니다. 이러한 방식으로 데이터는 계속 로드되며 화면 읽기 프로그램을 구동하는 데 사용할 수 있습니다. |
| `grayscale_dark` | Partial | 예 | 이 맵 스타일은 주로 비즈니스 인텔리전스 시나리오를 위해 디자인되었지만 기상 레이더 이미지와 같은 다채로운 레이어를 오버레이하는 데 유용합니다. |
| `grayscale_light` | Partial | 예 | 이 맵 스타일은 주로 비즈니스 인텔리전스 시나리오를 위해 디자인되었습니다. |
| `high_contrast_dark` | 예 | 예 | 어둡게 설정된 고대비 모드에서 사용자가 완전히 액세스할 수 있는 맵 스타일입니다. 맵이 로드되면 고대비 설정이 자동으로 검색됩니다. |
| `high_contrast_light` | 예 | 예 | 밝게 설정된 고대비 모드에서 사용자가 완전히 액세스할 수 있는 맵 스타일입니다. 맵이 로드되면 고대비 설정이 자동으로 검색됩니다. |
| `night` | Partial | 예 | 이 스타일은 사용자가 저조도 환경에 있고 밝은 맵을 사용하여 해당 감지에 방해가 되지 않도록 디자인되었습니다. |
| `road` | Partial | 예 | Azure Maps의 다채로운 주요 도로 맵 스타일입니다. 다양한 색상 수와 겹치는 색상 조합으로 인해 100% 액세스 가능하도록 하는 것은 거의 불가능합니다. 즉, 이 맵 스타일은 정기적인 접근성 테스트를 통해 수행되며 필요에 따라 레이블을 더 명확하게 읽을 수 있도록 개선되었습니다. |
| `road_shaded_relief` | Partial | 예 | 주요 도로 맵 스타일과 거의 동일하지만 상위 수준에서 축소된 경우 산의 음영 처리된 릴리프와 토지 피복 색상을 추가하는 배경에 추가된 타일 레이어가 있습니다. |
| `satellite` | 해당 없음 | 예 | 레이블이나 도로선이 없는 순수한 위성 및 항공 이미지입니다. 벡터 타일은 화면 읽기 프로그램에 전원을 공급하고 `satellite_with_roads`로 전환할 때 더 부드러운 전환을 위해 화면 뒤에서 로드됩니다. |
| `satellite_with_roads` | 아니요 | 예 | 레이블 및 도로 선이 오버레이된 위성 및 항공 이미지입니다. 전역 확장에서 오버레이 데이터와 이미지 사이에 발생할 수 있는 색상 조합의 수에는 제한이 없습니다. 대부분의 일반적인 시나리오에서는 레이블을 읽을 수 있도록 만드는 데 중점을 두지만 일부 장소에서는 배경 이미지와 색상 대비로 인해 레이블을 읽기 어려울 수 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure Maps에서 지도 스타일을 설정하는 방법에 대해 알아봅니다:

> [!div class="nextstepaction"]
> [지도 스타일 선택](./choose-map-style.md)

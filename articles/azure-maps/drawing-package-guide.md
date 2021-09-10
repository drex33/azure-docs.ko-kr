---
title: Microsoft Azure Maps Creator(미리 보기)의 그리기 패키지 가이드
description: Azure Maps Conversion 서비스용 그리기 패키지를 준비하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eb74ffb55536b18e48a4b5dcea83fac4db20abe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535721"
---
# <a name="conversion-drawing-package-guide"></a>변환 그리기 패키지 가이드

이 가이드에서는 변환 서비스를 위해 DWG 파일 및 매니페스트 파일을 올바르게 준비하기 위한 특정 CAD 명령을 사용하여 [Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)용 그리기 패키지를 준비하는 방법을 보여 줍니다.

먼저 그리기 패키지가 .zip 형식이고 다음 파일을 포함하는지 확인합니다.

* DWG 형식의 그리기 파일 한 개 이상.
* DWG 파일 및 기능 메타데이터를 설명하는 매니페스트 파일.

이 가이드와 함께 참조할 고유한 패키지가 없으면 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)를 다운로드할 수 있습니다.

기능 그리기 파일을 열고 준비하기 위해 CAD 소프트웨어를 선택할 수 있습니다. 그러나 이 가이드에서는 Autodesk의 AutoCAD® 소프트웨어를 사용하여 만듭니다. 이 가이드에서 참조하는 모든 명령은 Autodesk의 AutoCAD® 소프트웨어를 사용하여 실행됩니다.  

>[!TIP]
>이 가이드에서 다루지 않는 그리기 패키지 요구 사항에 대한 자세한 내용은 [그리기 패키지 요구 사항](drawing-requirements.md)을 참조하세요.

## <a name="glossary-of-terms"></a>용어집

쉽게 참조할 수 있도록 이 가이드를 읽을 때 중요한 몇 가지 용어와 정의는 다음과 같습니다.

| 용어  | 정의 |
|:-------|:------------|
| 계층 | 그리기 파일의 AutoCAD DWG 계층.|
| 엔터티 | 그리기 파일의 AutoCAD DWG 엔터티. |
| Level  |설정된 고도에 있는 건물의 영역입니다. 예를 들어 건물의 층입니다.    |
| 기능 | 기하 도형을 추가 메타데이터 정보와 결합하는 개체입니다. |
| 기능 클래스 | 기능에 대한 일반적인 청사진입니다. 예를 들어 ‘단위’는 기능 클래스이고, ‘사무실’은 기능입니다.  |

## <a name="step-1-dwg-file-requirements"></a>1단계: DWG 파일 요구 사항

변환 서비스에 대한 기능 그리기 파일을 준비하는 경우 다음과 같은 예비 요구 사항 및 권장 사항을 준수해야 합니다.

* 기능 그리기 파일은 DWG 형식으로 저장되어야 합니다. 이는 Autodesk의 AutoCAD® 소프트웨어에 대한 원시 파일 형식입니다.

* Conversion 서비스는 AutoCAD DWG 파일 형식으로 작동합니다.AC1032는 DWG 파일의 내부 형식 버전이며, 내부 DWG 파일 형식 버전으로 AC1032를 선택하는 것이 좋습니다.

* DWG 파일은 단일 층만 포함할 수 있습니다. 시설의 층은 별도의 DWG 파일로 제공되어야 합니다.  따라서 시설에 5개 층이 있는 경우 별도의 DWG 파일 5개를 만들어야 합니다.

## <a name="step-2-prepare-the-dwg-files"></a>2단계: DWG 파일 준비

가이드의 이 부분에서는 사용자의 DWG 파일이 변환 서비스의 요구 사항을 충족하는지 확인하기 위해 CAD 명령을 사용하는 방법을 보여 줍니다.

기능 그리기 파일을 열고 준비하기 위해 CAD 소프트웨어를 선택할 수 있습니다. 그러나 이 가이드에서는 Autodesk의 AutoCAD® 소프트웨어를 사용하여 만듭니다. 이 가이드에서 참조하는 모든 명령은 Autodesk의 AutoCAD® 소프트웨어를 사용하여 실행됩니다.  

### <a name="bind-external-references"></a>외부 참조 바인딩

시설의 각 층은 하나의 DWG 파일로 제공되어야 합니다. 외부 참조가 없으면 아무 작업도 수행하지 않아도 됩니다. 그러나 외부 참조가 있으면 단일 그리기에 바인딩되어야 합니다. 외부 참조를 바인딩하려면 `XREF` 명령을 사용할 수 있습니다. 바인딩 후 각 외부 참조 그리기는 블록 참조로 추가됩니다. 이러한 계층을 변경해야 하는 경우에는 `XPLODE` 명령을 사용하여 블록 참조를 분해해야 함을 기억하세요.

### <a name="unit-of-measurement"></a>측정 단위

그리기는 모든 측정 단위를 사용하여 만들 수 있습니다. 그러나 모든 그리기는 동일한 측정 단위를 사용해야 합니다. 따라서 시설 중 한 층이 밀리미터를 사용하는 경우 다른 모든 층(그리기)도 밀리미터 단위여야 합니다. `UNITS` 명령을 사용하여 측정 단위를 확인하거나 수정할 수 있습니다.

다음 그림은 측정 단위를 확인하는 데 사용할 수 있는 Autodesk의 AutoCAD® 소프트웨어 내 그리기 단위 창을 보여 줍니다.  

:::image border="true" type="content" source="./media/drawing-package-guide/units.png" alt-text="Autodesk의 AutoCAD® 소프트웨어 내 그리기 단위 창":::

### <a name="alignment"></a>맞춤

시설의 각 층은 개별 DWG 파일로 제공됩니다. 결과적으로 층이 서로 겹쳐져 있을 때 완벽하게 정렬되지 않을 수 있습니다. Azure Maps Conversion 서비스를 사용하려면 모든 그리기를 실제 공간에 맞춰야 합니다. 맞춤을 확인하려면 여러 층에 걸친 엘리베이터 또는 열과 같이 여러 층에 걸쳐 있을 수 있는 참조 지점을 사용합니다. 새 그리기를 열어 모든 층을 본 다음, `XATTACH` 명령을 사용하여 모든 층 그리기를 로드할 수 있습니다. 맞춤 문제를 해결해야 하는 경우 참조 지점과 `MOVE` 명령을 사용하여 이를 필요로 하는 층을 다시 정렬할 수 있습니다.

### <a name="layers"></a>계층

각 그리기 계층이 하나의 기능 클래스의 엔터티를 포함하는지 확인합니다. 계층에 벽에 대한 엔터티가 포함된 경우에는 단위 또는 문과 같은 다른 기능을 사용할 수 없습니다.  그러나 기능 클래스는 여러 계층으로 분할할 수 있습니다. 예를 들어 벽 엔터티가 포함된 그리기에 세 개의 계층이 있을 수 있습니다.

또한 각 계층에는 지원되는 엔터티 형식의 목록이 있으며 다른 모든 형식은 무시됩니다. 예를 들어 단위 레이블 계층에서 한 줄 텍스트만 지원하는 경우 동일한 계층의 여러 줄 텍스트 또는 폴리라인은 무시됩니다.

계층 및 기능 클래스에 대한 자세한 내용은 [그리기 패키지 요구 사항](drawing-requirements.md)을 참조하세요.

### <a name="exterior-layer"></a>실외 레이어

계층의 각 외부 계층에서 단일 수준 기능이 생성됩니다. 이 수준 기능은 수준의 경계를 정의합니다. 외부 계층의 엔터티가 계층의 요구 사항을 충족하는지 확인하는 것이 중요합니다. 예를 들어 닫힌 폴리라인은 지원되지만 열린 폴리라인은 지원되지 않습니다. 외부 계층이 여러 선 세그먼트로 구성된 경우 하나의 닫힌 폴리라인으로 제공되어야 합니다. 여러 줄 세그먼트를 함께 조인하려면 모든 선 세그먼트를 선택하고 `JOIN` 명령을 사용합니다.

다음 이미지는 샘플 패키지에서 가져온 것이며, 시설의 외부 계층은 빨간색으로 표시합니다. 시각화에 도움이 되도록 단위 계층이 꺼져 있습니다.

:::image border="true" type="content" source="./media/drawing-package-guide/exterior.png" alt-text="시설의 외부 계층입니다.":::

### <a name="unit-layer"></a>단위 레이어

단위는 사무실, 복도, 계단 및 엘리베이터와 같은 건물 내에서 탐색 가능한 공간입니다.각 단위를 나타내려면 다각형, 닫힌 폴리라인, 원 또는 닫힌 타원과 같은 닫힌 엔터티 형식이 필요합니다. 따라서 벽 및 문만으로는 단위를 나타내는 엔터티가 없기 때문에 단위를 만들지 않습니다.  

다음 이미지는 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 가져온 것으로 단위 레이블 계층과 단위 계층을 빨간색으로 표시합니다. 시각화를 지원하기 위해 다른 모든 계층이 꺼집니다. 또한 각 단위가 닫힌 폴리라인임을 보여 주기 위해 하나의 단위도 선택됩니다.  

:::image border="true" type="content" source="./media/drawing-package-guide/unit.png" alt-text="시설의 단위 계층입니다.":::

### <a name="unit-label-layer"></a>단위 레이블 계층

단위에 이름 속성을 추가하려면 단위 레이블에 대한 별도의 계층을 추가해야 합니다. 레이블은 단위의 범위 내에 속하는 단일 줄 텍스트 엔터티로 제공되어야 합니다. 해당하는 단위 속성은 `unitName`이 텍스트의 콘텐츠와 일치하는 매니페스트 파일에 추가되어야 합니다.  지원되는 모든 단위 속성에 대한 자세한 내용은 [`unitProperties`](#unitproperties)를 참조하세요.

### <a name="door-layer"></a>문 레이어

문은 선택 사항입니다. 그러나 단위에 대한 진입점을 지정하려는 경우에는 문을 사용할 수 있습니다. 문은 문 계층에서 지원되는 엔터티 유형인 경우 어떤 방식으로든 그릴 수 있습니다. 문은 단위 경계와 겹쳐져야 하며, 단위의 겹치는 가장자리는 단위에 대한 개구부로 처리됩니다.  

다음 이미지는 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 가져온 것으로 단위 경계에 문(빨간색)이 그려진 단위가 나와 있습니다.

:::image border="true" type="content" source="./media/drawing-package-guide/door.png" alt-text="시설의 문 계층입니다.":::

### <a name="wall-layer"></a>벽 레이어

벽 계층은 벽 및 열과 같은 시설의 물리적 확장을 나타냅니다. Azure Maps Conversion 서비스는 라우팅을 위한 장애물인 물리적 구조로 벽을 인식합니다. 이 점을 염두에 두면 벽은 볼 수는 있지만 걸을 수는 없는 물리적 구조로 간주되어야 합니다. 볼 수 없는 모든 항목은 이 계층에서 캡처되지 않습니다. 벽에 내부 벽 또는 열이 있는 경우에는 외부만 캡처되어야 합니다.  

## <a name="step-3-prepare-the-manifest"></a>3단계: 매니페스트 준비

그리기 패키지 매니페스트는 JSON 파일입니다. 매니페스트는 Azure Maps Conversion 서비스에 시설 DWG 파일 및 메타데이터를 읽는 방법을 알려줍니다. 이 정보의 몇 가지 예제는 각 DWG 계층이 포함하는 특정 정보 또는 시설의 지리적 위치일 수 있습니다.

성공적으로 변환하려면 모든 “필수” 속성을 정의해야 합니다. 샘플 매니페스트 파일은 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 찾을 수 있습니다. 이 가이드에서는 매니페스트에서 지원하는 속성에 대해 다루지 않습니다. 매니페스트 속성에 대한 자세한 내용은 [매니페스트 파일 속성](drawing-requirements.md#manifest-file-requirements)을 참조하세요.

### <a name="building-levels"></a>빌딩 수준

빌딩 수준은 해당 수준에 사용할 DWG 파일이 무엇인지 지정합니다. 수준에는 각 수준의 세로 순서를 설명하는 수준 이름과 서수가 있어야 합니다. 모든 시설에는 시설의 1층인 서수 0이 있어야 합니다. 그리기가 설비의 몇 개 층을 차지하더라도 서수 0이 제공되어야 합니다. 예를 들어 15-17층은 각각 서수 0-2로 정의할 수 있습니다.

다음 예제는 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 가져온 것입니다. 시설에는 지하실, 접지 및 수준 2의 세 가지 수준이 있습니다. 파일 이름에는 .zip 그리기 패키지 내의 매니페스트 파일을 기준으로 하는 파일의 전체 파일 이름 및 경로가 포함되어 있습니다.  

```json
    "buildingLevels": { 
      "levels": [ 
       { 
           "levelName": "Basement", 
           "ordinal": -1, 
           "filename": "./Basement.dwg" 
            }, { 

            "levelName": "Ground", 
            "ordinal": 0, 
            "filename": "./Ground.dwg" 
            }, { 

            "levelName": "Level 2", 
            "ordinal": 1, 
             "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
```

### <a name="georeference"></a>georeference

`georeference` 개체는 시설이 지리적으로 배치되는 위치와 시설을 회전하는 정도를 지정하는 데 사용됩니다. 그리기의 원점 위치는 `georeference` 개체와 함께 제공되는 위도 및 경도와 일치해야 합니다. 진북 및 그리기의 세로(Y) 축 사이의 시계 방향 각도(도)입니다.  

### <a name="dwglayers"></a>dwgLayers

`dwgLayers` 개체는 기능 클래스를 찾을 수 있는 DWG 계층 이름을 지정하는 데 사용됩니다. 속성 변환 시설을 받으려면 올바른 계층 이름을 제공하는 것이 중요합니다. 예를 들어, DWG 벽 계층은 단위 계층이 아니라 벽 계층으로 제공되어야 합니다. 그리기에는 가구 또는 배관 같은 다른 계층이 있을 수 있습니다. 그러나 매니페스트에서 지정되지 않은 경우 Azure Maps Conversion 서비스에서 무시됩니다.  

다음은 매니페스트에서 `dwgLayers` 개체의 예제입니다.  

```json
"dwgLayers": { 
        "exterior": [ 
            "OUTLINE" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    } 
```

다음 이미지는 Autodesk의 AutoCAD® 소프트웨어로 표시되는 해당 DWG 그리기의 계층을 보여 줍니다.

:::image border="true" type="content" source="./media/drawing-package-guide/layer.png" alt-text="Autodesk의 AutoCAD® 소프트웨어에서 DwgLayers":::

### <a name="unitproperties"></a>unitProperties

`unitProperties` 개체를 사용하면 DWG 파일에서 수행할 수 없는 단위에 대한 다른 속성을 정의할 수 있습니다. 예를 들면 단위의 디렉터리 정보 또는 단위의 범주 유형이 있습니다. 단위 속성은 `unitName` 개체가 `unitLabel` 계층의 레이블과 일치하도록 하여 단위와 연결됩니다.  

다음 이미지는 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 가져온 것입니다. 매니페스트의 단위 속성에 연결된 단위 레이블을 표시합니다.

:::image border="true" type="content" source="./media/drawing-package-guide/unit-property.png" alt-text="매니페스트의 단위 속성에 연결되는 단위 레이블입니다.":::

다음 코드 조각에서는 단위와 연결된 단위 속성 개체를 보여 줍니다.  

```json
 "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
```

## <a name="step-4-prepare-the-drawing-package"></a>4단계: 그리기 패키지 준비

이제 Azure Maps Conversion 서비스 요구 사항을 충족하기 위해 모든 DWG 그리기를 준비해야 합니다. 시설을 설명하는 데 도움이 되는 매니페스트 파일도 만들었습니다. 모든 파일은 `.zip` 확장명을 사용하여 단일 보관 파일에 압축해야 합니다. 매니페스트 파일의 이름이 `manifest.json`이고 압축된 패키지의 루트 디렉터리에 있어야 합니다. 파일 이름에 매니페스트에 대한 상대 경로가 포함된 경우 다른 모든 파일은 압축된 패키지의 모든 디렉터리에 있을 수 있습니다. 그리기 패키지의 예제는 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Creator 실내 맵 만들기](tutorial-creator-indoor-maps.md)

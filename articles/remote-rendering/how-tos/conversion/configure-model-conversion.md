---
title: 모델 변환 구성
description: 모든 모델 변환 매개 변수에 대한 설명
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 263531d24d50c27309163f0671a41ff7aacd36c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601371"
---
# <a name="configure-the-model-conversion"></a>모델 변환 구성

이 장에서는 모델 변환에 대한 옵션을 설명합니다.

## <a name="settings-file"></a>설정 파일

`<modelName>.ConversionSettings.json`이라는 파일이 입력 모델 `<modelName>.<ext>` 옆의 입력 컨테이너에 있으면 모델 변환 프로세스에 추가 구성을 제공하는 데 사용됩니다.
예를 들어 `box.gltf` 변환 시 `box.ConversionSettings.json`이 사용됩니다.

파일의 내용은 다음 json 스키마를 충족해야 합니다.

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

파일 예 `box.ConversionSettings.json`은 다음과 같을 수 있습니다.

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>기하 도형 매개 변수

* `scaling` - 이 매개 변수는 모델의 크기를 균일하게 조정합니다. 예를 들어 크기 조정을 사용하여 모델을 확장 또는 축소하고 테이블 탑 뷰로 건물 모델을 표시할 수 있습니다.
렌더링 엔진은 미터를 사용하기 때문에 미터 이외의 단위로 모델을 정의할 때는 크기 조정이 중요합니다.
예를 들어 모델이 센티미터 단위로 정의된 경우 눈금 0.01을 적용하면 모델을 올바른 크기로 렌더링되어야 합니다.
일부 원본 데이터 형식(예: .fbx)은 단위 크기 조정 힌트를 제공하며 이 경우 변환을 통해 모델 크기는 암시적으로 미터 단위로 조정됩니다. 원본 형식에서 제공하는 암시적 크기 조정이 크기 조정 매개 변수 위에 적용됩니다.
최종 배율 인수가 장면 그래프 노드의 기하 도형 꼭짓점과 로컬 변환에 적용됩니다. 루트 엔터티의 변환에 대한 크기 조정은 수정되지 않고 유지됩니다.

* `recenterToOrigin` - 해당 경계 상자의 중심이 원점에 오도록 모델을 변환해야 함을 설명합니다.
원본 모델이 원점에서 멀리 떨어져 있는 경우에는 부동 소수점 정밀도 문제로 인해 렌더링 아티팩트가 발생할 수 있습니다.
이 경우 모델을 중심으로 하는 것이 도움이 될 수 있습니다.

* `opaqueMaterialDefaultSidedness` - 렌더링 엔진은 불투명 재질을 양면으로 가정합니다.
이러한 가정이 특정 모델에 적용되지 않는 경우 이 매개 변수를 "SingleSided"로 설정해야 합니다. 자세한 내용은 [:::no-loc text="single sided"::: 렌더링](../../overview/features/single-sided-rendering.md)을 참조하세요.

### <a name="material-de-duplication"></a>재질 중복 제거

* `deduplicateMaterials` - 이 매개 변수는 동일한 속성과 질감을 공유하는 재질의 자동 중복 제거를 사용하거나 사용하지 않도록 설정합니다. 중복 제거는 재질 재정의가 처리된 후에 발생합니다. 기본적으로 사용하도록 설정되어 있습니다.

* 중복 제거 후에도 모델의 재질이 65,535개 이상인 경우 서비스는 유사한 속성으로 재질을 병합하려고 시도합니다. 마지막 수단으로 제한을 초과하는 재질은 빨간색 오류 재질로 바뀝니다.

![68,921개의 색 삼각형으로 구성된 두 큐브를 보여 주는 이미지입니다.](media/mat-dedup.png?raw=true)

68,921개의 색 삼각형으로 구성된 두 개의 큐브입니다. 왼쪽: 68,921개의 색 재질을 사용한 중복 제거 전. 오른쪽: 64,000개의 색 재질을 사용한 중복 제거 후. 재질 수의 한도는 65,535개입니다. ([제한](../../reference/limits.md) 참조)

### <a name="color-space-parameters"></a>색 공간 매개 변수

렌더링 엔진은 색 값이 선형 공간에 있을 것으로 예상합니다.
감마 공간을 사용하여 모델을 정의하는 경우 이러한 옵션을 true로 설정해야 합니다.

* `gammaToLinearMaterial` - 재질 색을 감마 공간에서 선형 공간으로 변환합니다.
* `gammaToLinearVertex` - :::no-loc text="vertex"::: 색을 감마 공간에서 선형 공간으로 변환합니다.

> [!NOTE]
> FBX 파일의 경우 이러한 설정은 기본적으로 `true`로 설정됩니다. 다른 모든 파일 형식의 경우 기본값은 `false`입니다.

### <a name="scene-parameters"></a>장면 매개 변수

* `sceneGraphMode` - 소스 파일의 장면 그래프를 변환하는 방법을 정의합니다.
  * `dynamic` (기본값): 파일의 모든 개체는 API에서 [엔터티로](../../concepts/entities.md) 노출 되며 임의로 변환 하 고 다시 부모로 지정할 수 있습니다. 런타임에 노드 계층 구조는 소스 파일의 구조와 동일합니다.
  * `static`:와 비슷하지만 `dynamic` , 장면 그래프의 개체는 런타임에 동적으로 다른 개체의 부모로 지정할 수 없습니다. 이동 부분이 많은 동적 모델 (예: ' 분해 뷰 ')의 경우이 `dynamic` 옵션은 렌더링 하는 데 더 효율적인 모델을 생성 하지만 `static` 모드는 개별 파트 변환을 허용 합니다. 동적으로 다시 부모로 만들지 않아도 되는 경우 `static` 이 옵션은 개별 부분이 많은 모델에 가장 적합 합니다.
  * `none`: 장면 그래프가 하나의 개체로 축소됩니다.

모드마다 런타임 성능이 다릅니다. `dynamic` 모드에서 성능 비용은 이동된 부분이 없더라도 그래프에서 [엔터티](../../concepts/entities.md) 수에 맞춰 선형으로 확장됩니다. `dynamic`' 분해 뷰 ' 애니메이션의 경우와 같이 여러 부분 또는 여러 하위 그래프를 동시에 이동 해야 하는 경우에만 모드를 사용 합니다.

`static`또한이 모드는 전체 장면 그래프를 내보냅니다. [공간 쿼리](../../overview/features/spatial-queries.md) 는 개별 파트를 반환 하 고 각 파트는 [상태 재정의](../../overview/features/override-hierarchical-state.md)를 통해 수정할 수 있습니다. 이 모드에서 개체당 런타임 오버헤드는 무시됩니다. 개체별 검사를 수행 해야 하는 경우에는 종종 개별 파트에서 변경 내용을 변환 하지만 개체는 다시 부모로 설정 하지 않는 것이 좋습니다.

`none` 모드에서는 런타임 오버헤드가 최소화되며 로드 시간도 약간 향상됩니다. 이 모드에서는 단일 개체의 검사 또는 변환이 불가능합니다. 예를 들어, 사용 사례는 첫 번째 위치에 의미 있는 장면 그래프가 없는 사진 측량 모델입니다.

> [!TIP]
> 많은 애플리케이션은 여러 모델을 로드합니다. 각 모델에 대한 변환 매개 변수를 사용되는 방법에 따라 최적화해야 합니다. 예를 들어 사용자가 분석하고 자세히 조사할 수 있게 자동차 모델을 표시하려는 경우 `dynamic` 모드로 변환해야 합니다. 그러나 추가로 쇼룸 환경에 자동차를 배치하려는 경우에는 해당 모델을 `static` 또는 `none`으로 설정된 `sceneGraphMode`로 변환할 수 있습니다.

### <a name="physics-parameters"></a>물리학 매개 변수

* `generateCollisionMesh` - 모델에서 [공간 쿼리](../../overview/features/spatial-queries.md) 지원이 필요한 경우 이 옵션을 사용하도록 설정해야 합니다. 최악의 경우 충돌 메시를 만들 때 변환 시간이 두 배로 늘어날 수 있습니다. 충돌 메시를 사용하는 모델은 로드하는 데 시간이 더 오래 걸리고 `dynamic` 장면 그래프를 사용하는 경우 런타임 성능 오버헤드가 더 높아집니다. 전체 성능을 최적화하려면 공간 쿼리가 필요하지 않은 모든 모델에서 이 옵션을 사용하지 않도록 설정해야 합니다.

### <a name="unlit-materials"></a>조명 꺼짐 재질

* `unlitMaterials` - 기본적으로 변환은 [PBR 재질](../../overview/features/pbr-materials.md)을 만드는 것을 선호합니다. 이 옵션은 변환기에 모든 재질을 대신 [색 재질](../../overview/features/color-materials.md)로 처리하도록 지시합니다. 사진 측량을 통해 만들어진 모델과 같이 이미 조명을 포함하는 데이터가 있는 경우 이 옵션을 사용하면 개별적으로 [각 재질을 재정의](override-materials.md)할 필요 없이 모든 재질에 대해 올바른 변환을 신속하게 적용할 수 있습니다.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>퐁 재질 모델을 사용하여 이전 FBX 형식에서 변환

* `fbxAssumeMetallic` - 이전 버전의 FBX 형식은 퐁 재질 모델을 사용하여 해당 재질을 정의합니다. 변환 프로세스에서는 이러한 재질이 렌더러의 [PBR 모델](../../overview/features/pbr-materials.md)에 매핑되는 방식을 유추해야 합니다. 일반적으로 이러한 유추는 잘 작동하지만 재질에 질감, 높은 반사 값 및 비회색 알베도 색이 없으면 모호성이 발생할 수 있습니다. 이 경우 변환은 높은 반사 값에 우선 순위를 주고, 알베도 색이 흩어지는 높은 반사형 금속 재질을 정의하는 경우와 알베도 색에 우선 순위를 주고 다양한 색의 빛나는 플라스틱과 같은 재질을 정의하는 경우 중에서 선택해야 합니다. 기본적으로 변환 프로세스에서는 모호성이 적용되는 경우 높은 반사 값이 금속 재질을 의미한다고 가정합니다. 이 매개 변수를 `false`로 설정하여 반대로 전환할 수 있습니다.

### <a name="coordinate-system-overriding"></a>좌표계 재정의

* `axis` - 좌표계 단위 벡터를 재정의합니다. 기본값은 `["+x", "+y", "+z"]`입니다. 이론적으로 FBX 형식에는 해당 벡터가 정의된 헤더가 있으며 변환에서는 해당 정보를 사용하여 장면을 변환합니다. 또한 glTF 형식은 고정 좌표계도 정의합니다. 실제로 일부 자산은 헤더에 잘못된 정보가 있거나 다른 좌표계 규칙을 사용하여 저장되었습니다. 이 옵션을 사용하면 좌표계를 재정의하여 보정할 수 있습니다. 예를 들어 `"axis" : ["+x", "+z", "-y"]`는 Z축과 Y축을 바꾸고 Y축 방향을 반전시켜 좌표계의 선호 손방향을 그대로 유지합니다.

### <a name="node-meta-data"></a>노드 메타데이터

* `metadataKeys` -변환 결과에서 유지하려는 노드 메타데이터 속성의 키를 지정할 수 있습니다. 정확한 키 또는 와일드카드 키를 지정할 수 있습니다. 와일드카드 키는 "ABC*" 형식이며 "ABC"로 시작하는 모든 키와 일치합니다. 지원되는 메타데이터 값 형식은 `bool`, `int`, `float` 및 `string`입니다.

    GLTF 파일의 경우 이 데이터는 [노드의 extras 개체](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras)에서 제공됩니다. FBX 파일의 경우이 데이터는 `Model nodes` 노드의 `Properties70` 데이터에서 제공됩니다. 자세한 내용은 3D 자산 도구의 설명서를 참조하세요.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: 형식

메시의 :::no-loc text="vertex"::: 형식을 조정하여 정밀도와 메모리 절감 간에 균형을 유지할 수 있습니다. 메모리 공간이 작으면 더 큰 모델을 로드하거나 더 나은 성능을 얻을 수 있습니다. 그러나 데이터에 따라, 형식이 잘못될 경우 렌더링 품질이 크게 저하될 수 있습니다.

> [!CAUTION]
> :::no-loc text="vertex"::: 형식을 변경하는 것은 모델이 더 이상 메모리에 맞지 않거나 최상의 성능을 위해 최적화할 때 최후의 수단이어야 합니다. 변경을 수행하면 명확하고 미묘한 렌더링 아티팩트가 쉽게 도입될 수 있습니다. 확인할 항목을 모르는 경우에는 기본값을 변경하면 안 됩니다.

다음과 같이 조정할 수 있습니다.

* 특정 데이터 스트림을 명시적으로 포함하거나 제외할 수 있습니다.
* 메모리 공간을 줄이기 위해 데이터 스트림의 정확도를 줄일 수 있습니다.

`.json` 파일의 다음 `vertex` 섹션은 선택 사항입니다. 명시적으로 지정하지 않은 각 부분에 대해 변환 서비스를 수행하면 기본 설정으로 대체됩니다.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

구성 요소를 강제로 `NONE`으로 설정하면 출력 메시에 해당 스트림이 적용되지 않습니다.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>:::no-loc text="vertex"::: 스트림별 구성 요소 형식

다음과 같은 형식이 각 구성 요소에 대해 허용됩니다.

| :::no-loc text="Vertex"::: 구성 요소 | 지원되는 형식(굵게 = 기본값) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **NONE**|
|노멀| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|tangent| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NONE |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NONE |

#### <a name="supported-component-formats"></a>지원되는 구성 요소 형식

형식의 메모리 공간은 다음과 같습니다.

| 형식 | Description | :::no-loc text="vertex":::당 바이트 수 |
|:-------|:------------|:---------------|
|32_32_FLOAT|2분 부동 소수점 배정밀도|8
|16_16_FLOAT|2분 부동 소수점 단정밀도|4
|32_32_32_FLOAT|3분 부동 소수점 배정밀도|12
|16_16_16_16_FLOAT|4분 부동 소수점 단정밀도|8
|8_8_8_8_UNSIGNED_NORMALIZED|4분 바이트, `[0; 1]` 범위로 정규화됨|4
|8_8_8_8_SIGNED_NORMALIZED|4분 바이트, `[-1; 1]` 범위로 정규화됨|4

#### <a name="best-practices-for-component-format-changes"></a>구성 요소 형식 변경에 대한 모범 사례

* `position`: 줄어든 정확도로 충분한 경우는 드뭅니다. **16_16_16_16_FLOAT** 는 작은 모델의 경우에도 눈에 띄는 양자화 아티팩트를 도입합니다.
* `normal`, `tangent`, `binormal`: 일반적으로 이러한 값은 함께 변경됩니다. 정규 양자화에서 발생하는 눈에 띄는 조명 아티팩트가 없으면 정확도를 높일 이유가 없습니다. 그러나 경우에 따라 이러한 구성 요소를 **NONE** 으로 설정할 수 있습니다.
  * `normal`, `tangent` 및 `binormal`은 모델에서 하나 이상의 재질이 켜진 경우에만 필요합니다. ARR에서는 언제든지 모델에서 [PBR 재질](../../overview/features/pbr-materials.md)이 사용되는 경우가 여기에 해당합니다.
  * `tangent` 및 `binormal`은 lit 재질에 일반 맵 질감을 사용하는 경우에만 필요합니다.
* `texcoord0`, `texcoord1` : 질감 좌표는 값이 `[0; 1]` 범위를 유지하고 주소 지정된 질감의 최대 크기가 2048 x 2048 픽셀일 때 감소된 정확도(**16_16_FLOAT**)를 사용할 수 있습니다. 이러한 제한을 초과하면 질감 매핑의 품질이 저하됩니다.

#### <a name="example"></a>예제

질감에 조명이 내재된 사진 측량 모델이 있다고 가정합니다. 모델을 렌더링하는 데 필요한 모든 것은 :::no-loc text="vertex"::: 위치 및 질감 좌표입니다.

기본적으로 변환기는 사용자가 특정 시점에 모델에 PBR 재질을 사용하려고 한다고 가정해야 하므로 `normal`, `tangent` 및 `binormal` 데이터를 생성합니다. 따라서 꼭짓점당 메모리 사용량은 `position`(12바이트) + `texcoord0`(8바이트) + `normal`(4바이트) + `tangent`(4바이트) + `binormal`(4바이트) = 32바이트입니다. 이 유형의 모델이 더 클 경우 수백만 개의 :::no-loc text="vertices":::이 있을 수 있으며 이로 인해 모델이 수 기가바이트의 메모리를 사용할 수 있습니다. 이러한 대량의 데이터는 성능에 영향을 주므로 메모리가 부족해질 수도 있습니다.

모델에 동적 조명이 필요하지 않으며 모든 질감 좌표가 `[0; 1]` 범위에 있는 것이 확인되면 `normal`, `tangent` 및 `binormal`을 `NONE`으로 설정하고 `texcoord0`을 반정밀도(`16_16_FLOAT`)로 설정하여 :::no-loc text="vertex":::당 16바이트만 사용할 수 있습니다. 메시 데이터를 절반으로 자르면 더 큰 모델을 로드하고 성능도 향상시킬 수 있습니다.

## <a name="memory-optimizations"></a>메모리 최적화

로드된 콘텐츠의 메모리 사용량이 렌더링 시스템에서 병목 상태가 될 수 있습니다. 메모리 페이로드가 너무 커지면 렌더링 성능이 저하되거나 모델이 완전히 로드되지 않을 수 있습니다. 이 단락에서는 메모리 공간을 줄이기 위한 몇 가지 중요한 전략에 대해 설명합니다.

### <a name="instancing"></a>인스턴싱

인스턴싱은 자체의 고유한 기하 도형을 참조하는 모든 파트가 아니라 메시를 고유한 공간 변환의 일부에 재사용하는 개념입니다. 인스턴싱은 메모리 공간에 매우 중요한 영향을 미칩니다.
인스턴싱에 대한 사용 사례 예는 엔진 모델의 나사 또는 아키텍처 모델의 의자입니다.

> [!NOTE]
> 인스턴싱은 메모리 사용(로딩 시간)을 크게 향상시킬 수 있지만 렌더링 성능 측면에서의 향상은 그리 크지 않습니다.

변환 서비스는 파트가 원본 파일에 적절하게 표시되는 경우 인스턴싱을 설정합니다. 그러나 변환에서는 메시 데이터의 심층 분석을 추가로 수행하여 재사용 가능한 파트를 식별하지 않습니다. 따라서 콘텐츠 생성 도구와 해당 내보내기 파이프라인은 적절한 인스턴싱 설정의 결정적인 조건입니다.

변환하는 동안 인스턴싱 정보를 유지할지 여부를 테스트하는 간단한 방법은 [출력 통계](get-information.md#example-info-file), 특히`numMeshPartsInstanced` 멤버를 확인하는 것입니다. `numMeshPartsInstanced`의 값이 0보다 크면 메시가 인스턴스 간에 공유됨을 나타냅니다.

#### <a name="example-instancing-setup-in-3ds-max"></a>예: 3ds Max의 인스턴싱 설정

[Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max)에는 내보낸 `.fbx` 파일의 인스턴싱과 관련하여 다르게 동작하는 **`Copy`** **`Instance`** 및 **`Reference`** 라는 고유한 개체 복제 모드가 있습니다.

![3ds Max에서 복제](./media/3dsmax-clone-object.png)

* **`Copy`** :이 모드에서는 메시가 복제되므로 인스턴싱이 사용되지 않습니다(`numMeshPartsInstanced` = 0).
* **`Instance`** : 두 개체가 동일한 메시를 공유하므로 인스턴싱이 사용됩니다(`numMeshPartsInstanced` = 1).
* **`Reference`** : 고유한 한정자를 기하 도형에 적용할 수 있으므로 내보내기는 보수적인 접근 방식을 선택하고 인스턴싱을 사용하지 않습니다(`numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>깊이 기반 컴퍼지션 모드

메모리가 중요한 경우에는 [깊이 기반 컴퍼지션 모드](../../concepts/rendering-modes.md#depthbasedcomposition-mode)로 렌더러를 구성합니다. 이 모드에서 GPU 페이로드는 여러 GPU에 걸쳐 분산됩니다.

### <a name="decrease-vertex-size"></a>꼭짓점 크기 줄이기

[구성 요소 형식 변경에 대한 모범 사례](configure-model-conversion.md#best-practices-for-component-format-changes) 섹션에서 설명한 것처럼 꼭짓점 형식을 조정하면 메모리 공간이 줄어들 수 있습니다. 하지만 이 옵션은 최후이 수단이어야 합니다.

### <a name="texture-sizes"></a>질감 크기

시나리오 유형에 따라 질감 데이터의 양은 메시 데이터에 사용되는 메모리보다 클 수 있습니다. 이에 대한 후보는 사진 측량 모델입니다.
변환 구성은 질감을 자동으로 축소하는 방법을 제공하지 않습니다. 필요한 경우 질감 크기 조정을 클라이언트 쪽 전처리 단계로 수행해야 합니다. 그러나 변환 단계에서는 적절한 [질감 압축 형식](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)을 선택합니다.

* 불투명한 색 질감의 경우 `BC1`
* 알파 채널이 있는 소스 색 질감의 경우 `BC7`

형식 `BC7`은 `BC1`에 비해 메모리 공간이 두 배이기 때문에 입력 질감이 불필요하게 알파 채널을 제공하지 않는지 확인하는 것이 중요합니다.

## <a name="typical-use-cases"></a>일반적인 사용 사례

지정된 사용 사례에 대해 적절한 가져오기 설정을 찾는 과정은 지루할 수 있습니다. 반면에 변환 설정은 런타임 성능에 상당한 영향을 미칠 수 있습니다.

특정 최적화에 적합한 특정 사용 사례 클래스가 있습니다. 몇 가지 예가 아래에 나와 있습니다.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>사용 사례: 아키텍처 시각화/대형 옥외 지도

* 이러한 유형의 장면은 정적인 경우가 많으므로 이동 가능한 부분이 필요하지 않습니다. 따라서 `sceneGraphMode`를 `static` 또는 `none`으로 설정하여 런타임 성능을 향상시킬 수 있습니다. `static` 모드에서는 장면의 루트 노드를 계속 이동, 회전 및 크기 조정할 수 있습니다. 예를 들어 1:1 배율(1인칭 시점)과 테이블 탑 뷰 간을 동적으로 전환할 수 있습니다.

* 부분을 이동해야 하는 경우에는 일반적으로 광선 캐스팅이나 기타 [공간 쿼리](../../overview/features/spatial-queries.md)를 지원해야 첫 번째 위치에서 해당 부분을 선택할 수 있음을 의미합니다. 반면에 항목을 이동하지 않으려는 경우 공간 쿼리에 참여할 필요가 없고 `generateCollisionMesh` 플래그를 해제할 수 있는 가능성이 높아집니다. 이러한 전환은 변환 시간, 로드 시간 및 프레임당 런타임 업데이트 비용에 상당한 영향을 미칩니다.

* 애플리케이션에서 [절단면](../../overview/features/cut-planes.md)을 사용하지 않는 경우 `opaqueMaterialDefaultSidedness` 플래그를 해제해야 합니다. 일반적으로 20%-30%의 성능 향상이 발생합니다. 절단면은 계속 사용할 수 있지만 개체의 내부를 들여다 볼 경우 배면 상태가 아니므로 이해하기 어려울 수 있습니다. 자세한 내용은 [:::no-loc text="single sided"::: 렌더링](../../overview/features/single-sided-rendering.md)을 참조하세요.

### <a name="use-case-photogrammetry-models"></a>사용 사례: 사진 측량 모델

사진 측량 모델을 렌더링할 때는 일반적으로 장면 그래프가 필요하지 않으므로 `sceneGraphMode`를 `none`으로 설정할 수 있습니다. 이러한 모델에는 시작할 때 복잡한 장면 그래프가 거의 포함되어 있지 않으므로 이 옵션의 영향은 중요하지 않습니다.

조명이 질감에 이미 내재되어 있으므로 동적 조명이 필요하지 않습니다. 따라서

* `unlitMaterials` 플래그를 `true`로 설정하여 모든 재질을 조명 꺼짐 [색 재질](../../overview/features/color-materials.md)로 전환합니다.
* 꼭짓점 형식에서 불필요한 데이터를 제거합니다. 위의 [예제](#example)를 참조하세요.

### <a name="use-case-visualization-of-compact-machines-etc"></a>사용 사례: 소형 머신 등의 시각화

이러한 사용 사례에서 모델은 소형 크기 내에 매우 자세한 사항을 포함하는 경우가 많습니다. 렌더러는 이러한 경우를 처리하도록 최적화되어 있습니다. 그러나 위의 사용 사례에서 언급된 대부분의 최적화가 여기에는 적용되지 않습니다.

* 개별 부분은 선택 및 이동 가능해야 하므로 `sceneGraphMode`를 `dynamic` 상태로 유지해야 합니다.
* 광선 캐스팅은 일반적으로 애플리케이션의 필수적인 부분이므로 충돌 메시를 생성해야 합니다.
* 절단면은 `opaqueMaterialDefaultSidedness` 플래그를 사용하도록 설정하면 더 나아 보입니다.

## <a name="deprecated-features"></a>사용되지 않는 기능

비 모델 관련 파일 이름 `conversionSettings.json`을 사용하는 설정은 아직 제공되지만 사용되지는 않습니다.
모델 관련 파일 이름 `<modelName>.ConversionSettings.json`을 대신 사용하세요.

변환 설정 파일에서 [재질 재정의 파일](override-materials.md)을 식별하는 `material-override` 설정을 사용하는 것은 여전히 지원되지만 사용되지 않습니다. 모델 관련 파일 이름 `<modelName>.MaterialOverrides.json`을 대신 사용하세요.

## <a name="next-steps"></a>다음 단계

* [모델 변환](model-conversion.md)
* [색 재질](../../overview/features/color-materials.md)
* [PBR 재질](../../overview/features/pbr-materials.md)
* [모델 변환 중 재질 재정의](override-materials.md)
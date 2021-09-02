---
title: 모델 변환 중 재질 재정의
description: 변환 시 재질 재정의 워크플로에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: f99439ae71f8a66b9fc36b32603681e62f2224a9
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968251"
---
# <a name="override-materials-during-model-conversion"></a>모델 변환 중 재질 재정의

원본 모델의 재질 설정은 렌더러에서 사용하는 [PBR 재질](../../overview/features/pbr-materials.md)을 정의하는 데 사용됩니다.
[기본 변환](../../reference/material-mapping.md)이 원하는 결과를 제공하지 않아 변경해야 하는 경우도 있습니다.
Azure Remote Rendering에서 사용하기 위해 모델을 변환할 때 재질 재정의 파일을 제공하여 재질을 기준으로 재질을 변환하는 방법을 사용자 지정할 수 있습니다.
`<modelName>.MaterialOverrides.json`이라는 파일이 입력 모델 `<modelName>.<ext>` 옆의 입력 컨테이너에 있으면 해당 파일은 재질 재정의 파일로 사용됩니다.

## <a name="the-override-file-used-during-conversion"></a>변환 시 사용되는 재정의 파일

간단한 예로, 상자 모델에 "Default"라는 단일 재질이 있다고 가정해 보겠습니다.
또한 ARR에서 사용할 수 있도록 알베도 색을 조정해야 한다고 가정합니다.
이 경우 다음과 같이 `box.MaterialOverrides.json` 파일을 만들 수 있습니다.

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box.MaterialOverrides.json` 파일은 `box.fbx` 옆의, 변환 서비스에 새 설정을 적용하도록 지시하는 입력 컨테이너에 배치됩니다.

### <a name="color-materials"></a>색 재질

[색 재질](../../overview/features/color-materials.md) 모델은 조명과 독립적인 지속적으로 음영 처리된 표면에 대해 설명합니다.
예를 들어 색 재질은 사진 측량 알고리즘에서 만든 자산에 유용합니다.
재질 재정의 파일에서 `unlit`를 `true`로 설정하여 재질을 색 재질로 선언할 수 있습니다.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>특정 질감 맵 무시

경우에 따라 변환 프로세스에서 특정 질감 맵을 무시하도록 할 수 있습니다. 특수 맵을 생성하는 도구로 생성된 모델이 렌더러에서 올바르게 인식되지 않는 경우가 될 수 있습니다. 불투명도 이외의 항목을 정의하는 데 사용되는 "OpacityMap" 또는 "BumpMap"으로 저장되는 "NormalMap"을 예로 들 수 있습니다. 후자의 경우 변환기가 "BumpMap"를 "NormalMap"으로 사용하게 되므로 "NormalMap"을 무시할 수 있습니다.

원칙은 간단합니다. `ignoreTextureMaps`라는 속성을 추가하고 무시할 질감 맵을 추가하기만 하면 됩니다.

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

무시할 수 있는 질감 맵의 전체 목록은 아래의 JSON 스키마를 참조하세요.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>여러 재질에 동일한 재정의 적용

기본적으로 재질 재정의 파일의 항목은 이름이 재질 이름과 정확히 일치하는 경우에 적용됩니다.
동일한 재정의가 여러 재질에 적용되는 것은 매우 일반적이기 때문에 필요에 따라 정규식을 항목 이름으로 제공할 수 있습니다.
`nameMatching` 필드의 기본값은 `exact`이지만 `regex`로 설정하면 일치하는 모든 재질에 항목을 적용할 수 있습니다.
사용되는 구문은 JavaScript에 사용되는 것과 동일합니다. 다음 예에서는 "Material2", "Material01" 및 "Material999"와 같은 이름을 가진 재질에 적용되는 재정의를 보여 줍니다.

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

재질 재정의 파일에서 최대 하나의 항목만 단일 재질에 적용됩니다.
재질 이름에 정확히 일치하는 항목이 있으면(예: `nameMatching`이 없거나 `exact`와 같음) 해당 항목이 선택됩니다.
그렇지 않으면 재질 이름과 일치하는 파일의 첫 번째 정규식 항목이 선택됩니다.

### <a name="getting-information-about-which-entries-applied"></a>적용된 항목에 대한 정보 가져오기

출력 컨테이너에 작성된 [정보 파일](get-information.md#information-about-a-converted-model-the-info-file)은 제공된 재정의 수와 재정의된 재질 수에 대한 정보를 전달합니다.

## <a name="json-schema"></a>JSON 스키마

여기에는 재질 파일에 대한 전체 JSON 스키마가 제공됩니다. `unlit` 및 `ignoreTextureMaps`를 제외하고 사용할 수 있는 속성은 [색 재질](../../overview/features/color-materials.md) 및 [PBR 재질](../../overview/features/pbr-materials.md) 모델의 섹션에 설명된 속성의 하위 집합입니다.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" },
            "transparencyWriteDepth": {"type" : "boolean" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>다음 단계

* [색 재질](../../overview/features/color-materials.md)
* [PBR 재질](../../overview/features/pbr-materials.md)

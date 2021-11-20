---
title: 모델 형식에 대한 재질 매핑
description: 모델 원본 형식에서 PBR 재질로의 기본 변환에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8004b514d9647a22dc0e70ea982ac3960804151c
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132865290"
---
# <a name="material-mapping-for-model-formats"></a>모델 형식에 대한 재질 매핑

원본 자산이 [모델로 변환](../how-tos/conversion/model-conversion.md)될 때 변환기는 각 [메시](../concepts/meshes.md)의 [재질](../concepts/materials.md)을 만듭니다. 재질을 만드는 방법은 [재정의](../how-tos/conversion/override-materials.md)할 수 있습니다. 하지만 기본적으로 변환은 [PBR 재질](../overview/features/pbr-materials.md)을 만듭니다. 모든 원본 파일 형식(예: FBX)은 자체 규칙을 사용하여 재질을 정의하므로 이러한 규칙은 Azure Remote Rendering의 PBR 재질 매개 변수에 매핑되어야 합니다. 

이 문서에서는 원본 자산의 재질을 런타임 재질로 변환하는 데 사용되는 정확한 매핑을 나열합니다.

## <a name="gltf"></a>glTF

Azure Remote Rendering에서는 *EmissiveFactor* 및 *EmissiveTexture* 를 제외하고 glTF 2.0 사양의 거의 모든 것이 지원됩니다.

다음 표에서 이러한 매핑을 보여줍니다.

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   roughness                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   occlusion                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode.MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode.BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

glTF의 각 텍스처에는 Azure Remote Rendering 재질에서도 지원되는 `texCoord` 값이 있을 수 있습니다.

### <a name="embedded-textures"></a>포함된 텍스처

*\*.bin* 또는 *\*.glb* 파일에 포함된 텍스처가 지원됩니다.

### <a name="supported-gltf-extension"></a>지원되는 glTF 확장

또한 Azure Remote Rendering은 기본 기능 집합에 대해 다음과 같은 glTF 확장을 지원합니다.

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): [색 재질](../overview/features/color-materials.md)에 해당합니다. 발광 재질의 경우 이 확장을 사용하는 것이 좋습니다.
* [KHR_materials_pbrSpecularGlossiness](https://kcoley.github.io/glTF/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/): metallic-roughness 텍스처 대신 diffuse-specular-glossiness 텍스처를 제공할 수 있습니다. Azure Remote Rendering 구현은 확장의 변환 수식 바로 뒤에 나옵니다.

## <a name="fbx"></a>FBX

FBX 형식은 폐쇄형이며 FBX 재질은 일반적으로 PBR 재질과 호환되지 않습니다. FBX는 다양한 고유 매개 변수 및 속성이 있는 표면에 대한 복잡한 설명을 사용하며, **일부는 Azure Remote Rendering 파이프라인에서 사용되지 않습니다**.

> [!IMPORTANT]
> Azure Remote Rendering 모델 변환 파이프라인은 **FBX 2011 이상** 만 지원합니다.

FBX 형식은 재질에 대한 보수적 접근 방식을 정의하며, 공식 FBX 사양에는 두 가지 유형만 있습니다.

* *Lambert* - 이미 상당히 오래 일반적으로 사용되지는 않지만 변환 시 Phong으로 변환에 의해 계속 지원됩니다.
* *Phong* - 거의 모든 재질 및 대부분의 콘텐츠 도구가 이 유형을 사용합니다.

Phong 모델이 더 정확하며 FBX 재질의 유일한 모델로 사용됩니다. 이하에서는 이를 FBX 재질이라고 합니다.

> Maya에서는 PBR 및 Stingray 재질 유형에 대한 사용자 지정 속성을 정의하여 두 가지 FBX용 사용자 지정 확장을 사용합니다. 이러한 세부 정보는 FBX 사양에 포함되지 않으므로 현재 Azure Remote Rendering에서 지원되지 않습니다.

FBX 재질은 Diffuse-Specular-SpecularLevel 개념을 사용하므로 확산 텍스처에서 albedo 맵으로 변환하려면 다른 매개 변수를 계산하여 확산에서 뺄 수 있도록 해야 합니다.

> FBX의 모든 색 및 텍스처는 sRGB 공간(감마 공간이라고도 함)에 있지만, Azure Remote Rendering은 시각화 중에 선형 공간에서 작동하고 프레임의 끝에서 모든 항목을 다시 sRGB 공간으로 변환합니다. Azure Remote Rendering 자산 파이프라인은 모든 항목을 선형 공간으로 변환하여 렌더러에 준비된 데이터로 보냅니다.

이 표에서는 텍스처가 FBX 재질에서 Azure Remote Rendering 재질로 매핑되는 방식을 보여줍니다. 그중 일부는 직접 사용되지 않고 수식에 참여하는 다른 텍스처(예: 확산 텍스처)와 함께 사용됩니다.

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Occlusion Map   |
| DiffuseColor | Albedo, Metalness에 사용됩니다. |
| TransparentColor | Albedo의 알파 채널에 사용됩니다. |
| TransparencyFactor | Albedo의 알파 채널에 사용됩니다. |
| 불투명도 | Albedo의 알파 채널에 사용됩니다. |
| SpecularColor | Albedo, Metalness, Roughness에 사용됩니다. |
| SpecularFactor| Albedo, Metalness, Roughness에 사용됩니다. |
| ShininessExponent | Albedo, Metalness, Roughness에 사용됩니다. |
| NormalMap | NormalMap |
| Bump | NormalMap으로 변환됩니다. |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

위의 매핑은 많은 가정이 필요하므로 재질 변환의 가장 복잡한 부분입니다. 아래에서 두 가지 가정을 설명합니다.

아래와 같은 정의가 사용됩니다.

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 +  `Specular`.Green ∗ 0.7154 + `Specular`.Blue ∗ 0.0721
* `DiffuseBrightness` = 0.299 * `Diffuse`.Red<sup>2</sup> + 0.587 * `Diffuse`.Green<sup>2</sup> + 0.114 * `Diffuse`.Blue<sup>2</sup>
* `SpecularBrightness` = 0.299 * `Specular`.Red<sup>2</sup> + 0.587 * `Specular`.Green<sup>2</sup> + 0.114 * `Specular`.Blue<sup>2</sup>
* `SpecularStrength` = max(`Specular`.Red, `Specular`.Green, `Specular`.Blue)

SpecularIntensity 수식은 [여기](https://en.wikipedia.org/wiki/Luma_(video))에서 가져옵니다.
이 [사양](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)에서는 밝기 수식을 설명합니다.

### <a name="roughness"></a>거칠기

`Roughness`는 [이 수식](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)을 사용하여 `Specular` 및 `ShininessExponent`에서 계산됩니다. 수식은 Phong 반사 지수에서 Roughness 근사값을 계산합니다.

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness`는 [glTF 사양의 이 수식](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)을 사용하여 `Diffuse` 및 `Specular`에서 계산됩니다.

여기서의 개념은 Ax<sup>2</sup> + Bx + C = 0 식을 푸는 것입니다.
기본적으로 유전체 표면에서는 빛의 약 4%가 반사되고 나머지는 확산됩니다. 금속성 표면에서는 빛이 확산되지 않고 모두 반사됩니다.
이 수식에는 광택 플라스틱 표면과 광택 금속 표면을 구분할 수 있는 방법이 없기 때문에 몇 가지 단점이 있습니다. 대부분의 경우 표면에는 금속 속성이 있으므로 흐리고 은은한 수고기 표면이 예상대로 보이지 않을 수 있다고 가정합니다.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`는 `Diffuse`, `Specular` 및 `Metalness`에서 계산됩니다.

Metalness 섹션에 설명한 대로 유전체 표면에서는 빛의 약 4%가 반사됩니다.  
여기서의 개념은 `Metalness` 값을 인수로 사용하여 `Dielectric` 색과 `Metal` 색 사이를 선형 보간하는 것입니다. Metalness가 `0.0`이면 반사에 따라 진한 색이 되거나(반사가 많은 경우) 확산이 변경되지 않습니다(반사가 없는 경우). Metalness 값이 클 경우 반사 색 때문에 확산 색이 사라집니다.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` 는 위의 수식에 의해 계산되었지만 알파 채널에는 더 많은 계산이 필요합니다. FBX 형식은 투명성에 대해 모호하며 이를 정의하는 여러 가지 방법이 있습니다. 콘텐츠 도구마다 다양한 방법을 사용합니다. 여기서의 개념은 이러한 방법을 하나의 수식으로 통합하는 것입니다. 그러나 자산이 공통된 방법으로 생성되지 않은 경우 일부 자산이 투명하게 잘못 표시됩니다.

이는 `TransparentColor`, `TransparencyFactor`, `Opacity`에서 계산됩니다.

`Opacity`가 정의된 경우 직접 사용합니다. `AlbedoAlpha` = `Opacity`  
`TransparencyColor`가 정의된 경우 `AlbedoAlpha` = 1.0 - ((`TransparentColor`.Red + `TransparentColor`.Green + `TransparentColor`.Blue) / 3.0)  
`TransparencyFactor`가 정의된 경우 `AlbedoAlpha` = 1.0 - `TransparencyFactor`

최종 `Albedo` 색에는 `AlbedoRGB`와 `AlbedoAlpha`를 조합한 4개의 채널이 있습니다.

### <a name="summary"></a>요약

요약하면 `Albedo`는 `Specular`가 0에 가까운 경우 원래 `Diffuse`와 매우 근사합니다. 그렇지 않으면 표면이 금속 표면처럼 보이고 확산 색이 사라집니다. `ShininessExponent`가 충분히 크고 `Specular`가 밝으면 표면이 더 매끄럽고 반사적입니다. 그렇지 않으면 표면이 거칠게 표시되고 환경이 거의 반사되지 않습니다.

### <a name="known-issues"></a>알려진 문제

* 현재 수식은 색이 지정된 간단한 기하 도형에는 제대로 작동하지 않습니다. `Specular`가 충분히 밝은 경우 모든 기하 도형은 색이 없는 금속성 반사 표면이 됩니다. 여기서 해결 방법은 `Specular`를 원래 값에서 30% 낮추거나 변환 설정 [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)을 사용하는 것입니다.
* 최근에 PBR 재질이 `Maya` 및 `3DS Max` 콘텐츠 생성 도구에 추가되었습니다. 이러한 도구는 사용자 지정 사용자 정의 블랙 박스 속성을 사용하여 FBX에 이를 전달합니다. Azure Remote Rendering 문서화되지 않고 형식이 폐쇄형 소스이므로 해당 속성을 읽지 않습니다.

## <a name="next-steps"></a>다음 단계

* [모델 변환](../how-tos/conversion/model-conversion.md)
* [모델 변환 중 재질 재정의](../how-tos/conversion/override-materials.md)

---
title: 개체 검색 문제 해결
description: Azure 개체 앵커를 사용 하 여 개체를 검색할 때 발생 하는 문제 해결
author: craigktreasure
manager: rgarcia
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: b70babbf3f105903cc7b14d175908b972bde9158
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601998"
---
# <a name="troubleshooting-object-detection"></a>개체 검색 문제 해결

이 문서에서는 이미 3D 모델을 Azure 개체 앵커 검색 모델로 변환 하 고 응용 프로그램에 모델을 성공적으로 로드 했다고 가정 합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계

* 최상의 환경을 위해 검색 하는 모델이 지원 되는 (1-10 미터) 크기에 포함 되어 있는지 확인 합니다.
* 공간의 질감이 충분하도록 몇 개의 포스터를 추가합니다.
* [아래](#remove-holograms-to-reset-the-map)설명 된 대로 현재 holograms을 제거 하 여 맵을 다시 설정 합니다.
* 개체를 보다 완전하게 스캔합니다.
* 개체의 전체 또는 대부분을 포함하는 근접한 경계 상자를 검색 영역으로 제공합니다.
* 공간 매핑 캐시를 지우고 개체를 다시 검사 합니다.
* [아래](#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct)설명 된 대로 모델 변환 중에 올바른 중력 방향 및 자산 차원이 사용 되었는지 확인 합니다.
* [아래](#visually-inspect-the-detection-models-mesh)에 설명 된 대로 검색 모델을 시각적으로 검사 합니다.
* [아래](#adjust-object-query-values)에 설명 된 대로 모델 쿼리 값을 조정 합니다.
* [아래](#capture-diagnostics)설명 된 대로 진단 정보를 캡처합니다.

### <a name="remove-holograms-to-reset-the-map"></a>Holograms을 제거 하 여 맵을 다시 설정 합니다.

다음 문제 중 하나를 사용 하 여 검색 되는 개체가 표시 되 면 맵을 제거 하 고 다시 설정 하 여 문제를 해결할 수 있습니다.
* 반전 방향
* 잘못 된 포즈
* 기울어진 모델

holograms를 제거 하 고 맵을 다시 설정 하려면 **설정** 앱을 열고 **시스템**  ->  **홀로그램스** 으로 이동 합니다. 그런 다음 **모두 제거 holograms** 를 선택 하 여 새 맵으로 시작 합니다.

Holograms를 지우면 최근에 이동 된 경우 현재 위치에서 개체가 제대로 검색 될 수 있습니다.

HoloLens를 입고 하는 환경에서 이동 하 여 환경을 다시 검사 합니다. 1-2 미터에서 몇 번 검색 하려는 개체를 살펴봅니다.

### <a name="ensure-the-gravity-direction-and-asset-dimension-unit-are-correct"></a>무게 방향 및 자산 차원 단위가 올바른지 확인 하세요.

개체 앵커 변환 SDK를 사용 하 여 변환을 위해 3D 모델을 제출 하는 경우 ( [여기](../quickstarts/get-started-model-conversion.md)참조) 3d 모델에 올바른 중력 방향 ( `Gravity` )과 측정 단위 ()를 입력 해야 `AssetDimensionUnit` 합니다. 이러한 값이 올바르지 않으면 개체 앵커가 개체를 올바르게 검색 하지 못하는 것일 수 있습니다.

무게 방향은 지구를 가리키는 아래쪽 벡터입니다. CAD 모델의 경우 중력 방향은 일반적으로 위쪽 방향의 반대입니다. 예를 들어, 대부분의 경우 +Z는 위쪽 방향을 나타내며 -Z 또는 `Vector3(0.0, 0.0, -1.0)`가 중력 방향을 나타냅니다. 또한 무게를 결정할 때 런타임에 모델이 표시 되는 방향을 고려해 야 합니다. 플랫 표면에서 실제 세계의 한도를 검색 하려는 경우에는 중력이 일 수 있습니다 `Vector3(0.0, 0.0, -1.0)` . 그러나 의자가 45도 기울어 있는 경우 중력은 `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)`이 될 수 있습니다.

무게 방향은 [MeshLab](http://www.meshlab.net/)와 같은 3d 렌더링 도구를 사용 하 여 결정할 수 있습니다.

측정 단위는 모델의 배율을 나타냅니다. 지원되는 단위는 **Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit** 열거형을 사용하여 찾을 수 있습니다.

[여기](../visualize-converted-model.md) 에 설명 된 지침에 따라 Unity에서 검색 모델을 시각화 하 여 무게 방향과 배율이 올바른지 시각적으로 확인할 수도 있습니다.

### <a name="visually-inspect-the-detection-models-mesh"></a>검색 모델의 메시를 시각적으로 검사

경우에 따라 방향, 규모 또는 기능 문제를 확인할 수 있도록 검색 모델의 메시를 시각적으로 검사 하는 것이 유용할 수 있습니다. Unity에서 변환 된 모델을 시각화 하려면 [여기](../visualize-converted-model.md) 의 지침을 따르세요.

### <a name="adjust-object-query-values"></a>개체 쿼리 값 조정

* 검색 속도 및 정확도를 향상하려면 이상적으로 전체 개체를 포함하는 검색 영역을 제공합니다.
* 기본값은 `ObjectQuery.MinSurfaceCoverage` 종종 충분 하지만 더 작은 값을 사용 하 여 더 빠른 검색을 수행할 수 있습니다.
* 개체가 오른쪽에 있을 것으로 예상 되는 경우에는 작은 값을 사용 `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` 합니다.
* 앱은 항상 `1:1` 개체 모델을 사용하여 검색해야 합니다. 예측 축적은 이상적으로 1% 오차 내에서 1에 근접해야 합니다. 앱은 `ObjectQuery.MaxScaleChange`를 `0` 또는 `0.1`로 설정하여 축적 예측을 해제 또는 설정하고 정성적으로 인스턴스 포즈를 평가할 수 있습니다.
* 자세한 내용은 [어려운 개체를 검색하는 방법](../detect-difficult-object.md)을 참조하세요.

### <a name="capture-diagnostics"></a>진단 캡처

응용 프로그램은 [ObjectDiagnosticsSession](../concepts/sdk-overview.md#objectdiagnosticssession) 개체를 사용 하 여 진단 보관을 캡처하고 저장할 수 있습니다.

[MRTK를 사용 하는 Unity 샘플 앱](../quickstarts/get-started-unity-hololens-mrtk.md) 은 **tempstate** 폴더에 진단을 기록 합니다. <a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">손 메뉴</a>를 열고 **추적 시작** 을 선택한 다음 검색 시도를 재현 하 여 진단 세션을 시작 하 고 **추적 중지** 를 선택 하 여 진단 보관 파일을 저장할 수 있습니다. 그런 다음 [Windows 장치 포털](/windows/mixed-reality/develop/platform-capabilities-and-apis/using-the-windows-device-portal) 을 사용 하 여 앱의 **tempstate** 폴더에서 진단 아카이브를 검색할 수 있습니다.

그런 다음 문제를 디버그 하는 데 도움이 되도록 진단 아카이브를 us와 공유할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문제 해결 가이드에서는 Azure 개체 앵커를 사용 하 여 물리적 개체의 검색 문제를 해결 하는 방법을 알아보았습니다.
다음은 몇 가지 관련 문서입니다.

> [!div class="nextstepaction"]
> [어려운 개체를 검색하는 방법](../detect-difficult-object.md)

> [!div class="nextstepaction"]
> [Object Anchors 모델을 시각화하는 방법](../visualize-converted-model.md)

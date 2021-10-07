---
title: 어려운 개체를 검색하는 방법
description: 어려운 개체를 검색하도록 구성할 수 있는 메커니즘을 설명합니다.
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 09/08/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: cbdf29aa7bb9c342e08b64af0745c689910b26bd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791976"
---
# <a name="how-to-detect-a-difficult-object"></a>어려운 개체를 검색하는 방법

경우에 따라 개체를 검색하기가 더 어려운 것으로 확인될 수 있습니다. 예를 들면 다음과 같습니다.

- 개체가 벽에 기대어 있으므로 큰 노출 영역에 액세스할 수 없는 경우
- 개체가 너무 커서 주변을 도는 데 너무 오래 걸리는 경우
- 디바이스 센서에서 개체의 표면이 검색되지 않는 경우

## <a name="adjusting-object-query-values"></a>개체 쿼리 값 조정

이러한 상황에서 도움이 될 수 있는 Azure Object Anchors SDK에서 제공하는 몇 가지 메커니즘은 다음과 같습니다.

- `ObjectQuery.MinSurfaceCoverage` 속성 개체 인스턴스를 참 긍정으로 간주하는 데 필요한 최소 노출 영역 범위 비율을 나타냅니다. 0에서 1.0(0% ~ 100%를 나타냄) 범위를 허용합니다. 기본 설정은 개체마다 다릅니다. 노출 영역이 클수록 필요한 최소 적용 범위는 작아집니다. 대부분의 상황에서는 있는 그대로 적용됩니다. 그러나 어려운 개체를 검색하는 경우에는 이 속성의 값을 줄여 개체를 검색해야 하는 범위 크기를 줄이는 것이 좋습니다.

- `ObjectQuery.MaxScaleChange` 속성 원본 모델이 검색할 개체에 대해 `1:1` 스케일이 아닌 경우 이 설정을 조정할 수 있습니다. 0에서 1.0(0% ~ 100%를 나타냄) 범위를 허용합니다. 기본 설정인 0을 사용하면 `1:1` 스케일 매핑이 필요한 스케일 예측이 사용하지 않도록 설정됩니다. 예를 들어, 이 속성을 10%로 설정하면 스케일 예측이 사용하도록 설정되고, 모델 스케일이 개체에 대해 `1:1`이 아닌 경우 유연성을 높일 수 있습니다.

- `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` 속성 개체의 위쪽 방향과 중력 사이의 최대 각도(도)를 나타냅니다. 범위는 0에서 180까지입니다. 즉, 원래 모델을 기준으로 개체의 기울기를 나타냅니다. 기본 설정인 3도에서 기울기를 늘려 개체 기울기가 원본 모델과 일치하지 않는 경우 더 많은 유연성을 허용할 수 있습니다.

- `ObjectQuery.IsExpectedToBeStandingOnGroundPlane` 속성 이 값은 개체가 지반면에 서 있어야 하는지 여부를 나타내는 부울입니다. 기본값은 false입니다. 개체가 지반면에 있는 경우 검색 속도를 높이기 위해 true로 전환할 수 있습니다.

- `ObjectQuery.SearchAreas` 속성 개체를 찾을 지역의 컬렉션을 나타냅니다. 모든 개체 또는 대부분의 개체를 포함하면서 좀 더 좁은 검색 영역을 제공하면 검색 속도와 정확도가 향상됩니다. 다음 중 하나를 선택할 수 있습니다.

  - `ObjectSearchArea.FromOriented`를 사용하여 방향이 지정된 경계 상자 선택
  - `ObjectSearchArea.FromFieldOfView`를 사용하여 보기 필드 선택
  - `ObjectSearchArea.FromLocation`을 사용하여 위치 선택
  - `ObjectSearchArea.FromSphere`를 사용하여 구 선택

자세한 내용은 [Unity](/dotnet/api/microsoft.azure.objectanchors.objectquery)또는 [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt/objectquery)에 대한 `ObjectQuery` 클래스를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문제 해결 가이드에서는 개체를 검색하기 어려운 검색 문제를 해결하는 방법을 알아보았습니다.
다음은 몇 가지 관련 문서입니다.

> [!div class="nextstepaction"]
> [개체 검색 문제 해결](./troubleshoot/object-detection.md)

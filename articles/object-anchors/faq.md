---
title: 질문과 대답
description: Azure Object Anchors 서비스에 대한 FAQ입니다.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 18069157b4c7f38216c01649a33ed5f999dc7577
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638390"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Azure Object Anchors FAQ

Azure Object Anchors를 사용하면 애플리케이션이 3D 모델을 사용하여 실제 세계의 개체를 검색하고 해당 6-DoF 포즈를 예측할 수 있습니다.

자세한 내용은 [Azure Object Anchors 개요](overview.md)를 참조하세요.

## <a name="product-faq"></a>제품 FAQ
**Q: 사용해야 하는 개체에 대한 권장 사항은 무엇인가요?**

**A:** 개체에 대해 다음 속성을 권장합니다.

* 각 치수가 1~10미터
* 비대칭이고 기하 도형 변화가 충분
* 낮은 반사율(무광택 표면), 밝은 색상
* 고정 개체
* 굴절이 없거나 적음
* 어수선한 물체가 없거나 최소화된 명확한 배경
* 스캔한 개체가 학습한 모델과 1:1 일치

**Q: 모델 변환을 위해 처리할 수 있는 최대 개체 치수는 얼마인가요?**

**A:** CAD 모델의 각 치수는 10미터 미만이어야 합니다. 자세한 내용은 [자산 요구 사항](overview.md)을 참조하세요.

**Q: 변환을 위해 처리할 수 있는 최대 CAD 모델 크기는 얼마인가요?**

**A:** 모델 파일 크기는 150MB보다 작아야 합니다. 자세한 내용은 [자산 요구 사항](overview.md)을 참조하세요.

**Q: 지원되는 CAD 형식은 무엇입니까?**

**A:** 현재 `fbx`, `ply`, `obj`, `glb` 및 `gltf` 파일 형식을 지원합니다. 자세한 내용은 [자산 요구 사항](overview.md)을 참조하세요.

**Q: 모델 변환 서비스에 필요한 중력 방향과 단위는 무엇인가요?**

**A:** 중력 방향은 지구를 가리키는 아래쪽 벡터이며 측정 단위는 모델의 배율을 나타냅니다. 모델을 변환할 때 [중력 방향과 자산 차원 단위가 올바른지 확인](./troubleshoot/object-detection.md#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct)해야 합니다.

**Q: CAD 모델을 변환하는 데 얼마나 걸리나요?**

**A:** `ply` 모델의 경우 일반적으로 3~15분입니다. 다른 형식으로 모델을 제출하는 경우 파일 크기에 따라 15~60분이 소요될 수 있습니다.

**Q: 모델 변환 오류에서 어떻게 복구하나요?**

**A:** 실패한 모델 변환 작업으로 인해 발생할 수 있는 다양한 오류 코드와 각 오류를 처리하는 방법에 대한 자세한 내용은 [변환 오류 코드 페이지](.\model-conversion-error-codes.md)를 참조하세요.

**Q: Object Anchors가 지원하는 디바이스는 무엇인가요?**

**A:** HoloLens 2.

**Q: HoloLens를 실행해야 하는 OS 빌드는 무엇인가요?**

**A:** 2020년 3월 12일 이후에 릴리스된 OS 빌드 18363.720 이상.

  자세한 내용은 [Windows 10 2020년 3월 12일 업데이트](https://support.microsoft.com/help/4551762)를 참조하세요.

**Q: HoloLens에서 개체를 검색하는 데 얼마나 걸리나요?**

**A:** 개체 크기와 스캔 프로세스에 따라 달라집니다. 검색 시간을 단축하려면 철저한 스캔에 대한 모범 사례를 따르세요.
각 치수가 2미터 이하인 작은 개체의 경우 몇 초 내에 검색을 완료할 수 있습니다. 자동차와 같은 대형 개체의 경우, 신뢰할 수 있는 검색을 얻으려면 사용자가 개체를 완전히 빙 돌아야 합니다. 즉, 검색은 수십 초까지 걸릴 수 있습니다.

**Q: HoloLens 애플리케이션에서 Object Anchors를 사용하는 경우 모범 사례는 무엇인가요?**

**A:**

 1. 정확한 렌더링을 얻으려면 안구 보정을 수행합니다.
 2. 공간에 풍부한 시각적 질감과 양호한 조명이 있어야 합니다.
 3. 개체를 고정 상태로 유지하고 가능하면 주위에 어수선한 물체가 없어야 합니다.
 4. 필요에 따라 HoloLens 디바이스에서 [공간 매핑](/windows/mixed-reality/spatial-mapping) 캐시를 제거합니다.
 5. 개체를 빙 돌면서 스캔합니다. 개체가 대부분 관찰되는지 확인합니다.
 6. 개체를 모두 포함할 수 있을 만큼 크게 검색 영역을 설정합니다.
 7. 검색하는 동안 개체는 고정 상태를 유지해야 합니다.
 8. 개체 검색을 시작하고 예측 포즈를 기준으로 렌더링을 시각화합니다.
 9. 포즈가 안정적이고 정확해지면 검색된 개체를 잠그거나 추적을 중지하여 배터리를 절약합니다.

**Q: Object Anchors Unity SDK로 작업할 수 있으려면 HoloLens Unity 애플리케이션에서 어떤 버전의 MRTK(Mixed Reality Toolkit)를 사용해야 하나요?**

**A:** Azure Object Anchors Unity SDK는 어떤 방식으로도 Mixed Reality Toolkit에 종속되지 않습니다. 즉, 원하는 버전을 자유롭게 사용할 수 있습니다. 자세한 내용은 [Unity용 MRTK 소개](/windows/mixed-reality/develop/unity/mrtk-getting-started)를 참조하세요.

**Q: 예측 포즈는 얼마나 정확한가요?**

**A:** 개체 크기, 재질, 환경 등에 따라 달라집니다. 작은 개체의 경우 예측 포즈는 2cm 오차 내에 있을 수 있습니다. 자동차와 같은 대형 개체의 경우 오차는 최대 2~8cm일 수 있습니다.

**Q: Object Anchors가 개체 이동을 처리할 수 있나요?**

**A:** **연속 이동** 또는 **동적** 개체는 지원하지 않습니다. 물리적으로 이동한 후에는 공간의 완전히 새로운 위치에 있는 개체를 지원하지만 이동되는 동안에는 추적할 수 없습니다.

**Q: Object Anchors가 변형 또는 굴절을 처리할 수 있나요?**

**A:** 부분적으로 변형 또는 굴절로 인한 개체 모양 또는 기하 도형 변화 크기에 따라 달라집니다. 개체의 기하 도형이 크게 변화하면 사용자는 해당 구성에 대해 다른 모델을 만들어 검색에 사용할 수 있습니다.

**Q: Object Anchors에서 동시에 검색할 수 있는 모델은 몇 개인가요?**

**A:** 현재 최상의 사용자 환경을 보장하기 위해 한 번에 세 개의 모델을 검색하도록 지원하지만 이를 제한하지 않습니다.

**Q: Object Anchors가 동일한 개체 모델의 여러 인스턴스를 검색할 수 있나요?**

**A:** 예, 최상의 사용자 환경을 보장하기 위해 동일한 모델 유형의 인스턴스를 최대 3개까지 검색하도록 지원하지만 이를 제한하지 않습니다. 검색 영역당 하나의 개체 인스턴스를 검색할 수 있습니다. `ObjectQuery.SearchAreas.Add`를 호출하면 더 많은 검색 영역을 쿼리에 추가하여 더 많은 인스턴스를 검색할 수 있습니다. 여러 쿼리를 통해 `ObjectObserver.DetectAsync`를 호출하여 여러 모델을 검색할 수 있습니다.

**Q: Object Anchors 런타임이 개체를 검색할 수 없는 경우 어떻게 해야 하나요?**

**A:** 개체가 제대로 검색되지 못할 수 있는 환경, 모델 변환 구성, 쿼리 설정 등의 많은 요인이 있습니다. [개체 검색 문제를 해결](./troubleshoot/object-detection.md)하는 방법에 대해 자세히 알아보세요.

**Q: 개체 쿼리 매개 변수는 어떻게 선택하나요?**

**A:** [검색하기 어려운 개체](./detect-difficult-object.md)에 대한 몇 가지 [일반적인 지침](./troubleshoot/object-detection.md#adjust-object-query-values) 및 자세한 가이드가 있습니다.

**Q: HoloLens에서 Object Anchors 진단 데이터를 가져오려면 어떻게 하나요?**

**A:** 애플리케이션은 진단 데이터 보관의 위치를 지정할 수 있습니다. Object Anchors 샘플 앱은 **TempState** 폴더에 진단 데이터를 기록합니다.

**Q: Object Anchors Unity SDK에서 반환한 포즈를 사용할 때 원본 모델이 물리적 개체와 정렬되지 않는 이유는 무엇인가요?**

**A:** Unity는 개체 모델을 가져올 때 좌표계를 변경할 수 있습니다. 예를 들어 Object Anchors Unity SDK는 오른손 좌표계에서 왼손 좌표계로 변환할 때 Z축을 반전하지만 Unity는 X축 또는 Y축에 추가 회전을 적용할 수 있습니다. 개발자는 좌표계를 시각화하고 비교하여 이 추가 회전을 결정할 수 있습니다.

**Q:** 2D를 지원하나요?

**A:** 기하 도형 기반이므로 3D만 지원합니다.

**Q: 동일한 모델을 색상에 따라 구분할 수 있나요?**

**A:** 알고리즘이 기하 도형 기반이므로 동일한 모델은 검색에서 색상에 따라 다르게 동작하지 않습니다.

**Q: 인터넷에 연결하지 않고 Object Anchors를 사용할 수 있나요?**

**A:**
* 모델 변환 및 학습의 경우 클라우드에서 수행되므로 연결이 필요합니다.
* 런타임 세션은 전적으로 디바이스 기반이고 모든 계산이 HoloLens 2에서 수행되므로 연결이 필요하지 않습니다.

## <a name="privacy-faq"></a>개인 정보 보호 FAQ
**Q: Azure Object Anchors는 데이터를 어떻게 저장하나요?**

**A:** Microsoft 관리되는 데이터 암호화 키를 사용하여 미사용 암호화된 시스템 메타데이터만 저장합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Object Anchors를 사용할 때 최상의 결과를 얻기 위한 일반적인 질문에 대한 몇 가지 대답을 알아보았습니다.
다음은 몇 가지 관련 문서입니다.

> [!div class="nextstepaction"]
> [모범 사례](./best-practices.md)

> [!div class="nextstepaction"]
> [개체 검색 문제 해결](./troubleshoot/object-detection.md)

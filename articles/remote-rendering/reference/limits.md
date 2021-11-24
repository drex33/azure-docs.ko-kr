---
title: 제한 사항
description: SDK 기능에 대한 코드 제한 사항
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 0681ee7113cb8c3f7d9c53a72ecd13ed80f14382
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964854"
---
# <a name="limitations"></a>제한 사항

여러 기능에는 크기, 수 또는 기타 제한 사항이 있습니다.

## <a name="azure-frontend"></a>Azure Frontend

프런트 엔드 API(C++ 및 C#)에는 다음과 같은 제한 사항이 적용됩니다.
* 프로세스당 총 [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) 인스턴스 수: 16.
* [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient)당 총 [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) 인스턴스 수: 16.

## <a name="objects"></a>개체

* 단일 형식([엔터티](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md) 등)에 대해 허용되는 총 개체 수: 16,777,215.
* 허용되는 총 활성 평면 잘라내기: 8.

## <a name="geometry"></a>기하 도형

* **애니메이션:** 애니메이션은 [게임 개체](../concepts/entities.md)의 개별 변환을 애니메이션화하는 것으로 제한됩니다. 스키닝 또는 꼭짓점 애니메이션이 있는 골격 애니메이션은 지원되지 않습니다. 원본 자산 파일의 애니메이션 트랙은 유지되지 않습니다. 대신, 개체 변환 애니메이션은 클라이언트 코드에 의해 구동되어야 합니다.
* **사용자 지정 셰이더:** 사용자 지정 셰이더 작성은 지원되지 않습니다. 기본 제공 [색 재질](../overview/features/color-materials.md) 또는 [PBR 재질](../overview/features/pbr-materials.md)만 사용할 수 있습니다.
* 자산의 **최대 고유 재질 수**: 65,535. 자동 재질 개수 감소에 대한 자세한 내용은 [재질 중복 제거](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 챕터를 참조하세요.
* **단일 텍스처의 최대 차원**: 16,384 x 16,384. 렌더러는 더 큰 질감을 사용할 수 없습니다. 변환 프로세스에서는 크기가 큰 질감을 줄일 수 있지만 일반적으로 이 제한보다 큰 질감을 처리하지 못합니다.

### <a name="overall-number-of-polygons"></a>전체 다각형 수

모든 로드된 모델의 허용 가능한 다각형 수는 [세션 관리 REST API](../how-tos/session-rest-api.md)에 전달된 VM의 크기에 따라 다릅니다.

| 서버 크기 | 최대 다각형 수 |
|:--------|:------------------|
|표준| 2천만 |
|프리미엄| 제한 없음 |

이 제한 사항에 대한 자세한 내용은 [서버 크기](../reference/vm-sizes.md) 챕터를 참조하세요.

## <a name="platform-limitations"></a>플랫폼 제한 사항

**Windows 10/11 데스크톱**

* Win32/x64는 유일하게 지원되는 Win32 플랫폼입니다. Win32/x86은 지원되지 않습니다.

**HoloLens 2**

* [PV 카메라에서 렌더링](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) 기능은 지원되지 않습니다.
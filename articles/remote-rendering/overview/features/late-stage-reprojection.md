---
title: 후기 단계 다시 프로젝션
description: 후기 단계 재투영 및 사용 방법에 대한 정보입니다.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 377e35b3107195841f49ecc8b1e5fb18fe38ed87
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658541"
---
# <a name="late-stage-reprojection"></a>후기 단계 다시 프로젝션

*LSR(후기 단계 재투영)* 은 사용자가 이동할 때 홀로그램을 안정화하는 데 도움이 되는 하드웨어 기능입니다.

정적 모델 주변을 이동할 경우 해당 모델 위치를 시각적으로 유지해야 합니다. 정적 모델이 불안정한 것으로 표시되는 경우 이 동작은 LSR 문제를 나타낼 수 있습니다. 애니메이션 또는 폭발 뷰와 같은 추가 동적 변환은 이 동작을 마스킹할 수 있습니다.

두 개의 다른 LSR 모드(즉, **평면 LSR** 또는 **깊이 LSR**) 중에서 선택할 수 있습니다. 활성 모드는 클라이언트 애플리케이션이 깊이 버퍼를 전송하는지 여부에 따라 결정됩니다.

두 LSR 모드는 별도의 제한이 있기는 하지만 홀로그램의 안정성을 향상시킵니다. 대부분의 경우 더 나은 결과를 제공하는 깊이 LSR을 사용하여 시작합니다.

## <a name="choose-lsr-mode-in-unity"></a>Unity에서 LSR 모드 선택

Unity 편집기에서 *:::no-loc text="File > Build Settings":::* 로 이동합니다. 왼쪽 아래에서 *:::no-loc text="Player Settings":::* 를 선택하고 *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* 에서 **:::no-loc text="Enable Depth Buffer Sharing":::** 이 선택되어 있는지 확인합니다.

![깊이 버퍼 공유 사용 플래그](./media/unity-depth-buffer-sharing-enabled.png)

해당 항목이 선택된 경우 앱은 깊이 LSR을 사용하고, 그렇지 않은 경우 평면 LSR을 사용합니다.

OpenXR를 사용 하는 경우 항상 깊이 버퍼를 제출 해야 합니다. 이 설정은에서 찾을 수 있습니다 *:::no-loc text="XR Plug-in Management > OpenXR":::* . 그런 다음 OpenXR 플러그 인에서 확장을 통해 reprojection 모드를 변경할 수 있습니다.

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

## <a name="depth-lsr"></a>깊이 LSR

깊이 LSR이 작동하려면 클라이언트 애플리케이션에서 LSR 중에 고려해야 하는 모든 관련 기하 도형을 비롯한 유효한 깊이 버퍼를 제공해야 합니다.

깊이 LSR은 제공된 깊이 버퍼의 내용에 따라 비디오 프레임을 안정화하려고 시도합니다. 결과적으로 투명 개체와 같이 렌더링되지 않은 콘텐츠는 LSR에서 조정할 수 없으며 불안정성 및 재투영 아티팩트가 표시될 수 있습니다.

투명 개체의 재투영 불안정을 완화하기 위해 깊이 버퍼 쓰기를 강제로 수행할 수 있습니다. [색](color-materials.md) 및 [PBR](pbr-materials.md) 재질의 경우 *TransparencyWritesDepth* 재질 플래그를 참조하세요. 그러나 이 플래그를 사용하도록 설정하면 투명/불투명 개체 상호 작용의 시각적 품질이 저하될 수 있습니다.

## <a name="planar-lsr"></a>평면 LSR

평면 LSR에는 깊이 LSR에 포함된 픽셀별 깊이 정보가 없습니다. 대신 각 프레임을 제공해야 하는 평면을 기반으로 모든 콘텐츠를 재투영합니다.

평면 LSR은 제공된 평면과 가까이 있는 개체를 가장 잘 재투영합니다. 개체가 멀수록 더 불안정합니다. 깊이 LSR은 서로 다른 수준에서 개체를 재투영할 때 더 적합한 반면 평면에 잘 정렬된 콘텐츠의 경우 평면 LSR이 더 적합할 수 있습니다.

### <a name="configure-planar-lsr-in-unity"></a>Unity에서 평면 LSR 구성

평면 매개 변수는 *포커스 지점* 이라고 하는에서 파생 됩니다. WMR를 사용 하는 경우 모든 프레임을 통해 포커스 지점을 설정 해야 `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` 합니다. 자세한 내용은 [Unity 포커스 포인트 API](/windows/mixed-reality/focus-point-in-unity)를 참조하세요. OpenXR의 경우에는 `ReprojectionSettings` 이전 섹션에 표시 된를 통해 포커스 지점을 설정 해야 합니다.
포커스 포인트를 설정하지 않으면 대체(fallback)가 선택됩니다. 하지만 자동 대체는 차선의 결과를 초래합니다.

Remote Rendering 호스트에서 계산된 항목을 기반으로 하는 것이 적합할 수 있지만 포커스 포인트를 직접 계산할 수 있습니다. `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint`를 호출하면 됩니다.

일반적으로 클라이언트와 호스트는 모두 클라이언트의 UI 요소와 같이 다른 쪽에서 인식되지 않는 콘텐츠를 렌더링합니다. 따라서 원격 포커스 포인트를 로컬로 계산된 것과 결합하는 것이 적합할 수 있습니다.

연속된 두 프레임에서 계산되는 포커스 포인트는 매우 다를 수 있습니다. 이를 그대로 사용하면 홀로그램이 흔들리게 표시될 수 있습니다. 이 동작을 방지하기 위해 이전 포커스 포인트와 현재 포커스 포인트를 보간하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [서버 쪽 성능 쿼리](performance-queries.md)
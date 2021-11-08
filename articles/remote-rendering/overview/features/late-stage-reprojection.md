---
title: 후기 단계 다시 프로젝션
description: 후기 단계 재투영 및 사용 방법에 대한 정보입니다.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 13a950f67053d9eaaea39e2df34df3dd12ed00c1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028729"
---
# <a name="late-stage-reprojection"></a>후기 단계 다시 프로젝션

*LSR(후기 단계 재투영)* 은 사용자가 이동할 때 홀로그램을 안정화하는 데 도움이 되는 하드웨어 기능입니다.

정적 모델 주변을 이동할 경우 해당 모델 위치를 시각적으로 유지해야 합니다. 정적 모델이 불안정한 것으로 표시되는 경우 이 동작은 LSR 문제를 나타낼 수 있습니다. 애니메이션 또는 폭발 뷰와 같은 추가 동적 변환은이 동작을 마스킹할 수 있습니다.

두 개의 다른 LSR 모드(즉, **평면 LSR** 또는 **깊이 LSR**) 중에서 선택할 수 있습니다. 두 LSR 모드는 별도의 제한이 있기는 하지만 홀로그램의 안정성을 향상시킵니다. 대부분의 경우 더 나은 결과를 제공하는 깊이 LSR을 사용하여 시작합니다.

## <a name="how-to-set-the-lsr-mode"></a>LSR 모드를 설정 하는 방법

사용 되는 LSR 모드는 클라이언트 응용 프로그램이 깊이 버퍼를 전송 하는지 여부에 따라 결정 됩니다. 깊이 버퍼를 제출 하는 경우 **깊이 lsr** 및 **평면 lsr** 를 사용 합니다.

다음 단락에서는 Unity 및 네이티브 응용 프로그램에서 각각 깊이 버퍼를 제출 하는 방법을 설명 합니다.

### <a name="unity"></a>Unity

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

### <a name="native-c-applications"></a>네이티브 c + + 응용 프로그램

깊이 버퍼를 제출 하는 것은 WMR 또는 OpenXR 버전과 관계 없이 네이티브 c + + 바인딩 코드를 완전히 제어할 수 있습니다. 를 충족 해야 하는 유일한 조건은가 호출 될 때 `GraphicsBinding::BlitRemoteFrame` 그래픽 API에 깊이 버퍼가 바인딩되어야 한다는 것입니다.

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

연속 되는 두 프레임에서 계산 되는 포커스 점은 크게 달라질 수 있습니다. 이를 그대로 사용하면 홀로그램이 흔들리게 표시될 수 있습니다. 이 동작을 방지하기 위해 이전 포커스 포인트와 현재 포커스 포인트를 보간하는 것이 좋습니다.

## <a name="reprojection-pose-modes"></a>다시 프로젝션 포즈 모드

하이브리드 렌더링을 사용 하는 일반적인 문제 범위는 다음과 같이 지정할 수 있습니다. 원격 및 로컬 내용이 서버에 의해 예측 되는 반면 로컬 포즈는 실제 현재 상태 이기 때문에 원격 및 로컬 내용이 고유 포즈 (즉, 좌표 공간) 내에 있습니다. 그러나 렌더링 프레임의 끝에서 원격 콘텐츠와 로컬 콘텐츠를 모두 정렬 하 여 화면에 표시 해야 합니다. 다음 그림에서는 로컬 및 원격 포즈를 표시 뷰포트에 비교 하 여 변환 하는 예를 보여 줍니다.

![대상 뷰포트에 관련 된 원격 및 로컬 포즈를 보여 주는 다이어그램입니다.](./media/reprojection-remote-local.png)

ARR은 위에서 설명한 LSR 모드로 orthogonally 작동 하는 두 개의 reprojection 모드를 제공 합니다. 이러한 모드를 **:::no-loc text="Remote pose mode":::** 및 라고 **:::no-loc text="Local pose mode":::** 합니다. LSR 모드와 달리 포즈 모드는 원격 및 로컬 콘텐츠를 결합 하는 방법을 정의 합니다. 이 모드를 선택 하면 런타임 성능에 대 한 로컬 콘텐츠의 시각적 품질을 거래 하므로 응용 프로그램에서 적절 한 옵션을 신중 하 게 고려해 야 합니다. 아래의 고려 사항을 참조 하세요.

### :::no-loc text="Remote pose mode":::

:::no-loc text="Remote pose mode"::: 는 ARR의 기본 모드입니다. 이 모드에서 로컬 콘텐츠는 원격 프레임의 원격 포즈를 사용 하 여 들어오는 원격 이미지 스트림의 맨 위에 렌더링 됩니다. 그런 다음 결합 된 결과가 최종 재 프로젝션을 위해 OS로 전달 됩니다. 이 방법은 하나의 재 프로젝션을 사용 하지만 최종 수정은 라운드트립 간격을 기반으로 하므로 전체 다시 프로젝션 오류가 로컬 콘텐츠에도 적용 됩니다. 결과적으로, 수정 변화량이 크면 UI 요소를 포함 하 여 로컬 기 하 도형에 상당한 왜곡이 발생할 수 있습니다.

위의 그림을 사용 하면에서 다음과 같은 변환이 적용 됩니다 :::no-loc text="Remote pose mode"::: .

![원격 포즈 모드의 단계를 다시 프로젝션 합니다.](./media/reprojection-pose-mode-remote.png)

### :::no-loc text="Local pose mode":::

이 모드에서 reprojection은 두 개의 고유한 단계로 분할 됩니다. 첫 번째 단계에서 원격 콘텐츠는 로컬 포즈 공간, 즉 로컬 콘텐츠가 기본적으로 VR/AR 장치에서 렌더링 되는 공간으로 다시 프로젝션 됩니다. 그러면 로컬 콘텐츠가 일반적인 로컬 포즈를 사용 하 여이 미리 변환 된 이미지의 맨 위에 렌더링 됩니다. 두 번째 단계에서는 최종 다시 프로젝션을 위해 결합 된 결과가 OS에 전달 됩니다. 이 두 번째 다시 프로젝션은 작은 델타만 발생 하므로 ARR이 없는 경우 사용 되는 것과 동일한 델타를 사용 합니다. 로컬 콘텐츠의 왜곡 아티팩트는 크게 완화 됩니다.

그에 따라 그림은 다음과 같습니다.

![로컬 포즈 모드의 단계를 다시 프로젝션 합니다.](./media/reprojection-pose-mode-local.png)

### <a name="performance-and-quality-considerations"></a>성능 및 품질 고려 사항

포즈 모드를 선택 하면 시각적 품질 및 성능에 영향을 미칩니다. HoloLens 2 장치에서 추가 다시 프로젝션을 수행 하는 클라이언트 쪽의 추가 런타임 비용은 :::no-loc text="Local pose mode"::: GPU 시간 프레임 당 약 1 밀리초입니다. 클라이언트 응용 프로그램이 이미 16 밀리초의 프레임 예산에 근접 한 경우에는이 추가 비용을 고려해 야 합니다. 반면에 왜곡 아티팩트에 취약 하지 않은 로컬 콘텐츠나 로컬 콘텐츠가 없는 응용 프로그램 유형도 있습니다. 이 경우 원격 콘텐츠를 다시 프로젝션 하는 :::no-loc text="Local pose mode"::: 품질이 영향을 받지 않으므로 시각적 효과를 얻을 수 없습니다.

일반적으로 사용 사례에 따라 모드를 테스트 하 고 시각적 품질의 이득을 추가 성능 오버 헤드를 정렬 하는지 여부를 확인 합니다. 또한 중요 한 Ui가 표시 되는 경우에만 로컬 모드를 사용 하도록 설정 하는 등의 방법으로 모드를 동적으로 전환할 수 있습니다.

### <a name="how-to-change-the-no-loc-textpose-mode-at-runtime"></a>런타임에를 변경 하는 방법 :::no-loc text="Pose mode":::

런타임에 모드를 변경 하는 데 사용할 수 있는 클라이언트 API는 다음과 같습니다.

```cs
RenderingSession session = ...;
session.GraphicsBinding.SetPoseMode(PoseMode.Local); // set local pose mode
```
 
```cpp
ApiHandle<RenderingSession> session = ...;
session->GetGraphicsBinding()->SetPoseMode(PoseMode::Local); // set local pose mode
```

그래픽 바인딩 개체를 사용할 수 있는 경우 언제 든 지 모드를 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [서버 쪽 성능 쿼리](performance-queries.md)
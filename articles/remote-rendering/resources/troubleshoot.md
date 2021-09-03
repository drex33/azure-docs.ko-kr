---
title: 문제 해결
description: Azure Remote Rendering 문제 해결 정보
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: bef6439ae51c6e15f7be997758acbbd3722ae4ff
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223263"
---
# <a name="troubleshoot"></a>문제 해결

이 페이지에는 Azure Remote Rendering과 관련된 일반적인 문제와 해결 방법이 나열되어 있습니다.

## <a name="client-cant-connect-to-server"></a>클라이언트가 서버에 연결할 수 없음

방화벽(디바이스, 라우터 내 등)이 [시스템 요구 사항](../overview/system-requirements.md#network-firewall)에서 언급한 포트를 차단하지 않는지 확인합니다.

## <a name="failed-to-load-model"></a>모델을 로드하지 못함

Blob 구성이 올바르지만 모델 로드(예: Unity 샘플을 통해)에 실패하면 Blob Storage가 제대로 연결되지 않았을 수 있습니다. 이는 [스토리지 계정 연결](../how-tos/create-an-account.md#link-storage-accounts) 장에 설명되어 있습니다. 올바른 연결 후 변경 사항이 적용될 때까지 최대 30분이 소요될 수 있습니다.

## <a name="cant-link-storage-account-to-arr-account"></a>스토리지 계정을 ARR 계정에 연결할 수 없음

[스토리지 계정을 연결](../how-tos/create-an-account.md#link-storage-accounts)하는 동안 Remote Rendering 계정이 나열되지 않는 경우가 가끔 있습니다. 이 문제를 해결하려면 Azure Portal에서 ARR 계정으로 이동하여 왼쪽에 있는 **설정** 그룹에서 **ID** 를 선택합니다. **상태** 가 **켬** 으로 설정되었는지 확인합니다.
![Unity 프레임 디버거](./media/troubleshoot-portal-identity.png)

## <a name="error-disconnected-videoformatnotavailable"></a>오류 '`Disconnected: VideoFormatNotAvailable`'

GPU가 하드웨어 비디오 디코딩을 지원하는지 확인합니다. [개발 PC](../overview/system-requirements.md#development-pc)를 참조하세요.

GPU가 2개인 랩톱에서 작업하는 경우 실행 중인 GPU가 기본적으로 하드웨어 비디오 디코딩 기능을 제공하지 않는 것일 수 있습니다. 이 경우에 해당한다면 앱이 다른 GPU를 사용하도록 강제해 보세요. 이 작업은 GPU 드라이버 설정에서 수행할 수 있는 경우가 많습니다.

## <a name="retrieve-sessionconversion-status-fails"></a>세션 검색/변환 상태 실패

REST API 명령을 너무 자주 보내면 서버가 제한되고 결국 실패를 반환합니다. 제한 사례의 http 상태 코드는 429입니다("요청이 너무 많음"). 일반적으로 **후속 호출 간에 5~10초** 지연이 발생합니다.

이 제한은 직접 호출될 때 REST API 호출에 영향을 줄 뿐만 아니라 `Session.GetPropertiesAsync`, `Session.RenewAsync` 또는 `Frontend.GetAssetConversionStatusAsync` 등의 C#/C++와 같은 항목에도 영향을 줄 수 있습니다.

서버 쪽 제한이 발생하는 경우 호출을 덜 자주 수행하도록 코드를 변경합니다. 서버는 1분마다 제한 상태를 초기화하므로 1분 후에 코드를 다시 실행하는 것이 안전합니다.

## <a name="h265-codec-not-available"></a>H265 코덱을 사용할 수 없음

서버가 `codec not available` 오류와 함께 연결을 거부하는 두 가지 이유가 있습니다.

**H265 코덱이 설치되지 않음:**

먼저 시스템 요구 사항의 [소프트웨어](../overview/system-requirements.md#software) 섹션에 설명된 대로 **HEVC 비디오 확장** 을 설치합니다.

그래도 문제가 발생하면 사용하는 그래픽 카드가 H265을 지원하고 최신 그래픽 드라이버가 설치되었는지 확인합니다. 공급업체별 정보는 시스템 요구 사항의 [개발 PC](../overview/system-requirements.md#development-pc) 섹션을 참조하세요.

**코덱이 설치되었지만 사용할 수 없음:**

이 문제가 발생하는 이유는 DLL의 보안 설정이 올바르지 않기 때문입니다. H265로 인코딩된 비디오를 시청할 때는 이 문제가 나타나지 않습니다. 또한 코덱을 다시 설치해도 문제가 해결되지 않습니다. 그 대신 다음 단계를 수행하세요.

1. **관리자 권한으로 PowerShell을 열고** 다음 명령을 실행합니다.

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    이 명령은 다음과 같이 코덱의 `InstallLocation`을 출력합니다.
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Windows 탐색기에서 이 폴더를 엽니다.
1. **x86** 및 **x64** 하위 폴더가 있을 것입니다. 폴더 중 하나를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
    1. **보안** 탭을 선택하고 **고급** 설정 단추를 클릭합니다.
    1. **소유자** 에 대한 **변경** 을 클릭합니다.
    1. 텍스트 필드에 **관리자** 를 입력합니다.
    1. **이름 확인** 을 클릭하고 **확인** 을 클릭합니다.
1. 다른 폴더에 대해 위의 단계를 반복합니다.
1. 두 폴더 안에 있는 각 DLL 파일에서도 위의 단계를 반복합니다. 총 4개의 DLL이 있어야 합니다.

이제 설정이 올바른지 확인하기 위해, 4개의 각 DLL에 대해 다음 작업을 수행합니다.

1. **속성 > 보안 > 편집** 을 클릭합니다.
1. 모든 **그룹/사용자** 목록을 살펴보고 각 항목의 **읽기 및 실행** 권한이 설정되었는지(**허용** 열의 확인 표시를 선택해야 함) 확인합니다.

## <a name="low-video-quality"></a>낮은 비디오 품질

네트워크 품질 때문에 또는 H265 비디오 코덱이 없어서 비디오 품질이 저하될 수 있습니다.

* [네트워크 문제 확인](#unstable-holograms) 단계를 참조하세요.
* 최신 그래픽 드라이버를 설치하기 위한 [시스템 요구 사항](../overview/system-requirements.md#development-pc)을 참조하세요.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>MRC로 녹화된 비디오는 라이브 환경의 품질을 반영하지 않습니다.

HoloLens에서 [MRC(Mixed Reality Capture)](/windows/mixed-reality/mixed-reality-capture-for-developers)를 통해 비디오를 녹화할 수 있습니다. 그러나 녹화된 비디오는 다음 두 가지 이유로 라이브 환경보다 품질이 떨어집니다.
* 비디오 프레임 속도는 60Hz가 아닌 30Hz로 제한됩니다.
* 비디오 이미지가 [후기 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md) 처리 단계를 거치지 않으므로 뚝뚝 끊어집니다.

둘 다 녹화 기술의 제한입니다.

## <a name="black-screen-after-successful-model-loading"></a>모델을 성공적으로 로드 후 나타나는 검은색 화면

렌더링 런타임에 연결하여 모델을 성공적으로 로드했지만 이후에 검은색 화면만 표시되는 경우 몇 가지 다른 원인이 있을 수 있습니다.

보다 자세한 분석을 수행하기 전에 다음 사항을 테스트하는 것이 좋습니다.

* H265 코덱이 설치되었나요? H264 코덱을 대체하는 다른 코덱이 있겠지만, 다른 코덱이 제대로 작동하지 않는 경우가 있습니다. 최신 그래픽 드라이버를 설치하기 위한 [시스템 요구 사항](../overview/system-requirements.md#development-pc)을 참조하세요.
* Unity 프로젝트를 사용하는 경우 Unity를 닫고, 프로젝트 디렉터리의 임시 *library* 및 *obj* 폴더를 삭제하고, 프로젝트를 다시 로드/빌드합니다. 특별한 이유 없이 캐시된 데이터로 인해 샘플이 올바르게 작동하지 않는 경우가 있습니다.

이러한 두 단계로 문제가 해결되지 않으면 클라이언트가 비디오 프레임을 수신하는지 여부를 확인해야 합니다. 이는 [서버 쪽 성능 쿼리](../overview/features/performance-queries.md) 챕터에서 설명한 대로 프로그래밍 방식으로 쿼리할 수 있습니다. `FrameStatistics struct`에는 수신된 비디오 프레임 수를 나타내는 멤버가 있습니다. 이 숫자가 0보다 크고 시간이 지날수록 증가하면 클라이언트가 서버에서 실제 비디오 프레임을 수신하는 것입니다. 따라서 클라이언트 쪽 문제입니다.

### <a name="common-client-side-issues"></a>일반적인 클라이언트 쪽 문제

**모델이 선택한 VM의 제한, 특히 최대 다각형 수를 초과하는 경우:**

특정 [서버 크기 제한](../reference/limits.md#overall-number-of-polygons)을 참조하세요.

**모델이 카메라 절두체 내에 없는 경우:**

모델이 올바르게 표시되지만 카메라 절두체의 외부에 위치한 경우가 자주 있습니다. 중심에서 멀리 떨어진 피벗을 사용하여 모델을 내보내면 모델이 카메라의 원거리 클리핑 거리에 의해 잘리는 것이 이 현상의 주요 원인입니다. 모델의 경계 상자를 프로그래밍 방식으로 쿼리하고 Unity를 사용하여 상자를 선 상자로 시각화하거나 해당 값을 디버그 로그에 출력하면 도움이 됩니다.

또한 변환 프로세스는 변환된 모델과 함께 [출력 json 파일](../how-tos/conversion/get-information.md)을 생성합니다. 모델 위치 지정 문제를 디버그하려면 [outputStatistics 섹션](../how-tos/conversion/get-information.md#the-outputstatistics-section)에서 `boundingBox` 항목을 확인하는 것이 좋습니다.

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

경계 상자는 3D 공간에서 `min` 및 `max` 위치(미터 단위)로 설명됩니다. 따라서 좌표 1000.0은 원점에서 1킬로미터 떨어져 있다는 뜻입니다.

이 경계 상자에 다음 두 가지 문제가 있으면 기하 도형이 보이지 않습니다.
* **상자가 중심에서 멀리 떨어져 있는 경우:** 원거리 클리핑 때문에 개체가 모두 잘립니다. 이 경우 `boundingBox` 값은 `min = [-2000, -5,-5], max = [-1990, 5,5]`와 비슷한 형태입니다. 여기서는 예를 들기 위해 x축에 큰 오프셋을 사용했습니다. 이러한 유형의 문제를 해결하려면 [모델 변환 구성](../how-tos/conversion/configure-model-conversion.md)에서 `recenterToOrigin` 옵션을 사용하도록 설정합니다.
* **상자가 가운데에 있지만 크기가 너무 큰 경우:** 카메라가 모델의 중앙에서 시작하더라도 모든 방향에서 기하도형이 잘립니다. 이 경우의 일반적인 `boundingBox` 값은 `min = [-1000,-1000,-1000], max = [1000,1000,1000]`과 비슷한 형태입니다. 이러한 유형의 문제가 발생하는 이유는 대부분 단위 배율 불일치입니다. 단위 배율을 보정하려면 [변환하는 동안 스케일링 값을 지정](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)하거나 올바른 단위로 원본 모델을 표시합니다. 런타임에 모델을 로드할 때 루트 노드에 스케일링을 적용할 수도 있습니다.

**Unity 렌더링 파이프라인에 렌더링 후크가 없는 경우:**

Azure Remote Rendering은 Unity 렌더링 파이프라인에 후크하여 비디오의 프레임 컴퍼지션과 다시 프로젝션을 수행합니다. 이러한 후크가 있는지 확인하고, 메뉴 *:::no-loc text="Window > Analysis > Frame debugger":::* 를 엽니다. 디버거를 사용하도록 설정하고 파이프라인에 `HolographicRemotingCallbackPass`에 대한 두 개의 항목이 있는지 확인합니다.

![Unity 렌더링 파이프라인](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>바둑판 패턴은 모델을 로드한 후 렌더링됩니다.

렌더링된 이미지가 ![와 같이 표시되는 경우 스크린샷은 도구 메뉴를 사용하여 검정 및 흰색 사각형의 그리드를 표시합니다.](../reference/media/checkerboard.png)
그런 다음, 렌더러에서 [표준 구성 크기에 대한 다각형 한도](../reference/vm-sizes.md)에 도달합니다. 이를 완화하려면 **프리미엄** 구성 크기로 전환하거나 보이는 다각형의 수를 줄입니다.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Unity에서 렌더링된 이미지가 거꾸로 작동합니다.

[Unity 자습서: 원격 모델 보기](../tutorials/unity/view-remote-models/view-remote-models.md)를 정확하게 따라야 합니다. 뒤집힌 이미지는 외부 렌더링 대상을 만드는 데 Unity가 필요함을 나타냅니다. 이 동작은 현재 지원되지 않으며 HoloLens 2의 성능에 큰 영향을 줍니다.

이 문제가 발생하는 이유는 MSAA, HDR 또는 post 처리를 사용할 수 있기 때문일 수 있습니다. 저품질 프로필을 선택하고 Unity에서 기본값으로 설정했는지 확인합니다. 이렇게 하려면 *편집 > 프로젝트 설정... > 품질* 로 이동합니다.

Unity 2020에서 OpenXR 플러그 인을 사용할 때 사용하도록 설정된 사후 처리에 관계없이 이 추가 외부 렌더링 대상을 만드는 URP(Universal Render Pipeline) 버전이 있습니다. 따라서 URP 버전을 수동으로 10.5.1(또는 그 이상) 이상으로 업그레이드하는 것이 중요합니다. 이는 [시스템 요구 사항](../overview/system-requirements.md#unity-2020)에 설명되어 있습니다.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Remote Rendering API를 사용하는 Unity 코드가 컴파일되지 않음

### <a name="use-debug-when-compiling-for-unity-editor"></a>Unity 편집기에 사용하기 위해 컴파일할 때 디버그 사용

Unity 솔루션의 *빌드 형식* 을 **디버그** 로 전환합니다. Unity 편집기에서 ARR을 테스트할 때 `UNITY_EDITOR` 정의는 '디버그' 빌드에서만 사용할 수 있습니다. [배포된 애플리케이션](../quickstarts/deploy-to-hololens.md)에 사용되는 빌드 형식과는 관련이 없습니다. 배포된 애플리케이션에는 '릴리스' 빌드를 사용해야 합니다.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>HoloLens 2용 Unity 샘플을 컴파일하는 동안 컴파일 오류 발생

HoloLens 2용 Unity 샘플(빠른 시작, ShowCaseApp 등)을 컴파일하려고 할 때 가짜 오류가 발생하는 경우가 있습니다. Visual Studio가 파일을 복사할 수 없다는 메시지를 표시하는데, 해당 파일이 분명 있습니다. 이 문제가 발생하는 경우:
* 프로젝트의 임시 Unity 파일을 모두 제거하고 다시 시도합니다. 즉, Unity 프로젝트를 닫고, 프로젝트 디렉터리의 임시 *library* 및 *obj* 폴더를 삭제하고, 프로젝트를 다시 로드/빌드합니다.
* 프로젝트가 있는 디렉터리의 경로가 너무 길지는 않은지 확인합니다. 파일 이름이 너무 길면 복사 단계에서 문제가 발생할 수 있습니다.
* 이렇게 해도 문제가 해결되지 않으면 MS Sense가 복사 단계를 방해하는 것일 수 있습니다. 예외를 설정하려면 명령줄에서 다음 레지스트리 명령을 실행합니다(관리자 권한 필요).
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>AudioPluginMsHRTF.dll가 누락되어 Unity 프로젝트에 대한 Arm64 빌드가 실패합니다.

Arm64에 대한 `AudioPluginMsHRTF.dll`가 버전 3.0.1의 *Windows Mixed Reality* 패키지 *(com.unity.xr.windowsmr.metro)* 에 추가되었습니다. Unity 패키지 관리자를 통해 버전 3.0.1 이상을 설치했는지 확인합니다. Unity 메뉴 모음에서 *창 > 패키지 관리자* 로 이동하여 *Windows Mixed Reality* 패키지를 찾습니다.

## <a name="native-c-based-application-does-not-compile"></a>네이티브 C++ 기반 애플리케이션이 컴파일되지 않음

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>UWP 애플리케이션 또는 DLL에 대한 '라이브러리를 찾을 수 없음' 오류

C++ NuGet 패키지 내에는 사용할 이진 버전을 정의하는 `microsoft.azure.remoterendering.Cpp.targets` 파일이 있습니다. `UWP`를 식별하기 위해 파일의 조건은 `ApplicationType == 'Windows Store'`에 대해 검사합니다. 따라서 이 형식이 프로젝트에 설정되어 있는지 확인해야 합니다. Visual Studio의 프로젝트 마법사를 통해 UWP 애플리케이션 또는 DLL을 만드는 경우에 해당합니다.

## <a name="unstable-holograms"></a>불안정한 홀로그램

머리를 움직이면 렌더링된 개체가 함께 이동하는 것처럼 보이는 경우 LSR(*후기 단계 다시 프로젝션*) 문제가 발생할 수 있습니다. 이러한 상황을 해결하는 방법에 대한 지침은 [후기 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md)의 섹션을 참조하세요.

네트워크 연결 불량 때문에, 특히 네트워크 대역폭이 부족하거나 대기 시간이 너무 길어도 홀로그램이 불안정(홀로그램 흔들림, 뒤틀림, 지터링 또는 점프)할 수 있습니다. 네트워크 연결 품질을 보여주는 좋은 지표는 [성능 통계](../overview/features/performance-queries.md) 값 `ServiceStatistics.VideoFramesReused`입니다. 재사용된 프레임은 새 비디오 프레임을 사용할 수 없어서 클라이언트 쪽에서 이전 비디오 프레임을 재사용해야만 했다는 상황을 보여줍니다. 패킷 손실, 네트워크 대기 시간의 변동 등을 예로 들 수 있습니다. `ServiceStatistics.VideoFramesReused`가 0보다 큰 상황이 자주 발생하면 네트워크에 문제가 있다는 뜻입니다.

확인할 또 다른 값은 `ServiceStatistics.LatencyPoseToReceiveAvg`입니다. 이 값은 지속적으로 100밀리초 미만이어야 합니다. 이 값을 초과하면 너무 멀리 떨어져 있는 데이터 센터에 연결되어 있을 수 있습니다.

문제를 완화하는 데 도움이 될 수 있는 방법 목록은 [네트워크 연결 지침](../reference/network-requirements.md#guidelines-for-network-connectivity)을 참조하세요.

## <a name="z-fighting"></a>z-fighting

ARR은 [Z-fighting 완화 기능](../overview/features/z-fighting-mitigation.md)을 제공하지만 z-fighting은 장면에 계속 표시될 수 있습니다. 이 가이드에서는 이러한 나머지 문제를 해결하는 방법을 설명합니다.

### <a name="recommended-steps"></a>권장되는 단계

z-fighting을 완화하기 위해 다음 워크플로를 사용합니다.

1. ARR의 기본 설정으로 장면 테스트(Z-fighting 완화)

1. [API](../overview/features/z-fighting-mitigation.md)를 통해 Z-fighting 완화 비활성화 

1. 가깝고 먼 평면의 카메라를 더 가까운 범위로 변경

1. 다음 섹션을 통해 장면 문제 해결

### <a name="investigating-remaining-z-fighting"></a>나머지 z-fighting 조사

위의 단계가 모두 사용되고 나머지 z-fighting을 사용할 수 없는 경우 z-fighting의 기본 원인을 조사해야 합니다. [z-fighting 완화 기능 페이지](../overview/features/z-fighting-mitigation.md)에 설명된 것처럼 z-fighting의 가장 큰 두 가지 이유는 깊이 범위의 끝에서 깊이 정밀도 손실과 동일 평면이 되는 동안 교차하는 표면입니다. 깊이 정밀도 손실은 수학적 우연성이며 위의 3단계를 수행하여 완화할 수 있습니다. 동일 평면 표면은 원본 자산 결함을 나타내며 원본 데이터에서 더 잘 수정되었습니다.

ARR에는 표면에서 z-fight할 수 있는지를 확인하는 기능([바둑판 강조 표시](../overview/features/z-fighting-mitigation.md))이 있습니다. z-fighting의 원인을 시각적으로 확인할 수도 있습니다. 다음 첫 번째 애니메이션은 거리의 깊이 정밀도 손실의 예제를 보여 주고, 두 번째 애니메이션은 거의 동일 평면 표면의 예제를 보여 줍니다.

![애니메이션은 거리의 깊이 정밀도 손실 예제를 보여 줍니다.](./media/depth-precision-z-fighting.gif)  ![애니메이션은 거의 동일 평면 표면의 예제를 보여 줍니다.](./media/coplanar-z-fighting.gif)

이러한 예제를 z-fighting과 비교하여 원인을 확인하거나 선택적으로 이 단계별 워크플로를 수행합니다.

1. 카메라를 z-fighting 표면 위에 배치하여 표면에서 직접 확인합니다.
1. 카메라를 표면 밖으로 뒤로 천천히 이동합니다.
1. z-fighting이 항상 표시되면 표면은 완벽하게 동일 평면입니다. 
1. z-fighting이 대부분의 시간 동안 표시되면 표면은 거의 동일 평면입니다.
1. z-fighting이 멀리에서만 표시되는 경우 원인은 깊이 정밀도의 부족입니다.

동일 평면 표면에는 여러 가지 원인이 있을 수 있습니다.

* 오류 또는 다른 워크플로 방법으로 인해 내보내기 애플리케이션에서 개체를 복제했습니다.

    각 애플리케이션 및 애플리케이션 지원에서 이러한 문제를 확인합니다.

* 전면 또는 후면 얼굴 고르기를 사용하는 렌더러에서 양면 표시를 위해 표면이 중복 및 대칭 이동됩니다.

    [모델 변환](../how-tos/conversion/model-conversion.md)을 통한 가져오기는 모델의 주 측면성을 결정합니다. 양면성은 기본값으로 가정됩니다. 표면은 양쪽에서 실제로 정확한 조명이 있는 얇은 벽으로 렌더링됩니다. 단일 측면성은 원본 자산의 플래그에 의해 묵시적이거나 [모델 변환](../how-tos/conversion/model-conversion.md) 중에 명시적으로 적용될 수 있습니다. 또한 필요에 따라 [단일 면 모드](../overview/features/single-sided-rendering.md)를 "normal"로 설정할 수 있습니다.

* 개체는 원본 자산에서 교차합니다.

     일부 표면이 겹치는 방식으로 변환되는 개체는 z-fighting도 만듭니다. ARR의 가져온 장면에서 장면 트리의 일부를 변환하는 경우에도 이 문제가 발생할 수 있습니다.

* 표면은 벽의 스티커 텍스트와 같이 터치에 대해 의도적으로 작성됩니다.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>네이티브 C++ 앱에서 다중 패스 스테레오 렌더링을 사용하는 그래픽 아티팩트

[**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image-openxr)을 호출한 후 로컬 콘텐츠에 대해 다중 패스 스테레오 렌더링 모드를 사용하는 사용자 지정 네이티브 C++ 앱(왼쪽과 오른쪽 눈에 별도의 패스로 렌더링)이 드라이버 버그를 트리거할 수 있는 경우도 있습니다. 버그로 인해 비결정적 래스터화 결함이 발생하여 개별 삼각형 또는 로컬 콘텐츠의 삼각형 부분이 무작위로 사라지게 됩니다. 성능상의 이유로 최신 단일 패스 스테레오 렌더링 기법을 사용하여 로컬 콘텐츠를 렌더링하는 것이 좋습니다(예: **SV_RenderTargetArrayIndex** 사용).

## <a name="conversion-file-download-errors"></a>변환 파일 다운로드 오류

Windows 및 서비스에 의해 적용되는 경로 길이 제한으로 인해 변환 서비스에서 Blob 스토리지의 파일을 다운로드하는 동안 오류가 발생할 수 있습니다. Blob 스토리지의 파일 경로 및 파일 이름은 178자를 초과하면 안 됩니다. 예를 들어 13자인 `models/Assets`의 `blobPrefix`를 제공합니다.

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

변환 서비스는 변환에 사용되는 파일뿐만 아니라 `blobPrefix`에 지정된 모든 파일을 다운로드합니다. 이러한 경우에는 문제의 원인이 되는 파일/폴더가 명확하지 않을 수 있으므로 `blobPrefix`에서 스토리지 계정에 포함된 모든 항목을 확인하는 것이 중요합니다. 다운로드되는 항목은 아래의 예제 입력을 참조하세요.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>다음 단계

* [시스템 요구 사항](../overview/system-requirements.md)
* [네트워크 요구 사항](../reference/network-requirements.md)
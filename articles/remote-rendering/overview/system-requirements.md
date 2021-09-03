---
title: 시스템 요구 사항
description: Azure Remote Rendering에 대한 시스템 요구 사항을 나열합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: 320f9ef1285f16a3f7b38b3b4be3e5c51eff269f
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729807"
---
# <a name="system-requirements"></a>시스템 요구 사항

이 장에는 *Azure Remote Rendering*(ARR)을 사용하기 위한 최소 시스템 요구 사항이 나열되어 있습니다.

## <a name="development-pc"></a>개발 PC

* Windows 10 버전 1903 이상
* 최신 그래픽 드라이버
* 선택 사항: [H265 하드웨어 비디오 디코더](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)(예: Unity에서 원격으로 렌더링된 콘텐츠의 로컬 미리 보기를 사용하려는 경우)

> [!IMPORTANT]
> Windows 업데이트에서 항상 최신 GPU 드라이버를 제공하는 것은 아닙니다. 다음과 같은 최신 드라이버는 GPU 제조업체의 웹 사이트를 확인하세요.
>
> * [AMD 드라이버](https://www.amd.com/en/support)
> * [Intel 드라이버](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA 드라이버](https://www.nvidia.com/Download/index.aspx)

아래 표에는 H265 하드웨어 비디오 디코딩을 지원하는 GPU가 나열되어 있습니다.

| GPU 제조업체 | 지원되는 모델 |
|-----------|:-----------|
| NVIDIA | [이 페이지의 하단에서](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) **NVDEC 지원 매트릭스** 를 확인합니다. GPU는 **H.265 4:2:0 8비트** 열에 YES가 필요합니다. |
| AMD | AMD의 [통합 비디오 디코더](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6) 버전이 6 이상인 GPU |
| Intel | Skylake 및 최신 CPU |

올바른 H265 코덱이 설치되어 있어도 코덱 DLL의 보안 속성으로 인해 코덱 초기화가 실패할 수 있습니다. [문제 해결 가이드](../resources/troubleshoot.md#h265-codec-not-available)에서는 이 문제를 해결하는 단계를 설명합니다. DLL 문제는 데스크톱 애플리케이션(예: Unity)에서 서비스를 사용하는 경우에만 발생할 수 있습니다.

## <a name="devices"></a>디바이스

Azure Remote Rendering은 현재 대상 디바이스로 **HoloLens 2** 및 Windows 데스크톱만 지원합니다. [플랫폼 제한 사항](../reference/limits.md#platform-limitations) 섹션을 참조하세요.

최신 버전의 HEVC 코덱은 대기 시간을 크게 단축시킬 수 있으므로 최신 HEVC 코덱을 사용하는 것이 중요합니다. 디바이스에 설치된 버전을 확인하려면 다음을 수행합니다.

1. **Microsoft Store** 를 시작합니다.
1. 오른쪽 위에 있는 **"..."** 단추를 클릭합니다.
1. **다운로드 및 업데이트** 를 선택합니다.
1. **디바이스 제조업체에서 HEVC 비디오 확장** 목록을 검색합니다. 이 항목이 업데이트 아래에 나열되어 있지 않은 경우 최신 버전이 이미 설치된 것입니다.
1. 나열된 코덱의 버전이 **1.0.21821.0** 이상인지 확인합니다.
1. **업데이트 가져오기** 단추를 클릭하고 설치가 완료될 때까지 기다립니다.

## <a name="network"></a>네트워크

대기 시간이 짧은 안정적인 네트워크 연결은 올바른 사용자 환경을 위해 매우 중요합니다.

[네트워크 요구 사항](../reference/network-requirements.md)은 전용 장을 참조하세요.

네트워크 문제를 해결하려면 [문제 해결 가이드](../resources/troubleshoot.md#unstable-holograms)를 참조하세요.

### <a name="network-firewall"></a>네트워크 방화벽

### <a name="sdk-version--0176"></a>SDK 버전 0.1.76 이상

Remote Rendering 가상 머신은 다음 IP 범위의 공유 IP 주소를 사용합니다.

| Name             | 지역         | IP 접두사         |
|------------------|:---------------|:------------------|
| 오스트레일리아 동부   | australiaeast  | 20.53.44.240/28   |
| 미국 동부          | eastus         | 20.62.129.224/28  |
| 미국 동부 2        | eastus2        | 20.49.103.240/28  |
| 일본 동부       | japaneast      | 20.191.165.112/28 |
| 북유럽     | northeurope    | 52.146.133.64/28  |
| 미국 중남부 | southcentralus | 20.65.132.80/28   |
| 동남 아시아   | southeastasia  | 20.195.64.224/28  |
| 영국 남부         | uksouth        | 51.143.209.144/28 |
| 서유럽      | westeurope     | 20.61.99.112/28   |
| 미국 서부 2        | westus2        | 20.51.9.64/28     |

디바이스의 방화벽, 라우터 내부의 방화벽 등 방화벽이 이러한 IP 범위 및 다음 포트 범위를 차단하지 않는지 확인합니다.

| 포트              | 프로토콜  | 허용    |
|-------------------|---------- |----------|
| 49152-65534       | TCP / UDP | 나가는 포트 |

#### <a name="sdk-version--0176"></a>SDK 0.1.76 이전 버전

디바이스의 방화벽, 라우터 내부의 방화벽 등 방화벽이 다음 포트를 차단하지 않는지 확인합니다.

| 포트              | 프로토콜 | 허용    | 설명 |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | 나가는 포트 | 초기 연결(HTTP 핸드셰이크) |
| 8266              | UDP      | 나가는 포트 | 데이터 전송 |
| 5000, 5433, 8443  | TCP      | 나가는 포트 | [ArrInspector 도구](../resources/tools/arr-inspector.md)에 필요|

## <a name="software"></a>소프트웨어

다음 소프트웨어가 설치되어 있어야 합니다.

* 최신 버전의 **Visual Studio 2019** [(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [혼합 현실용 Visual Studio 도구](/windows/mixed-reality/install-the-tools). 특히, 다음 *워크로드* 설치는 필수입니다.
  * **C++를 사용한 데스크톱 개발**
  * **UWP(유니버설 Windows 플랫폼) 개발**
* **Windows SDK 10.0.18362.0** [(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(다운로드)](https://git-scm.com/downloads)
* 선택 사항: 데스크톱 PC의 서버에서 비디오 스트림을 보려면 **HEVC 비디오 확장**[(Microsoft Store 링크)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)이 필요합니다. 스토어에서 업데이트를 확인하여 최신 버전이 설치되어 있는지 확인합니다.

## <a name="unity"></a>Unity

Unity를 사용하여 개발하려면 지원되는 버전의 Unity[(다운로드)](https://unity3d.com/get-unity/download)를 설치합니다. 설치 관리를 위해 Unity Hub를 사용하는 것이 좋습니다.
Unity 설치에 다음 모듈을 포함해야 합니다.
* **UWP** - 유니버설 Windows 플랫폼 빌드 지원
* **IL2CPP** - Windows 빌드 지원(IL2CPP)

### <a name="unity-2019"></a>Unity 2019

Unity 2019의 경우 버전 2019.3 또는 2019.4 LTS가 지원됩니다. 그러나 OpenXR 버전의 플러그 인을 사용하려면 2020 기반 버전이 필요합니다.

Unity 2019용 ARR은 Windows Mixed Reality를 위한 레거시 **기본 제공 XR** 통합과 새로운 **XR SDK** 플러그 인 프레임워크를 둘 다 지원합니다.

### <a name="unity-2020"></a>Unity 2020

Unity 2020의 경우 최신 버전의 Unity 2020.3을 사용합니다.

> [!IMPORTANT]
> OpenXR 버전의 플러그 인을 사용하는 경우 URP(‘유니버설 렌더링 파이프라인’)의 버전이 10.5.1 이상인지 확인해야 합니다. 이를 확인하려면 Unity ‘창’ 메뉴에서 ‘패키지 관리자’를 열고 ‘유니버설 RP’ 섹션인 ![유니버설 RP 버전](./media/unity-universal-rp-version-10-5-1.png)을 참조하세요.  

> [!IMPORTANT]
> **Unity 2020.3용 WMR(Windows Mixed Reality) 플러그 인** 은 현재 ARR에서 성능이 저하되었습니다. 더 나은 환경을 위해 Unity 2019.X를 유지하거나 OpenXR 버전으로 전환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
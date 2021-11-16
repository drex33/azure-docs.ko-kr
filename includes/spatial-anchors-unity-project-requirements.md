---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/12/2021
ms.author: parkerra
ms.openlocfilehash: 81dffe5fd5e5788538a0d263f305186933ee2574
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490516"
---
새 Unity 프로젝트를 시작할 때 [Unity XR 플러그 인 프레임워크와](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) 레거시 기본 제공 XR 중에서 선택할 수 있습니다.

### <a name="xr-plug-in-framework"></a>[XR 플러그 인 프레임워크](#tab/xr-plugin-framework)

XR 플러그 인 프레임워크는 ASA SDK 버전 2.9.0 이상에서만 지원됩니다. XR 플러그 인 프레임워크를 대상으로 지정하려면 플랫폼에 따라 Unity [2020.3(LTS)](https://unity3d.com/unity/whats-new/2020.3.0)및 AR Foundation [4.1.7을](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@4.1/manual/index.html) 다음 패키지와 함께 사용합니다.
> [!IMPORTANT]
> Mixed Reality OpenXR 플러그 인은 ASA SDK 버전 2.10.0 이상에서만 지원됩니다.
- Mixed Reality OpenXR 플러그 인: [1.1.2](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)
- Windows XR 플러그 인: [4.5.0](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr@4.5/manual/index.html)
- ARCore XR 플러그 인: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arcore@4.1/manual/index.html)
- ARKit XR 플러그 인: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arkit@4.1/manual/index.html)

### <a name="legacy-built-in-xr"></a>[레거시 기본 제공 XR](#tab/legacy-built-in-xr)

레거시 기본 제공 XR은 ASA SDK 버전 2.8.1 이하에서만 지원됩니다. 레거시 기본 제공 XR을 대상으로 지정하려면 플랫폼에 따라 다음 패키지와 함께 Unity [2019.4(LTS)](https://unity.com/releases/2019-lts) 및 AR Foundation [3.1.3을](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@3.1/manual/index.html) 사용합니다.
- Windows Mixed Reality: [4.2.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr.metro@4.2/manual/index.html)
- ARCore XR 플러그 인: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arcore@3.1/manual/index.html)
- ARKit XR 플러그 인: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arkit@3.1/manual/index.html)
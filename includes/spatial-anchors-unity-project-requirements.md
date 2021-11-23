---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/12/2021
ms.author: parkerra
ms.openlocfilehash: fd3d9e81e05b29f21f9953a631f425a349bb48f2
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954311"
---
새 Unity 프로젝트를 시작할 때 [UNITY XR 플러그 인 프레임 워크](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) 와 레거시 기본 제공 XR 중에서 선택할 수 있습니다.

### <a name="xr-plug-in-framework"></a>[XR 플러그 인 프레임 워크](#tab/xr-plugin-framework)

XR 플러그 인 프레임 워크는 GLOBAL.ASA SDK 버전 2.9.0 이상 에서만 지원 됩니다. XR 플러그 인 프레임 워크를 대상으로 하려면 플랫폼에 따라 다음 패키지와 함께 Unity [2020.3 (LTS)](https://unity3d.com/unity/whats-new/2020.3.0)및 AR Foundation [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@4.1/manual/index.html) 을 사용 합니다.
- Mixed Reality OpenXR 플러그 인: [1.1.2](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)
- Windows XR 플러그 인: [4.5.0](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr@4.5/manual/index.html)
- ARCore XR 플러그 인: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arcore@4.1/manual/index.html)
- ARKit XR 플러그 인: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arkit@4.1/manual/index.html)

### <a name="legacy-built-in-xr"></a>[레거시 기본 제공 XR](#tab/legacy-built-in-xr)

레거시 기본 제공 XR는 2.8.1 이전 버전 에서만 지원 됩니다. 레거시 기본 제공 XR를 대상으로 하는 경우 플랫폼에 따라 다음 패키지와 함께 Unity [2019.4 (LTS)](https://unity.com/releases/2019-lts) 및 AR Foundation [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@3.1/manual/index.html) 을 사용 합니다.
- Windows Mixed Reality: [4.2.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr.metro@4.2/manual/index.html)
- ARCore XR 플러그 인: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arcore@3.1/manual/index.html)
- ARKit XR 플러그 인: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arkit@3.1/manual/index.html)
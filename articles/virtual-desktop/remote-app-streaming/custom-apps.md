---
title: Azure Virtual Desktop 호스트 사용자 지정 앱 - Azure
description: Azure Virtual Desktop을 사용하여 사용자 지정 앱을 제공하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 35e07fad22760e6c8c87e60f77cd6d98e5db42a2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799206"
---
# <a name="how-to-host-custom-apps-with-azure-virtual-desktop"></a>Azure Virtual Desktop을 사용하여 사용자 지정 앱을 호스트하는 방법

Azure Virtual Desktop은 여러 유형의 Windows 애플리케이션을 호스트할 수 있습니다. 앱을 배포할 계획인 앱 패키지의 유형에 따라 앱을 준비하는 것이 좋습니다. 이 문서에서는 각 유형의 앱 패키지에 대해 수행해야 하는 작업을 설명합니다. 

>[!NOTE]
>다중 세션 호스트에서 앱을 호스트하는 것이 좋습니다. 또한 다중 세션 호스트에서 실행하는 동안 예상대로 작동하는지 확인하기 위해 앱을 테스트하는 것이 좋습니다. 예를 들어 테스트를 실행하여 동일한 세션 호스트에서 두 명 이상의 사용자가 동시에 앱을 성공적으로 실행할 수 있는지 확인합니다.

## <a name="msix"></a>MSIX

MSIX는 서비스의 기본 제공 [MSIX 앱 연결 기능](../app-attach-glossary.md)을 활용할 수 있으므로 Azure Virtual Desktop의 사용자 지정 앱에 권장되는 패키지 유형입니다. MSIX 형식으로 기존 Win32 애플리케이션을 다시 패키징하는 방법을 알아보려면 [기존 Win32 애플리케이션을 MSIX 형식으로 다시 패키징](/windows/application-management/msix-app-packaging-tool)을 참조하세요.

MSIX 형식으로 앱을 패키징한 후 Azure Virtual Desktop의 MSIX 앱 연결 기능을 사용하여 고객에게 앱을 제공할 수 있습니다. [MSIX 앱 연결을 사용하여 앱 배포](msix-app-attach.md)에서 앱에 대해 MSIX 앱 연결을 사용하는 방법을 알아봅니다.

## <a name="other-options-for-win32-applications"></a>Win32 애플리케이션에 대한 기타 옵션

다음 옵션을 사용하여 MSIX 형식으로 다시 패키징하지 않고 사용자에게 Win32 애플리케이션을 제공할 수도 있습니다.

### <a name="include-the-application-manually-on-session-hosts"></a>세션 호스트에 애플리케이션을 수동으로 포함

[마스터 VHD 이미지 준비 및 사용자 지정](../set-up-customize-master-image.md)의 지침에 따라 앱을 가상 머신에 사용하는 Windows 이미지의 일부로 포함합니다. 좀 더 구체적으로 말해서, [다른 애플리케이션 및 레지스트리 구성](../set-up-customize-master-image.md#other-applications-and-registry-configuration) 섹션의 지침에 따라 모든 사용자를 위한 애플리케이션을 설치합니다.

### <a name="use-microsoft-endpoint-manager-to-deploy-the-application-at-scale"></a>Microsoft Endpoint Manager를 사용하여 애플리케이션을 대규모로 배포

Microsoft Endpoint Manager를 사용하여 세션 호스트를 관리하는 경우 [Windows 10 디바이스에 앱 설치](/mem/intune/apps/apps-windows-10-app-deploy#install-apps-on-windows-10-devices)의 지침에 따라 애플리케이션을 배포할 수 있습니다. 배포의 모든 사용자가 애플리케이션에 액세스할 수 있도록 하려면 "디바이스 컨텍스트" 모드의 앱을 모든 세션 호스트에 배포해야 합니다.

### <a name="manual-installation"></a>수동 설치

앱을 수동으로 설치하는 것은 각 세션 호스트에 대해 프로세스를 반복해야 하기 때문에 권장하지 않습니다. 이 방법은 IT 전문가가 테스트 목적으로 사용하는 경우가 많습니다.

앱을 수동으로 설치해야 하는 경우 Azure Virtual Desktop 호스트 풀을 설정한 후 관리자 계정으로 세션 호스트에 원격으로 로그인해야 합니다. 그런 다음, 실제 PC와 같은 방식으로 애플리케이션을 설치합니다. 호스트 풀의 각 세션 호스트에 애플리케이션을 설치하려면 이 프로세스를 반복해야 합니다.

>[!NOTE]
>설치 프로세스에서 모든 사용자에 대해 애플리케이션을 설치하는 옵션을 제공하는 경우 해당 옵션을 선택합니다.

## <a name="microsoft-store-applications"></a>Microsoft Store 애플리케이션

현재 Azure Virtual Desktop의 원격 앱 스트리밍을 위해 Microsoft Store 앱을 사용하지 않는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결을 사용하여 앱을 패키징하고 배포하는 방법을 알아보려면 [MSIX 앱 연결을 사용하여 앱 배포](msix-app-attach.md)를 참조하세요.
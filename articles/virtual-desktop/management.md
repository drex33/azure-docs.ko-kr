---
title: Azure Virtual Desktop에 대한 Microsoft Endpoint Configuration Manager
description: Azure Virtual Desktop 환경을 관리하는 권장 방법입니다.
author: heidilohr
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c44b679daa000602aad83d98a04894c5cc267b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359108"
---
# <a name="microsoft-endpoint-manager-and-intune-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 Microsoft Endpoint Manager 및 Intune

배포 후 Azure Virtual Desktop 환경을 관리하려면 [Microsoft Endpoint Manager](https://www.microsoft.com/endpointmanager)를 사용하는 것이 좋습니다. Microsoft Endpoint Manager는 Microsoft Endpoint Configuration Manager 및 Microsoft Intune을 포함하는 통합 관리 플랫폼입니다.

## <a name="microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager

Microsoft Endpoint Configuration Manager 버전 1906 이상은 Azure Virtual Desktop 디바이스를 관리할 수 있습니다. 자세한 내용은 [Configuration Manager용 클라이언트 및 디바이스에 지원되는 OS 버전](/mem/configmgr/core/plan-design/configs/supported-operating-systems-for-clients-and-devices#windows-virtual-desktop)을 참조하세요.

## <a name="microsoft-intune"></a>Microsoft Intune

Intune은 Azure Virtual Desktop용 Windows 10 Enterprise VM(가상 머신)을 지원합니다. 지원에 대한 자세한 내용은 [Intune과 함께 Windows 10 Enterprise 사용](/mem/intune/fundamentals/windows-virtual-desktop)을 참조하세요.

Azure Virtual Desktop의 Windows 10 Enterprise 다중 세션 VM에 대한 Intune 지원은 현재 공개 미리 보기로 제공됩니다. 공개 미리 보기 버전이 현재 지원하는 기능을 확인하려면 [Intune에서 Windows 10 Enterprise 다중 세션 사용](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)을 확인하세요.

## <a name="licensing"></a>라이선스

[Microsoft Endpoint Configuration Manager 및 Microsoft Intune 라이선스](https://microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing)는 대부분의 Microsoft 365 구독에 포함되어 있습니다. 

다음 리소스에서 라이선싱 요구 사항에 대해 자세히 알아보세요.

- [Configuration Manager 분기 및 라이선스에 대한 질문과 대답](/mem/configmgr/core/understand/product-and-licensing-faq#bkmk_equiv-sub) 
- [Microsoft Intune 라이선스](/mem/intune/fundamentals/licenses)

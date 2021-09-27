---
title: Azure Firewall 원격 작업 지원
description: 이 문서에서는 Azure Firewall이 원격 작업 강제 요구 사항을 지원하는 방법을 보여 줍니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 5112cf06952754eb1313166b82a9cf14876c797f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598273"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall 원격 작업 지원

Azure Firewall은 Azure 가상 네트워크 리소스를 보호하는 클라우드 기반의 관리 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>VDI(가상 데스크톱 인프라) 배포 지원

재택 근무 정책은 많은 IP 조직이 용량, 네트워크, 보안 및 거버넌스에서 근본적인 변화를 해결할 것을 요합니다. 직원은 재택 근무를 하는 동안 온-프레미스 서비스와 관련된 계층화된 보안 정책으로 보호를 받지 못합니다. Azure의 VDI(가상 데스크톱 인프라) 배포를 통해 이렇듯 변화하는 환경에 조직이 신속하게 대응할 수 있도록 도와줍니다. 하지만 이러한 VDI 배포에 대한 인바운드/아웃바운드 인터넷 액세스를 보호하는 방법이 필요합니다. [위협 인텔리전스](threat-intel.md) 기반 필터링 기능과 함께 Azure Firewall [DNAT 규칙](rule-processing.md)을 사용하여 VDI 배포를 보호할 수 있습니다.

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows Virtual Desktop 지원

Windows Virtual Desktop은 Azure에서 실행되는 포괄적인 데스크톱 및 앱 가상화 서비스입니다. 간소화된 관리, 다중 세션 Windows 10, 엔터프라이즈용 Microsoft 365 앱에 대한 최적화, RDS(Remote Desktop Services) 환경에 대한 지원을 제공하는 유일한 VDI(가상 데스크톱 인프라)입니다. 몇 분 내에 Azure에서 Windows 데스크톱 및 앱을 배포하고 크기를 조정하며 기본 제공 보안 및 규정 준수 기능을 사용할 수 있습니다. Windows Virtual Desktop을 사용하여 가상 네트워크에 대한 인바운드 액세스를 열 필요는 없습니다. 하지만 가상 네트워크에서 실행하는 Windows Virtual Desktop 가상 머신에 대한 아웃바운드 네트워크 연결 집합을 허용해야 합니다. 자세한 내용은 [Azure Firewall을 사용하여 Windows Virtual Desktop 배포 보호](protect-azure-virtual-desktop.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Windows Virtual Desktop](../virtual-desktop/index.yml)에 대해 자세히 알아봅니다.
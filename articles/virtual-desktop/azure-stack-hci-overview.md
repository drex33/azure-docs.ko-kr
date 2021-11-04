---
title: Azure Stack HCI(미리 보기)용 Azure Virtual Desktop - Azure
description: Azure Stack HCI(미리 보기)용 Azure Virtual Desktop에 대한 간략한 개요입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5faea9929fb62af8736e62ca6e629cb2bde01f3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452170"
---
# <a name="azure-virtual-desktop-for-azure-stack-hci-preview"></a>azure Virtual Desktop for Azure Stack HCI(미리 보기)

> [!IMPORTANT]
> Azure Stack HCI용 Azure Virtual Desktop은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

azure Virtual Desktop for Azure Stack HCI(미리 보기)를 사용하면 온-프레미스 Azure Stack HCI 인프라에 Azure Virtual Desktop 세션 호스트를 배포할 수 있습니다. Azure Stack HCI Azure Virtual Desktop을 사용하여 Azure Portal 세션 호스트를 관리할 수도 있습니다. 기존 온-프레미스 VDI(Virtual Desktop Infrastructure) 배포가 이미 있는 경우 Azure Stack HCI Azure Virtual Desktop을 사용하여 관리자 및 최종 사용자 환경을 개선할 수 있습니다. 클라우드에서 Azure Virtual Desktop을 이미 사용하고 있는 경우 성능 또는 데이터 지역성 요구 사항을 더 잘 충족하도록 배포를 온-프레미스 인프라로 확장할 수 있습니다.

Azure Stack HCI Azure Virtual Desktop은 현재 공개 미리 보기로 제공됩니다. Azure Stack HCI 현재 특정 중요한 Azure Virtual Desktop 기능을 지원하지 않습니다. 이러한 제한 사항 때문에 프로덕션 워크로드에는 이 기능을 아직 사용하지 않는 것이 좋습니다.

## <a name="key-benefits"></a>주요 이점

Azure Stack HCI Azure Virtual Desktop이 무엇인지 설정했습니다. 질문은 다음과 같습니다. 무엇을 할 수 있나요?

Azure Stack HCI Azure Virtual Desktop을 사용하여 다음을 수행할 수 있습니다.

- 세션 호스트를 해당 위치에 더 가깝게 제공하여 Azure 퍼블릭 클라우드에 대한 연결이 좋지 못한 영역에서 Azure Virtual Desktop 사용자의 성능을 향상시킵니다.

- 앱 및 사용자 데이터를 온-프레미스에 유지하여 데이터 지역성 요구 사항을 충족합니다.  자세한 내용은 [Azure Virtual Desktop의 데이터 위치를 참조하세요.](data-locations.md)

- 가상 데스크톱 및 앱을 동일한 위치에 유지하여 레거시 온-프레미스 앱 및 데이터 원본에 대한 액세스를 개선합니다.

- Windows 10 및 Windows 11 Enterprise 다중 세션 가상 데스크톱을 사용하여 비용을 절감하고 사용자 환경을 개선합니다.

- Azure Portal 사용하여 기존 온-프레미스 VDI 솔루션에 비해 VDI 배포 및 관리를 간소화합니다.

## <a name="pricing"></a>가격 책정

다음 항목은 Azure Stack HCI Azure Virtual Desktop을 실행하는 데 드는 비용에 영향을 미칩니다.
 - 인프라 비용. Azure Stack HCI 월별 서비스 요금을 지불합니다. [Azure Stack HCI 가격 책정에서](https://azure.microsoft.com/pricing/details/azure-stack/hci/)자세히 알아보세요.
 
- Azure Virtual Desktop에 대한 사용자 액세스 권한. 클라우드에서 Azure Virtual Desktop에 대한 액세스 권한을 부여하는 동일한 라이선스는 Azure Stack HCI Azure Virtual Desktop에도 적용됩니다. [Azure Virtual Desktop 가격 책정에서](https://azure.microsoft.com/pricing/details/virtual-desktop/)자세히 알아보세요.

- Azure Virtual Desktop 하이브리드 서비스 요금입니다. 이 요금을 지불하려면 Azure Stack HCI 실행 중인 Azure Virtual Desktop 세션 호스트의 각 활성 vCPU(가상 CPU)에 대해 요금을 지불해야 합니다. 이 요금은 미리 보기 기간이 끝나면 활성화됩니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

Azure Stack HCI Azure Virtual Desktop의 공개 미리 보기 버전에 영향을 주는 다음과 같은 문제를 알고 있습니다.

- [RDP Shortpath를](shortpath.md) 사용하는 Azure Virtual Desktop 배포는 현재 Azure Stack HCI 실행되는 세션 호스트에 연결할 수 없습니다.

- Azure Stack HCI 호스트 풀은 현재 다음 Azure Virtual Desktop 기능을 지원하지 않습니다.
    
    - [Azure Virtual Desktop용 Azure Monitor](azure-monitor.md)
    - [Azure Automation 세션 호스트 크기 조정](set-up-scaling-script.md)
    - [자동 크기 조정(미리 보기)](autoscale-scaling-plan.md)
    - [연결 시 VM 시작](start-virtual-machine-connect.md)
    - [멀티미디어 리디렉션(미리 보기)](multimedia-redirection.md)
    - [사용자별 액세스 가격 책정](./remote-app-streaming/licensing.md)

- Azure Portal Azure Virtual Desktop 탭은 Azure Stack HCI 인프라에서 직접 새 가상 머신을 만들 수 없습니다. 대신 관리자는 온-프레미스 가상 머신을 별도로 만든 다음, Azure Virtual Desktop 호스트 풀에 등록해야 합니다.

- Windows 10 또는 11 Enterprise 다중 세션 가상 데스크톱에 연결할 때 사용자는 적격 라이선스가 있더라도 "Windows 활성화"라는 바탕 화면 워터마크와 같은 활성화 문제를 볼 수 있습니다.

- Azure Stack HCI용 Azure Virtual Desktop은 현재 클라우드 및 온-프레미스 세션 호스트를 모두 포함하는 호스트 풀을 지원하지 않습니다. 배포의 각 호스트 풀에는 호스트 풀 유형이 하나만 있어야 합니다.

- Azure Stack HCI 세션 호스트는 특정 클라우드 전용 Azure 서비스를 지원하지 않습니다.

- Azure Stack HCI 다양한 유형의 하드웨어 및 온-프레미스 네트워킹 기능을 지원하므로 성능 및 사용자 밀도는 Azure 클라우드에서 실행되는 세션 호스트마다 크게 다를 수 있습니다. Azure Virtual Desktop의 [가상 머신 크기 조정 지침은](/windows-server/remote/remote-desktop-services/virtual-machine-recs) 광범위하므로 초기 성능 예측에만 사용해야 합니다.

미리 보기 중에 이 목록에 없는 문제가 발생하는 경우 보고하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Stack HCI Azure Virtual Desktop에 익숙해졌으므로 azure Virtual Desktop for [Azure Stack HCI(미리 보기) 설정에서](azure-stack-hci.md)이 기능을 배포하는 방법을 알아봅니다.

---
title: Azure Lab Services에서 랩에 대한 VM의 자동 종료 구성
description: 원격 데스크톱 연결이 끊어질 때 VM 자동 종료를 사용하거나 사용하지 않도록 설정하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: 2b3e589e66749e1b1d9b2348a00cd3c3c432a4e5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180308"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>랩에 대한 VM의 자동 종료 구성

이 문서에서는 랩에 대한 VM의 자동 종료를 구성하는 방법을 설명합니다.

여러 자동 종료 비용 제어 기능을 사용하도록 설정하여 가상 머신이 활발하게 사용되지 않을 때 추가 비용을 사전에 방지할 수 있습니다. 다음 세 가지 자동 종료 및 연결 끊기 기능을 결합하면 사용자가 실수로 가상 머신을 실행 중인 상태로 두는 경우를 대부분 포착할 수 있습니다.
 
* OS가 유휴 상태로 간주하는 가상 머신에서 사용자 연결을 자동으로 끊습니다.
* 사용자가 연결을 끊을 때 가상 머신을 자동으로 종료합니다.
* 시작되었지만 사용자가 연결하지 않는 가상 머신을 자동으로 종료합니다.

자동 종료 기능에 대한 추가 세부 정보는 [자동 종료 설정을 사용하여 비용 컨트롤 최대화](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 섹션을 참조하세요.

랩 계정 관리자는 랩을 만드는 랩 계정에 대해 이 설정을 구성할 수 있습니다. 자세한 정보는 [랩 계정에 대한 VM 자동 종료 구성](how-to-configure-lab-accounts.md)을 참조하세요. 랩 소유자는 랩을 만들 때 또는 랩을 만든 후 설정을 재정의할 수 있습니다. 

## <a name="configure-for-the-lab-level"></a>랩 수준 구성

[Azure Lab Services](https://labs.azure.com/)에서 자동 종료 설정을 구성할 수 있습니다.

* (**랩 정책** 에서) 랩을 만드는 경우 또는
* (**설정** 에서) 랩이 생성된 이후

> [!div class="mx-imgBorder"]
> ![랩 생성 시 구성](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

[자동 종료 설정을 사용하여 비용 컨트롤 최대화](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 섹션에서 자동 종료에 대한 세부 정보를 검토해야 합니다.

> [!WARNING]
> VM에 대한 RDP 세션 연결을 끊기 전에 VM에서 Linux나 Windows OS(운영 체제)를 종료하면 자동 종료 기능이 제대로 작동하지 않습니다.  
## <a name="next-steps"></a>다음 단계

[랩용 대시보드](use-dashboard.md)

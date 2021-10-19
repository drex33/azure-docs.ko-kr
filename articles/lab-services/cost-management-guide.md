---
title: Azure Lab Services에 대한 비용 관리 가이드
description: Lab Services에 대한 비용을 보는 다양한 방법을 이해합니다.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: how-to
ms.openlocfilehash: 29965e2fe819debd6ea992dcdd2c8164dddab5f5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181201"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure Lab Services에 대한 비용 관리

Azure Lab Services 경우 비용 관리는 비용 예측 및 비용 분석의 두 가지 영역으로 나눌 수 있습니다. 비용 예측은 랩의 초기 구조가 예상 예산 내에 맞는지 확인하기 위해 랩을 설정하는 경우에 발생합니다. 비용 분석은 일반적으로 다음 달에 필요한 작업을 결정하기 위해 월말에 발생합니다.

## <a name="estimate-the-lab-costs"></a>랩 비용 예측

각 랩 대시보드에는 랩에서 해당 월의 예상 비용을 대략적인 예측값을 설명하는 **비용 및 청구** 섹션이 있습니다. 예상 비용은 시간당 예상 비용별로 최대 사용자 수와 함께 시간 사용량을 요약합니다. 가장 정확한 예상치를 얻으려면 [일정](how-to-create-schedules.md)을 포함하여 랩을 설정합니다. 대시보드는 예상 비용을 반영합니다.

이 예상치는 가능한 비용을 모두 표시하지 않을 수 있습니다. 다음과 같은 몇 가지 리소스는 포함되지 않습니다.

- 템플릿 준비 비용. 이는 템플릿을 만드는 데 필요한 시간에 따라 크게 달라질 수 있습니다. 템플릿을 실행하는 비용은 시간당 전체 랩 비용과 동일합니다.
- 갤러리를 여러 랩 간에 공유할 수 있으므로 [공유 이미지 갤러리 ](how-to-use-shared-image-gallery.md) 비용이 듭니다.
- 랩 작성자가 VM(가상 머신)을 시작할 때 발생하는 시간입니다.

> [!div class="mx-imgBorder"]
> ![대시보드 예상 비용을 보여주는 스크린샷입니다.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>이전 달 사용량 분석

비용 분석은 이전 달의 사용량을 검토하여 랩에 대한 조정을 결정하는 데 도움이 됩니다. [구독 비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md)에서 과거 비용의 분석을 찾을 수 있습니다. Azure Portal에서 검색 상자에 **구독** 을 입력한 다음, **구독** 옵션을 선택합니다.

> [!div class="mx-imgBorder"]
> ![검색 상자와 구독 옵션을 보여주는 스크린샷입니다.](./media/cost-management-guide/subscription-search.png)

검토할 특정 구독을 선택합니다.

> [!div class="mx-imgBorder"]
> ![선택한 구독을 보여주는 스크린샷입니다.](./media/cost-management-guide/subscription-select.png)

**Cost Management** 의 왼쪽 창에서 **비용 분석** 을 선택합니다.

> [!div class="mx-imgBorder"]
> ![그래프에 대한 구독 비용 분석을 보여주는 스크린샷입니다.](./media/cost-management-guide/subscription-cost-analysis.png)

이 대시보드에서는 일정에 따라 다양한 파일 형식으로 내보내는 기능을 포함하여 심층 비용 분석을 수행할 수 있습니다. 자세한 내용은 [Cost Management + 청구 개요](../cost-management-billing/cost-management-billing-overview.md)를 참조하세요.

리소스 종류별로 필터링할 수 있습니다. `microsoft.labservices/labaccounts`를 사용하면 Lab Services와 관련된 비용만 표시됩니다.

## <a name="understand-the-usage"></a>사용량 이해

다음 스크린샷은 비용 분석의 예를 보여줍니다.

> [!div class="mx-imgBorder"]
> ![구독에 대한 예제 비용 분석을 보여주는 스크린샷입니다.](./media/cost-management-guide/cost-analysis.png)

기본적으로 **리소스**, **리소스 종류**, **위치**, **리소스 그룹 이름**, **태그** 및 **비용** 의 6개 열이 있습니다. **리소스** 열에는 랩 계정, 랩 이름 및 VM에 대한 정보가 포함됩니다. 랩 계정, 랩 이름 및 기본값(두 번째 및 세 번째 행)을 표시하는 행은 랩에 대한 비용입니다. 사용된 VM에는 랩 계정, 랩 이름, 기본값 및 VM 이름을 표시하는 행에 대해 볼 수 있는 비용이 있습니다.

이 예제에서는 첫 번째 및 두 번째 행(둘 다 **aaalab/dockerlab** 으로 시작)을 추가하여 "aaalab" 랩 계정의 랩 "dockerlab"에 대한 총 비용을 제공합니다.

이미지 갤러리의 전체 비용을 얻으려면 리소스 종류를 `Microsoft.Compute/Galleries`로 변경합니다. 갤러리의 저장 위치에 따라 공유 이미지 갤러리가 비용에 표시되지 않을 수 있습니다.

> [!NOTE]
> 공유 이미지 갤러리를 랩 계정에 연결합니다. 즉, 여러 랩에서 동일한 이미지를 사용할 수 있습니다.

## <a name="separate-the-costs"></a>비용 구분

일부 대학에서는 클래스를 구분하는 방법으로 랩 계정 및 리소스 그룹을 사용하고 있습니다. 각 클래스에는 고유한 랩 계정 및 리소스 그룹이 있습니다.

비용 분석 창에서 리소스 그룹 이름에 기반하는 필터를 클래스에 대한 적절한 리소스 그룹 이름으로 추가합니다. 그러면 해당 클래스의 비용만 표시됩니다. 이렇게 하면 비용을 볼 때 클래스 간에 더 명확하게 구분할 수 있습니다. 비용 분석의 [예약된 내보내기](../cost-management-billing/costs/tutorial-export-acm-data.md) 기능을 사용하여 각 클래스의 비용을 별도의 파일에 다운로드할 수 있습니다.

## <a name="manage-costs"></a>비용 관리

클래스 유형에 따라 학생 없이 실행되는 VM 인스턴스를 줄이기 위해 비용을 관리하는 방법이 있습니다.

### <a name="automatic-shutdown-settings-for-cost-control"></a>비용 제어를 위한 자동 종료 설정

자동 종료 기능을 사용하면 랩에서 낭비되는 VM 사용 시간을 방지할 수 있습니다. 다음 설정은 사용자가 실수로 가상 머신을 실행 상태로 두는 대부분의 경우를 포착합니다.

> [!div class="mx-imgBorder"]
> ![세 가지 자동 종료 설정을 보여주는 스크린샷입니다.](./media/cost-management-guide/auto-shutdown-disconnect.png)

랩 계정 수준과 랩 수준에서 이러한 설정을 구성할 수 있습니다. 랩 계정 수준에서 사용하도록 설정하면 랩 계정 내의 모든 랩에 적용됩니다. 모든 새 랩 계정의 경우 이러한 설정은 기본적으로 설정됩니다.

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>OS가 유휴 상태로 간주하는 가상 머신에서 사용자 연결을 자동으로 끊습니다.

> [!NOTE]
> 이 설정은 Windows 가상 머신에서만 사용할 수 있습니다.

**가상 머신이 유휴 상태일 때 사용자 연결 끊기** 설정이 켜지면 Windows OS에서 세션이 유휴 상태(템플릿 가상 머신 포함)로 간주되는 경우 랩의 모든 머신에서 사용자의 연결이 끊어집니다. [유휴 상태의 Windows OS 정의](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state)는 다음 두 가지 조건을 사용합니다.

- 사용자 부재: 키보드 또는 마우스 입력이 없습니다.
- 리소스 사용 없음: 특정 시간(%) 동안 모든 프로세서 및 모든 디스크가 유휴 상태였습니다.

연결이 끊기기 전에 VM에 다음과 같은 메시지가 표시됩니다.

> [!div class="mx-imgBorder"]
> ![세션이 시간 제한을 초과하여 유휴 상태이고 연결이 끊어질 것이라는 경고 메시지를 보여주는 스크린샷입니다.](./media/cost-management-guide/idle-timer-expired.png)

사용자의 연결이 끊긴 경우에도 가상 머신이 계속 실행되고 있습니다. 사용자가 로그인하여 가상 머신에 다시 연결하는 경우 연결이 끊어지기 전에 열려 있거나 비저장된 작업인 창 또는 파일이 계속 존재합니다. 이 상태에서 가상 머신이 실행되고 있기 때문에 여전히 활성 및 발생 비용으로 계산됩니다.

연결이 끊긴 Windows 가상 머신을 자동으로 종료하려면 **가상 머신이 유휴 상태일 때 사용자 연결 끊기** 및 **사용자가 연결을 끊을 때 가상 머신을 종료** 설정 조합을 사용합니다.

예를 들어 다음과 같이 설정을 구성합니다.

- **가상 머신이 유휴 상태일 때 사용자 연결 끊기**: 유휴 상태가 감지된 후 15분
- **사용자가 연결을 끊으면 가상 머신 종료**: 사용자가 연결이 끊은 후 5분

Windows 가상 머신은 사용자가 사용을 중지한 후 20분 후에 자동으로 종료됩니다.

> [!div class="mx-imgBorder"]
> ![자동 VM 종료가 발생하는 설정의 조합을 보여주는 다이어그램입니다.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>사용자가 연결을 끊을 때 자동으로 가상 머신 종료

**사용자가 연결을 끊을 때 가상 머신 종료** 설정은 Windows 및 Linux 가상 머신을 모두 지원합니다. 이 설정이 켜져 있으면 다음과 같은 경우 자동 종료가 수행됩니다.

- Windows의 경우 RDP(원격 데스크톱) 연결의 연결이 끊깁니다.
- Linux의 경우 SSH 연결이 끊어집니다.

> [!IMPORTANT]
> [특정 배포 및 Linux 버전](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions)만 지원됩니다.  종료 설정은 Data Science Virtual Machine [- Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 이미지에서 지원되지 않습니다.

가상 머신이 자동으로 종료되기 전에 사용자가 다시 연결하도록 가상 머신이 대기해야 하는 시간을 지정할 수 있습니다.

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>시작되었지만 사용자가 연결하지 않는 가상 머신을 자동으로 종료합니다.

랩에서 사용자는 가상 머신을 시작할 수 있지만 연결하지는 않습니다. 예를 들면 다음과 같습니다.

- 랩의 일정은 클래스 세션의 모든 가상 머신을 시작하지만 일부 학생이 표시되지 않고 머신에 연결되지 않는 경우
- 사용자가 가상 머신을 시작하지만 연결을 잊어버린 경우

**사용자가 연결하지 않을 때 가상 머신 종료** 설정은 이러한 사례를 파악하여 가상 머신을 자동으로 종료합니다.

연결을 끊을 때 VM의 자동 종료를 구성하고 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [랩 계정에 대한 VM의 자동 종료 구성](how-to-configure-lab-accounts.md)
- [랩에 대한 VM의 자동 종료 구성](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>예약된 시간 및 할당량 시간

[예약된 시간](classroom-labs-concepts.md#schedules) 및 [할당량 시간](classroom-labs-concepts.md#quota)을 이해하면 교수 및 학생의 요구 사항에 잘 맞도록 랩을 구성하는 데 도움이 됩니다.

예약된 시간은 모든 학생 VM이 시작되고 연결에 사용할 수 있는 설정된 시간입니다. 예약된 시간은 일반적으로 모든 학생이 자신의 VM을 가지고 있으며 하루 중 정해진 시간(예: 학급 시간)에 교수의 지시를 따르는 경우에 사용됩니다. 단점은 학생이 VM에 로그인하지 않더라도 모든 학생 VM이 시작되고 비용이 발생한다는 것입니다.

할당량 시간은 재량에 따라 사용할 수 있도록 각 학생에게 할당된 시간이며 독립 학습에 사용되는 경우가 많습니다. VM은 학생이 VM을 시작할 때까지 시작되지 않습니다.

랩은 할당량 시간 또는 예약된 시간 또는 둘의 조합을 사용할 수 있습니다. 클래스에 예약된 시간이 필요하지 않은 경우 VM을 가장 효과적으로 사용하기 위해 할당량 시간만 사용합니다.

### <a name="scheduled-event-stop-only"></a>예약된 이벤트: 중지만

일정에서 특정 시간에 모든 컴퓨터를 중지하는 중지 전용 이벤트 유형을 추가할 수 있습니다. 일부 랩 소유자는 학생이 사용하려는 VM을 종료하는 것을 잊어버린 경우 비용 및 할당량 사용량을 줄이기 위해 매일 자정에 대한 중지 전용 이벤트를 설정했습니다. 이 유형의 이벤트에 대한 단점은 학생이 VM을 사용하는 경우에도 모든 VM이 종료된다는 것입니다.

### <a name="other-costs-related-to-labs"></a>랩과 관련된 기타 비용

일부 비용은 Lab Services로 롤백되지 않지만 랩 서비스에 연결될 수 있습니다. 공유 이미지 갤러리를 랩에 연결할 수 있지만 Lab Services 비용에 표시되지 않고 비용이 있습니다. 전반적인 비용을 줄일 수 있도록 이미지에 고유한 저장소 비용이 있으므로 갤러리에서 사용하지 않는 이미지를 제거해야 합니다.

랩은 가상 네트워크를 통해 다른 Azure 리소스에 연결할 수 있습니다. 랩이 제거되면 가상 네트워크 및 기타 리소스를 제거해야 합니다.

## <a name="conclusion"></a>결론

이 문서의 정보를 통해 사용 비용을 줄이는 데 도움이 되는 도구를 더 잘 이해할 수 있기를 바랍니다.

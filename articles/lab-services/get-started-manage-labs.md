---
title: Azure Lab Services 시작
description: 이 문서는 Azure Lab Services를 시작하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 11/18/2020
ms.openlocfilehash: a9342fa290bf6b12e59e0b56617683ebda6d25c0
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181182"
---
# <a name="get-started-with-lab-services"></a>Lab Services 시작 

Azure Lab Services는 학생과 교사에게 자신의 컴퓨터에서 직접 가상 컴퓨터 랩에 액세스할 수 있는 권한을 제공합니다.

교사들은 일대일 학생 발급 하드웨어를 통해 교육에서 Lab Services를 활용하도록 학생/부모를 교육하는 방법을 알아야 합니다. 따라서 학생은 VM(Virtual Machines)을 통해 학습 프로그램에 필요한 업계 표준 소프트웨어에 액세스할 수 있습니다. 

VM은 가상 컴퓨터 역할을 하는 가상 환경입니다. VM에는 자체 프로세서, 메모리 및 스토리지가 있습니다. VM은 실제 머신을 대체하며, 사용자가 자신의 디바이스에 설치할 필요 없이 운영 체제 및 소프트웨어에 대한 액세스 권한을 부여할 수 있습니다. Azure Lab Services는 학생이 VM에 액세스하고 탐색할 수 있는 도구와 직원이 가상 컴퓨터 랩을 관리할 수 있는 도구를 제공합니다. 

이 문서에서는 교직원에게 학생/부모가 Azure Lab Services 활용하도록 액세스, 관리 및 교육하는 방법에 대한 정보를 제공합니다.

## <a name="key-concepts"></a>주요 개념

### <a name="quota-hours"></a>할당량 시간

학생은 할당량 시간에 영향을 주지 않고 예약된 클래스 시간 동안 언제든지 VM에 액세스할 수 있습니다. 할당량 시간은 전체 학급에 대해 설정되며, 학생이 정기적으로 예약된 수업 시간 외에 VM을 사용할 수 있는 시간을 결정합니다.

매주 8시간, 일요일에 다시 설정되며 - 누적이 아닙니다.

자세한 내용은 [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)을 참조하세요.

### <a name="automatic-shut-down"></a>자동 종료

비용을 절감하고 학생의 할당량 시간을 절약하기 위해 랩에 대해 자동 종료를 사용하도록 설정됩니다. 자동 종료는 비활성 기간(마우스 또는 키보드 입력 없음) 후에 VM을 해제합니다. 자동 종료는 두 단계로 작동합니다. 먼저 학생은 비활성 기간 후에 VM에서 연결이 끊어집니다. 이 시점에서 VM은 여전히 **실행 중이며** 학생은 연결할 수 있습니다. 연결이 끊긴 후 다른 비활성 기간이 지나면 VM이 자체 종료됩니다.

자동 종료는 중요한 비용 절감 도구입니다. 그러나 학생에게는 작업 저장 및 큰 프로젝트 파일 렌더링과 관련하여 어려운 과제를 제시합니다. 학생들이 자주 연결되지 않거나 VM이 너무 빨리 꺼져 있는 경우 CTE 관리자에게 연락하세요. 

자세한 내용은 [랩 계정의 VM 자동 종료 구성](how-to-configure-lab-accounts.md)을 참조하세요.

### <a name="managing-virtual-machines"></a>가상 컴퓨터 관리

랩을 관리하면 교사가 랩 용량(학생용으로 사용할 수 있는 VM의 수)과 같은 작업을 제어하고 VM을 수동으로 시작, 중지 또는 초기화할 수 있습니다. 또한 교사는 학생 인터페이스를 경험하고, 파일에 액세스하고, 소프트웨어 또는 VM 자체와 관련된 문제를 해결하기 위해 VM에 연결할 수 있습니다.

Vm을 관리할 때 기억해야 하는 가장 중요한 사항은 컴퓨터가 **실행** 될 때마다 VM에 연결되어 있지 않은 경우에도 비용이 발생한다는 것입니다.

## <a name="lab-dashboards"></a>랩 대시보드

### <a name="overview"></a>개요

Azure Lab Services의 랩에 대한 대시보드는 VM 정보, 할당되고 할당되지 않은 VM 수, 등록된 사용자 수 및 등록되지 않은 사용자 수, 랩 일정에 대한 정보 등 특정 랩의 다양한 측면에 대한 스냅숏을 제공합니다. 

> [!NOTE]
> 대시보드 및 [Azure Lab Services 웹 사이트](https://labs.azure.com/)의 대부분 관리 측면은 교사에게 표시되지만, 사용자의 역할과 관련된 사용 권한은 대시보드의 특정 기준을 수정하는 기능에 영향을 줄 수 있습니다. 특정 랩 설정에 문제가 발생하는 경우 CTE 관리자에게 문의하세요.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Azure Lab Services 포털":::

### <a name="examine-a-dashboard"></a>대시보드 검사

1. [Azure Lab Services 웹 사이트](https://labs.azure.com/)로 이동 및 로그인합니다.
1. 랩을 선택합니다.
1. 창의 왼쪽에 **대시보드** 가 표시됩니다. **대시보드** 를 클릭하면 대시보드에 많은 타일이 표시됩니다.
1. **비용 & 청구** 타일 아래에는 템플릿, 가상 컴퓨터 풀, 사용자 및 일정에 대한 타일도 있으며, 이를 통해 여러 측면을 수정하고 교실 랩에서 자세한 정보를 볼 수 있습니다.

    * 템플릿-템플릿이 생성된 날짜와 마지막으로 게시한 날짜를 설명합니다. 
    * 가상 컴퓨터 풀-할당되거나 할당되지 않은 VM의 수입니다.
    * 사용자-등록된 사용자 및 랩에 추가되었지만 등록되지 않은 사용자 수입니다.
    * 일정-랩에 향후 예정 이벤트를 표시하고 더 많은 이벤트를 볼 수 있는 링크를 표시합니다.

자세한 내용은 [대시보드 사용](use-dashboard.md)을 참조하세요.

### <a name="manually-starting-vms"></a>수동으로 VM 시작

1. **가상 머신 풀** 페이지에서 페이지 맨 위에 있는 **모두 시작** 단추를 클릭하여 랩의 모든 VM을 시작할 수 있습니다.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="VM 시작":::
1. 상태 토글을 클릭하여 개별 VM을 시작할 수 있습니다. 

    토글은 VM이 시작될 때 **시작** 을 읽은 다음, VM이 시작되면 **실행 중** 으로 읽습니다.
1. **이름** 열의 왼쪽에 있는 검사를 사용하여 여러 VM을 선택할 수도 있습니다. 

    원하는 VM을 선택한 후 화면 맨 위에 있는 **시작** 단추를 클릭합니다.
1. 시작되면 **모두 중지** 단추를 클릭하여 모든 VM을 중지할 수 있습니다.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="VM 중지":::

### <a name="stopping-and-resetting-vms"></a>VM 중지 및 다시 설정

* 상태 토글을 클릭하여 개별 VM을 중지할 수 있습니다.
* 검사를 사용하고 화면 맨 위에 있는 "중지" 단추를 클릭하여 여러 VM을 중지할 수도 있습니다.

학생이 VM에 연결하는 데 어려움이 있거나 다른 이유로 VM을 다시 설정해야 하는 경우 초기화 함수를 사용할 수 있습니다.
1. 하나 이상의 VM을 초기화하려면 검사를 사용하여 VM을 선택한 다음 페이지 맨 위에 있는 **초기화** 단추를 클릭합니다.
1. 팝업 창에서 **초기화** 를 클릭합니다.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="VM 초기화":::

    > [!NOTE]
    > 학생 VM을 켜면 학생의 할당량에 영향을 미치지 않습니다. 사용자에 대한 할당량은 사용자가 예약된 수업 시간 이외의 시간에 사용할 수 있는 랩 시간 수를 지정합니다.

### <a name="connect-to-vms"></a>VM에 연결

VM은 켜져 있고 학생이 VM에 연결되지 않는 한 교사는 학생 VM에 연결할 수 있습니다. VM에 연결하면 VM의 로컬 파일에 액세스하고 학생들이 문제를 해결할 수 있습니다.

1. 학생 VM에 연결하려면 목록에서 해당 VM을 마우스로 가리키고 **연결** 단추로 클릭합니다. 
1. 그런 다음, 학생을 위한 Chromebook, Mac 또는 PC 시작 가이드를 따릅니다

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="학생 VM에 연결 단추":::

## <a name="manage-users-in-a-lab"></a>랩에서 사용자 관리

교사는 랩에 학생 사용자를 추가하고 시간 할당량을 모니터링할 수 있습니다. 이메일 주소 또는 스프레드시트 목록을 사용하여 사용자를 추가하고 사용자를 등록하는 방법에 대한 자세한 내용은 [랩 사용자 추가 및 관리](how-to-configure-student-usage.md)를 참조하세요.

사용자를 초대하거나 링크를 공유한 후에는 **상태** 열의 **사용자** 페이지에서 성공적으로 등록한 사용자를 모니터링할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스 계속 사용하지 않으려면 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

[랩 계정 설정](tutorial-setup-lab-account.md)

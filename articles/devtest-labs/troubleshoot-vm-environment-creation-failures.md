---
title: VM 및 환경 오류 문제 해결
description: Azure DevTest Labs에서 VM(가상 머신) 및 환경 만들기 오류 문제를 해결하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c0765254110302c6306cd37b9d55b8465510a586
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561280"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Azure DevTest Labs에서 VM(가상 머신) 및 환경 만들기 오류 문제 해결
DevTest Labs는 컴퓨터 이름이 잘못되었거나 랩 정책을 위반하는 경우 사용자에게 경고합니다. 경우에 따라 문제가 발생했음을 알리는 랩 VM 또는 환경 상태 옆에 빨간색 `X`가 표시됩니다.  이 문서에서는 기본 문제를 찾고 차후에 문제를 방지하는 데 사용할 수 있는 몇 가지 트릭을 제공합니다.

## <a name="portal-notifications"></a>포털 알림
Azure Portal을 사용하는 경우 **알림 패널** 이 가장 먼저 표시되는 위치입니다.  주요 명령 모음에서 **종 모양 아이콘** 을 클릭하여 사용할 수 있는 알림 패널에는 랩 VM 또는 환경 생성이 성공했는지 여부가 표시됩니다.  오류가 발생한 경우 생성 오류와 관련된 오류 메시지가 표시됩니다. 이 세부 정보는 문제를 해결하는 데 도움이 되는 추가 정보를 제공하는 경우가 많습니다. 다음 예제에서는 코어 부족으로 인해 가상 머신을 생성하지 못했습니다. 세부 메시지는 문제를 해결하고 코어 할당량 증가를 요청하는 방법을 알려 줍니다.

![Azure Portal 알림](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>손상 상태의 VM
랩의 VM 상태가 **손상됨** 으로 표시되는 경우 사용자가 DevTest Labs 페이지가 아닌 **가상 머신** 페이지에서 탐색할 수 있는 기본 VM이 **가상 머신** 페이지에서 삭제되었을 수 있습니다. 랩에서 VM을 삭제하여 DevTest Labs에서 랩을 정리합니다. 그런 다음 랩에서 VM을 다시 생성합니다. 

![손상됨 상태의 VM](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>활동 로그
VM 또는 환경을 생성하려고 시도한 후 오류를 조사 중인 경우 활동 로그를 확인합니다. 이 섹션에서는 VM 및 환경에 대한 로그를 찾는 방법을 보여 줍니다.

## <a name="activity-logs-for-virtual-machines"></a>가상 머신의 활동 로그

1. 랩의 홈페이지에서 VM을 선택하여 **가상 머신** 페이지를 시작합니다.
2. **가상 머신** 페이지의 왼쪽 메뉴에 있는 **모니터링** 섹션에서 **활동 로그** 를 선택하여 VM에 연결된 모든 로그를 확인합니다.
3. 활동 로그 항목에서 실패한 작업을 선택합니다. 일반적으로 실패한 작업을 `Write Virtualmachines`라고 합니다.
4. 오른쪽 창에서 JSON 탭으로 전환합니다. 로그의 JSON 보기에 세부 정보가 표시됩니다.

    ![VM의 활동 로그](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. `statusMessage` 속성을 찾을 때까지 JSON 로그를 살펴봅니다. 여기에는 기본 오류 메시지와 추가 세부 정보(해당하는 경우)가 있습니다. 다음 JSON은 이 문서의 앞부분에서 인용된 코어 초과 오류의 예입니다.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>환경의 활동 로그

환경 생성에 대한 활동 로그를 보려면 다음 단계를 수행합니다.

1. 랩의 홈페이지에서 왼쪽 메뉴에 있는 **구성 및 정책** 을 선택합니다.
2. **구성 및 정책** 페이지의 메뉴에서 **활동 로그** 를 선택합니다.
3. 로그의 작업 목록에서 오류를 찾아 선택합니다.
4. 오른쪽 창에서 JSON 탭으로 전환하고 **statusMessage** 를 찾습니다.

    ![환경 활동 로그](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager 템플릿 배포 로그
환경 또는 가상 머신이 자동화를 통해 생성된 경우 마지막으로 오류 정보를 확인할 수 있는 위치가 있습니다. Azure Resource Manager 템플릿 배포 로그입니다. 자동화를 통해 랩 리소스를 만들 때 Azure Resource Manager 템플릿 배포를 통해 수행되는 경우가 많습니다. DevTest Labs 리소스를 만드는 샘플 Azure Resource Manager 템플릿은 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)의 내용을 참조하세요.

랩 템플릿 배포 로그를 보려면 다음 단계를 수행합니다.

1. 랩이 있는 리소스 그룹의 페이지를 시작합니다.
2. **설정** 아래에서 왼쪽 메뉴에 있는 **배포** 를 선택합니다.
3. 실패 상태의 배포를 찾아 선택합니다.
4. **배포** 페이지에서 실패한 작업의 **작업 세부 정보** 링크를 선택합니다.
5. **작업 세부 정보** 창에 실패한 작업의 세부 정보가 표시됩니다.

## <a name="next-steps"></a>다음 단계
[아티팩트 오류 문제 해결](devtest-lab-troubleshoot-artifact-failure.md) 참조

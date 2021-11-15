---
title: Azure Resource Manager 템플릿을 사용하여 Azure DevTest Labs에서 랩 만들기
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. 랩 관리자는 랩을 설정하고, 랩에 VM을 만들고, 정책을 구성합니다.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/15/2021
ms.openlocfilehash: 0cdc7db0f29a09040b7d40507cfad840b75f0c91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246960"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>빠른 시작: ARM 템플릿을 사용하여 DevTest Labs에서 랩 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. 랩에는 Windows Server 2019 Datacenter VM(가상 머신)이 포함되어 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

이 빠른 시작에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 템플릿을 검토합니다.
> * 템플릿을 배포합니다.
> * 배포를 확인합니다.
> * 리소스를 정리합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서는 [Windows Server VM을 사용하여 Azure DevTest Labs에서 랩 만들기](https://azure.microsoft.com/resources/templates/dtl-create-lab-windows-vm/) 템플릿을 사용합니다. 템플릿은 다음 리소스를 정의합니다.

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs)
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm/azuredeploy.json":::

더 많은 ARM 템플릿 샘플을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. ARM 템플릿을 사용하려면 다음 **Azure에 배포** 단추를 선택합니다. 이 템플릿은 Azure Portal에서 랩 만들기 화면을 엽니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm%2Fazuredeploy.json)

1. **Windows Server VM을 사용하여 Azure DevTest Labs에서 랩 만들기** 화면에서 다음 항목을 입력합니다.

   - **리소스 그룹**: 나중에 쉽게 정리할 수 있도록 새 리소스 그룹을 만듭니다.
   - **지역**: 리소스 그룹에 대한 위치를 선택합니다.
   - **랩 이름**: 새 랩 인스턴스의 이름을 입력합니다.
   - **VM 이름**: 만들 VM의 이름을 입력합니다. 
   - **사용자 이름**: VM에 액세스할 수 있는 사용자의 이름을 입력합니다. 
   - **암호**: 사용자의 암호를 입력합니다.

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="랩 만들기 페이지 스크린샷":::

## <a name="validate-the-deployment"></a>배포 유효성 검사
1. 배포하는 동안 화면 맨 위에서 **알림** 을 선택하고 **배포 진행 중** 을 선택하여 배포 **개요** 페이지를 볼 수 있습니다. 특히 VM을 만드는 배포에는 시간이 걸립니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="배포가 진행 중임을 보여 주는 스크린샷":::

1. 배포가 완료되면 **리소스 그룹으로 이동** 을 선택하여 랩의 리소스 그룹으로 이동합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="배포 완료 및 리소스 그룹으로 이동 단추를 보여 주는 스크린샷":::

1. **리소스 그룹** 페이지에는 랩과 가상 네트워크 및 VM 같은 종속 리소스를 포함하는 리소스 그룹의 리소스가 나열됩니다. **DevTest Lab** 리소스를 선택하여 랩의 **개요** 페이지로 이동합니다.

1. 랩의 **개요** 페이지에서 **클레임 가능한 가상 머신** 을 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="클레임 가능한 가상 머신 링크가 있는 랩 개요 페이지를 보여 주는 스크린샷":::

1. **클레임 가능한 가상 머신** 목록에서 VM 옆에 있는 **추가 옵션** 줄임표를 선택하고 **머신 클레임** 을 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/claim-vm.png" alt-text="머신 클레임 옵션이 있는 클레임 가능한 가상 머신 페이지를 보여 주는 스크린샷":::

1. 랩 **개요** 페이지에서 **내 가상 머신** 아래에 **시작 중** 상태인 VM이 표시되는지 확인합니다. 상태가 **실행 중** 으로 변경될 때까지 기다립니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-vm.png" alt-text="VM이 나열된 랩 개요 페이지를 보여 주는 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 랩 및 VM을 실행하는 데 드는 요금이 발생하지 않도록 빠른 시작 리소스를 삭제합니다. 랩에서 VM에 액세스하기 위한 다음 자습서를 진행하려는 경우 해당 자습서를 완료한 후 리소스를 정리할 수 있습니다.

랩이 있는 리소스 그룹은 삭제할 수 없습니다. 먼저 리소스 그룹을 삭제할 수 있도록 랩을 삭제합니다.

1. 랩의 홈페이지에 있는 도구 모음에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="랩 홈페이지의 삭제 단추를 보여 주는 스크린샷":::

1. 확인 페이지에서 랩 이름을 입력한 다음, **삭제** 를 선택합니다.

1. 삭제하는 동안 화면 위쪽에서 **알림** 을 선택하여 진행 상황을 볼 수 있습니다. 랩을 삭제하는 데는 시간이 걸립니다.

1. 랩이 삭제되면 랩 홈페이지에서 **리소스 그룹** 을 선택합니다. 랩의 홈페이지를 더 이상 사용할 수 없는 경우 Azure 검색 상자에서 **리소스 그룹** 을 검색한 다음, 랩이 포함된 리소스 그룹을 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="알림에서 삭제 확인을 보여 주는 스크린샷":::

1. **리소스 그룹** 페이지에서 도구 모음에 있는 **리소스 그룹 삭제** 를 선택합니다. 확인 페이지에서 리소스 그룹 이름을 입력하고 **삭제** 를 선택합니다. 알림을 확인하여 리소스 그룹이 삭제되었는지 확인할 수 있습니다.
 
   :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="리소스 그룹 삭제 단추를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 VM이 있는 랩을 만들었습니다. 랩 및 VM에 액세스하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [자습서: 랩에 액세스](tutorial-use-custom-lab.md)

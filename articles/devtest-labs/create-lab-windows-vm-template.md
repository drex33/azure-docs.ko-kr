---
title: Azure Resource Manager 템플릿을 사용하여 Azure DevTest Labs에서 랩 만들기
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. 랩 관리자는 랩을 설정하고, 랩에 VM을 만들고, 정책을 구성합니다.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/29/2021
ms.openlocfilehash: 7d6a1390c935d68b978146d9eb06e8d2681e50c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847538"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>빠른 시작: ARM 템플릿을 사용하여 DevTest Labs에서 랩 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DevTest Labs에서 랩을 만드는 방법을 설명합니다. 랩에는 Windows Server 2019 Datacenter VM(가상 머신)이 포함되어 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm-claimed/azuredeploy.json":::

템플릿에 정의된 세 가지 Azure 리소스는 다음과 같습니다.

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs): DevTest Labs 랩을 만듭니다.
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks): DevTest Labs 가상 네트워크를 만듭니다.
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines): DevTest Labs 가상 머신을 만듭니다.

더 많은 ARM 템플릿 샘플을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 아래 **Azure에 배포** 단추를 선택하여 Azure에 로그인하고 ARM 템플릿을 엽니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

1. 다음 값을 입력하거나 선택합니다.

    |속성 | Description |
    |---|---|
    |Subscription| 드롭다운 목록에서 랩에 사용할 Azure 구독을 선택합니다.|
    |Resource group| 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
    |지역 | 이 값은 리소스 그룹에 사용되는 위치로 자동 입력됩니다.|
    |랩 이름| 구독에 고유한 랩 이름을 입력합니다.|
    |위치| 있는 그대로 둡니다. |
    |Vm 이름| 구독에 고유한 VM 이름을 입력합니다.|
    |Vm 크기 | 있는 그대로 둡니다. |
    |사용자 이름 | VM에 대한 사용자 이름을 입력합니다.|
    |암호| 8~123자 사이의 암호를 입력합니다.|

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="랩 만들기 페이지 스크린샷":::

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다. 그러면 진행률을 모니터링할 수 있는 배포 **개요** 페이지로 이동합니다. 배포 시간은 선택한 하드웨어, 기본 이미지 및 아티팩트에 따라 달라집니다. 이 템플릿에 사용되는 구성에 대한 배포 시간은 약 12분입니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. 배포가 완료되면 템플릿 **개요** 페이지 또는 **알림** 에서 **리소스 그룹으로 이동** 을 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="배포 완료 및 리소스 그룹으로 이동 단추를 보여 주는 스크린샷":::

1. **리소스 그룹** 페이지에는 랩과 가상 네트워크 및 VM 같은 종속 리소스를 포함하는 리소스 그룹의 리소스가 나열됩니다. **DevTest Lab** 리소스를 선택하여 랩의 **개요** 페이지로 이동합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-overview.png" alt-text="리소스 그룹 개요의 스크린샷.":::

1. 랩의 **개요** 페이지에 있는 **내 가상 머신** 섹션에서 VM을 확인할 수 있습니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="가상 머신이 있는 랩 개요 페이지를 보여주는 스크린샷.":::

1. 한 단계 뒤로 물러나서 구독에 대한 리소스 그룹을 나열합니다. 배포에 의해 VM을 보유할 새 리소스 그룹이 만들어졌는지 확인합니다. 구문은 랩 이름 + VM 이름 + 난수입니다. 이 문서에 사용된 값에 따라 자동 생성된 이름은 `MyOtherLab-myVM-173385`입니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-list.png" alt-text="리소스 그룹 목록의 스크린샷.":::

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 랩 및 VM을 실행하는 데 드는 요금이 발생하지 않도록 리소스를 삭제합니다. 랩에서 VM에 액세스하기 위한 다음 자습서를 진행하려는 경우 해당 자습서를 완료한 후 리소스를 정리할 수 있습니다. 액세스 권한이 없는 경우 다음 단계를 수행하세요. 

1. 만든 랩의 홈 페이지로 돌아갑니다.

1. 위쪽 메뉴에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/create-lab-windows-vm-template/portal-lab-delete.png" alt-text="랩 삭제 단추의 스크린샷":::

1. **삭제하시겠습니까?** 페이지의 텍스트 상자에서 랩 이름을 입력한 다음, **삭제** 를 선택합니다.

1. 삭제하는 동안 화면 위쪽에서 **알림** 을 선택하여 진행 상황을 볼 수 있습니다. 랩을 삭제하는 데 시간이 걸립니다. 랩이 삭제되면 다음 단계를 계속합니다.

1. 기존 리소스 그룹에서 랩을 만든 경우 모든 랩 리소스가 제거됩니다. 이 자습서에 대한 새 리소스 그룹을 만든 경우 이제 비어 있으며 삭제할 수 있습니다. 랩이 아직 남아 있는 동안에는 리소스 그룹을 먼저 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 VM이 있는 랩을 만들었습니다. 랩 및 VM에 액세스하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [자습서: 랩에 액세스](tutorial-use-custom-lab.md)

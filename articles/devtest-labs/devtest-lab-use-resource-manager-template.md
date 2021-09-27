---
title: 가상 머신의 Azure Resource Manager 템플릿 보기 및 사용
description: 가상 머신에서 Azure Resource Manager 템플릿을 사용하여 다른 VM을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d298ef123fac319ec211d45c877f314c5f008fef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646723"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 가상 머신 만들기 

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)을 통해 DevTest Labs에서 VM(가상 머신)을 만드는 경우 VM을 저장하기 전에 Azure Resource Manager 템플릿을 볼 수 있습니다. 그런 다음 템플릿을 기초로 사용해서 동일한 설정으로 더 많은 랩 VM을 만들 수 있습니다.

이 문서는 여러 VM과 단일 VM Resource Manager 템플릿을 설명하고 VM을 만들 때 템플릿을 보고 저장하는 방법을 보여 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>다중 VM 및 단일 VM Resource Manager 템플릿
Resource Manager 템플릿을 사용하여 DevTest Labs에서 VM을 생성하는 두 가지 방법은 Microsoft.DevTestLab/labs/virtualmachines 리소스를 프로비전하거나 Microsoft.Commpute/virtualmachines 리소스를 프로비전하는 것입니다. 각 방법은 서로 다른 시나리오에서 사용되며, 필요한 사용 권한도 다릅니다.

- Microsoft.DevTestLab/labs/virtualmachines 리소스 종류(템플릿의 “resource” 속성에 선언됨)를 사용하는 Resource Manager 템플릿은 개별 랩 VM을 프로비전할 수 있습니다. 그러면 각 VM이 DevTest Labs 가상 머신 목록에서 단일 항목으로 표시됩니다.

   ![DevTest Labs 가상 머신 목록에서 가상 머신 목록을 단일 항목으로 보여주는 스크린샷.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   이 형식의 Resource Manager 템플릿은 Azure PowerShell 명령 **New-AzResourceGroupDeployment** 또는 Azure CLI 명령 **az deployment group create** 를 통해 프로비전할 수 있습니다. 관리자 권한이 필요하므로 DevTest Labs 사용자 역할이 할당된 사용자는 배포를 수행할 수 없습니다. 

- Microsoft.Compute/virtualmachines 리소스 종류를 사용하는 Resource Manager 템플릿은 여러 VM을 DevTest Labs 가상 머신 목록의 단일 환경으로 프로비전할 수 있습니다.

   ![DevTest Labs 가상 머신 목록에서 단일 항목으로 표시되는 VM 목록](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   동일한 환경의 VM은 함께 관리할 수 있으며, 동일한 수명 주기를 공유합니다. DevTest Labs 사용자 역할이 할당된 사용자는 이러한 템플릿을 사용하여 환경을 만들 수 있지만 관리자가 해당 방식으로 랩을 구성한 경우에만 가능합니다.

이 문서의 나머지 부분에서는 Microsoft.DevTestLab/labs/virtualmachines를 사용하는 Resource Manager 템플릿에 대해 설명합니다. 이러한 템플릿은 랩 관리자가 랩 VM 생성(예: 클레임 가능한 VM) 또는 골든 이미지 생성(예: 이미지 팩터리)을 자동화하는 데 사용됩니다.

[Azure Resource Manager 템플릿 작성에 대한 모범 사례](../azure-resource-manager/templates/best-practices.md)에 안정적이고 사용하기 쉬운 Azure Resource Manager 템플릿을 만드는 데 도움이 되는 다양한 지침과 제안이 나와 있습니다.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>가상 머신의 Resource Manager 템플릿 보기 및 저장
1. [랩에서 첫 번째 VM 만들기](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)의 단계에 따라 가상 머신 생성을 시작합니다.
1. 가상 머신에 대한 필요한 정보를 입력하고 이 VM에 사용하려는 아티팩트를 모두 추가합니다.
1. **고급 설정** 탭으로 이동합니다. 
1. 설정 구성 창의 맨 아래에서 **ARM 템플릿 보기** 를 선택합니다.
1. 나중에 다른 가상 머신을 만드는 데 사용할 Resource Manager 템플릿을 복사하고 저장합니다.

   ![나중에 사용하기 위해 저장할 Resource Manager 템플릿](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Resource Manager 템플릿을 저장한 후 먼저 템플릿의 매개 변수 섹션을 업데이트해야 템플릿을 사용할 수 있습니다. 실제 Resource Manager 템플릿 외부에서 매개 변수만 사용자 지정하는 parameter.json을 만들 수 있습니다. 

![JSON 파일을 사용하여 매개 변수 사용자 지정](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

이제 [VM을 만드는](devtest-lab-create-environment-from-arm.md) 데 Resource Manager 템플릿을 사용할 준비가 되었습니다.

## <a name="set-expiration-date"></a>만료 날짜 설정
학습, 데모, 체험 등의 시나리오에서 가상 머신을 생성하고 일정한 기간 후에 자동으로 삭제하여 불필요한 비용이 발생하지 않도록 할 수 있습니다. VM에 대한 **expirationDate** 속성을 지정하여 만료 날짜로 랩 vm을 생성할 수 있습니다. [GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)에서 동일한 리소스 관리자 템플릿을 확인하세요.



### <a name="next-steps"></a>다음 단계
* [Resource Manager 템플릿으로 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md) 방법을 알아봅니다.
* [Resource Manager 템플릿을 배포하여 VM 만들기](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* [공용 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 DevTest Labs 자동화를 위한 더 많은 빠른 시작 Resource Manager 템플릿을 살펴봅니다.
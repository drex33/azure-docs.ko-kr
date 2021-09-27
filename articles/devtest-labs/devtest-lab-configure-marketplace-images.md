---
title: Azure Marketplace 이미지 설정 구성
description: Azure DevTest Labs에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지 구성
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: a42326662758e045e8a36d6de1ec2b7170b8d1cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645021"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure DevTest Labs에서 Azure Marketplace 이미지 설정 구성
DevTest Lab에서는 Azure Marketplace 이미지를 랩에서 사용하도록 구성한 방법에 따라 Azure Marketplace 이미지를 기준으로 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지(있는 경우)를 지정하는 방법을 보여 줍니다. 이 경우 팀은 필요한 Marketplace 이미지에만 액세스할 수 있습니다. 

## <a name="specify-allowed-images-for-creating-vms"></a>VM을 만들기 위한 허용 이미지 지정
VM을 만들 때 허용되는 Azure Marketplace 이미지를 지정하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 랩을 선택합니다. 
4. 랩 페이지의 홈페이지에서 **구성 및 정책** 을 선택합니다.
5. 랩의 **구성 및 정책** 페이지에 있는 **Virtual Machine 기본** 에서 **Marketplace 이미지** 를 선택합니다.
6. 모든 정규화된 Azure Marketplace 이미지를 새 VM의 기반으로 사용할 수 있게 할지 여부를 지정합니다. **예** 를 선택하는 경우 다음 조건을 모두 충족하는 모든 Azure Marketplace 이미지가 랩에서 허용됩니다.
   
   * 이 이미지는 단일 VM을 만듭니다. **그리고**
   * 이 이미지는 Azure Resource Manager를 사용하여 VM을 프로비전합니다. **그리고**
   * 이 이미지는 추가 라이선스 계획을 구입하지 않아도 사용할 수 있습니다.
     
     이미지를 허용하지 않거나 사용할 수 있는 이미지를 지정하려면 **아니요** 를 선택합니다.
     
     ![모든 Marketplace 이미지를 VM에 대한 기본 이미지로 사용할 수 있도록 허용하는 옵션](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 이전 단계에서 **아니요** 를 선택하는 경우 **허용된 이미지/모두 선택** 확인란이 활성화됩니다. 
   검색 상자와 함께 이 옵션을 사용하면 목록에 표시된 모든 항목을 빠르게 선택하거나 선택 취소할 수 있습니다.
   * 각 이미지의 해당 확인란을 선택하여 VM 만들기를 개별적으로 허용하려는 Azure Marketplace 이미지를 선택합니다.
   * 랩에서 Azure Marketplace 이미지를 사용하도록 허용하지 않으려면 목록에서 아무 것도 선택하지 않습니다.
   
     ![VM에 대한 기본 이미지로 사용할 수 있는 Azure Marketplace 이미지를 지정할 수 있습니다.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>문제 해결
랩에 사용할 특정 이미지를 찾을 수 없는 경우 다음 단계를 수행합니다. 

- 컴퓨팅 VM을 만드는 동안 이미지를 볼 수 있는지 확인합니다.
- 사용하는 구독 유형에서 해당 이미지를 사용하지 못할 수 있습니다. 구독 관리자에게 구독 유형(예: MSDN, 무료, 종량제 등)을 확인합니다. 
- DevTest Labs의 Gen 2 이미지에 대한 지원은 제한됩니다. 이미지에 Gen 1과 Gen 2 버전을 모두 사용할 수 있으면 DevTest Labs는 VM을 만들 때 Gen 1 버전의 이미지만 보여줍니다. 해결 방법은 랩 외부에 사용자 지정 Gen 2 이미지를 만든 후 이 이미지를 사용하여 VM을 만드는 것입니다. 사용 가능한 이미지의 Gen 2 버전만 있는 경우 DevTest Labs에서 지원되고 목록에 표시됩니다. 
      


## <a name="next-steps"></a>다음 단계
VM을 만들 때 Azure Marketplace 이미지를 허용하는 방법을 구성했으면 다음 단계는 [VM을 랩에 추가](devtest-lab-add-vm.md)하는 것입니다.

---
title: Azure Marketplace에서 가상 머신 제품을 만듭니다.
description: Azure Marketplace에서 가상 머신 제품을 만듭니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2021
ms.openlocfilehash: 9c6ec7350358b59e108e5fc26426d7623b0a716c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730490"
---
# <a name="create-a-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace에서 가상 머신 제품 만들기

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com/)용 Azure 가상 머신 제품을 만드는 방법을 설명합니다. 운영 체제, VHD(가상 하드 디스크) 및 최대 16개의 데이터 디스크가 포함된 Windows 기반 및 Linux 기반 가상 머신을 모두 처리합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만듭니다](create-account.md). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

아직 수행하지 않은 경우 [가상 머신 제품 계획](marketplace-virtual-machines.md)을 검토합니다. 가신 머신에 대한 기술 요구 사항을 설명하고 제품을 만들 때 필요한 정보 및 자산을 나열합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제안 페이지에서 **+ 새 제안** Azure Virtual  >  **Machine을** 선택합니다.

    [![왼쪽 창 메뉴 옵션과 "새 제안" 단추를 보여주는 스크린샷. ](./media/create-vm/new-offer-azure-virtual-machine-workspaces.png) ](./media/create-vm/new-offer-azure-virtual-machine-workspaces.png#lightbox)

> [!NOTE]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제품을 변경한 후에는 항상 다시 게시해 주세요.

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Azure Marketplace 제품의 웹 주소와 Azure PowerShell 및 Azure CLI(해당되는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1** 을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**별칭** 을 입력합니다. 제품 별칭은 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Azure Marketplace에서 사용되지 않습니다. 고객에게 표시되는 제품 이름 및 다른 값과는 다릅니다.

**만들기** 를 선택하여 제품을 생성하고 계속 진행합니다. 파트너 센터에서 **제품 설정** 페이지를 엽니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 창에서 **상업용 마켓플레이스** > **개요** 를 차례로 선택합니다.
3. **개요** 페이지에서 **+ 새 제품** > **Azure Virtual Machine** 을 차례로 선택합니다.

    ![왼쪽 창 메뉴 옵션과 "새 제품" 단추를 보여 주는 스크린샷](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제품을 변경한 후에는 항상 다시 게시해 주세요.

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Azure Marketplace 제품의 웹 주소와 Azure PowerShell 및 Azure CLI(해당되는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1** 을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**별칭** 을 입력합니다. 제품 별칭은 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Azure Marketplace에서 사용되지 않습니다. 고객에게 표시되는 제품 이름 및 다른 값과는 다릅니다.

**만들기** 를 선택하여 제품을 생성하고 계속 진행합니다. 파트너 센터에서 **제품 설정** 페이지를 엽니다.

---

## <a name="test-drive-optional"></a>시험 사용(선택 사항)

[!INCLUDE [Test drives section](includes/test-drives.md)]

VM 제안의 경우 ARM(Azure Resource Manager) 배포가 사용 가능한 유일한 시험 사용 옵션입니다. 배포 템플릿에는 솔루션을 구성하는 모든 Azure 리소스가 포함되어야 합니다.

시험 사용을 사용하도록 설정하려면 시험 사용 **확인란을** 선택합니다. 그러면 왼쪽 탐색 메뉴에서 시험 사용 탭이 활성화됩니다. [나중에 VM](azure-vm-test-drive.md)시험 사용 구성에서 해당 탭을 사용하여 시험 사용 목록을 구성하고 만듭니다.

시험 사용할 경우 잠재 고객에 대한 CRM을 구성해야 합니다(다음 섹션 참조). 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Customer leads](includes/customer-leads.md)]

왼쪽 탐색 메뉴에서 다음 탭을 계속하려면 **초안 저장** 을 선택하고 **속성** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [가상 머신 제품 속성 구성](azure-vm-properties.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)

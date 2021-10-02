---
title: Azure Virtual Machines의 여러 IP 주소 - 포털 | Microsoft Docs
description: Azure Portal | Resource Manager를 사용하여 가상 머신에 여러 IP 주소를 할당하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: a05eb2853c679d25c0a7e1c8a72b710597a3667e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372841"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신에 여러 IP 주소 할당

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> 이 문서는 Azure Portal을 사용하여 Azure Resource Manager 배포 모델을 통해 VM(가상 머신)을 만드는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../../azure-resource-manager/management/deployment-models.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

여러 IP 주소 또는 고정 개인 IP 주소를 사용하여 VM을 만들려면 PowerShell 또는 Azure CLI를 사용하여 만들어야 합니다. 방법을 알아보려면 이 문서의 맨 위에 있는 PowerShell 또는 CLI 옵션을 클릭합니다. 단일 동적 개인 IP 주소 및 (선택 사항) 단일 공용 IP 주소를 사용하여 VM을 만들 수 있습니다. [Windows VM 만들기](../../virtual-machines/windows/quick-create-portal.md) 또는 [Linux VM 만들기](../../virtual-machines/linux/quick-create-portal.md) 문서의 단계에 따라 포털을 사용합니다. VM을 만든 후에 이 문서의 [VM에 IP 주소 추가](#add) 섹션에 나오는 단계에 따라 포털을 사용하여 IP 주소 형식을 동적에서 고정으로 변경하고 추가 IP 주소를 추가할 수 있습니다.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 Azure 네트워크 인터페이스에 추가할 수 있습니다. 다음 섹션의 예제는 [시나리오](#scenario)에서 설명한 3개의 IP로 구성된 VM이 이미 있다고 가정하지만 필수 사항은 아닙니다.

### <a name="core-steps"></a><a name="coreadd"></a>핵심 단계

1. https://portal.azure.com의 Azure Portal로 이동한 후 필요한 경우 로그인합니다.
2. 포털에서 **더 많은 서비스** 를 클릭하고 필터 상자에 *가상 머신* 를 입력하고 **가상 머신** 를 클릭합니다.
3. **가상 머신** 창에서 IP 주소를 추가할 VM을 클릭합니다. **네트워킹** 탭으로 이동합니다. 이 페이지에서 **네트워크 인터페이스** 를 클릭합니다. 아래 그림을 참조하세요. 


    ![VM에 공용 IP 주소 추가](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. **네트워크 인터페이스** 창에서 **IP 구성** 을 클릭합니다.

5. 선택한 NIC에 대해 표시되는 창에서 **IP 구성** 을 클릭합니다. **추가** 를 클릭하고, 추가하려는 IP 주소의 유형에 따라 이어지는 섹션 중 하나의 단계를 완료하고, **확인** 을 클릭합니다. 

### <a name="add-a-private-ip-address"></a>개인 IP 주소 추가

새 개인 IP 주소를 추가하려면 다음 단계를 완료합니다.

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다. 현재 VM 네트워크 인터페이스의 **IP 구성** 섹션에 있는지 확인하세요.  기본값으로 표시되는 서브넷을 검토합니다(예: 10.0.0.0/24).
2. **추가** 를 클릭합니다. 표시되는 **IP 구성 추가** 창에서 마지막 8진수로 새 숫자를 선택하여 새로운 *고정* 개인 IP 주소를 갖는 IP 구성을 만들고 이름을 *IPConfig-4* 로 지정한 다음 **확인** 을 클릭합니다.  (10.0.0.0/24 서브넷의 IP는 *10.0.0.7* 일 수 있습니다.)

    > [!NOTE]
    > 고정 IP 주소를 추가할 경우 NIC가 연결된 서브넷의 사용하지 않은 유효한 주소를 지정해야 합니다. 선택한 주소를 사용할 수 없는 경우 포털에 IP 주소로 X가 표시되며 다른 주소를 선택해야 합니다.

3. 확인을 클릭하면 창이 닫히고 새 IP 구성이 나열됩니다. **확인** 을 클릭하여 **IP 구성 추가** 창을 닫습니다.
4. **추가** 를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다.
5. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 있는 단계를 완료하여 VM 운영 체제에 개인 IP 주소를 추가합니다.

### <a name="add-a-public-ip-address"></a>공용 IP 주소 추가

공용 IP 주소 리소스를 새 IP 구성 또는 기존 IP 구성에 연결하면 공용 IP 주소가 추가됩니다.

> [!NOTE]
> 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>공용 IP 주소 리소스 만들기

공용 IP 주소는 공용 IP 주소 리소스에 대한 한 가지 설정입니다. IP 구성에 연결하려는 IP 구성에 현재 연결되어 있지 않은 공용 IP 주소 리소스가 있는 경우 다음 단계를 건너뛰고 필요에 따라 이어지는 섹션 중 하나에 나와 있는 단계를 완료합니다. 사용 가능한 공용 IP 주소 리소스가 없는 경우 다음 단계를 완료하여 리소스를 하나 만듭니다.

1. https://portal.azure.com의 Azure Portal로 이동한 후 필요한 경우 로그인합니다.
3. 포털에서 **리소스 만들기** > **네트워킹** > **공용 IP 주소** 를 클릭합니다.
4. 다음 그림과 같이 표시되는 **공용 IP 주소 만들기** 창에서 **이름** 을 입력하고 **IP 주소 할당** 형식, **구독**, **리소스 그룹** 및 **위치** 를 선택한 다음, **만들기** 를 클릭합니다.

    ![공용 IP 주소 리소스 만들기](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. 공용 IP 주소 리소스를 IP 구성에 연결하려면 이어지는 섹션 중 하나에 나와 있는 단계를 완료합니다.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>새 IP 구성에 공용 IP 주소 리소스 연결

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. **추가** 를 클릭합니다. 표시되는 **IP 구성 추가** 창에서 *IPConfig-4* 라는 IP 구성을 만듭니다. 표시되는 **공용 IP 주소 선택** 창에서 **공용 IP 주소** 를 사용하도록 설정하고 사용 가능한 기존 공용 IP 주소 리소스를 선택합니다.

    공용 IP 주소 리소스를 선택한 후 **확인** 을 클릭하면 창이 닫힙니다. 기존 공용 IP 주소가 없는 경우 이 문서의 [공용 IP 주소 리소스 만들기](#create-public-ip) 섹션에 나와 있는 단계를 완료하여 주소를 하나 만들 수 있습니다. 

3. 새 IP 구성을 검토합니다. 개인 IP 주소가 명시적으로 할당되지 않았더라도 모든 IP 구성에는 개인 IP 주소가 있어야 하므로 IP 구성에 자동으로 주소가 하나 할당되어 있습니다.
4. **추가** 를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다.
5. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>기존 IP 구성에 공용 IP 주소 리소스 연결

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. 공용 IP 주소 리소스를 추가할 IP 구성을 클릭합니다.
3. 표시되는 IPConfig 창에서 **IP 주소** 를 클릭합니다.
4. 표시되는 **공용 IP 주소 선택** 창에서 공용 IP 주소를 선택합니다.
5. **저장** 을 클릭하면 창이 닫힙니다. 기존 공용 IP 주소가 없는 경우 이 문서의 [공용 IP 주소 리소스 만들기](#create-public-ip) 섹션에 나와 있는 단계를 완료하여 주소를 하나 만들 수 있습니다.
3. 새 IP 구성을 검토합니다.
4. **추가** 를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

> [!NOTE]
> IP 주소 구성을 변경한 후에는 VM을 다시 시작해야 VM에 변경 사항이 적용됩니다.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

---
title: '자습서: Azure Bastion 호스트 만들기: Windows VM: 포털'
description: Azure Bastion 호스트를 만들고 Windows VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d2e071fd7dc368df867b17a53f9e994ee5c16a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430974"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm"></a>자습서: Bastion을 구성하고 Windows VM에 연결

이 자습서에서는 Azure Bastion 및 Azure Portal을 사용하여 브라우저를 통해 가상 머신에 연결하는 방법을 보여줍니다. 이 자습서에서는 Azure Portal을 사용하여 Bastion을 가상 네트워크에 배포합니다. 서비스가 프로비저닝되면 동일한 가상 네트워크의 모든 가상 머신에서 RDP/SSH 환경을 사용할 수 있습니다. Bastion을 사용하여 연결하는 경우 VM에 공용 IP 주소 또는 특수 소프트웨어가 필요하지 않습니다. Bastion이 배포되면 다른 작업에 필요하지 않은 경우 VM에서 공용 IP 주소를 제거할 수 있습니다. 다음으로 Azure Portal을 사용하여 개인 IP 주소를 통해 VM에 연결합니다. Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * VNet에 대한 베스천 호스트 만들기
> * 가상 머신에서 공용 IP 주소 제거
> * Windows 가상 머신에 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* [가상 네트워크](../virtual-network/quick-create-portal.md).
* 가상 네트워크의 Windows 가상 머신 VM이 없는 경우 [빠른 시작: VM 만들기](../virtual-machines/windows/quick-create-portal.md)를 참조하여 하나 만듭니다.
* 리소스에 필요한 역할은 다음과 같습니다.
   * 필요한 VM 역할:
     * 가상 머신에 대한 읽기 권한자 역할
     * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할

* 포트: Windows VM에 연결하려면 Windows VM에서 다음 포트가 열려 있어야 합니다.
  * 인바운드 포트: RDP(3389)

 >[!NOTE]
 >Azure 프라이빗 DNS 영역에서 Azure Bastion을 사용하는 것은 현재 지원되지 않습니다. 시작하기 전에 Bastion 리소스를 배포하려는 가상 네트워크가 프라이빗 DNS 영역에 연결되어 있지 않은지 확인하세요.
 >

### <a name="example-values"></a><a name="values"></a>예제 값

이 구성을 만들 때 다음 예제 값을 사용해도 되고 자체적인 값으로 바꿔도 됩니다.

**기본 VNet 및 VM 값:**

|**이름** | **값** |
| --- | --- |
| 가상 머신| TestVM |
| 리소스 그룹 | TestRG1 |
| Azure 지역 | 미국 동부 |
| 가상 네트워크 | VNet1 |
| 주소 공간 | 10.1.0.0/16 |
| 서브넷 | FrontEnd: 10.1.0.0/24 |

**Azure Bastion 값:**

|**이름** | **값** |
| --- | --- |
| 속성 | VNet1-bastion |
| + 서브넷 이름 | AzureBastionSubnet |
| AzureBastionSubnet 주소 | 서브넷 마스크가 /26 이상인 VNet 주소 공간 내의 서브넷입니다.<br> 예: 10.1.1.0/26  |
| 계층/SKU | 표준 |
| 인스턴스 수(호스트 크기 조정)| 3 이상 |
| 공용 IP 주소 |  새로 만들기 |
| 공용 IP 주소 이름 | VNet1-ip  |
| 공용 IP 주소 SKU |  Standard  |
| 할당  | 정적 |

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 VNet에서 베스천 개체를 만드는 데 도움이 됩니다. VNet의 VM에 안전하게 연결하려면 다음을 수행해야 합니다.

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.
1. 검색에 **Bastion** 을 입력합니다.
1. 서비스 아래에서 **Bastion** 을 클릭합니다.
1. Bastion 페이지에서 **+ 만들기** 를 클릭하여 **Bastion 만들기** 페이지를 엽니다.
1. **Bastion 만들기** 페이지에서 새 Bastion 리소스를 구성합니다.

   :::image type="content" source="./media/tutorial-create-host-portal/review-create.png" alt-text="Bastion 포털 만들기 페이지의 스크린샷" lightbox="./media/tutorial-create-host-portal/create.png":::

### <a name="project-details"></a>프로젝트 세부 정보

* **구독**: 사용하려는 Azure 구독입니다.

* **리소스 그룹**: 새 Bastion 리소스가 만들어질 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.

### <a name="instance-details"></a>인스턴스 세부 정보

* **이름**: 새 Bastion 리소스의 이름입니다.

* **지역:** 리소스가 만들어질 Azure 퍼블릭 지역입니다. 가상 네트워크가 있는 지역을 선택합니다.

* **계층:** : 계층은 **SKU** 라고도 합니다. 이 자습서에서는 드롭다운에서 **표준** SKU를 선택합니다. 표준 SKU를 선택하면 호스트 크기 조정에 대한 인스턴스 수를 구성할 수 있습니다. 기본 SKU는 호스트 크기 조정을 지원하지 않습니다. 자세한 내용은 [구성 설정 - SKU](configuration-settings.md#skus)를 참조하세요.

* **인스턴스 수:** **호스트 크기 조정** 설정이며 배율 단위 증분으로 구성됩니다. 슬라이더를 사용하여 인스턴스 수를 구성합니다. 기본 계층 SKU를 지정한 경우 이 설정을 구성할 수 없습니다. 자세한 내용은 [구성 설정 - 호스트 크기 조정](configuration-settings.md#instance)을 참조하세요. 이 자습서에서는 모든 배율 단위 [가격 책정](https://azure.microsoft.com/pricing/details/azure-bastion) 고려 사항을 염두에 두고 원하는 인스턴스 수를 선택할 수 있습니다.

### <a name="configure-virtual-networks"></a>가상 네트워크 구성

* **가상 네트워크**: Bastion 리소스를 만들 가상 네트워크입니다. 이 과정에서 포털에서 새 가상 네트워크를 만들어도 되고, 아니면 기존 가상 네트워크를 사용해도 됩니다. 기존 가상 네트워크를 사용하는 경우 기존 가상 네트워크에 Bastion 서브넷 요구 사항을 수용하기에 충분한 여유 주소 공간이 있는지 확인합니다. 드롭다운에 가상 네트워크가 보이지 않는 경우 올바른 리소스 그룹을 선택했는지 확인합니다.

* **서브넷**: 가상 네트워크를 만들거나 선택하면 서브넷 필드가 페이지에 표시됩니다. 이는 Bastion 인스턴스가 배포될 서브넷입니다. 이름은 **AzureBastionSubnet** 이어야 합니다. 서브넷을 추가하려면 다음 단계를 참조하세요.

#### <a name="manage-subnet-configuration"></a>서브넷 구성 관리

대부분의 경우 AzureBastionSubnet이 아직 구성되어 있지 않습니다. 베스천 서브넷을 구성하려면 다음을 수행합니다. 

1. **서브넷 구성 관리** 를 선택합니다. 그러면 **서브넷** 페이지로 이동됩니다.

   :::image type="content" source="./media/tutorial-create-host-portal/subnet.png" alt-text="서브넷 구성 관리의 스크린샷":::
1. **서브넷** 페이지에서 **+ 서브넷** 을 선택하여 **서브넷 추가** 페이지를 엽니다. 

1. 다음 지침에 따라 서브넷을 만듭니다.

   * 서브넷 이름은 **AzureBastionSubnet** 이어야 합니다.
   * 서브넷은 /26 이상이어야 합니다. 표준 SKU의 경우 향후 추가 호스트 크기 조정 인스턴스를 수용할 수 있도록 /26 이상을 사용하는 것이 좋습니다.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-subnet.png" alt-text="AzureBastionSubnet 서브넷의 스크린샷":::

1. 이 페이지에서는 추가 필드를 채울 필요가 없습니다. 페이지 아래쪽에서 **저장** 을 선택하여 설정을 저장하고 **서브넷 추가** 페이지를 닫습니다.

1. **서브넷** 페이지의 위쪽에서 **Bastion 만들기** 를 선택하여 Bastion 구성 페이지로 돌아갑니다.

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="Bastion 만들기의 스크린샷":::

### <a name="public-ip-address"></a>공용 IP 주소

443 포트를 통해 RDP/SSH에 액세스할 Bastion 리소스의 공용 IP 주소입니다. **새 공용 IP 주소** 를 만듭니다. 공용 IP 주소는 만들려는 Bastion 리소스와 동일한 지역에 있어야 합니다. 이 IP 주소는 연결하려는 VM과 아무 관련이 없습니다. Bastion 호스트 리소스의 공용 IP 주소입니다.

   * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다. 이 자습서에서는 기본 설정을 그대로 사용해도 됩니다.
   * **공용 IP 주소 SKU**: 이 설정은 기본적으로 **표준** 으로 미리 채워져 있습니다. Azure Bastion은 표준 공용 IP SKU만 사용/지원합니다.
   * **할당**: 이 설정은 기본적으로 **정적** 으로 미리 채워져 있습니다.

### <a name="review-and-create"></a>검토 후 만들기

1. 설정 지정이 완료되면 **검토 + 만들기** 를 선택합니다. 그러면 값의 유효성이 검사됩니다. 유효성 검사를 통과하면 Bastion 리소스를 만들 수 있습니다.
1. 설정을 검토합니다. 
1. 페이지의 맨 아래에서 **만들기** 를 선택합니다.
1. 배포가 진행되고 있음을 알리는 메시지가 표시됩니다. 리소스가 생성되면 이 페이지에 상태가 표시됩니다. Bastion 리소스가 생성되고 배포될 때까지 약 5분 정도 걸립니다.

## <a name="remove-vm-public-ip-address"></a> VM 공용 IP 주소 제거

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>VM에 연결

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 해당 리소스 그룹이 보이면 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.
1. **리소스 그룹 이름 입력** 에 해당 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Bastion 호스트를 만들고 가상 네트워크에 연결했습니다. 그런 다음, VM에서 공용 IP 주소를 제거하고 연결했습니다. Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하도록 선택할 수 있습니다. 이렇게 하려면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [NSG 사용](bastion-nsg.md)

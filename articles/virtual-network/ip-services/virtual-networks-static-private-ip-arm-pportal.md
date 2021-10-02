---
title: 고정 개인 IP 주소를 사용하여 VM 만들기 - Azure Portal
description: Azure Portal 사용하여 고정 개인 IP 주소가 있는 가상 머신을 만드는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eec4e9e07f19130732219c0932f66a1cfa0fa2b7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372908"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-portal"></a>Azure Portal 사용하여 고정 개인 IP 주소가 있는 가상 머신 만들기

VM(가상 머신)에는 지정한 범위의 개인 IP 주소가 자동으로 할당됩니다. 이 범위는 VM이 배포되는 서브넷을 기반으로 합니다. VM은 VM이 삭제될 때까지 주소를 유지합니다. Azure는 VM을 만드는 서브넷에서 사용 가능한 다음 개인 IP 주소를 동적으로 할당합니다. 서브넷에서 특정 IP 주소를 원하는 경우 VM에 고정 IP 주소를 할당합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>가상 머신 만들기

다음 단계를 사용하여 가상 머신, 가상 네트워크 및 서브넷을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

3. **+ 만들기를** 선택한 다음, **가상 머신에서 + 가상** **머신을** 선택합니다.

4. **가상 머신 만들기에서** 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **이름** 에 **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **(미국) 미국 동부 2** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다.** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen2를** 선택합니다. |
    | Azure Spot 인스턴스 | 확인되지 않은 상태로 둡니다. |
    | 크기 | 크기를 선택합니다. |
    | **관리자 계정** |   |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 를 선택합니다. |

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/create-vm.png" alt-text="가상 머신 만들기 스크린샷":::

    > [!WARNING]
    > 인터넷에서 Windows Server 가상 머신에 원격 액세스할 수 있도록 포털 3389가 선택됩니다. 인터넷에서 포트 3389를 여는 것은 프로덕션 워크로드 관리에 권장되지 않습니다. </br> Azure 가상 머신에 안전하게 액세스하는 방법에 대해서는  **[Azure Bastion이란?](../../bastion/bastion-overview.md)을 참조하세요.**

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | 기본 네트워크 이름 적용 |
    | 서브넷 | 기본 서브넷 구성을 적용합니다. |
    | 공용 IP | 기본 공용 IP 구성을 적용합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 를 선택합니다. |

5. **검토 + 만들기** 를 선택합니다. 
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="change-private-ip-address-to-static"></a>개인 IP 주소를 고정으로 변경

이 섹션에서는 이전에 만든 가상 머신에 대한 개인 IP 주소를 **동적에서** **고정으로** 변경합니다.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

2. **가상 머신에서** **myVM을** 선택합니다.

3. **myVM** 의 **설정** 에서 **네트워킹** 을 선택합니다.

4. **네트워킹** 에서 **네트워크 인터페이스** 옆에 있는 네트워크 인터페이스 이름을 선택합니다. 이 예제에서 NIC의 이름은 **myvm472** 입니다.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-nic.png" alt-text="네트워크 인터페이스 선택 스크린샷.":::

5. 네트워크 인터페이스 속성에서 **설정** **IP 구성을** 선택합니다.

6. **IP 구성** 페이지에서 **ipconfig1을** 선택합니다.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-ip-configuration.png" alt-text="IP 구성 선택 스크린샷.":::

7. **할당** 에서 **정적** 을 선택합니다. **저장** 을 선택합니다.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-static-assignment.png" alt-text="정적 할당 선택 스크린샷":::

    > [!NOTE]
    > **저장** 을 선택한 후에도 할당이 **동적** 으로 설정되어 있으면 입력한 IP 주소가 이미 사용 중인 것입니다. 다른 IP 주소를 사용해 보세요.

IP 주소를 동적으로 다시 변경하려면 개인 IP 주소에 대한 할당을 **동적** 으로 설정한 **다음, 저장을** 선택합니다.

> [!WARNING]
> VM의 운영 체제 내에서 Azure VM에 할당된 ‘개인’ IP를 정적으로 할당하면 안 됩니다. [VM에 많은 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)하는 경우와 같이 필요한 경우에만 개인 IP의 정적 할당을 수행합니다. 
>
>운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 일치하는지 확인합니다. 그러지 않으면 VM에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 **myResourceGroup** 을 입력합니다. 검색 결과에 **myResourceGroup** 이 보이면 선택합니다.

2. **리소스 그룹 삭제** 를 선택합니다.

3. **리소스 그룹 이름 입력:** 에 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [고정 공용 IP 주소](public-ip-addresses.md#ip-address-assignment)에 대해 자세히 알아봅니다.

- Azure에서 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.

- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.

- [개인 IP 주소](private-ip-addresses.md), 및 Azure 가상 머신에 [고정 개인 IP 주소](virtual-network-network-interface-addresses.md#add-ip-addresses) 할당에 대해 알아봅니다.


---
title: 고정 공용 IP 주소를 사용하는 VM 만들기 - Azure Portal
description: Azure Portal을 사용하여 고정 공용 IP 주소를 사용하는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: allensu
ms.openlocfilehash: cebaccb67d2731bf4e669b2ab12102ace3a20fcc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373043"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure Portal을 사용하여 고정 공용 IP 주소로 가상 머신 만들기

공용 IP 주소를 사용하면 인터넷에서 가상 머신과 통신할 수 있습니다. 

동적 주소 대신 고정 공용 IP 주소를 할당하여 주소가 변경되지 않도록 합니다.   

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 선택하거나 검색 상자에 **가상 머신** 을 검색합니다.
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **새로 만들기** 를 선택합니다. </br> **이름** 에서 **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1** 을 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 를 선택합니다. |

    > [!WARNING]
    > 인터넷에서 Windows Server 가상 머신에 원격 액세스할 수 있도록 포털 3389가 선택됩니다. 인터넷에서 포트 3389를 여는 것은 프로덕션 워크로드 관리에 권장되지 않습니다. </br> Azure 가상 머신에 안전하게 액세스하는 방법에 대해서는  **[Azure Bastion이란?](../../bastion/bastion-overview.md)을 참조하세요.**

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | 기본 네트워크 이름 적용 |
    | 서브넷 | 기본 서브넷 구성을 적용합니다. |
    | 공용 IP | **새로 생성** 를 선택합니다. </br> **공용 IP 주소 만들기** 에서, 이름에 **myPublicIP** 를 입력합니다. </br> **SKU** 의 경우 **표준** 을 선택합니다. </br> **할당** 에서 **고정** 을 선택합니다. </br> **확인** 을 선택합니다.  |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 를 선택합니다. |
   
5. **검토 + 만들기** 를 선택합니다. 
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 **myResourceGroup** 을 입력합니다. 검색 결과에 **myResourceGroup** 이 보이면 선택합니다.

2. **리소스 그룹 삭제** 를 선택합니다.

3. **리소스 그룹 이름 입력:** 에 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

[IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)를 참조하세요.

* 공용 IP 주소를 동적에서 정적으로 변경하기.

* 개인 IP 주소를 사용하여 작업합니다.

공용 IP 주소에는 [명목 요금](https://azure.microsoft.com/pricing/details/ip-addresses)이 부과됩니다. 구독당 사용할 수 있는 공용 IP 주소의 수는 [제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)되어 있습니다.

가상 머신 공용 IP 주소의 SKU는 백 엔드 풀에 추가될 때 Azure Load Balancer의 공용 IP SKU와 일치해야 합니다. 자세한 내용은 [Azure Load Balancer](../../load-balancer/skus.md)를 참조하세요.

Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.

- [고정 공용 IP 주소](public-ip-addresses.md#ip-address-assignment)에 대해 자세히 알아봅니다.

- Azure에서 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.

- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.

- [개인 IP 주소](private-ip-addresses.md), 및 Azure 가상 머신에 [고정 개인 IP 주소](virtual-network-network-interface-addresses.md#add-ip-addresses) 할당에 대해 알아봅니다.

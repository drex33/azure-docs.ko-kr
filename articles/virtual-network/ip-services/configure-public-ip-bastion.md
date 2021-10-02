---
title: Azure Bastion에서 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 Azure Bastion에서 사용하는 방법과 구성을 변경하는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 22fa5e1843cdff45a5bea9fb61393a0579278964
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373010"
---
# <a name="manage-a-public-ip-address-with-azure-bastion"></a>Azure Bastion에서 공용 IP 주소 관리

공용 IP 주소는 표준과 기본의 두 가지 SKU로 제공됩니다. SKU를 선택하면 IP 주소의 기능이 결정됩니다. SKU에 따라 IP 주소를 연결할 수 있는 리소스가 결정됩니다.

Azure Bastion은 가상 네트워크의 가상 머신에 안전한 관리 연결 기능을 제공하기 위해 배포됩니다. Azure Bastion 서비스를 사용하면 가상 네트워크의 VM에 안전하고 원활하게 RDP 및 SSH를 제공할 수 있습니다. Azure Bastion을 사용하면 VM에서 공용 IP를 노출하지 않고 연결할 수 있습니다. 추가 클라이언트/에이전트 또는 소프트웨어를 설치하지 않아도 Azure Portal에서 직접 연결할 수 있습니다. Azure Bastion은 표준 SKU 공용 IP 주소를 지원합니다.

Azure Bastion 호스트를 사용하려면 구성에 공용 IP 주소가 필요합니다.

이 문서에서는 구독에서 기존 공용 IP를 사용하여 Azure Bastion 호스트를 만드는 방법에 대해 알아봅니다. Azure Bastion은 만든 후에 공용 IP 주소를 변경할 수 없습니다.  Azure Bastion은 공용 IP 접두사를 지원하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독의 표준 SKU 공용 IP 주소 1개. IP 주소를 리소스에 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md)을 참조하세요.
    - 이 문서에 나오는 예의 목적에 맞게 새 공용 IP 주소의 이름을 **myStandardPublicIP** 로 지정합니다.

## <a name="create-azure-bastion-using-existing-ip"></a>기존 IP를 사용하여 Azure Bastion 만들기

이 섹션에서는 Azure Bastion 호스트를 만듭니다. 필수 구성 요소에서 만든 IP 주소를 베스천 호스트용 공용 IP로 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **Bastion** 을 입력합니다.

3. 검색 결과에서 **Bastion** 을 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **Bastion 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 | 
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독 선택 |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **이름** 에 **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 이름 | **MyBastionHost** 를 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | **가상 네트워크 구성** |   |
    | 가상 네트워크 | **새로 만들기** 를 선택합니다. </br> **이름** 에 **MyVNet** 을 입력합니다. </br> 기본 주소 공간인 **10.4.0.0/16** 으로 그대로 둡니다. </br> 기본 서브넷인 **10.4.0.0/24** 으로 그대로 둡니다. </br> **기본** 서브넷 아래 텍스트 상자에 **AzureBastionSubnet** 을 입력합니다. </br> 주소 범위에 **10.4.1.0/27** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | 서브넷 | **AzureBastionSubnet** 을 선택합니다. |
    | **공용 IP 주소** |   |
    | 공용 IP 주소 | **기존 항목 사용** 을 선택합니다. |
    | 공용 IP 주소 선택 | **myStandardPublicIP** 를 선택합니다. |

6. **검토 + 만들기** 탭을 선택하거나, 파란색 **검토 + 만들기** 단추를 선택합니다.

7. **만들기** 를 선택합니다.

> [!NOTE]
> Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](../../bastion/bastion-overview.md)을 참조하세요.

## <a name="change-or-remove-public-ip-address"></a>공용 IP 주소 변경 또는 제거

Azure Bastion은 만든 후에 공용 IP 주소를 변경할 수 없습니다.

## <a name="more-information"></a>자세한 정보

* Azure Bastion을 통해 연결할 때 가상 머신에 별도의 공용 IP가 필요하지는 않습니다. 트래픽은 먼저 Bastion의 공용 IP로 라우팅됩니다. 그런 다음 Bastion에서 가상 컴퓨터와 연결된 개인 IP 주소에 RDP 또는 SSH 연결을 라우팅합니다. 

## <a name="caveats"></a>제한 사항

* 현재 Azure Bastion에 대한 공용 IPv6 주소는 지원되지 않습니다.  

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Bastion을 만들고 기존 공용 IP를 사용하는 방법에 대해 알아보았습니다. 

- Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](../../bastion/bastion-overview.md)을 참조하세요.
- Azure의 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](../../virtual-network/public-ip-addresses.md)를 참조하세요.

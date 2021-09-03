---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 기타 네트워크 요구 사항 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)에 대한 추가 네트워크 요구 사항에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/3/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a91f2116eb7eb2e6d7908e5c8c4e5171fff8c294
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361075"
---
# <a name="other-network-requirements-for-large-instances"></a>대규모 인스턴스의 기타 네트워크 요구 사항

이 문서에서는 Azure의 SAP HANA(대규모 인스턴스)를 배포할 때 필요할 수 있는 다른 네트워크 요구 사항을 살펴보겠습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 사용자가 다음 단계를 완료했다고 가정합니다.
- [Azure VM을 HANA 큰 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)
- [가상 네트워크를 HANA 대규모 인스턴스에 연결](hana-connect-vnet-express-route.md)

## <a name="add-more-ip-addresses-or-subnets"></a>더 많은 IP 주소 또는 서브넷 추가

IP 주소 또는 서브넷을 더 추가해야 할 수도 있습니다. IP 주소 또는 서브넷을 추가할 때 Azure Portal, PowerShell 또는 Azure CLI를 사용합니다.

새 IP 주소 범위를 가상 네트워크 주소 공간에 새 범위로 추가합니다. 새 집계 범위를 생성하지 마세요. 이 변경 사항을 Microsoft에 제출합니다. 이렇게 하면 해당 새 IP 주소 범위에서 클라이언트의 HANA 대규모 인스턴스로 연결할 수 있습니다. Azure 지원 요청을 열고 새 가상 네트워크 주소 공간이 추가되도록 할 수 있습니다. 확인 메시지가 수신되면 [Azure VM을 HANA 대규모 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)에서 설명하는 단계를 수행합니다. 

Azure Portal에서 다른 서브넷을 만들려면 [Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요. PowerShell에서 서브넷을 하나 만들려면 [PowerShell을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요.

## <a name="add-virtual-networks"></a>가상 네트워크 추가

Azure 가상 네트워크를 하나 이상 처음 연결한 후에 Azure의 SAP HANA(대규모 인스턴스)에 액세스하는 가상 네트워크를 추가로 연결하는 것이 좋습니다. 먼저 Azure 지원 요청을 제출합니다. 해당 요청에 특정 Azure 배포를 식별하는 구체적인 정보를 포함합니다. 또한, Azure 가상 네트워크 주소 공간의 IP 주소 공간 범위도 포함합니다. 그러면 Microsoft Service Management의 SAP HANA에서 추가된 가상 네트워크와 Azure ExpressRoute를 연결하는 데 필요한 정보를 제공합니다. 모든 가상 네트워크에 대해 ExpressRoute 회로를 HANA 대규모 인스턴스에 연결하는 데 고유한 권한 부여 키가 필요합니다.

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 증가

Microsoft Service Management의 SAP HANA에 문의하세요. Azure의 SAP HANA(대규모 인스턴스) ExpressRoute 회로의 대역폭을 늘리라는 안내를 받는 경우 Azure 지원 요청을 만듭니다. (최대 10GBPS의 단일 회로 대역폭으로 증가하도록 요청할 수 있습니다.) 그러면 작업이 완료된 후에 Azure에서 이보다 속도를 빠르게 하기 위해 아무 작업도 수행할 필요가 없다는 알림이 표시됩니다.

## <a name="add-another-expressroute-circuit"></a>다른 ExpressRoute 회로 추가

Microsoft Service Management의 SAP HANA에 문의하세요. 또 다른 ExpressRoute 회선을 추가하도록 권장되는 경우 Azure 지원 요청(새 회로에 연결하기 위한 권한 부여 정보를 얻기 위한 요청 포함)을 만듭니다. 요청하기 전에 가상 네트워크에 사용되는 주소 공간을 정의해야 합니다. 그러면 Microsoft Service Management의 SAP HANA에서 권한을 제공할 수 있습니다.

새 회로가 만들어지고 Microsoft Service Management의 SAP HANA 구성이 완료되면 계속해야 하는 정보가 포함된 알림을 받게 됩니다. 동일한 Azure 지역에 있는 Azure(대규모 인스턴스) ExpressRoute 회로상의 또 다른 SAP HANA에 이미 연결된 경우 Azure 가상 네트워크를 이 추가된 회로에 연결할 수 없습니다.

## <a name="delete-a-subnet"></a>서브넷 삭제

가상 네트워크 서브넷을 제거하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용할 수 있습니다. Azure 가상 네트워크 IP 주소 범위 또는 주소 공간이 집계 범위인 경우 Microsoft와 함께 조치를 취하지 않아도 됩니다. (가상 네트워크는 삭제된 서브넷을 포함하는 BGP 경로 주소 공간을 아직 전파하고 있습니다.) 

Azure 가상 네트워크 주소 범위 또는 주소 공간을 여러 IP 주소 범위로 정의했을 수 있습니다. 이러한 범위 중 하나가 삭제된 서브넷에 할당되었을 수 있습니다. 이러한 할당된 IP 주소 범위를 가상 네트워크 주소 공간에서 삭제해야 합니다. 그런 다음, Microsoft Service Management의 SAP HANA에 Azure의 SAP HANA(대규모 인스턴스)가 통신하도록 허용되는 범위에서 해당 주소 공간을 삭제할 것을 알립니다.

자세한 내용은 [서브넷 삭제](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet)를 참조하세요.

## <a name="delete-a-virtual-network"></a>가상 네트워크 삭제

자세한 내용은 [가상 네트워크 삭제](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조하세요.

Microsoft Service Management의 SAP HANA는 Azure의 SAP HANA(대규모 인스턴스) ExpressRoute 회로에 대한 기존 권한을 제거합니다. 또한, HANA 대규모 인스턴스와의 통신을 위해 Azure 가상 네트워크 IP 주소 범위 또는 주소 공간도 제거합니다.

가상 네트워크를 제거한 후에는 Azure 지원 요청을 열어 제거할 IP 주소 공간 범위를 제공합니다.

모든 것을 제거해야 합니다. 다음을 삭제합니다.
- ExpressRoute 연결
- 가상 네트워크 게이트웨이
- 가상 네트워크 게이트웨이 공용 IP
- 가상 네트워크

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute 회로 삭제

여분의 Azure의 SAP HANA(대규모 인스턴스) ExpressRoute 회로를 제거하려면 Microsoft Service Management의 SAP HANA에 대한 Azure 지원 요청을 엽니다. 회로 삭제를 요청합니다. Azure 구독 내에서 필요에 따라 가상 네트워크를 삭제하거나 유지할 수 있습니다. 그러나 HANA 대규모 인스턴스 ExpressRoute 회로와 연결된 가상 네트워크 게이트웨이 간에 연결을 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure의 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [SAP HANA(대규모 인스턴스) 설치 및 구성](hana-installation.md)

---
title: Azure DNS 프라이빗 영역의 자동 등록 기능이란?
description: Azure DNS 프라이빗 영역의 자동 등록 기능에 대한 개요입니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: 0854412a83f941e43e79ebcde2f70ae62dabdf15
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965164"
---
# <a name="what-is-the-auto-registration-feature-in-azure-dns-private-zones"></a>Azure DNS 프라이빗 영역의 자동 등록 기능이란?

Azure DNS 프라이빗 영역 자동 등록 기능을 사용하면 가상 네트워크에 배포된 가상 머신에 대한 DNS 레코드 관리가 간편해집니다. 이 설정을 사용하는 프라이빗 DNS 영역에 [가상 네트워크를 연결하는](./private-dns-virtual-network-links.md) 경우 가상 네트워크에 배포된 각 가상 머신에 대한 DNS 레코드가 만들어집니다. 

각 가상 머신에 대해 A 레코드와 PTR 레코드가 만들어집니다. 연결된 프라이빗 DNS 영역에서 새로 배포된 가상 머신에 대한 DNS 레코드도 자동으로 만들어집니다. 가상 머신이 삭제되면 연결된 모든 DNS 레코드도 프라이빗 DNS 영역에서 삭제됩니다.

자동 등록을 사용하려면 가상 네트워크 링크를 만들 때 “자동 등록 사용”의 확인란을 선택합니다.

:::image type="content" source="./media/privatedns-concepts/enable-autoregistration.png" alt-text="가상 네트워크 링크 추가 페이지의 자동 등록 사용 스크린샷":::

## <a name="restrictions"></a>제한

* 자동 등록은 가상 머신에 대해서만 작동합니다. 내부 부하 분산 장치 등의 다른 모든 리소스의 경우 가상 네트워크에 연결된 프라이빗 DNS 영역에서 DNS 레코드를 수동으로 만들 수 있습니다.
* DNS 레코드는 주 가상 머신 NIC에 대해서만 자동으로 생성됩니다. 가상 머신에 NIC가 둘 이상 있는 경우 다른 네트워크 인터페이스에 대한 DNS 레코드를 수동으로 만들 수 있습니다.
* 주 가상 머신 NIC에서 DHCP를 사용하는 경우에만 DNS 레코드가 자동으로 생성됩니다. [Azure에서 여러 IP 주소가](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config) 포함된 구성 등, 고정 IP를 사용할 경우 자동 등록에서 해당 가상 머신에 대한 레코드를 만들지 않습니다.
* IPv6에 대한 자동 등록(AAAA 레코드)은 지원되지 않습니다.
* 프라이빗 DNS 영역에 대한 자동 등록은 단일 VNET으로 제한됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.yml)를 참조하세요.

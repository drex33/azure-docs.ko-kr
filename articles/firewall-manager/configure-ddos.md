---
title: Azure 방화벽 관리자를 사용 하 여 Azure DDoS Protection 계획 구성
description: Azure 방화벽 관리자를 사용 하 여 Azure DDoS Protection Plan Standard를 구성 하는 방법을 알아봅니다.
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ec6c08fab14c2c08ed719d616b84f1a4d744eb9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372850"
---
# <a name="configure-an-azure-ddos-protection-plan-using-azure-firewall-manager-preview"></a>Azure 방화벽 관리자를 사용 하 여 Azure DDoS Protection 계획 구성 (미리 보기)

Azure 방화벽 관리자는 대규모로 네트워크 리소스를 관리 하 고 보호 하는 플랫폼입니다. Azure 방화벽 관리자 내에서 DDoS 보호 계획과 가상 네트워크를 연결할 수 있습니다.

> [!IMPORTANT]
> Azure 방화벽 관리자를 사용 하 여 Azure DDoS Protection 계획을 구성 하는 것은 현재 미리 보기 상태입니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 

> [!TIP]
> 현재 DDoS Protection Standard는 가상 Wan을 지원 하지 않습니다. 그러나 DDoS Protection 계획을 연결 하는 가상 네트워크의 Azure 방화벽에 인터넷 트래픽을 강제로 터널링 하 여이 제한을 해결할 수 있습니다.

단일 테 넌 트에서 DDoS 보호 계획을 여러 구독에서 가상 네트워크에 적용할 수 있습니다. DDoS 보호 계획에 대 한 자세한 내용은  [Azure DDoS Protection 표준 개요](../ddos-protection/ddos-protection-overview.md)를 참조 하세요.

이 기능이 어떻게 작동 하는지 확인 하려면 방화벽 정책을 만든 다음 Azure 방화벽으로 보호 된 가상 네트워크를 만듭니다. 그런 다음 DDoS Protection 계획을 만든 다음 가상 네트워크와 연결 합니다.

## <a name="create-a-firewall-policy"></a>방화벽 정책 만들기

방화벽 관리자를 사용 하 여 방화벽 정책을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 방화벽 관리자를 엽니다.
1. **Azure 방화벽 정책** 을 선택 합니다.
1. **Azure Firewall 정책 만들기** 를 선택합니다.
1. **리소스 그룹** 에 대해 **DDoS-rg** 를 선택 합니다.
1. **정책 세부 정보**, **이름** 에서 **fw-registry.pol-01** 을 입력 합니다.
1. **지역** 에서 **미국 서 부 2** 를 선택 합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.


## <a name="create-a-secured-virtual-network"></a>보안 가상 네트워크 만들기

방화벽 관리자를 사용 하 여 보안 가상 네트워크를 만듭니다.

1. Firewall Manager를 엽니다.
1. **가상 네트워크** 를 선택 합니다.
1. **새 보안 Virtual Network 만들기** 를 선택 합니다.
1. **리소스 그룹** 에 대해 **DDoS-rg** 를 선택 합니다.
1. **지역** 에서 **미국 서 부 2** 를 선택 합니다.
1. **허브 Virtual Network 이름** 에 **hub-vnet-01** 을 입력 합니다.
1. **주소 범위** 에 **10.0.0.0/16** 을 입력 합니다.
1. **다음: Azure 방화벽** 을 선택 합니다.
1. **공용 IP 주소** 에 대해 **새로 추가** 를 선택 하 고 이름에 **fw-Pip** 를 입력 한 다음 **확인** 을 선택 합니다.
1. **방화벽 서브넷 주소 공간** 에 **10.0.0.0/24** 를 입력 합니다.
1. **방화벽 정책** 에 대해 **registry.pol-01** 을 선택 합니다.
1. **다음: 검토 + 만들기** 를 선택합니다.
1. **생성** 를 선택합니다.

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

방화벽 관리자를 사용 하 여 DDoS Protection 계획을 만듭니다. **DDoS Protection 계획** 페이지를 사용 하 여 Azure DDoS Protection 계획을 만들고 관리할 수 있습니다.

:::image type="content" source="media/configure-ddos/firewall-ddos.png" alt-text="방화벽 관리자 DDoS Protection 계획 페이지의 스크린샷":::

1. Firewall Manager를 엽니다.
1. **DDoS Protection 계획** 을 선택 합니다.
1. **생성** 를 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택합니다.
1. 리소스 그룹 이름에 **DDos-rg** 를 입력 합니다.
1. **인스턴스 세부 정보**, **이름** 에서 **DDoS-01** 을 입력 합니다.
1. **지역** 에서 **(Us) 미국 서 부 2** 를 선택 합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="associate-a-ddos-protection-plan"></a>DDoS Protection 계획 연결

이제 DDoS Protection 요금제를 보안 가상 네트워크와 연결할 수 있습니다.

1. Firewall Manager를 엽니다.
1. **가상 네트워크** 를 선택 합니다.
1. **허브-vnet-01** 의 확인란을 선택 합니다.
1. **보안 관리**, **DDoS Protection 계획 추가** 를 선택 합니다.
1. **DDoS protection standard** 에서 **사용** 을 선택 합니다.
1. **DDoS 보호 계획** 의 경우 **DDoS-01** 을 선택 합니다.
1. **추가** 를 선택합니다.
1. 배포가 완료 되 면 **새로 고침** 을 선택 합니다.

이제 가상 네트워크에 연결 된 DDoS Protection 계획이 있음을 볼 수 있습니다.

:::image type="content" source="media/configure-ddos/ddos-protection.png" alt-text="DDoS Protection 요금제를 사용 하는 가상 네트워크를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

DDoS Protection 계획에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure DDoS Protection 표준 개요](../ddos-protection/ddos-protection-overview.md)
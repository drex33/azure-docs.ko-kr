---
title: Azure Portal을 사용하여 Azure DDoS Protection 표준 관리
description: Azure DDoS Protection 표준을 사용하여 공격을 완화하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: yitoh
ms.openlocfilehash: 803d89dedc59c53001f078492d956e5e50d5411b
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305125"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>빠른 시작: Azure DDoS Protection Standard 만들기 및 구성

Azure Portal을 사용하여 Azure DDoS Protection Standard를 시작합니다. 

DDoS 보호 계획은 구독 전반에 걸쳐 DDoS 보호 표준을 사용하도록 설정된 일단의 가상 네트워크를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다. 

이 빠른 시작에서는 DDoS 보호 계획을 만들고 가상 네트워크에 연결합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- https://portal.azure.com 에서 Azure Portal에 로그인합니다. 계정이 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할이나 [권한](manage-permissions.md)의 방법 가이드에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 할당되었는지 확인합니다.

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.
2. *DDoS* 용어를 검색합니다. **DDos 보호 계획** 이 검색 결과에 표시되면 선택합니다.
3. **만들기** 를 선택합니다.
4. 다음 값을 입력하거나 선택하고 **만들기** 를 선택합니다.

    |설정        |값                                              |
    |---------      |---------                                          |
    |이름           | _MyDdosProtectionPlan_ 을 입력합니다.                     |
    |Subscription   | 구독을 선택합니다.                         |
    |Resource group | **새로 만들기** 를 선택하고, _MyResourceGroup_ 을 입력합니다.|
    |Location       | _미국 동부_ 를 입력합니다.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>가상 네트워크에 DDoS 보호 사용

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>새 가상 네트워크에 DDoS 보호 사용

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.
2. **네트워킹** 을 선택한 다음 **가상 네트워크** 를 선택합니다.
3. 다음 값을 입력하거나 선택하고, 나머지 기본값을 그대로 적용한 다음, **만들기** 를 선택합니다.

    | 설정         | 값                                           |
    | ---------       | ---------                                       |
    | 이름            | _MyVNet_ 을 입력합니다.                                 |
    | Subscription    | 구독을 선택합니다.                                    |
    | Resource group  | **기존 항목 사용** 을 선택한 다음, **MyResourceGroup** 을 선택합니다. |
    | Location        | _미국 동부_ 를 입력합니다.                                                    |
    | DDoS Protection 표준 | **사용** 을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.|

가상 네트워크에 대해 DDoS 표준을 사용하도록 설정하면 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. DDoS 표준을 사용하도록 설정된 가상 네트워크를 이동해야 하는 경우 먼저 DDoS 표준을 사용하지 않도록 설정하고, 가상 네트워크를 이동한 다음, DDoS 표준을 사용하도록 설정합니다. 이동 후에는 가상 네트워크의 모든 보호된 공용 IP 주소에 대한 자동 조정된 정책 임계값이 다시 설정됩니다.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>기존 가상 네트워크에 DDoS 보호 사용

1. 기존 DDoS 보호 계획이 없는 경우 [DDoS 보호 계획 만들기](#create-a-ddos-protection-plan)의 단계를 완료하여 DDoS 보호 계획을 만듭니다.
2. Azure Portal 위쪽의 **리소스, 서비스 및 문서 검색 상자** 에서 DDoS 보호 표준을 사용하도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
3. **설정** 아래에서 **DDoS 보호** 를 선택합니다.
4. **표준** 을 선택합니다. **DDoS 보호 계획** 아래에서 기존 DDoS 보호 계획 또는 1단계에서 만든 계획을 선택한 다음, **저장** 을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>모든 가상 네트워크에 DDoS 보호 사용

이 [기본 정책](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)은 정의된 범위에서 DDoS Protection 표준을 사용하지 않는 가상 네트워크를 검색한 다음, 선택적으로 VNet을 보호하기 위한 연결을 만드는 수정 작업을 만듭니다. 기본 제공 정책의 전체 목록은 [Azure DDoS Protection 표준에 대한 Azure Policy 기본 제공 정의](policy-reference.md)를 참조하세요. 

## <a name="validate-and-test"></a>유효성 검사 및 테스트

먼저 DDoS 보호 계획의 세부 정보를 확인합니다.

1. 포털의 왼쪽 위에서 **모든 서비스** 를 선택합니다.
2. **필터** 상자에서 *DDoS* 를 입력합니다. **DDoS 보호 계획** 이 결과에 표시되면 선택합니다.
3. 목록에서 DDoS 보호 계획을 선택합니다.

_MyVnet_ 가상 네트워크가 나열되어야 합니다. 

### <a name="view-protected-resources"></a>보호된 리소스 보기
**보호된 리소스** 에서 보호된 가상 네트워크 및 공용 IP 주소를 보거나 DDoS 보호 계획에 가상 네트워크를 더 추가할 수 있습니다.

![보호된 리소스 보기](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 위해 리소스를 보관할 수 있습니다. 더는 필요 없으면 _MyResourceGroup_ 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 DDoS 보호 계획과 모든 관련 리소스도 함께 삭제됩니다. 이 DDos 보호 계획을 사용하지 않으려면 필요한 요금이 청구되지 않도록 리소스를 제거해야 합니다.

   >[!WARNING]
   >이 작업은 되돌릴 수 없습니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하여 선택하거나 Azure Portal 메뉴에서 **리소스 그룹** 을 선택합니다.

2. 필터링하거나 아래로 스크롤하여 _MyResourceGroup_ 리소스 그룹을 찾습니다.

3. 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

4. 확인할 리소스 그룹 이름을 입력한 다음, **삭제** 를 선택합니다.

가상 네트워크에 DDoS 보호를 사용하지 않으려면 다음을 수행합니다. 

1. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자** 에서 DDoS 보호 표준을 사용하지 않도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
2. **DDoS Protection 표준** 에서 **사용하지 않음** 을 선택합니다.

DDoS 보호 계획을 삭제하려면 먼저 모든 가상 네트워크를 해당 계획에서 분리해야 합니다. 

## <a name="next-steps"></a>다음 단계

DDoS 보호 계획에 대한 원격 분석을 보고 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [DDoS 보호 원격 분석 보기 및 구성](telemetry.md)

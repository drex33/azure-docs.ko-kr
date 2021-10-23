---
title: Azure Cost Management 데이터에 대한 액세스 할당
description: 이 문서에서는 다양 한 액세스 범위에 대 한 Cost Management 데이터에 대 한 사용 권한을 할당 하는 방법을 안내 합니다.
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 00d8b5b2e9a3792c3dd9212607916317c7eb41f8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247519"
---
# <a name="assign-access-to-cost-management-data"></a>Cost Management 데이터에 대한 액세스 할당

Azure Enterprise 규약을 사용 하는 사용자의 경우 Azure Portal 및 EA (Enterprise) 포털에서 부여 된 사용 권한 조합을 통해 사용자의 Cost Management 데이터 액세스 수준을 정의 합니다. 다른 Azure 계정 유형을 사용하는 사용자의 경우 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Cost Management 데이터에 대한 사용자의 액세스 수준을 정의하는 것이 더 간편합니다. 이 문서에서는 Cost Management 데이터에 대한 액세스 권한을 할당하는 방법을 안내합니다. 권한 조합이 할당되면 사용자는 액세스 권한이 있는 범위와 Azure Portal에서 선택한 범위에 따라 Cost Management에서 데이터를 봅니다.

사용자가 선택한 범위는 Cost Management 전체에서 데이터 통합을 제공하고 비용 정보에 대한 액세스를 제어하는 데 사용됩니다. 범위를 사용하는 경우 사용자는 여러 개의 범위를 선택하지 않습니다. 대신 자식 범위가 롤업하는 더 큰 범위를 선택한 다음, 보려는 대상으로 필터링합니다. 일부 사용자는 자식 범위가 롤업하는 부모 범위에 액세스할 수 없으므로 데이터 통합을 이해해야 합니다.

[Cost Management 액세스 제어](https://www.youtube.com/watch?v=_uQzQ9puPyM) 비디오를 보고 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 비용 및 요금을 볼 수 있는 액세스 권한을 할당하는 방법에 대해 알아봅니다. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Cost Management 범위

Cost Management는 다양한 유형의 Azure 계정을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 계정 유형에 따라 사용할 수 있는 범위가 결정됩니다.

### <a name="azure-ea-subscription-scopes"></a>Azure EA 구독 범위

Azure EA 구독의 비용 데이터를 보려면 사용자에게 적어도 다음 범위 중 하나 이상에 대한 읽기 액세스 권한이 있어야 합니다.

| **범위** | **정의되는 위치** | **데이터를 보는 데 필요한 액세스 권한** | **필수 구성 요소 EA 설정** | **데이터 통합 위치** |
| --- | --- | --- | --- | --- |
| 청구 계정<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | 엔터프라이즈 관리자 | None | 기업계약의 모든 구독 |
| department | [https://ea.azure.com](https://ea.azure.com/) | 부서 관리자 | **DA 요금 보기** 사용 | 부서에 연결된 등록 계정에 속하는 모든 구독 |
| 등록 계정<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | 계정 소유자 | **AO 요금 보기** 사용 | 등록 계정의 모든 구독 |
| 관리 그룹 | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 읽기 권한자(또는 기여자) | **AO 요금 보기** 사용 | 관리 그룹 아래의 모든 구독 |
| Subscription | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 읽기 권한자(또는 기여자) | **AO 요금 보기** 사용 | 구독의 모든 리소스/리소스 그룹 |
| Resource group | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 읽기 권한자(또는 기여자) | **AO 요금 보기** 사용 | 리소스 그룹의 모든 리소스 |

<sup>1</sup> 청구 계정은 기업 계약 또는 등록이라고도 합니다.

<sup>2</sup> 등록 계정은 계정 소유자라고도 합니다.

직접 엔터프라이즈 관리자는 [Azure Portal](https://portal.azure.com/)에서 청구 계정, 부서 및 등록 계정 범위를 할당할 수 있습니다. 자세한 내용은 [직접 Enterprise 계약에 대 한 Azure Portal 관리](../manage/direct-ea-administration.md)를 참조 하세요.

## <a name="other-azure-account-scopes"></a>다른 Azure 계정 범위

다른 Azure 구독의 비용 데이터를 보려면 사용자에게 적어도 다음 범위 중 하나 이상에 대한 읽기 액세스 권한이 있어야 합니다.

- 관리 그룹
- 구독
- Resource group

파트너가 Microsoft 고객 계약을 체결한 후에는 다양한 범위를 사용할 수 있습니다. CSP 고객은 CSP 파트너가 사용하도록 설정된 경우 Cost Management 기능을 사용할 수 있습니다. 자세한 내용은 [파트너용 Cost Management 시작](get-started-partners.md)을 참조하세요.

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Azure Portal에서 비용에 대한 액세스를 사용하도록 설정

부서 범위에서 **부서 관리자가 요금을 볼 수 있음**(DA 보기 요금) 옵션을 **On** 으로 설정해야 합니다. Azure Portal 또는 EA 포털에서 옵션을 구성합니다. 다른 모든 범위에는 **계정 소유자가 요금을 볼 수 있음**(AO 보기 요금) 옵션을 **On** 으로 설정해야 합니다.

Azure Portal에서 옵션을 사용하려면 다음을 수행합니다.

1. https://portal.azure.com 에서 Azure Portal에 엔터프라이즈 관리자 계정으로 로그인합니다.
1. 모든 **Cost Management + 청구** 메뉴 항목을 선택합니다.
1. **청구 범위** 를 선택하여 사용 가능한 청구 범위 및 청구 계정 목록을 표시합니다.
1. 사용 가능한 청구 계정 목록에서 **청구 계정** 을 선택합니다.
1. **설정** 에서 **정책** 메뉴 항목을 선택하고 설정을 구성합니다.  
    ![청구 범위 정책에서 청구 비용 옵션 표시](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

요금 보기 옵션을 사용하도록 설정하면 대부분의 범위에 Azure Portal의 Azure RBAC(Azure 역할 기반 액세스 제어) 권한 구성도 필요합니다.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>EA 포털에서 비용에 대한 액세스를 사용하도록 설정

부서 범위에는 EA 포털에서 **사용** 으로 설정된 **DA 요금 보기** 옵션이 필요합니다. Azure Portal 또는 EA 포털에서 옵션을 구성합니다. 다른 모든 범위에는 EA 포털에서 **사용** 으로 설정된 **AO 요금 보기** 옵션이 필요합니다.

EA 포털에서 옵션을 사용하려면 다음을 수행합니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리** 를 선택합니다.
3. 액세스를 제공하려는 Cost Management 범위의 경우 요금 옵션으로 **DA 요금 보기** 및/또는 **AO 요금 보기** 를 사용하도록 설정합니다.  
    ![DA 및 AO 보기 요금 옵션이 표시된 등록 탭](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

요금 보기 옵션을 사용하도록 설정하면 대부분의 범위에 Azure Portal의 Azure RBAC(Azure 역할 기반 액세스 제어) 권한 구성도 필요합니다.

## <a name="enterprise-administrator-role"></a>엔터프라이즈 관리자 역할

기본적으로 엔터프라이즈 관리자는 청구 계정(기업 계약/등록) 및 자식 범위인 다른 모든 범위에 액세스할 수 있습니다. 엔터프라이즈 관리자는 다른 사용자의 범위에 대한 액세스 권한을 할당합니다. 비즈니스 연속성을 위한 모범 사례로, 엔터프라이즈 관리자 액세스 권한이 있는 두 명의 사용자가 있어야 합니다. 다음 섹션은 다른 사용자의 범위에 대한 액세스 권한을 할당하는 엔터프라이즈 관리자의 연습 예제입니다.

## <a name="assign-billing-account-scope-access"></a>청구 계정 범위 액세스 할당

청구 계정 범위에 액세스하려면 EA 포털에서 엔터프라이즈 관리자 권한이 필요합니다. 엔터프라이즈 관리자는 전체 EA 등록 또는 여러 등록에서 비용을 볼 수 있습니다. Azure Portal에서는 청구 계정 범위에 대한 어떠한 작업도 필요하지 않습니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리** 를 선택합니다.
3. **등록** 탭에서 관리하려는 등록을 선택합니다.  
    ![EA 포털에서 등록 선택](./media/assign-access-acm-data/ea-portal.png)
4. **+ 관리자 추가** 를 선택합니다.
5. [관리자 추가] 상자에서 인증 유형을 선택하고 사용자의 이메일 주소를 입력합니다.
6. 사용자에게 비용 및 사용량 데이터에 대한 읽기 전용 액세스 권한이 있어야 하는 경우 **읽기 전용** 아래에서 **예** 를 선택합니다.  그렇지 않은 경우 **아니요** 를 선택합니다.
7. **추가** 를 클릭하여 계정을 선택합니다.  
    ![관리자 추가 상자에 표시된 예제 정보](./media/assign-access-acm-data/add-admin.png)

새 사용자가 Cost Management의 데이터에 액세스하는 데 최대 30분이 걸릴 수 있습니다.

### <a name="assign-department-scope-access"></a>부서 범위 액세스 할당

부서 범위에 액세스하려면 EA 포털에서 부서 관리자(DA 요금 보기) 액세스 권한이 필요합니다. 부서 관리자는 한 부서 또는 여러 부서와 연결된 비용 및 사용량 데이터를 볼 수 있습니다. 부서에 대한 데이터에는 부서에 연결된 등록 계정에 속한 모든 구독이 포함됩니다. Azure Portal에서는 어떠한 작업도 필요하지 않습니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리** 를 선택합니다.
3. **등록** 탭에서 관리하려는 등록을 선택합니다.
4. **부서** 탭을 선택한 다음, **관리자 추가** 를 선택합니다.
5. [부서 관리자 추가] 상자에서 인증 유형을 선택한 다음, 사용자의 이메일 주소를 입력합니다.
6. 사용자에게 비용 및 사용량 데이터에 대한 읽기 전용 액세스 권한이 있어야 하는 경우 **읽기 전용** 아래에서 **예** 를 선택합니다.  그렇지 않은 경우 **아니요** 를 선택합니다.
7. 부서 관리자 권한을 부여하려는 부서를 선택합니다.
8. **추가** 를 클릭하여 계정을 선택합니다.  
    ![부서 관리자 추가 상자에 필요한 정보 입력](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>등록 계정 범위 액세스 할당

등록 계정 범위에 액세스하려면 EA 포털에서 계정 소유자(AO 요금 보기) 액세스 권한이 있어야 합니다. 계정 소유자는 해당 등록 계정에서 생성된 구독과 관련된 비용 및 사용량 데이터를 볼 수 있습니다. Azure Portal에서는 어떠한 작업도 필요하지 않습니다.

직접 엔터프라이즈 관리자는 Azure Portal 부서 관리자 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Portal 부서 관리자 추가를](../manage/direct-ea-administration.md#add-a-department-administrator)참조하세요.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리** 를 선택합니다.
3. **등록** 탭에서 관리하려는 등록을 선택합니다.
4. **계정** 탭을 선택한 다음, **계정 추가** 를 선택합니다.
5. [계정 추가] 상자에서 계정을 연결하려는 **부서** 를 선택하거나 할당되지 않은 상태로 그대로 둡니다.
6. 인증 유형을 선택하고, 계정 이름을 입력합니다.
7. 사용자의 이메일 주소를 입력한 다음, 선택적으로 비용 센터를 입력합니다.
8. **추가** 를 선택하여 계정을 만듭니다.  
    ![등록 계정에 대한 계정 추가 상자에 필요한 정보 입력](./media/assign-access-acm-data/add-account.png)

위의 단계를 완료하면 사용자 계정이 Enterprise Portal의 등록 계정이 되고 구독을 만들 수 있습니다. 사용자는 자신이 만든 구독에 대한 비용 및 사용량 데이터에 액세스할 수 있습니다.

## <a name="assign-management-group-scope-access"></a>관리 그룹 범위 액세스 할당

관리 그룹 범위를 보기 위해 액세스하려면 Cost Management Reader(또는 Reader) 이상의 권한이 필요합니다. Azure Portal에서 관리 그룹에 대한 권한을 구성할 수 있습니다. 다른 사람들의 액세스가 가능하도록 설정하려면 관리 그룹에 대해 사용자 액세스 관리자(또는 소유자) 이상의 권한이 있어야 합니다. 그리고 Azure EA 계정의 경우 EA 포털에서 **AO 요금 보기** 설정을 사용하도록 설정해야 합니다.

- Cost Management 읽기 권한자 역할을 관리 그룹 범위의 사용자에게 할당합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

직접 엔터프라이즈 관리자는 Azure Portal 계정 소유자 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Portal 계정 소유자 추가를 참조하세요.](../manage/direct-ea-administration.md#add-an-account-and-account-owner)

## <a name="assign-subscription-scope-access"></a>구독 범위 액세스 할당

구독에 액세스하려면 Cost Management 읽기 권한자(또는 읽기 권한자) 이상의 권한이 필요합니다. Azure Portal에서 구독에 대한 권한을 구성할 수 있습니다. 다른 사람들의 액세스가 가능하도록 설정하려면 구독에 대해 사용자 액세스 관리자(또는 소유자) 이상의 권한이 있어야 합니다. 그리고 Azure EA 계정의 경우 EA 포털에서 **AO 요금 보기** 설정을 사용하도록 설정해야 합니다.

- 구독 범위의 사용자에게 Cost Management 읽기 권한자 역할을 할당합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="assign-resource-group-scope-access"></a>리소스 그룹 범위 액세스 할당

리소스 그룹에 액세스하려면 Cost Management 읽기 권한자(또는 읽기 권한자) 이상의 권한이 필요합니다. Azure Portal에서 리소스 그룹에 대한 권한을 구성할 수 있습니다. 다른 사람들의 액세스가 가능하도록 설정하려면 리소스 그룹에 대해 사용자 액세스 관리자(또는 소유자) 이상의 권한이 있어야 합니다. 그리고 Azure EA 계정의 경우 EA 포털에서 **AO 요금 보기** 설정을 사용하도록 설정해야 합니다.


- 리소스 그룹 범위의 사용자에게 Cost Management 읽기 권한자 역할을 할당합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="cross-tenant-authentication-issues"></a>테넌트 간 인증 문제

현재 Cost Management 테넌트 간 인증에 대한 지원이 제한되어 있습니다. 테넌트 간에 인증을 시도하는 경우 비용 분석에서 **액세스 거부** 오류가 발생할 수 있습니다. 이 문제는 Azure RBAC(Azure 역할 기반 액세스 제어)를 다른 테넌트의 구독에 구성한 다음, 비용 데이터를 보려고 할 때 발생할 수 있습니다.

*이 문제를 해결하려면 다음을 수행합니다*. 테넌트 간 Azure RBAC를 구성한 후 1시간을 기다립니다. 그런 다음, 비용 분석에서 비용을 보거나 두 테넌트의 사용자에게 Cost Management 액세스 권한을 부여합니다.  


## <a name="next-steps"></a>다음 단계

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.

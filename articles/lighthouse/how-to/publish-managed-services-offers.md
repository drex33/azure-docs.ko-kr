---
title: Azure Marketplace에 관리형 서비스 제안 게시
description: Azure Lighthouse에 고객을 온보딩하는 관리형 서비스 제안을 게시하는 방법을 알아봅니다.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: af5ca37d312f5bdfcfae179997b920a466f01462
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536449"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Azure Marketplace에 관리형 서비스 제안 게시

이 문서에서는 파트너 센터에서 [상업용 Marketplace](../../marketplace/overview.md) 프로그램을 사용하여 [Azure Marketplace](https://azuremarketplace.microsoft.com)에 퍼블릭 또는 프라이빗 관리형 서비스 제안을 게시하는 방법을 알아봅니다. 제안을 구매하는 고객은 구독 또는 리소스 그룹을 위임하여 [Azure Lighthouse](../overview.md)를 통해 관리할 수 있습니다.

## <a name="publishing-requirements"></a>게시 요구 사항

제안을 만들고 게시하려면 [파트너 센터에 유효한 계정](../../marketplace/create-account.md)이 있어야 합니다. 계정이 아직 없는 경우 [등록 프로세스](https://aka.ms/joinmarketplace)를 통해 파트너 센터에서 계정을 만들고 상업용 Marketplace 프로그램에 등록하는 단계를 진행합니다.

[관리형 서비스 제안 인증 요구 사항](/legal/marketplace/certification-policies#700-managed-services)에 따라 관리형 서비스 제안을 게시하려면 [실버 또는 골드 클라우드 플랫폼 역량 수준](/partner-center/learn-about-competencies)이 있거나 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp)여야 합니다. 또한 고객이 제안을 배포할 때마다 [CRM 시스템에 레코드를 생성할 잠재 고객 대상을 입력](../../marketplace/plan-managed-service-offer.md#customer-leads)해야 합니다.

Azure Marketplace에 제안을 게시하지 않으려는 경우 또는 일부 요구 사항을 충족하지 않는 경우 Azure Resource Manager 템플릿을 사용하여 수동으로 고객을 온보딩할 수 있습니다. 자세한 내용은 [Azure Lighthouse에 고객 온보딩](onboard-customer.md)을 참조하세요.

다음 표를 사용하면 고객을 온보딩할 때 관리형 서비스 제안을 게시할지 Azure Resource Manager 템플릿을 사용할지를 결정하는 데 도움이 됩니다.

|**고려 사항**  |**관리형 서비스 제안**  |**ARM 템플릿**  |
|---------|---------|---------|
|[파트너 센터 계정](../../marketplace/create-account.md) 필요   |예         |예        |
|[실버 또는 골드 클라우드 플랫폼 역량 수준](/partner-center/learn-about-competencies)이나 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) 필요      |예         |예         |
|Azure Marketplace를 통해 신규 고객에게 제공     |예     |예       |
|특정 고객으로 제안을 제한할 수 있음     |예(CSP(클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정된 구독에서 사용할 수 없는 프라이빗 제안에만 해당)         |예         |
|Azure Portal을 통한 고객 동의 필요     |예     |예   |
|자동화를 사용하여 여러 구독, 리소스 그룹 또는 고객을 온보딩할 수 있음 |예     |예    |
|새로운 기본 제공 역할 및 Azure Lighthouse 기능에 즉시 액세스     |항상 그렇지는 않음(약간의 지연 후 일반적으로 사용 가능)         |예         |
|고객이 Azure Portal에서 업데이트된 제안을 검토하고 수락할 수 있음 | 예 | 예 |

> [!NOTE]
> 관리형 서비스 제안은 Azure Government 및 기타 국가 클라우드에서 사용하지 못할 수 있습니다.

## <a name="create-your-offer"></a>제품 만들기

제공해야 하는 모든 정보 및 자산을 포함하여 제안을 만드는 방법에 대한 자세한 지침은 [관리형 서비스 제안 만들기](../../marketplace/create-managed-service-offer.md)를 참조하세요.

일반 게시 프로세스에 관해 알아보려면 [상업용 Marketplace 설명서](../../marketplace/overview.md)를 검토하세요. [상업 마켓플레이스 인증 정책](/legal/marketplace/certification-policies), 특히 [관리 서비스](/legal/marketplace/certification-policies#700-managed-services) 섹션도 검토해야 합니다.

고객이 제안을 추가하면 하나 이상의 구독이나 리소스 그룹을 위임할 수 있습니다. 그러면 [Azure Lighthouse에 온보딩](#the-customer-onboarding-process)됩니다.

> [!IMPORTANT]
> 관리형 서비스 제안의 각 플랜에는 **매니페스트 세부 정보** 섹션이 있으며, 여기에서 해당 플랜을 구매하는 고객의 위임된 리소스 그룹 및/또는 구독에 액세스할 수 있는 테넌트의 Azure AD(Azure Active Directory) 엔터티를 정의합니다. 포함되는 모든 그룹(또는 사용자나 서비스 주체)은 플랜을 구매하는 모든 고객에 대해 동일한 권한을 갖게 됩니다. 각 고객과 작업할 서로 다른 그룹을 할당하려면 각 고객에게만 제공되는 별도의 [프라이빗 플랜](../../marketplace/private-offers.md)을 게시할 수 있습니다. 프라이빗 플랜은 CSP(클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정된 구독에서는 지원되지 않습니다.

## <a name="publish-your-offer"></a>제품 게시

모든 섹션을 완료한 후, 다음 단계는 Azure Marketplace에 제품을 게시하는 것입니다. **게시** 단추를 선택하여 제품을 라이브로 전환하는 프로세스를 시작합니다. 이 프로세스에 대한 자세한 내용은 [여기](../../marketplace/review-publish-offer.md)에서 확인할 수 있습니다.

언제든지 [제품의 업데이트된 버전을 게시](../../marketplace/update-existing-offer.md)할 수 있습니다. 예를 들어 이전에 게시된 제품에 새 역할 정의를 추가하려고 할 수 있습니다. 이렇게 하면 제품을 이미 추가한 고객은 Azure Portal의 [**서비스 공급자**](view-manage-service-providers.md) 페이지에 업데이트를 사용할 수 있음을 알려 주는 아이콘이 표시됩니다. 각 고객은 [변경 내용을 검토](view-manage-service-providers.md#update-service-provider-offers)하고 새 버전으로 업데이트할지 여부를 결정할 수 있습니다. 

## <a name="the-customer-onboarding-process"></a>고객 온보딩 프로세스

고객이 제안을 추가하면 [하나 이상의 특정 구독이나 리소스 그룹을 위임](view-manage-service-providers.md#delegate-resources)할 수 있습니다. 그러면 Azure Lighthouse에 온보딩됩니다. 고객이 제품을 수락했지만 아직 리소스를 위임하지 않은 경우 Azure Portal에서 [**서비스 공급자**](view-manage-service-providers.md) 페이지의 **공급자 제품** 섹션 맨 위에 메모가 표시됩니다.

> [!IMPORTANT]
> 위임은 온보딩하려는 구독(또는 온보딩하려는 리소스 그룹을 포함하는 구독)에 대해 [소유자](../../role-based-access-control/built-in-roles.md#owner)와 같이 `Microsoft.Authorization/roleAssignments/write` 권한을 갖는 역할이 있는 고객의 테넌트에서 게스트가 아닌 계정으로 수행해야 합니다. 구독을 위임할 수 있는 사용자를 찾기 위해 고객 테넌트의 사용자는 Azure Portal에서 구독을 선택하고, **IAM(액세스 제어)** 을 열고, [소유자 역할이 있는 모든 사용자를 볼](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription) 수 있습니다.

고객이 구독 또는 구독 내의 여러 리소스 그룹을 위임하면 해당 구독에 대해 **Microsoft.ManagedServices** 리소스 공급자가 등록되고 테넌트의 사용자는 제안의 권한 부여에 따라 위임된 리소스에 액세스할 수 있습니다.

> [!NOTE]
> 나중에 추가 구독 또는 리소스 그룹을 동일한 제품에 위임하려면 고객은 위임하기 전에 각 구독에 대해 [**Microsoft.ManagedServices** 리소스 공급자를 수동으로 등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)해야 합니다.

제안의 업데이트된 버전을 게시하면 고객은 [Azure Portal에서 변경 내용을 검토하고 새 버전을 수락](view-manage-service-providers.md#update-service-provider-offers)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace](../../marketplace/overview.md)에 대해 알아봅니다.
- [파트너 ID를 연결](partner-earned-credit.md)하여 고객 참여 간의 영향을 추적합니다.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객** 으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
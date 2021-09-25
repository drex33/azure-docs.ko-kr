---
title: 대규모 Azure Migrate 프로젝트 관리
description: Azure Lighthouse는 위임된 고객 리소스에서 Azure Migrate를 효과적으로 사용할 수 있도록 지원합니다.
ms.date: 09/13/2021
ms.topic: how-to
ms.openlocfilehash: afca0cedb8a79d5eaf3365ded5985f3b1f38052e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652304"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>Azure Lighthouse를 사용하여 대규모로 Azure Migrate 프로젝트 관리

이 항목에서는 [Azure Lighthouse](../overview.md)를 사용하여 여러 Azure AD(Azure Active Directory) 테넌트에서 스케일링 가능한 방식으로 [Azure Migrate](../../migrate/migrate-services-overview.md)를 사용하는 방법에 대한 개요를 제공합니다.

Azure Lighthouse를 사용하여 서비스 공급자는 여러 테넌트에 걸쳐 대규모로 작업을 한 번에 수행할 수 있으므로 관리 작업을 보다 효율적으로 수행할 수 있습니다.

Azure Migrate는 온-프레미스 서버, 인프라, 애플리케이션 및 데이터를 평가하고 Azure로 마이그레이션할 수 있는 중앙 허브를 제공합니다. 일반적으로 여러 고객에 대한 대규모 평가 및 마이그레이션을 수행하는 파트너는 [CSP(클라우드 솔루션 공급자) 구독 모델](/partner-center/customers-revoke-admin-privileges)을 사용하거나 [고객 테넌트에서 게스트 사용자를 만들어](../../active-directory/external-identities/what-is-b2b.md) 각 고객 구독에 개별적으로 액세스해야 합니다.

Azure Lighthouse를 Azure Migrate와 통합하면 서비스 공급자가 대규모로 여러 고객에 대한 워크로드를 검색, 평가, 마이그레이션할 수 있을 뿐만 아니라 고객이 환경을 완벽하게 보고 제어할 수 있습니다. Azure의 위임된 리소스 관리를 통해 서비스 공급자는 여러 고객 테넌트에서 관리하는 모든 Azure Migrate 프로젝트의 단일 보기를 제공합니다.

> [!NOTE]
> 파트너는 Azure Lighthouse를 통해 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버, AWS/GCP 인스턴스에 대한 검색, 평가, 마이그레이션을 수행할 수 있습니다. [VMware VM 마이그레이션](../../migrate/server-migrate-overview.md)에는 두 가지 옵션이 있습니다. 위임된 고객 구독의 마이그레이션 프로젝트를 작업하는 경우, 현재는 에이전트 기반 마이그레이션 메서드만 사용할 수 있습니다. 현재 에이전트 없는 복제를 사용한 마이그레이션은 고객 범위에 위임된 액세스를 통해 지원되지 않습니다.

> [!TIP]
> 이 항목은 서비스 공급자 및 고객을 염두에 두고 만들었지만 이 지침은 [Azure Lighthouse를 사용하여 여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에도 적용됩니다.

시나리오에 따라 고객 테넌트 또는 관리 테넌트에 Azure Migrate를 만들 수 있습니다. 아래 고려 사항을 검토하고 고객의 마이그레이션 요구에 가장 적합한 모델을 결정합니다.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>고객 테넌트에서 Azure Migrate 프로젝트 만들기

Azure Lighthouse를 사용할 때 한 가지 옵션은 고객 테넌트에 Azure Migrate 프로젝트를 만드는 것입니다. 그러면 관리 테넌트의 사용자가 마이그레이션 프로젝트를 만들 때 고객 구독을 선택할 수 있습니다. 서비스 공급자는 관리 테넌트에서 필요한 마이그레이션 작업을 수행할 수 있습니다. 여기에는 워크로드를 검색하기 위해 Azure Migrate 어플라이언스를 배포하는 것, VM을 그룹화하고 클라우드 관련 비용을 계산하여 워크로드를 평가하는 것, VM 준비 상태를 검토하는 것, 마이그레이션을 수행하는 것이 포함될 수 있습니다.

이 시나리오에서는 검색 및 평가 단계를 시작하고 해당 테넌트에서 실행할 수 있는 경우에도 리소스가 관리 테넌트에서 생성되고 저장되지 않습니다. 마이그레이션 프로젝트, 온-프레미스 워크로드에 대한 평가 보고서, 대상 공급자의 마이그레이션된 리소스와 같은 모든 리소스가 고객 구독에 배포됩니다. 그러나 서비스 공급자는 자체 테넌트 및 포털 환경에서 모든 고객 프로젝트에 액세스할 수 있습니다.

이 방법은 여러 고객 간에 작업하는 서비스 공급자를 위한 컨텍스트 전환을 최소화하는 동시에 고객이 자신의 테넌트에 모든 리소스를 유지할 수 있도록 합니다.

이 모델의 워크플로는 다음과 유사합니다.

1. 고객이 [Azure Lighthouse에 온보딩](onboard-customer.md)됩니다. Azure Migrate와 함께 사용될 ID에는 참가자 기본 제공 역할이 필요합니다. 이 역할을 사용하는 예제를 보려면 [위임된-리소스-관리-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) 샘플 템플릿을 참조하세요.
1. 지정된 사용자가 Azure Portal에서 관리 테넌트에 로그인한 다음 Azure Migrate로 이동합니다. 이 사용자는 적절한 위임된 고객 구독을 선택하여 [Azure Migrate 프로젝트](../../migrate/create-manage-projects.md)를 만듭니다.
1. 그다음에 [검색 및 평가를 위한 단계를 수행](../../migrate/tutorial-discover-vmware.md)합니다.

   VMware VM의 경우 어플라이언스를 구성하기 전에 검색을 vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 VM으로 제한할 수 있습니다. 범위를 설정하려면 어플라이언스에서 vCenter Server에 액세스하는 데 사용하는 계정에 대한 사용 권한을 할당합니다. 이는 여러 고객의 VM이 하이퍼바이저에서 호스트되는 경우에 유용합니다. Hyper-V의 검색 범위는 제한할 수 없습니다.

    > [!NOTE]
    > Azure Lighthouse에서 제공하는 위임된 액세스를 통해 Azure Migrate를 사용하여 마이그레이션할 VMware 가상 머신을 검색하고 평가할 수 있습니다. VMware 가상 머신 마이그레이션의 경우, 위임된 고객 구독의 마이그레이션 프로젝트에서 작업할 때 현재는 에이전트 기반 메서드만 지원됩니다.

1. 대상 고객 구독이 준비되면 Azure Lighthouse에서 부여된 액세스를 통해 마이그레이션을 진행합니다. 평가 결과 및 마이그레이션된 리소스를 포함하는 마이그레이션 프로젝트는 대상 구독 아래의 고객 테넌트에 생성됩니다.

> [!TIP]
> 마이그레이션에 앞서, 기반 인프라 리소스를 프로비저닝하고 가상 머신이 마이그레이션되는 구독을 준비하기 위해 랜딩 존을 배포해야 합니다. 랜딩 존 내 일부 리소스에 액세스하거나 그러한 리소스를 만들려면 Azure Lighthouse에서 현재 지원하지 않는 소유자 기본 제공 역할이 필요할 수 있습니다. 이러한 시나리오의 경우 고객은 CSP 구독 모델을 통해 게스트 액세스 역할을 제공하거나 관리자 액세스 권한을 위임해야 할 수 있습니다. 다중 테넌트 랜딩 존을 만드는 방법에 대한 자세한 내용은 GitHub의 [다중-테넌트-랜딩-존 데모 솔루션](https://github.com/Azure/Multi-tenant-Landing-Zones)을 참조하세요.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>관리 테넌트에서 Azure Migrate 프로젝트 만들기

이 시나리오에서는 관리 테넌트의 사용자가 검색 및 평가와 같은 마이그레이션 관련 작업을 계속 수행합니다. 그러나 마이그레이션 프로젝트 및 관련된 모든 리소스는 파트너 테넌트에 상주하며 고객은 프로젝트를 직접 볼 수 없습니다(하지만 원하는 경우 평가를 고객과 공유할 수는 있음). 각 고객의 마이그레이션 대상은 고객의 구독입니다.

이 방법을 통해 서비스 공급자는 마이그레이션 검색 및 평가 프로젝트를 신속하게 시작하여 고객 구독 및 테넌트의 초기 단계를 추상화합니다.

이 모델의 워크플로는 다음과 유사합니다.

1. 고객이 [Azure Lighthouse에 온보딩](onboard-customer.md)됩니다. Azure Migrate와 함께 사용될 ID에는 참가자 기본 제공 역할이 필요합니다. 이 역할을 사용하는 예제를 보려면 [위임된-리소스-관리-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) 샘플 템플릿을 참조하세요.
1. 지정된 사용자가 Azure Portal에서 관리 테넌트에 로그인한 다음 Azure Migrate로 이동합니다. 이 사용자는 관리 테넌트에 속하는 구독에 [Azure Migrate 프로젝트를 만듭니다](../../migrate/create-manage-projects.md).
1. 그다음에 [검색 및 평가를 위한 단계를 수행](../../migrate/tutorial-discover-vmware.md)합니다. 온-프레미스 VM은 관리 테넌트에서 생성된 마이그레이션 프로젝트 내에서 검색 및 평가된 후 거기서 마이그레이션됩니다.

   동일한 Hyper-V 호스트에서 여러 고객을 관리하는 경우 모든 워크로드를 한 번에 검색할 수 있습니다. 특정 고객의 VM을 동일한 그룹에서 선택할 수 있으며, 그러면 평가를 만들 수 있고 적절한 고객의 구독을 대상 공급자로 선택하여 마이그레이션을 수행할 수 있습니다. 검색 범위를 제한하지 않아도 되며, 하나의 마이그레이션 프로젝트에서 모든 고객 워크로드에 대한 전체 개요를 유지 관리할 수 있습니다.

1. 준비가 되면 워크로드를 복제하고 마이그레이션하기 위한 대상 공급자로 위임된 고객 구독을 선택하여 마이그레이션을 진행합니다. 새로 만든 리소스는 고객 구독에 존재하는 반면, 마이그레이션 프로젝트와 관련된 평가 데이터 및 리소스는 관리 테넌트에 유지됩니다.

참고: https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate 을 배포하기 전에 사용자 환경을 반영하도록 매개 변수 파일을 수정해야 합니다.

## <a name="partner-recognition-for-customer-migrations"></a>고객 마이그레이션에 대한 파트너 인정

[Microsoft 파트너 네트워크](https://partner.microsoft.com) 멤버로서, 위임된 고객 리소스를 관리하는 데 사용되는 자격 증명과 파트너 ID를 연결할 수 있습니다. 이를 통해 Microsoft는 마이그레이션 프로젝트를 비롯하여 사용자가 고객에 대해 수행한 작업을 기반으로 영향 및 Azure 소비 수익을 조직의 성과로 볼 수 있습니다.

자세한 내용은 [파트너 ID를 연결하여 위임된 리소스에 대한 영향 추적](partner-earned-credit.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Migrate](../../migrate/migrate-services-overview.md)에 대해 알아봅니다.
- Azure Lighthouse에서 지원하는 다른 [교차 테넌트 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.

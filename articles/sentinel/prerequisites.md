---
title: Azure Sentinel을 배포하기 위한 필수 조건
description: Azure Sentinel을 배포하기 위한 사전 배포 활동 및 필수 구성요소에 대해 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: b288a826a0911e73f516fa13a00d3e26b25d2ace
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529766"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-azure-sentinel"></a>Azure Sentinel을 배포하기 위한 사전 배포 활동 및 필수 구성요소

이 문서에서는 Azure Sentinel을 배포하기 위한 사전 배포 활동 및 필수 구성요소를 소개합니다.

## <a name="pre-deployment-activities"></a>사전 배포 활동

Azure Sentinel을 배포하기 전에 배포를 통해 최대한 빨리 최대 가치를 제공하는 데 집중할 수 있도록 다음 단계를 수행하는 것이 좋습니다.

1. 배포 예산과 타임라인을 정확하게 예측하는 데 도움이 되는 [데이터 원본](connect-data-sources.md)과 데이터 크기 요구 사항을 결정합니다.

    비즈니스 사용 사례를 검토하는 동안 또는 이미 구축된 현재 SIEM을 평가하여 이 정보를 결정할 수 있습니다. SIEM이 이미 있는 경우 데이터를 분석하여 가장 많은 가치를 제공하는 데이터 원본을 파악하고 Azure Sentinel 수집해야 합니다.

1. Azure Sentinel 작업 영역 디자인 다음과 같은 매개 변수를 고려합니다.

    - 단일 테넌트 또는 다중 테넌트 사용 여부
    - 데이터 수집 및 스토리지의 규정 준수 요구 사항
    - Azure Sentinel 데이터에 대한 액세스를 제어하는 방법

    자세한 내용은 [작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md) 및 [샘플 작업 영역 디자인](sample-workspace-designs.md)을 참조하세요.

1. 비즈니스 사용 사례, 데이터 원본 및 데이터 크기 요구 사항을 식별한 후 계획된 시나리오별 비용 영향을 고려하여 [예산 계획을 시작](azure-sentinel-billing.md)합니다.

    예산이 Azure Sentinel 및 Azure Log Analytics, 배포할 플레이북 등에 대한 데이터 수집 비용을 포함하는지 확인합니다.

    자세한 내용은 다음을 참조하세요.

    - [Azure Sentinel 비용 및 청구](azure-sentinel-billing.md)
    - [Azure Sentinel 가격 책정](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)
    - [논리 앱(플레이북) 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [장기 로그 보존 시 Azure Data Explorer 통합](store-logs-in-azure-data-explorer.md)

1. 요구 사항 및 타임라인에 따라 엔지니어 또는 설계자가 배포를 주도하도록 지정합니다. 이 개인은 배포를 주도해야 하며 팀의 기본 연락 지점이 되어야 합니다.

## <a name="azure-tenant-requirements"></a>Azure 테넌트 요구 사항

Azure Sentinel을 배포하기 전에 Azure 테넌트에 다음 요구 사항이 있는지 확인합니다.

- Azure에 액세스하고 리소스를 배포하려면 [Azure Active Directory 라이선스 및 테넌트](../active-directory/develop/quickstart-create-new-tenant.md) 또는 [유효한 결제 수단이 있는 개별 계정](https://azure.microsoft.com/free/)이 필요합니다.

- 테넌트가 있는 경우 리소스 생성 및 청구를 추적하려면 [Azure 구독](../cost-management-billing/manage/create-subscription.md)이 있어야 합니다.

- 구독한 후 구독을 사용하기 시작하려면 [적절한 권한](../role-based-access-control/index.yml)이 필요합니다. 새 구독을 사용하는 경우 AAD 테넌트의 관리자 이상을 구독의 [소유자/기여자](../role-based-access-control/rbac-and-directory-admin-roles.md)로 지정해야 합니다.

    - 사용 가능한 최소 권한이 부여된 액세스를 유지하려면 리소스 그룹 수준에서 역할을 할당합니다.
    - 사용 권한 및 액세스를 더 세밀하게 제어하려면 사용자 지정 역할을 설정합니다. 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/custom-roles.md)를 참조하세요.
    - 사용자와 보안 사용자를 추가로 분리하기 위해 [resource-context](resource-context-rbac.md) 또는 [table-level RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)를 사용하는 것이 좋습니다.

    Azure Sentinel에 지원되는 다른 역할 및 권한에 대한 자세한 내용은 [Azure Sentinel의 권한](roles.md)을 참조하세요.

- [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md)은 Azure Sentinel이 수집하고 감지, 분석, 기타 기능에 사용할 모든 데이터를 보관하는 데 필요합니다. 자세한 내용은 [Azure Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)를 참조하세요.

> [!TIP]
> Azure Sentinel 작업 영역을 설정할 때 Azure Sentinel 및 Log Analytics 작업 영역, 플레이북, 통합 문서 등 Azure Sentinel 사용자가 사용하는 리소스 전용 [리소스 그룹을 생성](../azure-resource-manager/management/manage-resource-groups-portal.md)합니다.
>
> 전용 리소스 그룹을 사용하면 리소스 그룹 수준에서 권한을 한 번만 할당할 수 있으며 권한은 모든 관련 리소스에 자동으로 적용됩니다. 리소스 그룹을 통해 액세스를 관리하면 부적절한 권한을 발급하지 않고도 Azure Sentinel을 효율적으로 사용할 수 있습니다. 리소스가 여러 리소스 그룹에 분산되어 있는 Azure Sentinel용 리소스 그룹이 없으면 사용자 또는 서비스 주체가 권한 부족으로 인해 필요한 작업을 수행하거나 데이터를 볼 수 없음을 알 수 있습니다.
>
> 계층별로 리소스에 대한 더 세밀한 액세스 제어를 구현하려면 추가 리소스 그룹을 사용하여 해당 그룹에서만 액세스해야 하는 리소스를 수용합니다. 리소스 그룹의 여러 계층을 사용하면 해당 계층 간에 액세스를 구분할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Sentinel 온보딩](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)
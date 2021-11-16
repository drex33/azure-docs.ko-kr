---
title: Microsoft 센티널 배포를 위한 필수 구성 요소
description: Microsoft 센티널 배포를 위한 배포 전 작업 및 사전 요구 사항에 대해 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bae2e792ab85ec87f1187c632f9582e9d38e87ed
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521133"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-microsoft-sentinel"></a>Microsoft 센티널 배포를 위한 배포 전 작업 및 필수 조건

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널 배포를 위한 배포 전 작업 및 필수 구성 요소를 소개 합니다.

## <a name="pre-deployment-activities"></a>사전 배포 활동

Microsoft 센티널을 배포 하기 전에 다음 단계를 수행 하 여 최대한 빨리 최대한 신속 하 게 배포에 집중할 수 있도록 하는 것이 좋습니다.

1. 배포 예산과 타임라인을 정확하게 예측하는 데 도움이 되는 [데이터 원본](connect-data-sources.md)과 데이터 크기 요구 사항을 결정합니다.

    비즈니스 사용 사례를 검토하는 동안 또는 이미 구축된 현재 SIEM을 평가하여 이 정보를 결정할 수 있습니다. SIEM이 이미 있는 경우 데이터를 분석 하 여 가장 많은 가치를 제공 하는 데이터 소스를 파악 하 고 Microsoft 센티널로 수집 합니다.

1. Microsoft 센티널 작업 영역을 디자인 합니다. 다음과 같은 매개 변수를 고려합니다.

    - 단일 테넌트 또는 다중 테넌트 사용 여부
    - 데이터 수집 및 저장에 대한 모든 규정 준수 요구 사항
    - Microsoft 센티널 데이터에 대 한 액세스를 제어 하는 방법

    자세한 내용은 [작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md) 및 [샘플 작업 영역 디자인](sample-workspace-designs.md)을 참조하세요.

1. 비즈니스 사용 사례, 데이터 원본 및 데이터 크기 요구 사항을 식별한 후 계획된 시나리오별 비용 영향을 고려하여 [예산 계획을 시작](azure-sentinel-billing.md)합니다.

    예산은 Microsoft 센티널 및 Azure Log Analytics, 배포할 플레이 북 등 모두에 대 한 데이터 수집 비용을 포함 하는지 확인 합니다.

    자세한 내용은 다음을 참조하세요.

    - [Microsoft 센티널 비용 및 청구](azure-sentinel-billing.md)
    - [Microsoft 센티널 가격 책정](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)
    - [논리 앱(플레이북) 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [장기 로그 보존 시 Azure Data Explorer 통합](store-logs-in-azure-data-explorer.md)

1. 요구 사항 및 타임라인에 따라 엔지니어 또는 설계자가 배포를 주도하도록 지정합니다. 이 개인은 배포를 주도해야 하며 팀의 기본 연락 지점이 되어야 합니다.

## <a name="azure-tenant-requirements"></a>Azure 테넌트 요구 사항

Microsoft 센티널을 배포 하기 전에 Azure 테 넌 트에 다음과 같은 요구 사항이 있는지 확인 합니다.

- Azure에 액세스하고 리소스를 배포하려면 [Azure Active Directory 라이선스 및 테넌트](../active-directory/develop/quickstart-create-new-tenant.md) 또는 [유효한 결제 수단이 있는 개별 계정](https://azure.microsoft.com/free/)이 필요합니다.

- 테넌트가 있는 경우 리소스 생성 및 청구를 추적하려면 [Azure 구독](../cost-management-billing/manage/create-subscription.md)이 있어야 합니다.

- 구독한 후 구독을 사용하기 시작하려면 [적절한 권한](../role-based-access-control/index.yml)이 필요합니다. 새 구독을 사용하는 경우 AAD 테넌트의 관리자 이상을 구독의 [소유자/기여자](../role-based-access-control/rbac-and-directory-admin-roles.md)로 지정해야 합니다.

    - 사용 가능한 최소 권한이 부여된 액세스를 유지하려면 리소스 그룹 수준에서 역할을 할당합니다.
    - 사용 권한 및 액세스를 더 세밀하게 제어하려면 사용자 지정 역할을 설정합니다. 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/custom-roles.md)를 참조하세요.
    - 사용자와 보안 사용자를 추가로 분리하기 위해 [resource-context](resource-context-rbac.md) 또는 [table-level RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)를 사용하는 것이 좋습니다.

    Microsoft 센티널에 대해 지원 되는 다른 역할 및 권한에 대 한 자세한 내용은 [Microsoft 센티널의 권한](roles.md)을 참조 하세요.

- Microsoft 센티널이 수집 하 고 검색, 분석 및 기타 기능을 위해 사용 하는 모든 데이터를 보관 하려면 [Log Analytics 작업 영역이](../azure-monitor/logs/quick-create-workspace.md) 필요 합니다. 자세한 내용은 [Microsoft 센티널 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)를 참조 하세요.

> [!TIP]
> Microsoft 센티널 작업 영역을 설정할 때 microsoft 센티널 전용 [리소스 그룹과](../azure-resource-manager/management/manage-resource-groups-portal.md) microsoft에서 사용자에 게 Log Analytics 작업 영역, 모든 플레이 북, 통합 문서 등을 포함 하는 리소스를 만듭니다.
>
> 전용 리소스 그룹을 사용하면 리소스 그룹 수준에서 권한을 한 번만 할당할 수 있으며 권한은 모든 관련 리소스에 자동으로 적용됩니다. 리소스 그룹을 통해 액세스를 관리 하면 부적절 한 사용 권한을 잠재적으로 발급 하지 않고 Microsoft 센티널을 효율적으로 사용 하는 데 도움이 됩니다. 리소스가 여러 리소스 그룹 간에 분산 되는 Microsoft 센티널에 대 한 리소스 그룹이 없으면 사용자 또는 서비스 주체는 권한 부족으로 인해 필요한 작업을 수행 하거나 데이터를 볼 수 없는 경우를 발견할 수 있습니다.
>
> 계층별로 리소스에 대한 더 세밀한 액세스 제어를 구현하려면 추가 리소스 그룹을 사용하여 해당 그룹에서만 액세스해야 하는 리소스를 수용합니다. 리소스 그룹의 여러 계층을 사용하면 해당 계층 간에 액세스를 구분할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[온-보드 Microsoft 센티널](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)

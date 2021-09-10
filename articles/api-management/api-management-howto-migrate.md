---
title: 지역 간에 Azure API Management를 마이그레이션하는 방법
description: 한 지역에서 다른 지역으로 API Management 인스턴스를 마이그레이션하는 방법에 대해 알아봅니다.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 08/20/2021
ms.author: apimpm
ms.custom: subject-moving-resources
ms.openlocfilehash: 4958cc4684cc1ecc8ed43de987246c435b4982eb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698609"
---
# <a name="how-to-move-azure-api-management-across-regions"></a>지역 간에 Azure API Management를 이동하는 방법

이 문서에서는 API Management 인스턴스를 다른 Azure 지역으로 이동하는 방법에 대해 설명합니다. 여러 가지 이유로 인스턴스를 다른 지역으로 이동시킬 수도 있습니다. 예를 들면 다음과 같습니다.

* API 소비자에게 더 가까운 인스턴스 찾기
* 특정 지역에서만 사용할 수 있는 기능 배포
* 내부 정책 및 거버넌스 요구 사항 충족

한 Azure 지역에서 다른 지역으로 API Management 인스턴스를 이동하려면 [백업 및 복원](api-management-howto-disaster-recovery-backup-restore.md) 작업을 사용합니다. 다른 API Management 인스턴스 이름 또는 기존 이름을 사용할 수 있습니다. 

> [!NOTE]
> 또한 API Management는 [다중 지역 배포](api-management-howto-deploy-multi-region.md)를 지원하며, 이를 통해 여러 Azure 지역에 단일 Azure API Management 서비스를 배포할 수 있습니다. 다중 지역 배포를 사용하면 지리적으로 분산된 API 소비자가 느끼는 요청 대기 시간을 줄일 수 있으며 한 지역이 오프라인으로 전환되는 경우 서비스 가용성이 개선됩니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="considerations"></a>고려 사항

* 원본 및 대상 지역에서 동일한 API Management 가격 책정 계층을 선택합니다. 
* 서로 다른 클라우드 유형 간에 마이그레이션할 때는 백업 및 복원이 작동하지 않습니다. 이 시나리오를 위해 리소스를 [템플릿으로](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) 내보냅니다. 그런 다음 대상 Azure 지역에 대해 내보낸 템플릿을 조정하고 리소스를 다시 만듭니다. 

## <a name="prerequisites"></a>전제 조건

* API Management [백업 및 복원](api-management-howto-disaster-recovery-backup-restore.md) 작업의 요구 사항 및 제한 사항을 검토 합니다. 
* [백업되지 않는 항목](api-management-howto-disaster-recovery-backup-restore.md#what-is-not-backed-up)을 참조하세요. 인스턴스를 이동한 후 수동으로 다시 만들어야 하는 레코드 설정 및 데이터입니다.
* 원본 지역에 [스토리지 계정](../storage/common/storage-account-create.md?tabs=azure-portal)을 만듭니다. 원본 인스턴스를 백업하는 데 이 계정을 사용합니다. 

## <a name="prepare-and-move"></a>준비 및 이동

### <a name="option-1-use-a-different-api-management-instance-name"></a>옵션 1: 다른 API Management 인스턴스 이름 사용

1. 대상 영역에서 원본 API Management 인스턴스와 동일한 가격 책정 계층을 사용하여 새 API Management 인스턴스를 만듭니다. 새로운 인스턴스에는 다른 이름을 사용합니다.
1. 기존 API Management 인스턴스를 스토리지 계정에 [백업](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service)합니다. 
1. 새 API Management 인스턴스로 원본 인스턴스의 백업을 [복원](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service)합니다.
1. 원본 지역 API Management 인스턴스를 가리키는 사용자 지정 도메인이 있는 경우 새 API Management 인스턴스를 가리키도록 사용자 지정 도메인 CNAME을 업데이트합니다. 

### <a name="option-2-use-the-same-api-management-instance-name"></a>옵션 2: 동일한 API Management 인스턴스 이름 사용

> [!WARNING]
> 이 옵션은 원래의 API Management 인스턴스를 삭제하고 마이그레이션하는 동안 가동 중지 시간이 발생하도록 합니다. 원본 인스턴스를 삭제하기 전에 유효한 백업이 있는지 확인하세요.

1. 기존 API Management 인스턴스를 스토리지 계정에 [백업](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service)합니다. 
1. 원본 지역에서 API Management 인스턴스를 삭제합니다. 
1. 원본 지역의 이름과 동일한 이름을 사용하여 대상 지역에 새 API Management 인스턴스를 만듭니다.
1. 원본 인스턴스의 백업을 대상 지역의 새로운 API Management 인스턴스로 [복원](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service)합니다.  

## <a name="verify"></a>확인

1. 복원 작업이 성공적으로 완료되었는지 확인한 후 대상 지역의 API Management 인스턴스에 액세스합니다.
1. 복원 작업 중에 자동으로 이동되지 않는 설정을 구성합니다. 예: 가상 네트워크 구성, 관리되는 ID, 개발자 포털 콘텐츠, 사용자 지정 도메인 및 사용자 지정 CA 인증서.
1. 대상 지역에서 API Management 엔드포인트에 액세스합니다. 예를 들어, API를 테스트하거나 개발자 포털에 액세스합니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

옵션 1을 사용하여 API Management 인스턴스를 이동한 경우, 대상 인스턴스를 성공적으로 복원 및 구성한 후 원본 인스턴스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 백업 및 복원 기능에 대한 자세한 내용은 [재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)을 참조하세요.
* Azure 리소스 마이그레이션에 대한 정보는 [Azure 지역 간 마이그레이션 참고 자료](https://github.com/Azure/Azure-Migration-Guidance)을 참조하세요.
* [클라우드 비용을 최적화하고 비용을 절약합니다](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

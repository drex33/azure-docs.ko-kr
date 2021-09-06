---
title: Backup 센터를 사용하여 백업 자산 관리
description: 백업 센터를 통해 모든 리소스가 백업 관점에서 규정을 준수하는지 확인하기 위해 Azure 환경을 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 8b62c2968dccb8d225e472db84c30f9513dd596d
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605111"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Backup 센터를 사용하여 백업 자산 관리

백업 센터를 사용하면 모든 리소스가 백업 관점에서 규정을 준수하도록 Azure 환경을 관리할 수 있습니다. 다음은 백업 센터의 몇 가지 거버넌스 기능입니다.

* 백업용 Azure 정책 보기 및 할당

* 백업에 대한 모든 기본 제공 Azure 정책에서 리소스의 규정 준수를 확인합니다.

* 백업용으로 구성되지 않은 모든 데이터 원본을 확인합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 지원되는 시나리오와 지원되지 않는 시나리오 상세 목록은 [지원 매트릭스](backup-center-support-matrix.md)를 참조하세요.

## <a name="azure-policies-for-backup"></a>백업용 Azure 정책

백업에 사용할 수 있는 모든 [Azure 정책](../governance/policy/overview.md)을 보려면 **백업용 Azure 정책** 메뉴 항목을 선택합니다. 그러면 구독 및 리소스 그룹에 할당할 수 있는 기본 제공/사용자 지정 [백업용 Azure Policy 정의](policy-reference.md)가 모두 표시됩니다.

정의 중 하나를 선택하면 범위에 [정책을 할당](../governance/policy/tutorials/create-and-manage.md#assign-a-policy)할 수 있습니다.

![Azure Policy 정의 선택](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>백업 규정 준수

백업 규정 준수 메뉴 항목을 클릭하면 Azure 환경에 할당된 다양한 기본 제공 정책에 따라 리소스의 [규정 준수](../governance/policy/how-to/get-compliance-data.md)를 확인할 수 있습니다. 하나 이상의 비규격 리소스가 있는 정책뿐만 아니라 모든 정책에 대해 규정을 준수하는 리소스의 백분율을 볼 수 있습니다.

![백업 규정 준수 보기](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>보호 가능한 데이터 원본

**보호 가능한 데이터 원본** 메뉴 항목을 선택하면 백업용으로 구성되지 않은 모든 데이터 원본을 볼 수 있습니다. 데이터 원본 구독, 리소스 그룹, 위치, 형식 및 태그별로 목록을 필터링할 수 있습니다. 백업해야 하는 데이터 원본을 식별한 후 해당 그리드 항목을 마우스 오른쪽 단추로 클릭하고 **백업** 을 선택하여 리소스에 대한 백업을 구성할 수 있습니다.

![보호 가능한 데이터 원본 메뉴](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> **Azure VM의 SQL** 을 데이터 원본 유형으로 선택하는 경우 **보호 가능한 데이터 원본** 보기에는 백업용으로 구성된 SQL 데이터베이스가 없는 모든 갤러리 VM의 목록이 표시됩니다.
> 데이터 원본 형식으로 **Azure Storage(Azure Files)** 를 선택하는 경우 **보호 가능한 데이터 원본** 보기에 백업용으로 구성된 파일 공유가 없는 모든 스토리지 계정(파일 공유 지원)의 목록이 표시됩니다.


## <a name="next-steps"></a>다음 단계

* [백업 모니터링 및 운영](backup-center-monitor-operate.md)
* [백업 센터를 사용하여 작업 수행](backup-center-actions.md)
* [백업에 대한 인사이트 얻기](backup-center-obtain-insights.md)
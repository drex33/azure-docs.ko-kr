---
title: Azure Cosmos DB에서 데이터 상주 요구 사항을 충족하는 방법
description: 데이터와 백업이 단일 지역에 유지되도록 Azure Cosmos DB의 데이터 상주 요구 사항을 충족하는 방법에 대해 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 869b28c8c65c402936dcb7b7641a6e4af8db3f47
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100949"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 상주 요구 사항을 충족하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB에서 [ 상주 요구 사항을 충족하기 위해 단일 지역에 유지되도록 데이터 및 백업을 구성할 수 있습니다.](https://azure.microsoft.com/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>데이터의 상주 요구 사항

Azure Cosmos DB에서 지역 간 데이터 복제를 명시적으로 구성해야 합니다. [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account), [Azure CLI](scripts/cli/common/regions.md)를 사용하여 지역 복제를 구성하는 방법을 알아봅니다. 데이터 상주 요구 사항을 충족하기 위해 특정 지역에서 원치 않는 지역으로 데이터를 복제하지 못하도록 하는 Azure Policy 정의를 만들 수 있습니다.

## <a name="residency-requirements-for-backups"></a>백업의 상주 요구 사항

**연속 모드 백업**: 이러한 백업은 로컬 중복 또는 영역 중복 스토리지에 저장되므로 기본적으로 상주합니다. 자세히 알아보려면 [지속적인 백업](provision-account-continuous-backup.md) 문서를 참조하세요.

**정기적 모드 백업**: 기본적으로 정기적 모드 계정 백업은 지역 중복 스토리지에 저장됩니다. 정기적 백업 모드의 경우 계정 수준에서 데이터 중복성을 구성할 수 있습니다. 백업 스토리지에는 세 가지 중복 옵션이 있습니다. 로컬 중복성, 영역 중복성 또는 지역 중복성입니다. 자세히 알아보려면 포털을 사용하여 [백업 중복성을 구성](configure-periodic-backup-restore.md#configure-backup-interval-retention)하는 방법을 참조하세요.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Azure Policy를 사용하여 상주 요구 사항 적용

모든 데이터를 단일 Azure 지역에 유지해야 하는 데이터 상주 요구 사항이 있는 경우 Azure Policy를 사용하여 계정에 대해 영역 중복 또는 로컬 중복 백업을 적용할 수 있습니다.  Azure Cosmos DB 계정이 다른 지역으로 지역 복제되지 않도록 하는 정책을 적용할 수도 있습니다.

Azure Policy는 Azure 리소스에 규칙을 적용하는 정책을 만들고, 할당하고, 관리하는 데 사용할 수 있는 서비스입니다. Azure Policy는 해당 리소스가 회사 표준 및 서비스 수준 계약을 준수하도록 유지하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy](policy.md)를 사용하여 Azure Cosmos DB 리소스에 대한 거버넌스 및 제어를 구현하는 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](configure-periodic-backup-restore.md)을 사용하여 정기적 백업 구성 및 관리
* [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) 또는 [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)를 사용하여 지속적인 백업을 프로비저닝합니다.
* [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli) 또는 [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)를 사용하여 지속적인 백업 계정을 복원합니다.
* [정기 백업에서 연속 백업으로 계정에 마이그레이션](migrate-continuous-backup.md)합니다.


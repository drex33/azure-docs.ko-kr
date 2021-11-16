---
title: Azure Cosmos DB에서 자동 온라인 백업 및 주문형 데이터 복원
description: 이 문서에서는 자동 백업, 주문형 데이터 복원의 작동 방식에 대해 설명합니다. 또한 지속적인 백업 모드와 주기적 백업 모드 간의 차이점을 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/15/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a87371e697d39b73a647e7dea1dbbceb1e028d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523223"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB에서 자동 온라인 백업 및 주문형 데이터 복원
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 자동으로 데이터의 백업을 정기적으로 수행합니다. 자동 백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. 모든 백업은 스토리지 서비스에 별도로 저장됩니다. 자동 백업은 Azure Cosmos 계정, 데이터베이스 또는 컨테이너를 실수로 삭제하거나 업데이트하고 나중에 데이터를 복구해야 하는 시나리오에서 유용합니다. Azure Cosmos DB 백업은 Microsoft 관리 서비스 키로 암호화됩니다. 이러한 백업은 안전한 비공용 네트워크를 통해 전송됩니다. 즉, 백업 데이터는 유선 및 휴지 상태로 전송되는 동안 암호화된 상태로 유지됩니다. 지정된 지역에 있는 계정의 백업은 동일한 지역의 스토리지 계정에 업로드됩니다.

## <a name="backup-modes"></a>백업 모드

백업 모드는 두 가지입니다.

* **주기적 백업 모드** - 이 모드는 모든 기존 계정에 사용되는 기본 백업 모드입니다. 이 모드에서는 백업이 정기적 간격을 두고 수행되며 지원 팀을 대상으로 요청을 만들면 데이터가 복원됩니다. 이 모드에서는 계정에 사용할 백업 간격 및 보존 기간을 구성할 수 있습니다. 최대 보존 기간은 한 달로 연장할 수 있습니다. 최소 백업 간격은 1시간으로 단축할 수 있습니다.  자세한 내용은 [주기적 백업 모드](configure-periodic-backup-restore.md) 문서를 참조하세요.

* **지속적인 백업 모드** – Azure Cosmos DB 계정을 만드는 동안 이 모드를 선택합니다. 이 모드를 사용하면 지난 30일 이내에 모든 시점으로 복원할 수 있습니다. 자세한 내용은 [지속적인 백업 모드 소개](continuous-backup-restore-introduction.md), [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) 또는 [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) 문서를 사용하여 지속적인 백업 구성 프로비저닝을 참조하세요.

  > [!NOTE]
  > 새 계정을 지속적인 백업 모드로 구성하는 경우 Azure Portal, PowerShell 또는 CLI를 통해 셀프 서비스 복원을 수행할 수 있습니다. 계정이 지속적인 백업 모드로 구성되어 있는 경우 주기적 백업 모드로 다시 전환할 수 없습니다.

Azure Synapse Link 사용 계정의 경우 분석 저장소 데이터는 백업 및 복원에 포함되지 않습니다. Synapse Link를 사용하도록 설정하면 Azure Cosmos DB는 예약된 백업 간격에 따라 컨테이너의 트랜잭션 저장소에 있는 데이터를 자동으로 계속 백업합니다. 현재, 분석 저장소의 데이터 자동 백업 및 복원이 지원되지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-restore-from-an-account-a-in-subscription-s1-to-account-b-in-a-subscription-s2"></a>구독 S1의 계정 A에서 구독 S2의 계정 B로 복원할 수 있나요?

아니요. 동일한 구독 내의 계정 간에만 복원할 수 있습니다.

### <a name="can-i-restore-into-an-account-that-has-fewer-partitions-or-low-provisioned-throughput-than-the-source-account"></a>원본 계정보다 적은 파티션 또는 낮은 프로비전된 처리량이 있는 계정으로 복원할 수 있나요?

아니요. RU/s 이하의 파티션이 있는 계정으로 복원할 수 없습니다.

## <a name="next-steps"></a>다음 단계

다음으로 계정에 주기적 백업 모드와 지속적인 백업 모드를 구성 및 관리하는 방법에 대해 알아볼 수 있습니다.

* [주기적 백업 구성 및 관리](configure-periodic-backup-restore.md) 정책
* [지속적인 백업](continuous-backup-restore-introduction.md) 모드란 무엇인가요?
* [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) 또는 [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)를 사용하여 지속적인 백업을 프로비전합니다.
* [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli) 또는 [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)를 사용하여 지속적인 백업 계정을 복원합니다.
* [정기 백업에서 연속 백업으로 계정에 마이그레이션](migrate-continuous-backup.md)합니다.
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md)합니다.
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)

---
title: 비즈니스 연속성 - Azure Database for MySQL
description: Azure Database for MySQL 서비스를 사용하는 경우 비즈니스 연속성(지정 시간 복구, 데이터 센터 중단, 지리적 복원)에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: df9ee4d51cc3e60ed49ae7b95fd029eb995fb68a
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133437356"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 단일 서버의 비즈니스 연속성 개요

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서는 Azure Database for MySQL에서 비즈니스 연속성 및 재해 복구를 위해 제공하는 기능에 대해 설명합니다. 데이터 손실을 유발하거나 데이터베이스 및 애플리케이션을 사용할 수 없게 될 수 있는 중단 이벤트로부터 복구하는 옵션에 대해 알아봅니다. 사용자 또는 애플리케이션 오류가 데이터 무결성에 영향을 주거나, Azure 지역에 가동 중단이 발생하거나, 애플리케이션에 유지 관리가 필요할 때 수행할 작업을 알아봅니다.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>비즈니스 연속성을 제공하는 데 사용할 수 있는 기능

비즈니스 연속성 계획을 개발할 때는 중단 이벤트 후 애플리케이션이 완벽하게 복구되기까지 허용되는 최대 시간 즉, RTO(복구 시간 목표)를 이해해야 합니다. 중단 이벤트 후 복구될 때 애플리케이션이 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 크기(시간 간격)인 RPO(복구 지점 목표)도 이해해야 합니다.

Azure Database for MySQL 단일 서버는 지역 복원을 시작할 수 있는 지역 중복 백업, 다른 지역에 읽기 복제본 배포 등의 비즈니스 연속성 및 재해 복구 기능을 제공합니다. 기능마다 복구 시간과 잠재적 데이터 손실에 대한 각기 다른 특성이 있습니다. [지역 복원](concepts-backup.md) 기능을 사용하면 다른 지역에서 복제된 백업 데이터를 사용하여 새 서버를 만듭니다. 복원 및 복구하는 데 걸리는 전체 시간은 데이터베이스의 크기와 복구할 로그의 양에 따라 다릅니다. 서버를 설정하는 전체 시간은 몇 분에서 몇 시간까지 다릅니다. [읽기 복제본](concepts-read-replicas.md)을 사용하는 경우 주 데이터베이스의 트랜잭션 로그는 복제본으로 비동기 스트리밍됩니다. 영역 수준 또는 지역 수준 오류로 인해 주 데이터베이스가 중단되는 경우 복제본에 대한 장애 조치(failover)는 더 짧은 RTO를 제공하고 데이터 손실을 줄입니다.

> [!NOTE]
> 주 서버와 복제본 사이의 지연은 사이트 간의 대기 시간, 전송할 데이터의 양, 주 서버의 쓰기 워크로드(가장 중요)에 따라 달라집니다. 대규모 쓰기 워크로드에서 상당한 지연이 발생할 수 있습니다. 
>
> 읽기 복제본에 사용되는 복제의 비동기 특성 때문에 지연 시간이 길어지면 RTO 및 RPO가 높아질 수 있으므로 HA(고가용성) 솔루션으로 간주되어서는 **안 됩니다.** 지연 시간이 사용량이 많은 시간 및 많지 않은 시간보다 더 적게 유지되는 워크로드의 경우에만 읽기 복제본이 HA 대체 역할을 할 수 있습니다. 그렇지 않은 경우 읽기 복제본은 대규모 워크로드 및 재해 복구(DR) 시나리오에 대한 true 읽기 확장용으로 사용됩니다.

다음 표에서는 **일반적인 워크로드** 시나리오에서 RTO 및 RPO를 비교합니다.

| **기능** | **기본** | **범용** | **메모리 최적화** |
| :------------: | :-------: | :-----------------: | :------------------: |
| 백업에서 특정 시점 복원 | 보존 기간 내 모든 복원 지점 <br/> RTO - 다양 <br/>RPO < 15분| 보존 기간 내 모든 복원 지점 <br/> RTO - 다양 <br/>RPO < 15분 | 보존 기간 내 모든 복원 지점 <br/> RTO - 다양 <br/>RPO < 15분 |
| 지리적으로 복제된 백업에서 지역 복원 | 지원되지 않음 | RTO - 다양 <br/>RPO < 1시간 | RTO - 다양 <br/>RPO < 1시간 |
| 읽기 복제본 | RTO - 분* <br/>RPO < 5분* | RTO - 분* <br/>RPO < 5분*| RTO - 분* <br/>RPO < 5분*|

 \* RTO 및 RPO는 사이트 간의 대기 시간, 전송되는 데이터의 양, 주 데이터베이스 쓰기 워크로드에 따라 **증가할 수 있습니다**.

## <a name="recover-a-server-after-a-user-or-application-error"></a>사용자 또는 애플리케이션 오류가 발생한 후 서버 복구

서비스 백업을 사용하여 다양한 중단 이벤트로부터 서버를 복구할 수 있습니다. 사용자가 실수로 데이터를 삭제할 수도 있고, 의도치 않게 중요한 테이블을 삭제할 수도 있고, 전체 데이터베이스를 삭제할 수도 있습니다. 애플리케이션에서 결함으로 인해 양호한 데이터를 잘못된 데이터로 덮어쓸 수도 있습니다.

특정 시점 복원을 수행하면 알려진 특정 시점에서 서버의 복사본을 만들 수 있습니다. 이 특정 시점은 서버에 대해 구성한 백업 보존 기간 내에 있어야 합니다. 데이터가 새 서버로 복원된 후에는 원래 서버를 새로 복원된 서버로 바꾸거나 복원된 서버에서 원래 서버로 필요한 데이터를 복사할 수 있습니다.

> [!IMPORTANT]
> 삭제된 서버는 백업이 삭제된 후 **5일** 내에 복원될 수 있습니다. 데이터베이스 백업은 서버를 호스트하는 Azure 구독에서만 액세스하고 복원할 수 있습니다. 삭제된 서버를 복원하려면 [설명 단계](howto-restore-dropped-server.md)를 참조하세요. 배포 후에 실수로 인한 삭제 또는 예기치 않은 변경에서 서버 리소스를 보호하려면 관리자는 [관리 잠금](../azure-resource-manager/management/lock-resources.md)을 활용할 수 있습니다.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Azure 지역 데이터 센터 중단에서 복구

드문 경우지만 Azure 데이터 센터에서 가동 중단이 발생할 수 있습니다. 중단이 발생하면 비즈니스 중단이 몇 분만 지속될 수도 있지만 몇 시간 동안 지속될 수도 있습니다.

한 가지 옵션은 데이터 센터 중단이 끝날 때 서버가 온라인 상태가 되기까지 기다리는 것입니다. 이것은 개발 환경처럼 일정 기간 동안 서버를 오프라인 상태로 유지할 수 있는 애플리케이션에 적합합니다. 데이터 센터가 중단되면 중단이 얼마나 지속될지 알 수 없으므로, 이 옵션은 한동안 서버가 필요 없는 경우에만 적합합니다.

## <a name="geo-restore"></a>지역 복원

지역 복원 기능은 지역 중복 백업을 사용하여 서버를 복원합니다. 백업은 서버의 [페어링된 지역](../availability-zones/cross-region-replication-azure.md)에서 호스팅됩니다. 이러한 백업은 서버를 호스트하는 지역이 오프라인인 경우에도 액세스가 가능합니다. 이러한 백업에서 다른 지역으로 복원하여 서버를 다시 온라인 상태로 만들 수 있습니다. [백업 및 복원 개념 문서](concepts-backup.md)에서 지역 복원에 대해 자세히 알아보세요.

> [!IMPORTANT]
> 지역 복원은 지역 중복 백업 스토리지로 서버를 프로비전한 경우에만 가능합니다. 기존 서버에 대한 로컬 중복 백업을 지역 중복 백업으로 전환하려는 경우 mysqldump를 사용하여 기존 서버를 덤프한 후 지역 중복 백업으로 구성된 새로 만든 서버로 복원해야 합니다.

## <a name="cross-region-read-replicas"></a>지역 간 읽기 복제본

지역 간 읽기 복제본을 사용하여 비즈니스 연속성 및 재해 복구 계획을 향상할 수 있습니다. 읽기 복제본은 MySQL의 이진 로그 복제 기술을 사용하여 비동기적으로 업데이트됩니다. [읽기 복제본 개념 문서](concepts-read-replicas.md)에서 복제본 읽기, 사용 가능한 지역 및 장애 조치(failover) 방법에 대한 자세히 알아봅니다. 

## <a name="faq"></a>FAQ

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Azure Database for MySQL은 고객 데이터를 어디에 저장합니까?
기본적으로 Azure Database for MySQL은 배포된 지역 외부로 고객 데이터를 이동하거나 저장하지 않습니다. 하지만 고객은 [지역 중복 백업](concepts-backup.md#backup-redundancy-options)을 사용하도록 선택하거나 다른 지역에 데이터를 저장하기 위해 [지역 간 읽기 복제본](concepts-read-replicas.md#cross-region-replication)을 만들도록 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL의 자동화된 백업](concepts-backup.md)에 대해 자세히 알아봅니다.
- [Azure Portal](howto-restore-server-portal.md) 또는 [Azure CLI](howto-restore-server-cli.md)를 사용하여 복원하는 방법을 알아봅니다.
- [Azure Database for MySQL의 읽기 복제본](concepts-read-replicas.md)에 대해 알아봅니다.

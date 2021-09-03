---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: BCDR(비즈니스 연속성 및 재해 복구)'
description: 중요 업무용 시스템과 마찬가지로 백업 및 복원 및 재해 복구(BCDR) 전략은 전체 시스템 디자인의 중요한 부분입니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 35ab4f952b2e8082f4923926f11698fef352c8f8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296217"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-business-continuity-and-disaster-recovery-bcdr"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: BCDR(비즈니스 연속성 및 재해 복구)

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[최적화](11-optimization.md)

## <a name="back-up-and-restore"></a>백업 및 복원

중요 업무용 시스템과 마찬가지로 백업 및 복원 및 재해 복구(BCDR) 전략은 전체 시스템 디자인의 중요한 부분입니다. 예기치 않은 이벤트가 발생하는 경우 적절한 시간(복구 시간 목표) 내에 데이터를 특정 시점(복구 지점 목표)으로 복원하는 기능이 있어야 합니다.

### <a name="backup"></a>Backup

Azure Database for MySQL은 기본적으로 7일간 자동 백업을 지원합니다. 이를 현재 최대 35일로 수정하는 것이 적절할 수 있습니다. 값이 35일로 변경되면 할당된 스토리지의 1배 이상 추가 백업 스토리지에 대한 요금이 청구됩니다.

[Azure Database for MySQL의 백업 및 복원](../../concepts-backup.md) 문서에 설명된 대로 데이터베이스 백업 기능에 대한 몇 가지 최신 제한이 있습니다. 구현해야 하는 추가 전략을 결정할 때 이를 이해하는 것이 중요합니다.

알아두어야 할 일부 항목은 다음과 같습니다.

- 백업에 대한 직접 액세스 없음

- 최대 4TB를 허용하는 계층에는 일주일에 한 번 전체 백업, 매일 두 번 차등 및 5분마다 로그가 포함됩니다.

- 최대 16TB를 허용하는 계층에는 스냅샷 기반 백업이 있습니다.

    > [!NOTE]
    > [일부 지역](../../concepts-pricing-tiers.md#storage)에서는 아직 최대 16TB의 스토리지를 지원하지 않습니다.

### <a name="restore"></a>복원

서버를 만드는 동안 중복성(로컬 또는 지역)을 구성해야 합니다. 그러나 지역 복원을 수행할 수 있으며 복원 프로세스 중에 이러한 옵션을 수정할 수 있습니다. 복원 작업을 수행하면 일시적으로 연결을 중지하고 복원 프로세스 중에 애플리케이션을 종료할 수 있습니다.

데이터베이스를 복원하는 동안 데이터베이스 외부의 모든 지원 항목을 복원해야 합니다. 마이그레이션 프로세스를 검토합니다. 자세한 내용은 [복원 후 작업 수행](../../concepts-backup.md#perform-post-restore-tasks)을 참조하세요.

## <a name="read-replicas"></a>읽기 복제본

[읽기 복제본](../../concepts-read-replicas.md)을 사용하여 MySQL 읽기 처리량을 늘리고, 지역 사용자의 성능을 향상시키고 재해 복구를 구현할 수 있습니다. 하나 이상의 읽기 복제본을 만드는 경우 주 서버와 동일한 컴퓨팅 및 스토리지에 대한 추가 요금이 적용됩니다.

## <a name="deleted-servers"></a>삭제된 서버

관리자나 잘못된 행위자가 Azure Portal 또는 자동화된 방법을 통해 서버를 삭제하는 경우 모든 백업 및 읽기 복제본이 삭제됩니다. Azure Database for MySQL 리소스 그룹에 [리소스 잠금](../../../azure-resource-manager/management/lock-resources.md)을 만들어 인스턴스에 추가 삭제 방지 계층을 추가하는 것이 중요합니다.

## <a name="regional-failure"></a>지역 오류

드문 경우지만 지역 실패가 지역 중복 백업에서 발생하거나 읽기 복제본을 사용하여 데이터 작업을 다시 실행하는 경우를 들 수 있습니다. 예기치 않은 지역 오류로부터 최상의 보호를 위해 지역 복제와 읽기 복제본을 모두 사용하는 것이 가장 좋습니다.

> [!NOTE]
> 데이터베이스 서버 영역을 변경하는 것도 엔드포인트에서 변경하고 애플리케이션 구성을 적절하게 업데이트해야 함을 의미합니다.

### <a name="load-balancers"></a>부하 분산 장치

애플리케이션이 전 세계 여러 인스턴스로 구성된 경우 모든 클라이언트를 업데이트하는 것은 불가능할 수 있습니다. [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 또는 [Application Gateway](../../../application-gateway/overview.md)를 활용하여 원활한 장애 조치(failover) 기능을 구현합니다. 유용하고 시간이 절약되기는 하지만 이러한 도구는 지역 장애 조치(failover) 기능에 필요하지 않습니다.

## <a name="wwi-scenario"></a>WWI 시나리오

WWI는 아래에 설명된 단계를 수행하도록 읽기 복제본의 장애 조치(failover) 기능을 테스트합니다.

### <a name="creating-a-read-replica"></a>읽기 복제본 만들기

- Azure Portal을 엽니다.

- Azure Database for MySQL 인스턴스로 이동합니다.

- **설정** 에서 **복제** 를 선택합니다.

- **복제본 추가** 를 선택합니다.

- 서버 이름을 입력합니다.

- 지역을 선택합니다.

- **확인** 을 선택하고 인스턴스가 배포될 때까지 기다립니다. 주 인스턴스의 크기에 따라 복제하는 데 다소 시간이 걸릴 수 있습니다.

    > [!NOTE]
    > 각 복제본에는 주 인스턴스와 동일한 추가 요금이 발생합니다.

### <a name="fail-over-to-read-replica"></a>읽기 복제본으로 장애 조치(failover)

읽기 복제본이 생성되고 복제 프로세스를 완료한 후에는 장애 조치(failover)에 사용할 수 있습니다. 장애 조치(failover) 중에 복제를 중지하고 읽기 복제본을 주 인스턴스로 만듭니다.

장애 조치(failover) 단계:

- Azure Portal을 엽니다.

- Azure Database for MySQL 인스턴스로 이동합니다.

- **설정** 에서 **복제** 를 선택합니다.

- 읽기 복제본 중 하나를 선택합니다.

- **복제 중지** 를 선택합니다. 이렇게 하면 읽기 복제본이 중단됩니다.

- 새 주 인스턴스를 가리키도록 모든 애플리케이션 연결 문자열을 수정합니다.

## <a name="bcdr-checklist"></a>BCDR 검사 목록

- 요구 사항에 맞게 백업 빈도를 수정합니다.

- 읽기 집약적 작업 및 지역 장애 조치(failover)를 위한 읽기 복제본을 설정합니다.

- 리소스 그룹에 대한 리소스 잠금을 만듭니다.

- 빠른 장애 조치(failover)를 위해 애플리케이션에 대한 부하 분산 전략을 구현합니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [보안](./13-security.md)
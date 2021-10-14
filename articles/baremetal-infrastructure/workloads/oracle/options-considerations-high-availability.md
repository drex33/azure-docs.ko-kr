---
title: Oracle BareMetal Infrastructure 서버의 옵션
description: Oracle BareMetal Infrastructure 서버의 옵션 및 고려 사항에 관해 알아봅니다.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/12/2021
ms.openlocfilehash: 0c4442910ed40ece8147bca919039a7c4564a4f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999590"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Oracle BareMetal Infrastructure 서버의 옵션

이 문서에서는 BareMetal Infrastructure 서버에서 Oracle을 실행하는 가장 높은 수준의 보호 및 성능을 얻기 위한 옵션과 권장 사항을 살펴봅니다. 

## <a name="data-guard-protection-modes"></a>Data Guard 보호 모드

Data Guard는 Oracle RAC(Real Applications Cluster), 논리적 복제(예: GoldenGate) 및 스토리지 기반 복제를 통해서만으로는 사용할 수 없는 보호를 제공합니다. 

| 보호 모드 | Description |
| --- | --- |
| **최대 성능** | 기본 보호 모드입니다. 주 데이터베이스의 성능에 영향을 주지 않고 가장 높은 수준의 보호를 제공합니다. 데이터는 주 데이터베이스 다시 게시 스트림에 기록되는 즉시 커밋된 것으로 간주됩니다. 이후 데이터는 대기 데이터베이스에 비동기적으로 복제됩니다. 일반적으로 대기 데이터베이스는 몇 초 내에 데이터를 수신하지만 해당 효과는 보장되지 않습니다. 일반적으로 이 모드는 주 사이트와 대기 사이트 간에 대기 시간이 짧은 네트워크 연결 없이도 지연 모니터링과 함께 비즈니스 요구 사항을 충족합니다.<br /><br />최상의 작동 지속성을 제공하지만 데이터 손실을 보장하지는 않습니다.   |
| **최대 가용성** | 주 데이터베이스의 가용성에 영향을 주지 않고 가장 높은 수준의 보호를 제공합니다. 데이터는 하나 이상의 대기 데이터베이스에 커밋될 때까지 주 데이터베이스에 커밋된 것으로 간주되지 않습니다. 주 데이터베이스는 하나 이상의 대기 데이터베이스에 다시 실행 변경 내용을 쓸 수 없는 경우 사용할 수 없는 상태가 되는 대신 최대 성능 모드로 대체됩니다. <br /><br />이를 통해 대기 사이트를 사용할 수 없는 경우 서비스를 계속 진행할 수 있습니다. 하나의 사이트만 작동하는 경우 두 번째 사이트가 온라인 상태가 되고 동기화가 다시 설정될 때까지 데이터의 복사본 하나만 유지됩니다. |
| **최대 보호** | 최대 가용성과 유사한 보호 수준을 제공합니다. 주 데이터베이스는 하나 이상의 대기 데이터베이스에 다시 실행 변경 내용을 쓸 수 없는 경우 추가된 기능을 사용하여 종료됩니다. 이렇게 하면 데이터 손실은 발생하지 않지만 더 약한 가용성이 손상됩니다. |

>[!IMPORTANT]
>RPO(복구 지점 목표)가 0이어야 하는 경우 최대 가용성 구성을 사용하는 것이 좋습니다. 이렇게 하면 여러 오류가 발생하더라도 RPO를 보장할 수 있습니다. 예를 들어, 주 데이터베이스에서 네트워크 중단이 발생한 후 주 데이터베이스가 손실되는 경우에도 이후 한동안 네트워크가 계속 중단되어 있습니다.

### <a name="data-guard-deployment-patterns"></a>Data Guard 배포 패턴

Oracle을 사용하면 다시 실행 생성에 대해 여러 대상을 구성하여 여러 대기 데이터베이스를 사용할 수 있습니다. 가장 일반적인 구성은 다음 그림에 나와 있는 다른 지역에 있는 단일 대기 데이터베이스입니다.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Oracle의 기본 Data Guard 배포를 보여 주는 다이어그램":::

Data Guard는 기본 배포에 대해 최대 성능 모드로 구성됩니다. 이 구성은 비동기 다시 실행 전송을 통해 거의 실시간으로 데이터 복제를 제공합니다. 대기 데이터베이스는 RAC 배포 내에서 실행할 필요가 없습니다. 그러나 대기 데이터베이스는 기본 사이트의 성능 요구를 충족하는 것이 좋습니다.

엄격한 작동 시간이 필요하거나 RPO가 0인 환경에 대해서는 다음 그림과 같이 배포하는 것이 좋습니다. 최대 가용성 구성은 다음으로 구성됩니다.
- 동기 모드에서 다시 실행이 적용되는 로컬 대기 데이터베이스입니다.
- 원격 지역에서 실행되는 두 번째 대기 데이터베이스입니다.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="최대 가용성 Data Guard 배포를 보여 주는 다이어그램":::

별도의 지역에서 데이터베이스와 애플리케이션 서버를 실행하여 애플리케이션 성능이 저하되는 경우 로컬 대기 데이터베이스를 만들 수 있습니다. 로컬 대기 데이터베이스는 주 클러스터에서 계획되거나 계획되지 않은 유지 관리가 필요할 때 사용됩니다. 이러한 데이터베이스는 동일한 지역에 있으므로 동기 복제를 통해 실행하여 데이터베이스 간에 데이터가 손실되지 않도록 할 수 있습니다.

### <a name="data-guard-configuration-considerations"></a>Data Guard 구성 고려 사항

Data Guard 브로커를 통해 Data Guard 구성 구현을 간소화하고 모범 사례를 준수할 수 있으므로 이를 구현해야 합니다. 이는 성능 모니터링 기능을 제공하고 전환, 장애 조치(failover) 및 다시 인스턴스화 절차를 크게 간소화합니다.

Data Guard를 사용하면 Data Guard 구성의 모든 데이터베이스를 모니터링하여 데이터베이스 가용성을 확인하는 관찰자 프로세스를 실행할 수 있습니다. 주 데이터베이스에 오류가 발생하는 경우 Data Guard 관찰자는 구성의 대기 데이터베이스에 대한 장애 조치(failover)를 자동으로 시작할 수 있습니다. 실제 사이트 수(최대 3개)에 따라 여러 관찰자가 포함된 Data Guard 관찰자를 구현할 수 있습니다. 

이 관찰자는 애플리케이션 계층을 지원할 인프라에 배치해야 합니다. 주 관찰자는 항상 주 데이터베이스가 없는 실제 사이트에 있어야 합니다. Data Guard 관찰자에 의해 트리거되는 장애 조치(failover) 작업을 자동화하는 경우 주의해야 합니다. 먼저 데이터베이스가 분리된 위치에서 실행될 때 허용되는 서비스를 제공하도록 애플리케이션을 디자인하고 테스트해야 합니다.

애플리케이션이 로컬로만 작동할 수 있는 경우 보조 사이트에 대한 장애 조치(failover)는 수동이어야 합니다. 고가용성 수준(99.99% 또는 99.999% 작동 시간)이 필요한 환경은 이전 그림에 나와 있는 것처럼 로컬 및 원격 대기 데이터베이스를 둘 다 사용해야 합니다. 이 경우 FastStartFailoverTarget 매개 변수는 로컬 대기 데이터베이스로만 설정됩니다.

사이트 간 애플리케이션/데이터베이스 액세스를 지원하는 모든 애플리케이션의 경우 FastStartFailoverTarget은 Data Guard 구성의 모든 대기 데이터베이스로 설정됩니다.

### <a name="active-data-guard"></a>Active Data Guard

Oracle ADG(Active Data Guard)는 Oracle Database Enterprise Edition에 포함된 기본 Data Guard 기능의 상위 세트입니다. Oracle Exadata 배포에서 사용되는 다음 추가 기능을 제공합니다.

- 고유한 손상 검색 및 자동 복구.
- 프로덕션의 동기화된 복제본에 대한 신속한 장애 조치(failover) - 수동 또는 자동.
- 읽기 전용으로 열린 동기화된 대기에 대한 프로덕션 워크로드 오프로드.
- 데이터베이스 롤링 업그레이드 및 대기. 실제 대기를 사용한 첫 번째 패치.
- 증분 백업을 대기로 오프로드.
- 성능에 영향을 주지 않고 모든 거리에서 데이터 손실이 없는 데이터 복구 보호.

[https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf)에서 사용할 수 있는 백서는 다음 그림에 표시된 대로 이전 그림의 좋은 개요를 제공합니다.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Oracle Active Data Guard 기능의 개요를 보여 주는 다이어그램":::

## <a name="backup-recommendations"></a>백업 권장 사항

데이터베이스를 백업해야 합니다. 복원 및 복구 기능을 사용하여 데이터베이스를 동일한 또는 다른 시스템으로 복원하거나 데이터베이스 파일을 복구합니다.

데이터 손실로부터 Oracle Database 어플라이언스 데이터베이스를 보호하는 백업 복구 전략을 만드는 것이 중요합니다. 관련 손실은 데이터베이스를 실행하는 데 필요한 디스크 파일에 대한 읽기 또는 쓰기 실패를 유발하는 디스크의 실제 문제로 인해 발생할 수 있습니다. 사용자 오류로 인해 데이터가 손실될 수도 있습니다. 백업 기능을 사용하면 **PITR(Point In Time Restore)에서 데이터베이스 복원, SCN(시스템 변경 번호) 복구 및 최신 복구를** 사용할 수 있습니다. 브라우저 사용자 인터페이스 또는 명령줄 인터페이스에서 백업 정책을 만들 수 있습니다.

다음 백업 옵션을 사용할 수 있습니다.

- NFS 스토리지 볼륨(빠른 복구 영역-FRA- /u98)에 백업합니다.
- Azure NetApp Files SnapCenter – 스냅샷을 사용합니다.

고려해야 할 프로세스:

- 수동 또는 자동 백업.
- 자동 백업은 NFS 스토리지 볼륨(예: /u98)에 기록됩니다.
- 백업은 데이터베이스 시스템의 표준 시간대로 오전 12:00~오전 6:00 사이에 실행됩니다.
- 보존 기간(7, 15, 30, 45 및 60일)을 제공합니다.

- 개체 스토리지에 저장된 백업에서 데이터베이스를 다음과 같이 복구합니다.
  - 데이터 손실 가능성을 최소화하는 마지막으로 알려진 정상 상태로 복구.
  - 지정된 타임스탬프를 사용하여 복구.
  - 지정된 SCN을 사용하여 복구.
  - BackupReport – ‘지정된 SCN 대신 백업 보고서의 SCN을 사용합니다.’

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="FRA(/u98) 및 FRA 이외(/u95)에 대한 고객 데이터베이스 백업을 보여 주는 다이어그램":::

### <a name="backup-policy"></a>백업 정책

백업 정책은 백업 세부 정보를 정의합니다. 백업 정책을 만들 때 데이터베이스 백업 FRA(NFS 위치)의 대상을 정의하고 복구 기간을 정의합니다.

기본적으로 기본 압축 알고리즘이 사용됩니다. 디스크 또는 NFS 백업 정책에 대해 낮음, 보통 또는 높음 압축 알고리즘을 사용하는 경우 라이선스 고려 사항이 있습니다.

### <a name="backup-levels"></a>백업 수준

백업을 수행할 때 백업 수준을 지정합니다.

- Level 0 - 전체
- Level 1 – 증분
- LongTerm/ Archievelog - 백업 보존 정책을 제외하고 FRA 이외 위치(예: /u95)를 사용합니다.

## <a name="next-steps"></a>다음 단계

오류가 발생하는 경우 Oracle Database를 복구하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure BareMetal Infrastructure에서 Oracle Database 복구](oracle-high-availability-recovery.md)

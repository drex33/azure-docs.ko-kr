---
title: 향상된 사후 전 스크립트 프레임워크를 사용하는 데이터베이스 일치 스냅샷
description: Azure Backup 통해 Azure VM 백업을 활용하고 패키지된 사후 스크립트를 사용하여 데이터베이스 일치 스냅샷을 만들 수 있는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa9f4ba3dc344e07a3383c6f8081c9304e3ebbeb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092208"
---
# <a name="enhanced-pre-post-scripts-for-database-consistent-snapshot"></a>데이터베이스 일치 스냅샷에 대한 향상된 사후 전 스크립트

Azure Backup 서비스는 이미 Azure Backup 사용하여 Linux VM에서 애플리케이션 일관성을 달성하기 위한 [ _사후 스크립트_ 프레임워크를](./backup-azure-linux-app-consistent.md) 제공합니다. 여기에는 디스크의 스냅샷을 만들기 전에 사전 스크립트(애플리케이션 정지)를 호출하고 스냅샷이 완료된 후 사후 스크립트(애플리케이션을 고정 해제하는 명령)를 호출하여 애플리케이션을 표준 모드로 반환하는 작업이 포함됩니다.

e 사전/사후 스크립트의 작성, 디버깅 및 유지 관리는 어려울 수 있습니다. 이러한 복잡성을 제거하기 위해 Azure Backup 오버헤드가 가장 적은 애플리케이션 일치 스냅샷을 얻기 위해 데이터베이스를 위한 간소화된 사전/사후 스크립트 환경을 제공합니다.

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/linux-application-consistent-snapshot.png" alt-text="Azure Backup Linux 애플리케이션 일치 스냅샷을 보여 주는 다이어그램":::

향상된 새로운 _사후_ 전 스크립트 프레임워크에는 다음과 같은 주요 이점이 있습니다.

- 이러한 사후 전 스크립트는 백업 확장과 함께 Azure VM에 직접 설치됩니다. 이렇게 하면 작성을 제거하고 외부 위치에서 다운로드할 수 있습니다.
- [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)사전 게시 스크립트의 정의와 콘텐츠를 볼 수 있으며 제안 및 변경 내용을 제출할 수도 있습니다. GitHub 통해 제안 및 변경 내용을 제출할 수도 있습니다. 이 제안 사항은 더 광범위한 커뮤니티에 도움이 되며 심사되고 추가됩니다.
- [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)를 통해 다른 데이터베이스에 대한 새로운 사후 스크립트를 추가할 수도 있습니다. 이 _스크립트는 더 광범위한 커뮤니티에 도움이 되며 심사 및 처리됩니다._
- 강력한 프레임워크는 사전 스크립트 실행 실패 또는 충돌과 같은 시나리오를 처리하는 데 효율적입니다. 어떤 경우에도 사후 스크립트는 자동으로 실행하여 사전 스크립트에서 수행된 모든 변경 내용을 롤백합니다.
- 또한 프레임워크는 외부 도구에 대한 _메시징_ 채널을 제공하여 업데이트를 가져오고 모든 메시지/이벤트에 대한 자체 작업 계획을 준비합니다.

## <a name="solution-flow-preview"></a>솔루션 흐름(미리 보기)

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/solution-flow.png" alt-text="솔루션 흐름을 보여 주는 다이어그램":::

## <a name="support-matrix"></a>지원 매트릭스

다음 데이터베이스 목록은 향상된 프레임워크에서 다룹니다.

- [Oracle(일반 제공)](../virtual-machines/workloads/oracle/oracle-database-backup-azure-backup.md)  -  [지원 매트릭스에 대한 링크](backup-support-matrix-iaas.md#support-matrix-for-managed-pre-post-scripts-for-linux-databases)
- MySQL(미리 보기)

## <a name="prerequisites"></a>사전 요구 사항

연결 세부 정보를 제공하려면 에서 구성 파일 _workload.conf만_ `/etc/azure` 수정하면 됩니다. 이렇게 하면 Azure Backup 관련 애플리케이션에 연결하고 사전 및 사후 스크립트를 실행할 수 있습니다. 구성 파일에는 다음 매개 변수가 있습니다.

```json
[workload]
# valid values are mysql, oracle
workload_name =
command_path = 
linux_user =
credString = 
ipc_folder = 
timeout =
```

다음 표는 매개 변수를 설명합니다.

|매개 변수  |필수  |설명  |
|---------|---------|---------|
|workload_name     |   예      |     여기에는 애플리케이션 일치 백업이 필요한 데이터베이스의 이름이 포함됩니다. 현재 지원되는 값은 `oracle` 또는 입니다. `mysql`    |
|command_path/configuration_path     |         |   여기에는 워크로드 이진 파일에 대한 경로가 포함됩니다. 워크로드 이진이 경로 변수로 설정된 경우 필수 필드가 아닙니다.      |
|linux_user     |    예     |   여기에는 데이터베이스 사용자 로그인에 액세스할 수 있는 Linux 사용자의 사용자 이름이 포함됩니다. 이 값을 설정하지 않으면 루트가 기본 사용자로 간주됩니다.      |
|credString     |         |     이는 데이터베이스에 연결할 자격 증명 문자열을 의미합니다. 여기에는 전체 로그인 문자열이 포함됩니다.    |
|ipc_folder     |         |   워크로드는 특정 파일 시스템 경로에만 쓸 수 있습니다. 사전 스크립트가 이 폴더 경로에 상태를 쓸 수 있도록 이 폴더 경로를 여기에 제공해야 합니다.      |
|시간 제한     |    예     |     데이터베이스가 정지 상태에 있는 최대 시간 제한입니다. 기본값은 90초입니다. 60초 미만의 값을 설정하는 것은 권장되지 않습니다.    |

> [!NOTE]
> JSON 정의는 Azure Backup 서비스가 특정 데이터베이스에 맞게 수정할 수 있는 템플릿입니다. 각 데이터베이스에 대한 구성 파일을 이해하려면 각 데이터베이스의 수동 을 [참조하세요.](#support-matrix)

향상된 사후 전 스크립트 프레임워크를 사용하는 전반적인 환경은 다음과 같습니다.

- 데이터베이스 환경 준비
- 구성 파일 편집
- VM 백업 트리거
- 필요에 따라 애플리케이션 일치 복구 지점에서 VM 또는 디스크/파일을 복원합니다.

## <a name="build-a-database-backup-strategy"></a>데이터베이스 백업 전략 빌드

### <a name="using-snapshots-instead-of-streaming"></a>스트리밍 대신 스냅샷 사용

일반적으로 스트리밍 백업(예: 전체, 차등 또는 증분) 및 로그는 데이터베이스 관리자가 백업 전략에 사용됩니다. 다음은 디자인의 몇 가지 주요 피벗입니다.

- **성능 및 비용:** 매일 전체 + 로그는 복원하는 동안 가장 빠르지만 상당한 비용이 수반됩니다. 차등/증분 스트리밍 백업 유형을 포함하면 비용이 절감되지만 복원 성능에 영향을 줄 수 있습니다. 그러나 스냅샷은 성능과 비용의 최상의 조합을 제공합니다.  스냅샷은 본질적으로 증분적이기 때문에 백업 중에 성능에 미치는 영향이 가장 적고 빠르게 복원되며 비용도 절감됩니다.
- **데이터베이스/인프라에 미치는 영향:** 스트리밍 백업의 성능은 스트림이 원격 위치를 대상으로 할 때 사용할 수 있는 기본 스토리지 IOPS 및 네트워크 대역폭에 따라 달라집니다. 스냅샷에는 이 종속성이 없으며 IOPS 및 네트워크 대역폭에 대한 수요가 크게 줄어듭니다.
- **재사용성:** 서로 다른 스트리밍 백업 유형을 트리거하는 명령은 데이터베이스마다 다릅니다. 따라서 스크립트를 쉽게 다시 사용할 수 없습니다. 또한 다른 백업 유형을 사용하는 경우 종속성 체인을 평가하여 수명 주기를 유지 관리해야 합니다. 스냅샷의 경우 종속성 체인이 없기 때문에 스크립트를 쉽게 작성할 수 있습니다.
- **장기 보존:** 전체 백업은 독립적으로 이동하고 복구할 수 있기 때문에 장기 보존0에 항상 유용합니다. 그러나 단기 보존이 있는 운영 백업의 경우 스냅샷이 더 적합합니다.

따라서 장기 보존을 위해 가끔 전체 백업이 있는 일별 스냅샷 + 로그는 데이터베이스에 가장 적합한 백업 정책입니다.

### <a name="log-backup-strategy"></a>로그 백업 전략

향상된 사후 전 스크립트 프레임워크는 하루에 한 번 백업을 예약하는 Azure VM 백업을 기반으로 합니다. 따라서 RPO가 24시간인 데이터 손실 기간은 프로덕션 데이터베이스에 적합하지 않습니다. 이 솔루션은 로그 백업이 명시적으로 스트리밍되는 로그 백업 전략으로 보완됩니다.

[Blob의 NFS](../storage/blobs/network-file-system-protocol-support.md) 및 [AFS의 NFS(미리 보기)는](../storage/files/files-nfs-protocol.md) 데이터베이스 VM에서 직접 볼륨을 쉽게 탑재하고 데이터베이스 클라이언트를 사용하여 로그 백업을 전송하는 데 도움이 될 수 있습니다. RPO인 데이터 손실 기간은 로그 백업 빈도로 떨어집니다. 또한 데이터베이스 일치 스냅샷이 있는 후에는 운영 백업에 대해 일반 스트리밍(전체 및 증분)을 트리거할 필요가 없기 때문에 NFS 대상은 높은 성과를 발휘할 필요가 없습니다.

>[!NOTE]
>향상된 사전 스크립트는 일반적으로 데이터베이스가 스냅샷을 생성하도록 정지하기 전에 전송 중인 모든 로그 트랜잭션을 로그 백업 대상으로 플러시합니다. 따라서 스냅샷은 복구하는 동안 데이터베이스가 일관되고 안정적입니다.

### <a name="recovery-strategy"></a>복구 전략

데이터베이스 일치 스냅샷이 생성되고 로그 백업이 NFS 볼륨으로 스트리밍되면 데이터베이스의 복구 전략은 Azure VM 백업의 복구 기능을 사용할 수 있습니다. 로그 백업 기능은 데이터베이스 클라이언트를 사용하여 추가로 적용됩니다. 다음은 복구 전략의 몇 가지 옵션입니다.

- 데이터베이스 일치 복구 지점에서 새 VM을 만듭니다. VM에 이미 로그 탑재 지점이 연결되어 있어야 합니다. 데이터베이스 클라이언트를 사용하여 시점 복구에 대한 복구 명령을 실행합니다.
- 데이터베이스 일치 복구 지점에서 디스크를 만들고 다른 대상 VM에 연결합니다. 그런 다음 로그 대상을 탑재하고 데이터베이스 클라이언트를 사용하여 시점 복구에 대한 복구 명령을 실행합니다.
- 파일 복구 옵션을 사용하고 스크립트를 생성합니다. 대상 VM에서 스크립트를 실행하고 복구 지점을 iSCSI 디스크로 연결합니다. 그런 다음 데이터베이스 클라이언트를 사용하여 연결된 디스크에서 데이터베이스별 유효성 검사 함수를 실행하고 백업 데이터의 유효성을 검사합니다. 또한 데이터베이스 클라이언트를 사용하여 전체 데이터베이스를 복구하는 대신 몇 개의 테이블/파일을 내보내고 복구합니다.
- 지역 간 복원 기능을 사용하여 지역 재해 중 보조 쌍을 이루는 지역에서 위의 작업을 수행합니다.

## <a name="summary"></a>요약

사용자 지정 솔루션을 사용 하 여 백업 된 데이터베이스 일관성 스냅숏 + 로그를 사용 하 여 Azure VM 백업의 이점을 활용 하 고 데이터베이스 클라이언트의 기능을 다시 사용 하 여 성능과 비용 효율적인 데이터베이스 백업 솔루션을 구축할 수 있습니다.

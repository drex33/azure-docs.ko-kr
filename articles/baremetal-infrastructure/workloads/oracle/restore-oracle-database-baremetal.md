---
title: Oracle Database 복원
description: SnapCenter를 사용하여 BareMetal Infrastructure에서 Oracle Database를 복원하는 방법을 알아봅니다.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 10/12/2021
ms.openlocfilehash: e4acd2b0c438ebee80571360b540e235ccb2c0f8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001545"
---
# <a name="restore-oracle-database"></a>Oracle Database 복원

이 문서에서는 SnapCenter를 사용하여 BareMetal Infrastructure에서 Oracle Database를 복원하는 단계를 안내합니다.

BareMetal Infrastructure에서 Oracle Database를 복원하는 몇 가지 옵션이 있습니다. 복원을 수행하기 전에 [Oracle 복원 의사 결정 매트릭스](oracle-high-availability-recovery.md#choose-your-method-of-recovery)를 참조하는 것이 좋습니다. 이 매트릭스는 복구 시간 및 잠재적 데이터 손실을 고려할 때 가장 적절한 복원 방법을 선택하는 데 도움이 될 수 있습니다. 

일반적으로 데이터베이스를 알려진 마지막 복구 지점으로 복원하는 것이 목표이므로 데이터 및 보관 로그 볼륨에 대한 최신 스냅샷을 복원합니다. 스냅샷 복원은 영구적인 프로세스입니다. 

>[!IMPORTANT]
>적절한 스냅샷이 복원되도록 주의해야 합니다. 스냅샷에서 복원하면 다른 모든 스냅샷과 관련 데이터가 삭제됩니다. 여기에는 복원을 위해 선택한 스냅샷보다 훨씬 더 많은 최신 스냅샷이 포함됩니다. 따라서 복원 프로세스에 보수적으로 접근하는 것이 좋습니다. 어떤 스냅샷을 복구해야 하는지에 대한 질문이 있는 경우 먼저 최신 스냅샷을 사용하는 쪽에서 오류가 발생합니다.

## <a name="restore-database-locally-with-restored-archive-logs"></a>복원된 보관 로그를 사용하여 데이터베이스를 로컬로 복원

복구를 시도하기 전에 데이터베이스가 아직 오프라인 상태가 아닌 경우 오프라인으로 전환해야 합니다. 데이터베이스가 Oracle RAC(Real Application Clusters)의 노드에서 실행되고 있지 않다고 확인되면 시작할 준비가 된 것입니다. 먼저 보관 로그를 복원합니다. 

1. 사용 가능한 백업을 식별합니다. SnapCenter의 왼쪽 메뉴에서 **리소스** 를 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-available-backups.png" alt-text="데이터베이스 보기를 보여 주는 스크린샷":::

2. 목록에서 복원하려는 데이터베이스를 선택합니다. 데이터베이스에는 만들어진 리소스 그룹 및 관련 정책의 목록이 포함되어 있습니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/available-backups-list.png" alt-text="복원하는 데 사용할 수 있는 백업의 전체 목록을 보여 주는 스크린샷":::

3. 그러면 주 백업 목록이 표시됩니다. 백업은 백업 이름과 해당 유형(로그 또는 데이터)으로 식별됩니다. SnapCenter는 보관 로그 볼륨을 직접 복원하도록 설계되지 않았으므로 로그 복원을 먼저 처리합니다. 복원이 필요한 보관 로그 볼륨을 식별합니다. 대부분의 복구는 알려진 마지막 양호한 데이터 파일 백업의 모든 보관 로그를 롤포워드하므로 일반적으로 복원할 볼륨은 최신 백업입니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/primary-backups-list.png" alt-text="주 백업 목록을 보여 주는 스크린샷":::

4. 보관 로그 볼륨을 선택하면 백업 목록의 오른쪽 위 모서리에 있는 탑재 옵션이 사용하도록 설정됩니다. **탑재** 를 선택합니다. 백업 탑재 페이지의 드롭다운에서 백업을 탑재할 호스트를 선택합니다. 모든 RAC 노드를 선택할 수 있지만, 보관 로그 및 데이터 파일 복구 호스트는 모두 동일해야 합니다. 이후 단계에서 데이터 파일을 복구하는 데 사용되므로 탑재 경로를 복사합니다. **탑재** 를 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-backups.png" alt-text="탑재할 백업을 보여 주는 스크린샷":::

5. 백업 작업과 달리 이 페이지의 작업 뷰어에는 탑재 프로세스의 상태가 표시되지 않습니다. 탑재 상태를 보려면 왼쪽 메뉴에서 **모니터** 를 선택합니다. 그러면 실행된 모든 작업의 상태가 강조 표시됩니다. 탑재 작업이 첫 번째 항목이어야 합니다. 복사한 탑재 경로로 이동하여 보관 로그가 있는지 확인할 수도 있습니다.

    해당 파일 시스템을 탑재하기 위해 선택한 호스트는 위의 탑재 경로를 사용하여 항목도 /etc/fstab에 받습니다. 복구가 완료되면 이를 제거할 수 있습니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-output.png" alt-text="탑재 출력을 보여 주는 스크린샷":::

6. 다음으로, 데이터 파일과 제어 파일을 복원합니다. 이전과 마찬가지로 왼쪽 메뉴에서 **리소스** 를 선택하고, 복원할 데이터베이스를 선택합니다. 복원할 데이터 백업을 선택합니다. 다시 말하지만, 일반적으로 복원되는 백업은 가장 최근 백업입니다. 이때 데이터 백업을 선택하면 복원 옵션이 사용하도록 설정됩니다. **복원** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-selected-data-backup.png" alt-text="복원할 데이터 백업의 스크린샷":::

7. **복원 범위** 탭: 드롭다운에서 이전에 로그 파일을 탑재하기 위해 선택한 것과 동일한 호스트를 선택합니다. **모든 데이터 파일** 이 선택되어 있는지 확인하고, **제어 파일** 확인란을 선택합니다. **다음** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-recovery-scope.png" alt-text="데이터 파일에 대한 복원 범위 세부 정보를 보여 주는 스크린샷":::

8. **복구 범위** 탭: 탑재된 로그 백업의 SCN(시스템 변경 번호)가 입력됩니다. 이제 **SCN까지** 도 선택됩니다. 
 
    SCN은 각 백업에 대한 모든 백업 목록에서 찾을 수 있습니다. 이전에 복사한 탑재 지점 위치도 **외부 보관 로그 파일의 위치 지정** 아래에 입력됩니다. 둘 이상의 보관 로그 복원 위치를 처리하는 것은 현재 이 문서의 범위를 벗어납니다. **다음** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-choose-recovery-scope.png" alt-text="복구 범위 옵션을 보여 주는 스크린샷":::

9. **PreOps** 탭: 현재 복원 전 프로세스의 일부로 권장되는 스크립트가 없습니다. **다음** 을 선택합니다.

10. PostOps 탭: 다시 말하지만, 현재 복원 전 프로세스의 일부로 지원되는 스크립트가 없습니다. **복구 후 READ-WRITE 모드에서 데이터베이스 또는 컨테이너 데이터베이스 열기** 확인란을 선택합니다. **다음** 을 선택합니다.

11. **알림** 탭: SMTP 서버를 사용할 수 있고 복원 프로세스에 대한 이메일 알림을 원하는 경우 이메일 설정을 입력합니다. **다음** 을 선택합니다.

12. **요약** 탭: 모든 세부 정보가 올바른지 확인합니다. **마침** 을 선택합니다.

13. 복원 상태는 **작업** 아래쪽 화면에서 복원 작업을 선택하여 확인할 수 있습니다. 녹색 화살표를 선택하여 진행률에 따라 각 복원 하위 섹션과 진행 상황을 엽니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-job-details.png" alt-text="복원 작업 세부 정보를 보여 주는 스크린샷":::

    복원이 완료되면 모든 단계가 확인 표시로 변경됩니다. **닫기** 를 선택합니다.

    만들어진 단순 데이터베이스에서 제거된 행이 이제 복원된 것으로 확인됩니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/output-of-restored-files.png" alt-text="복원된 파일의 출력 확인을 보여 주는 스크린샷":::

## <a name="clone-database-locally-or-remotely"></a>데이터베이스를 로컬 또는 원격으로 복제

데이터베이스 복제는 복원 프로세스와 비슷합니다. 데이터베이스 복제 작업은 원하는 결과에 따라 로컬 또는 원격으로 사용할 수 있습니다. DR(재해 복구) 위치에서 데이터베이스를 복제하는 것은 프로덕션 데이터에 대한 재해 복구 장애 조치(failover) 테스트 및 QA 스테이징을 포함하여 여러 용도에 유용합니다. 이는 재해 복구 위치가 테스트, 개발, QA 등에 사용된다고 가정합니다.

### <a name="create-a-clone"></a>복제본 만들기

SnapCenter를 사용하면 복제본을 만들 수 있으므로 스냅샷을 시점 참조로 사용할 수 있습니다. 그런 다음 포인터를 사용하여 부모 볼륨과 복제된 볼륨 간에 유사한 데이터 집합을 캡처할 수 있습니다. 복제된 볼륨은 읽기 쓰기 가능하며 쓰기를 통해서만 확장됩니다. 그러나 부모 볼륨에서 여전히 읽기가 발생합니다. 이 기능을 사용하면 부모 볼륨에 있는 데이터를 방해하지 않고 호스트에서 사용할 수 있는 "중복" 데이터 세트를 만들 수 있습니다. 

이 기능은 재해 복구 테스트에 특히 유용합니다. 임시 파일 시스템은 실제 복구에 사용되는 것과 동일한 스냅샷을 기반으로 할 수 있습니다. 데이터와 애플리케이션이 예상대로 작동하는지 확인한 다음, 재해 복구 볼륨 또는 복제에 영향을 주지 않고 재해 복구 테스트를 종료할 수 있습니다.

데이터베이스를 복제하는 단계는 다음과 같습니다. 

1. SnapCenter에서 **리소스** 를 선택한 다음, 복제할 데이터베이스를 선택합니다. 복제본을 로컬로 만드는 경우 다음 단계를 계속합니다. 보조 위치에서 복제본을 복원하는 경우 미러 복사본 상자 위의 백업을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-manage-copies.png" alt-text="데이터베이스 관리 복사본 복제 다이어그램":::

2. 제공된 백업 목록에서 적절한 데이터 백업을 선택합니다. **복제** 를 선택합니다.

    >[!NOTE]
    >타임스탬프 복제가 필요한 경우 데이터 백업이 타임스탬프 또는 SCN(시스템 변경 번호)보다 먼저 만들어졌어야 합니다. 

3. **이름** 탭: 복제본에 대한 **SID** 이름을 입력합니다. 데이터 및 보관 로그의 **원본 볼륨** 및 **대상 볼륨** 이 재해 복구 매핑을 위해 Microsoft Operations에서 제공한 것과 일치하는지 확인합니다. **다음** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/provide-clone-database-sid.png" alt-text="데이터베이스 SID를 입력하는 위치를 보여 주는 스크린샷":::

4. **위치** 탭: 데이터 파일 위치, 제어 파일 및 다시 실행 로그는 복제 작업에서 복구에 필요한 파일 시스템을 만드는 위치입니다. 이러한 위치를 그대로 두는 것이 좋습니다. 대안을 살펴보는 것은 이 문서의 범위를 벗어납니다. 복원 위치에 적절한 호스트가 선택되어 있는지 확인합니다. **다음** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-select-host.png" alt-text="복제할 호스트를 선택하는 방법을 보여 주는 스크린샷":::

5. **자격 증명** 탭: 자격 증명 및 Oracle 홈 설정 값은 기존 프로덕션 위치에서 가져옵니다. 보조 위치에 기본 위치와 다른 값이 있는 경우에만이를 그대로 두는 것이 좋습니다. **다음** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-credentials.png" alt-text="복제에 대한 데이터베이스 자격 증명을 입력하기 위한 스크린샷":::

6. **PreOps** 탭: 현재 지원되는 복구 전 스크립트가 없습니다. **다음** 을 선택합니다.

7. **PostOps** 탭: 데이터베이스가 특정 시간 또는 SCN까지만 복구되는 경우 적절한 라디오 단추를 선택하고 타임스탬프 또는 SCN을 추가합니다. SnapCenter는 해당 시점에 도달할 때까지 데이터베이스를 복구합니다. 그렇지 않으면 **취소까지** 가 선택된 상태로 둡니다. **다음** 을 선택합니다.

8. **알림** 탭: 복제가 완료될 때 알림 이메일을 보내려면 필요한 SMTP 정보를 입력합니다. **다음** 을 선택합니다.

9. **요약** 탭: 적절한 **복제 SID** 를 입력하고, 올바른 호스트가 선택되었는지 확인합니다. 아래로 스크롤하여 적절한 복구 범위가 입력되었는지 확인합니다. **마침** 을 선택합니다.

10. 복제 작업은 화면 아래쪽의 활성 팝업에 표시됩니다. 복제 작업을 선택하여 작업 세부 정보를 표시합니다. 작업이 완료되면 [작업 세부 정보] 페이지에 녹색 확인 표시가 모두 표시되고 완료 시간이 제공됩니다. 복제에는 일반적으로 7~10분 정도 걸립니다.

11. 작업이 완료되면 복제 대상으로 사용되는 호스트로 전환하고, cat /etc/fstab을 사용하여 탑재 지점을 확인합니다. 이 확인은 복제 마법사 중에 나열 된 데이터베이스에 대 한 적절 한 탑재 지점이 있는지 확인 합니다. 마법사에 입력 한 데이터베이스 SID도 강조 표시 됩니다. 아래 예에서 SID는 호스트의 탑재 지점에서 지정한 dbsc4입니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-switch-to-target-host.png" alt-text="대상 호스트로 전환하는 명령의 스크린샷":::

12. 호스트에서 **oraenv** 를 입력하고 **Enter** 키를 누릅니다. 새로 만든 SID를 입력합니다.

    사용자는 데이터베이스가 적절하게 복원되었는지 확인해야 합니다. 그러나 추가된 다음 단계는 Oracle 이외의 사용자가 만든 데이터베이스를 기반으로 합니다. 

13. **sysdba로 sqlplus /** 를 입력합니다. 다른 사용자가 테이블을 만들었으므로 잘못된 원래 사용자 이름과 암호가 자동으로 입력됩니다. 올바른 사용자 이름과 암호를 입력합니다. 로그인에 성공하면 SQL> 프롬프트가 표시됩니다.

기본 데이터베이스의 쿼리를 입력하여 적절한 데이터를 받는지 확인합니다. 다음 예제에서는 보관 로그를 사용하여 데이터베이스를 롤포워드했습니다. 다음은 데이터 백업이 생성 된 후 복제 테스트 항목이 생성 되었으므로 보관 로그를 적절히 사용한 것을 보여 줍니다. 따라서 보관 로그가 롤포워드 되지 않으면 해당 항목이 나열 되지 않습니다.

```sql
SQL> select * from acolvin.t;

COL1
-----------------------------------------
COL2
---------------
first insert
17-DEC-20

log restore
17-DEC-20

clonetest
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
after first insert
17-DEC-20

next insert
17-DEC-20

final insert
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
BILLY
17-DEC-20

7 rows selected.

```

### <a name="delete-a-clone"></a>클론 삭제

복제가 완료되면 복제본을 삭제하는 것이 중요합니다. 계속 사용하려면 복제본을 분할해야 합니다. 복제본의 부모인 스냅숏은 삭제할 수 없으며 보존 횟수의 일부로 건너뜁니다. 완료한 후에 복제본을 삭제하지 않으면 유지 관리하는 많은 수의 스냅샷 수에서 과도한 스토리지를 사용할 수 있습니다. 

복제본 분할은 기존 볼륨과 스냅샷의 데이터를 새 볼륨에 복사합니다. 이 프로세스는 해당 보존 수에 도달하면 스냅샷을 삭제할 수 있도록 복제본과 부모 스냅샷 간의 관계를 끊습니다. 보존 수에 이미 도달한 경우 해당 스냅샷은 다음 스냅샷에서 삭제됩니다. 그러나 복제본 분할에도 스토리지 비용이 발생합니다.

복제본이 만들어지면 해당 데이터베이스의 리소스 탭에 로컬 또는 원격인지의 여부에 관계없이 존재하는 복제본이 나열됩니다. 복제본을 삭제하려면 다음을 수행합니다.

1. **리소스** 탭: 삭제하려는 복제본이 포함된 상자를 선택합니다.

2. [보조 미러 복제본] 페이지에 복제본이 표시됩니다. 이 예에서 복제본은 보조 위치에 있습니다. 복제본 목록의 오른쪽 위 모서리에서 **삭제** 를 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-secondary-mirror-clones.png" alt-text="보조 미러 복제본의 스크린샷":::

3. 실행하기 전에 SQLPLUS에서 종료했는지 확인합니다. **확인** 을 선택합니다.

4. 작업 진행률을 보려면 왼쪽 메뉴에서 **작업** 을 선택합니다. 복제본이 삭제되면 녹색 확인 표시가 표시됩니다.

5. 복제본이 삭제되면 복제 프로세스의 일부로 사용된 보관 로그를 분리하는 것도 유용할 수 있습니다(해당하는 경우). 적절한 백업 목록(복제본을 만든 시점부터)으로 돌아가서 백업이 탑재되었는지 여부에 따라 백업 목록을 정렬할 수 있습니다. 정렬 알고리즘은 완전하지 않고 항상 모든 항목을 함께 정렬하지는 않지만 일반적으로 올바른 방향으로 정렬합니다. 다음 볼륨은 이전에 탑재되었습니다. **분리** 를 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-unmount-backup.png" alt-text="백업 목록을 보여 주는 스크린샷":::

6. **확인** 을 선택합니다. 왼쪽 메뉴에서 **모니터** 를 선택하여 분리 작업의 상태를 볼 수 있습니다. 백업이 분리되면 녹색 확인 표시가 표시됩니다.

### <a name="split-a-clone"></a>복제본 분할

클론을 분할 하면 부모 볼륨의 복사본이 생성 됩니다. 이는 복제본을 만드는 데 사용 된 스냅숏이 만들어진 지점에 부모 볼륨의 모든 데이터를 복제 합니다. 이는 복제 볼륨에서 부모 볼륨을 분리 하 고 복제 볼륨을 만드는 데 사용 되는 스냅숏에서 보류를 제거 합니다. 그러면 보존 정책의 일환으로 스냅샷을 삭제할 수 있습니다.

복제본 분할은 프로덕션 환경 또는 재해 복구 환경에서 데이터를 채우는 데 유용합니다. 새 볼륨이 부모 볼륨에 독립적으로 작동할 수 있습니다.

>[!NOTE]
>복제본 분할 프로세스는 되돌리거나 취소할 수 없습니다.

복제본을 분할하려면 다음을 수행합니다.

1. 복제본이 이미 포함된 데이터베이스를 선택합니다.

2. 복제본의 위치를 선택하면 [보조 미러 복제본] 페이지의 목록에 해당 위치가 표시됩니다.

3. 복제본 목록 바로 위에서 **복제본 분할** 을 선택합니다. SnapCenter는 분할 프로세스의 일환으로 변경을 허용하지 않으므로 다음 페이지에는 발생되는 변경 내용만 표시됩니다. **시작** 을 선택합니다.

    >[!NOTE]
    >분할 프로세스는 복사해야 하는 데이터의 양, 스토리지의 데이터베이스 레이아웃 및 스토리지의 작업 수준에 따라 상당한 시간이 걸릴 수 있습니다.

프로세스가 완료 되 면 분할 된 복제본이 백업 목록에서 제거 됩니다. 이제 SnapCenter에 대 한 일반 보존 계획의 일부로 복제본과 연결 된 스냅숏이 무료로 제거 될 수 있습니다.

## <a name="restore-database-remotely-after-disaster-recovery-event"></a>재해 복구 이벤트 후 데이터베이스를 원격으로 복원

SnapCenter는 현재 장애 조치(failover) 프로세스를 자동화하도록 설계되지 않았습니다. 재해 복구 이벤트가 발생하는 경우 보조 위치에서 데이터베이스를 복원하려면 Microsoft Operations 또는 REST 복구 스크립트가 필요합니다. REST 복구 스크립트를 실행하는 프로세스를 자세히 설명하는 것은 현재 이 문서의 범위를 벗어납니다.

## <a name="restart-all-rac-nodes-after-restore"></a>복원 후 모든 RAC 노드 다시 시작

SnapCenter에서 데이터베이스가 복원되면 아래와 같이 데이터베이스를 복원할 때 선택한 RAC 노드에서만 활성화됩니다. 이 예제에서는 데이터베이스를 bn6sc2로 복원 하 여 복원을 수행할 RAC 노드를 선택할 수 있음을 보여 줍니다.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-example-output-rac-node.png" alt-text="데이터베이스가 bn6sc2로 복원되었음을 보여 주는 스크린샷":::

_srvctl start database_ 명령을 사용하여 나머지 RAC 노드를 시작합니다. 명령이 완료되면 모든 RAC 노드가 참여하고 있는지에 대한 상태를 확인합니다.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-srvctl-start-database-command.png" alt-text="나머지 RAC 노드를 다시 시작하는 srvctl start database 명령을 보여 주는 스크린샷":::

## <a name="unmount-log-archive-volume"></a>로그 보관 볼륨 분리

데이터베이스가 복제되거나 복원되면 데이터베이스를 복원하는 데만 사용되고 더 이상 사용되지 않으므로 로그 보관 볼륨을 분리해야 합니다. 

1. **리소스** 탭에서 로컬 또는 원격 목록을 선택하여 데이터베이스의 적절한 백업 목록을 선택합니다.

2. 적절한 백업 목록이 선택되면 백업이 탑재되었는지 여부에 따라 백업 목록을 정렬합니다. 정렬 알고리즘은 완전하지 않고 항상 모든 항목을 함께 정렬하지는 않지만 사용 가능한 결과를 제공합니다. 
 
3. 이전에 탑재된 원하는 볼륨이 있으면 **분리** 를 선택합니다. **확인** 을 선택합니다.

4. 왼쪽 메뉴에서 **모니터** 를 선택하여 분리 작업의 상태를 볼 수 있습니다. 볼륨이 분리되면 녹색 확인 표시가 표시됩니다.

>[!NOTE]
>분리를 선택 하면 탑재 된 호스트에서/etc/fstab의 항목이 제거 됩니다. 또한 분리하면 보호 정책에 설정된 보존 정책의 일환으로 필요에 따라 백업을 자유롭게 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

BareMetal의 Oracle에 대한 고가용성 및 재해 복구에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [BareMetal 기반 Oracle을 위한 고가용성 및 재해 복구](concepts-oracle-high-availability.md)

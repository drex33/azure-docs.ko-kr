---
title: Azure VM에서 SQL Server DB 관리 및 모니터링
description: 이 문서에서는 Azure VM에서 실행되는 SQL Server 데이터베이스를 관리하고 모니터링하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 6da639ea369b0676a34af12a8e37c13b018c57d7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435008"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>백업한 SQL Server 데이터베이스 관리 및 모니터링

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Azure Backup Recovery Services 자격 증명 모음으로 백업된 SQL Server 데이터베이스(Azure VM(가상 머신)에서 실행)를 관리하고 모니터링하기 위한 일반적인 작업을 설명합니다. 작업 및 경고를 모니터링하고 데이터베이스 보호를 중지 및 다시 시작하고 백업 작업을 실행하고 백업에서 VM을 등록 취소하는 방법을 알아봅니다.

SQL Server 데이터베이스에 대한 백업을 아직 구성하지 않은 경우 [Azure VM에서 SQL Server 데이터베이스 백업](backup-azure-sql-database.md)을 참조하세요.

## <a name="monitor-backup-jobs-in-the-portal"></a>포털에서 백업 작업 모니터링

Azure Backup Azure Portal는 **백업 센터** 에서 백업 센터의 **백업 작업** 에 대 한 모든 예약 된 작업 및 주문형 작업을 표시 합니다. 단, 예약 된 로그 백업은 매우 빈번 하 게 사용할 수 있기 때문입니다. 이 포털에는 데이터베이스 검색 및 등록, 백업 구성과 백업 및 복원 작업이 표시됩니다.

:::image type="content" source="./media/backup-azure-sql-database/backup-operations-in-backup-center-jobs-inline.png" alt-text="백업 작업의 백업 작업을 보여 주는 스크린샷" lightbox="./media/backup-azure-sql-database/backup-operations-in-backup-center-jobs-expanded.png":::

모니터링 시나리오에 대한 자세한 내용은 [Azure Portal의 모니터링](backup-azure-monitoring-built-in-monitor.md) 및 [Azure Monitor를 사용한 모니터링](backup-azure-monitoring-use-azuremonitor.md)을 참조하세요.  

## <a name="view-backup-alerts"></a>백업 경고 보기

로그 백업은 15분마다 발생하기 때문에 백업 작업 모니터링이 번거로울 수 있습니다. Azure Backup은 이메일 경고를 보내 모니터링을 용이하게 합니다. 이메일 경고는 다음과 같습니다.

- 모든 백업 실패에 대해 트리거됩니다.
- 오류 코드별로 데이터베이스 수준에서 통합됩니다.
- 데이터베이스의 첫 번째 백업 실패에 대해서만 전송 됩니다.

데이터베이스 백업 경고를 모니터링하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 자격 증명 모음 대시보드에서 **백업 경고** 를 선택 합니다.

   ![백업 경고 선택](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

다음과 같은 여러 가지 방법으로 SQL Server 데이터베이스 백업을 중지할 수 있습니다.

- 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
- 미래의 모든 백업 작업을 중지하고 복구 지점을 원래 상태로 유지합니다.

복구 지점을 그대로 두기로 선택하는 경우 다음 세부 정보를 염두에 두어야 합니다.

- 모든 복구 지점은 영구적으로 유지되고, 모든 정리는 데이터 보관을 통해 보호 중지에서 중지됩니다.
- 보호된 인스턴스와 사용한 스토리지 요금이 청구됩니다. 자세한 내용은 [Microsoft Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)을 참조하세요.
- 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다. 이전 복구 지점은 정책에 따라 만료되지만 가장 최근의 복구 지점은 백업을 중지하고 데이터를 삭제할 때까지 항상 유지됩니다.

데이터베이스에 대한 보호를 중지하려면:

1. **Backup center** 로 이동 하 고 메뉴에서 **백업 인스턴스** 를 클릭 합니다.

2. **Azure VM에서 SQL** 를 데이터 원본 유형으로 선택 합니다.

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-instance-inline.png" alt-text="Azure VM에서 SQL를 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/backup-azure-sql-database/backup-center-instance-expanded.png":::

3. 보호를 중지하려는 데이터베이스를 선택합니다.

   :::image type="content" source="./media/backup-azure-sql-database/sql-select-instance-inline.png" alt-text="보호를 중지할 데이터베이스를 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/backup-azure-sql-database/sql-select-instance-expanded.png":::

4. 데이터베이스 메뉴에서 **백업 중지** 를 선택합니다.

   백업 인스턴스 보기에서 특정 행을 마우스 오른쪽 단추로 클릭 하 고 **백업 중지** 를 선택할 수도 있습니다.

   :::image type="content" source="./media/backup-azure-sql-database/sql-stop-backup-inline.png" alt-text="백업 중지를 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/backup-azure-sql-database/sql-stop-backup-expanded.png":::

5. **백업 중지** 메뉴에서 데이터를 보존할지 아니면 삭제할지 선택합니다. 원하는 경우 이유나 설명을 입력합니다.

    ![백업 중지 메뉴에서 데이터 보존 또는 삭제](./media/backup-azure-sql-database/stop-backup-button.png)

6. **백업 중지** 를 선택합니다.

> [!NOTE]
>
>데이터 삭제 옵션에 대한 자세한 내용은 아래 FAQ를 참조하세요.
>
>- [자동 보호된 인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 되나요?](faq-backup-sql-server.yml#if-i-delete-a-database-from-an-autoprotected-instance--what-will-happen-to-the-backups-)
>- [자동 보호된 데이터베이스의 백업 작업을 중지하면 어떻게 되나요?](faq-backup-sql-server.yml#if-i-change-the-name-of-the-database-after-it-has-been-protected--what-will-be-the-behavior-)
>
>

## <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

**백업 데이터 보존** 옵션을 선택하고 SQL Database에 대한 보호를 중지하는 경우 나중에 보호를 다시 시작할 수 있습니다. 백업 데이터를 유지하지 않으면 보호를 계속할 수 없습니다.

SQL Database에 대한 보호를 재개하려면:

1. 백업 항목을 열고 **백업 다시 시작** 을 선택합니다.

    ![백업 다시 시작을 선택하여 데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

2. **백업 정책** 메뉴에서 정책을 선택한 다음, **저장** 을 클릭합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

다음과 같은 다양한 유형의 주문형 백업을 실행할 수 있습니다.

- 전체 백업
- 복사 전용 전체 백업
- 차등 백업
- 로그 백업

복사 전용 전체 백업의 보존 기간을 지정해야 하지만 주문형 전체 백업의 보존 기간은 현재 시간으로부터 45일로 자동 설정됩니다.

자세한 내용은 [SQL Server 백업 유형](backup-architecture.md#sql-server-backup-types)을 참조하세요.

## <a name="modify-policy"></a>정책 수정

백업 빈도 또는 보존 범위를 변경하려면 정책을 수정합니다.

> [!NOTE]
> 보존 기간의 변경 내용은 새 복구 지점이 아닌 모든 이전 복구 지점에 소급 적용됩니다.

자격 증명 모음 대시보드에서 **관리** > **백업 정책** 으로 이동하고 편집하려는 정책을 선택합니다.

  ![백업 정책 관리](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![백업 정책 수정](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

정책 수정은 관련된 모든 백업 항목에 영향을 주며 해당하는 **보호 구성** 작업을 트리거합니다.

>[!Note]
>정책 수정은 기존 복구 지점에도 영향을 줍니다. <br><br> 보관 계층에서 180일간 유지되지 않은 보관의 복구 지점에 대해, 해당 복구 지점을 삭제하면 초기 삭제 비용이 발생합니다. [자세히 알아보기](../storage/blobs/access-tiers-overview.md).

### <a name="inconsistent-policy"></a>일관성 없는 정책

경우에 따라 정책 수정 작업으로 인해 일부 백업 항목에 대한 정책 버전에 **일관성이 없을** 수 있습니다. 정책 수정 작업이 트리거된 후 백업 항목에 대한 **보호 구성** 작업이 실패하는 경우 이러한 상황이 발생합니다. 백업 항목 보기에 다음과 같이 나타납니다.

  ![일관성 없는 정책](./media/backup-azure-sql-database/inconsistent-policy.png)

한 번의 클릭으로 영향을 받는 모든 항목에 대한 정책 버전을 수정할 수 있습니다.

  ![일관되지 않은 정책 수정](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>SQL Server 인스턴스 등록 취소

다음과 같이 보호를 사용하지 않도록 설정한 후 자격 증명 모음을 삭제하기 전에 SQL Server 인스턴스 등록을 취소합니다.

1. 자격 증명 모음 대시보드의 **관리** 아래에서 **백업 인프라** 를 선택합니다.  

   ![백업 인프라 선택](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **관리 서버** 에서 **보호된 서버** 를 선택합니다.

   ![보호된 서버 선택](./media/backup-azure-sql-database/protected-servers.png)

3. **보호된 서버** 에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

4. 보호된 서버를 마우스 오른쪽 단추로 클릭하고 **등록 취소** 를 선택합니다.

   ![삭제 선택](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM 확장을 다시 등록합니다.

VM의 워크로드 확장이 어떤 이유로 영향을 받게 되는 경우가 있습니다. 이러한 경우 VM에서 트리거되는 모든 작업이 실패하기 시작합니다. 그런 다음, VM에서 확장을 다시 등록해야 할 수 있습니다. **다시 등록** 하면 작업을 계속할 수 있도록 VM에 워크로드 백업 확장이 다시 설치됩니다. 이 옵션은 Recovery Services 자격 증명 모음의 **백업 인프라** 에서 찾을 수 있습니다.

![백업 인프라의 보호된 서버](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

이 옵션은 주의해서 사용해야 합니다. 이미 정상 상태의 확장이 있는 VM에서 이 작업이 트리거되면 확장이 다시 시작됩니다. 이로 인해 진행 중인 모든 작업이 실패할 수 있습니다. 다시 등록 작업을 트리거하기 전에 하나 이상의 [증상](backup-sql-server-azure-troubleshoot.md#re-registration-failures)을 확인하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SQL Server 데이터베이스에서 백업 문제 해결](backup-sql-server-azure-troubleshoot.md)을 참조하세요.
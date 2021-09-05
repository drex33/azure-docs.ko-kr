---
title: Microsoft Azure Backup Server v3 릴리스 정보
description: 이 문서에서는 MABS(Microsoft Azure Backup Server) v3에 대해 알려진 이슈와 해결 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/27/2021
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b570442fb439c73886b23b07b39ecefb553c0ecf
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720752"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server 릴리스 정보

이 문서에서는 MABS(Microsoft Azure Backup Server) V3에 대해 알려진 문제와 해결 방법을 제공합니다.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>클러스터된 워크로드에 대한 백업 및 복구 실패

**설명:** MABS V2를 MABS V3으로 업그레이드하면 Hyper-V 클러스터, SQL 클러스터(SQL Always On) 또는 DAG(데이터베이스 가용성 그룹)의 Exchange와 같은 클러스터된 데이터 원본에 대해 백업/복구가 실패합니다.

**해결 방법:** 이를 방지하려면 SSMS(SQL Server Management Studio)를 열고 DPM DB에서 다음 SQL 스크립트를 실행합니다.

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>러시아어 로캘에서 MABS V3로의 업그레이드 실패

**설명:****4387** 오류 코드로 인해 러시아어 로캘의 MABS V2에서 MABS V3으로 업그레이드하지 못했습니다.

**해결 방법:** 다음 단계에 따라 러시아어 설치 패키지를 사용하여 MABS V3으로 업그레이드합니다.

1. SQL 데이터베이스를 [백업](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure)하고, MABS V2를 제거합니다(제거하는 동안 보호된 데이터를 유지하도록 선택함).
2. SQL 2017(Enterprise)로 업그레이드하고, 업그레이드의 일환으로 보고를 제거합니다.
3. SSRS(SQL Server Reporting Services)를 [설치](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server)합니다.
4. SSMS(SQL Server Management Studio)를 [설치](/sql/ssms/download-sql-server-management-studio-ssms)합니다.
5. [SQL 2017에서 SSRS 구성](./backup-azure-microsoft-azure-backup.md#upgrade-mabs)에서 설명한 대로 매개 변수를 사용하여 보고를 구성합니다.
6. MABS V3을 [설치](backup-azure-microsoft-azure-backup.md)합니다.
7. SSMS를 사용하여 SQL을 [복원](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)하고, [여기](/system-center/dpm/back-up-the-dpm-server#using-dpmsync)서 설명한 대로 DPM 동기화 도구를 실행합니다.
8. 다음 명령을 사용하여 dbo.tbl_DLS_GlobalSetting 테이블의 'DataBaseVersion' 속성을 업데이트합니다.

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. MSDPM 서비스를 시작합니다.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>UR1을 설치한 후 MABS 보고서가 새 RDL 파일로 업데이트되지 않음

**설명**: UR1에서는 MABS 보고서 서식 이슈가 업데이트된 RDL 파일로 해결되었습니다. 기존 파일은 새 RDL 파일로 자동으로 대체되지 않습니다.

>[!NOTE]
>이 문제는 MABS v3 UR2에서 수정되었습니다.

**해결 방법**: RDL 파일을 바꾸려면 아래 단계를 수행합니다.

1. MABS 머신에서 SQL Reporting Services 웹 포털 URL을 엽니다.
1. 웹 포털 URL에서 DPMReports 폴더는 **`DPMReports_<GUID>`** 형식으로 표시됩니다.

    >[!NOTE]
    >이 명명 규칙을 사용하는 폴더는 항상 하나뿐입니다. MABS를 이전 버전에서 업그레이드한 경우 다른 이전 폴더도 있을 수 있지만 열 수는 없습니다.

    ![DPMReports 폴더](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. **`DPMReports_<GUID>`** 폴더를 선택하고 엽니다. 개별 보고서 파일이 아래와 같이 나열됩니다.

    ![개별 보고서 파일 목록](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. **Report** 로 끝나지 않는 보고서 파일을 선택하고 **옵션** 을 마우스 오른쪽 단추로 클릭한 다음, **관리** 를 선택합니다.

    ![보고서 파일에 대해 관리 선택](./media/backup-mabs-release-notes-v3/manage-files.png)

1. 새 페이지에서 **바꾸기** 옵션을 선택하여 파일을 최신 보고서 파일로 바꿉니다.

    최신 보고서 파일은 `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports` 경로에서 찾을 수 있습니다.

    예: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![최신 보고서 파일로 파일 바꾸기](./media/backup-mabs-release-notes-v3/replace-files.png)

    파일이 바뀌면 **이름** 및 **설명** 이 그대로 유지되고 비어 있지 않은지 확인합니다.

1. 파일을 바꾼 후에 MABS 서비스를 다시 시작하고 보고서 파일을 사용합니다.

## <a name="next-steps"></a>다음 단계

[MABS의 새로운 기능](backup-mabs-whats-new-mabs.md)

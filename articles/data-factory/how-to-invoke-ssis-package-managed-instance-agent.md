---
title: Azure SQL Managed Instance Agent를 통한 SSIS 패키지 실행
description: Azure SQL Managed Instance Agent를 사용하여 SSIS 패키지를 실행하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.author: lle
author: lrtoyou1223
ms.date: 04/14/2020
ms.openlocfilehash: b16cc04837fef57d5510d9c34be94f76dd87033d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730767"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance Agent를 사용하여 SSIS 패키지 실행하기

이 문서에서는 Azure SQL Managed Instance Agent를 사용하여 SSIS(SQL Server Integration Services) 패키지를 실행하는 방법을 설명합니다. 이 기능은 온-프레미스 환경에서 SQL Server Agent를 사용하여 SSIS 패키지를 예약하는 경우와 유사한 동작을 제공합니다.

이 기능을 사용하여 SQL Managed Instance의 SSISDB, Azure Files 등의 파일 시스템, 또는 Azure SSIS 통합 런타임 패키지 저장소에 저장된 SSIS 패키지를 실행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능을 사용하려면 최신 SQL Server Management Studio(SSMS)를 [다운로드](/sql/ssms/download-sql-server-management-studio-ssms)하여 설치합니다. 버전 지원 세부 정보:

- SSISDB 또는 파일 시스템에서 패키지를 실행하려면 SSMS 버전 18.5 이상을 설치합니다.
- 패키지 저장소에서 패키지를 실행하려면 SSMS 버전 18.6 이상을 설치합니다.

또한 Azure Data Factory에서 [Azure SSIS 통합 런타임 프로비저닝](./tutorial-deploy-ssis-packages-azure.md)을 해야 합니다. SQL Managed Instance를 엔드포인트 서버로 사용합니다.

## <a name="run-an-ssis-package-in-ssisdb"></a>SSISDB에서 SSIS 패키지 실행

이 프로시저에서는 SQL Managed Instance Agent를 사용하여 SSISDB에 저장된 SSIS 패키지를 호출합니다.

1. 최신 버전의 SSMS에서 SQL Managed Instance에 연결합니다.
1. 새 에이전트 작업 및 새 작업 단계 만들기. **SQL Server Agent** 에서 **작업** 폴더를 마우스 우클릭한 다음 **새 작업** 을 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png" alt-text="새 에이전트 작업을 만들기 위한 선택 항목":::

1. **새 작업 단계** 페이지에서 형식을 **SQL Server Integration Services 패키지** 로 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png" alt-text="새 SSIS 작업 단계를 만들기 위한 선택 항목":::

1. **패키지** 탭에서 패키지 위치를 **SSIS 카탈로그** 로 선택합니다.
1. SSISDB는 SQL Managed Instance에 있으므로 인증을 지정할 필요가 없습니다.
1. SSISDB에서 SSIS 패키지를 지정합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png" alt-text="패키지 원본 형식에 대한 선택 항목이 포함된 패키지 탭":::

1. **구성** 탭에서 다음을 수행할 수 있습니다.
  
   - **매개 변수** 아래에 매개 변수 값을 지정합니다.
   - **연결 관리자** 아래에서 값을 재정의합니다.
   - 속성을 재정의하고 **고급** 에서 로깅 수준을 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png" alt-text="패키지 원본 형식에 대한 선택 항목이 포함된 구성 탭":::

1. **확인** 을 클릭하여 에이전트 작업 구성을 저장합니다.
1. 에이전트 작업을 시작하여 SSIS 패키지를 실행합니다.

## <a name="run-an-ssis-package-in-the-file-system"></a>파일 시스템에서 SSIS 패키지 실행

이 절차에서는 SQL Managed Instance Agent를 사용하여 파일 시스템에 저장된 SSIS 패키지를 실행합니다.

1. 최신 버전의 SSMS에서 SQL Managed Instance에 연결합니다.
1. 새 에이전트 작업 및 새 작업 단계 만들기. **SQL Server Agent** 에서 **작업** 폴더를 마우스 우클릭한 다음 **새 작업** 을 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png" alt-text="새 에이전트 작업을 만들기 위한 선택 항목":::

1. **새 작업 단계** 페이지에서 형식을 **SQL Server Integration Services 패키지** 로 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png" alt-text="새 SSIS 작업 단계를 만들기 위한 선택 항목":::

1. **패키지** 탭을 선택합니다.

   1. **패키지 위치** 에서 **파일 시스템** 을 선택합니다.

   1. **파일 원본 형식**:

      - 패키지가 Azure Files에 업로드된 경우 **Azure 파일 공유** 를 선택합니다.

        :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png" alt-text="파일 원본 형식에 대한 옵션":::

        패키지 경로는 **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** 입니다.

        **패키지 파일 액세스 자격 증명** 에서 Azure 파일 계정 이름 및 계정 키를 입력하여 Azure 파일에 액세스합니다. 도메인은 **Azure** 로 설정됩니다.

      - 패키지를 네트워크 공유에 업로드하는 경우 **네트워크 공유** 를 선택합니다.

        패키지 경로는 .dtsx 확장명을 사용하는 패키지 파일의 UNC 경로입니다.

        해당 도메인, 사용자 이름 및 암호를 입력하여 네트워크 공유 패키지 파일에 액세스합니다.
   1. 패키지 파일이 패스워드로 암호화된 경우, **암호화 패스워드** 를 선택하고 패스워드를 입력합니다.
1. SSIS 패키지를 실행하는 데 구성 파일이 필요한 경우, **구성** 탭에서 구성 파일 경로를 입력합니다.
   Azure Files에 구성을 저장하는 경우, 해당 구성 경로는 **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** 입니다.
1. **실행 옵션** 탭에서 **Windows 인증** 또는 **32 비트 런타임** 의 사용 여부를 선택하여 SSIS 패키지를 실행할 수 있습니다.
1. **로깅** 탭에서 로깅 경로 및 해당 로깅 액세스 자격 증명을 선택하여 로그 파일을 저장할 수 있습니다. 
   기본적으로 로깅 경로는 패키지 폴더 경로와 같으며 로깅 액세스 자격 증명은 패키지 액세스 자격 증명과 동일합니다.
   Azure Files에 로그를 저장하는 경우 로깅 경로는 **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** 입니다.
1. **값 설정** 탭에서 속성 경로 및 값을 입력하여 패키지 속성을 재정의할 수 있습니다.

   예를 들어, 사용자 변수 값을 재정의하려면 **`\Package.Variables[User::<variable name>].Value`** 와 같이 해당 경로를 입력합니다.
1. **확인** 을 클릭하여 에이전트 작업 구성을 저장합니다.
1. 에이전트 작업을 시작하여 SSIS 패키지를 실행합니다.

## <a name="run-an-ssis-package-in-the-package-store"></a>패키지 저장소에서 SSIS 패키지 실행

이 프로시저에서는 SQL Managed Instance Agent를 사용하여 Azure SSIS IR 패키지 저장소에 저장된 SSIS 패키지를 실행합니다.

1. 최신 버전의 SSMS에서 SQL Managed Instance에 연결합니다.
1. 새 에이전트 작업 및 새 작업 단계 만들기. **SQL Server Agent** 에서 **작업** 폴더를 마우스 우클릭한 다음 **새 작업** 을 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png" alt-text="새 에이전트 작업을 만들기 위한 선택 항목":::

1. **새 작업 단계** 페이지에서 형식을 **SQL Server Integration Services 패키지** 로 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png" alt-text="새 SSIS 작업 단계를 만들기 위한 선택 항목":::

1. **패키지** 탭을 선택합니다.

   1. **패키지 위치** 에서 **패키지 저장소** 를 선택합니다.

   1. **패키지 경로**:

      패키지 경로는 **`<package store name>\<folder name>\<package name>`** 입니다.

      :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png" alt-text="패키지 저장소 형식에 대한 옵션":::

   1. 패키지 파일이 패스워드로 암호화된 경우, **암호화 패스워드** 를 선택하고 패스워드를 입력합니다.
1. SSIS 패키지를 실행하는 데 구성 파일이 필요한 경우, **구성** 탭에서 구성 파일 경로를 입력합니다.
   Azure Files에 구성을 저장하는 경우, 해당 구성 경로는 **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** 입니다.
1. **실행 옵션** 탭에서 **Windows 인증** 또는 **32 비트 런타임** 의 사용 여부를 선택하여 SSIS 패키지를 실행할 수 있습니다.
1. **로깅** 탭에서 로깅 경로 및 해당 로깅 액세스 자격 증명을 선택하여 로그 파일을 저장할 수 있습니다.
   기본적으로 로깅 경로는 패키지 폴더 경로와 같으며 로깅 액세스 자격 증명은 패키지 액세스 자격 증명과 동일합니다.
   Azure Files에 로그를 저장하는 경우 로깅 경로는 **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** 입니다.
1. **값 설정** 탭에서 속성 경로 및 값을 입력하여 패키지 속성을 재정의할 수 있습니다.

   예를 들어, 사용자 변수 값을 재정의하려면 **`\Package.Variables[User::<variable name>].Value`** 와 같이 해당 경로를 입력합니다.
1. **확인** 을 클릭하여 에이전트 작업 구성을 저장합니다.
1. 에이전트 작업을 시작하여 SSIS 패키지를 실행합니다.

## <a name="cancel-ssis-package-execution"></a>SSIS 패키지 실행 취소

SQL Managed Instance Agent 작업에서 패키지 실행을 취소하려면, 에이전트 작업을 직접 중지하는 대신 다음 단계를 수행합니다.

1. **msdb.dbo.sysjobs** 에서 SQL Agent **jobId** 를 찾습니다.
1. 이 쿼리를 사용하여 작업 ID를 기반으로 해당 SSIS **executionId** 를 찾습니다.
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   SSIS 패키지가 SSISDB에 있는 경우 작업 실행을 위해 **ssisdb.internal.execution_parameter_values** 를 테이블로 사용합니다. SSIS 패키지가 파일 시스템에 있는 경우, **ssisdb.internal.execution_parameter_values_noncatalog** 를 사용합니다.
1. SSISDB 카탈로그를 마우스 오른쪽 단추로 클릭한 다음 **활성 작업** 을 선택합니다.

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png" alt-text="&quot;&quot;SSISDB 카탈로그의 바로 가기 메뉴에서 활성 작업":::

1. **executionId** 을 기준으로 해당 작업을 중지합니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory를 사용하여 SSIS 패키지를 예약할 수도 있습니다. 단계별 지침은 [Azure Data Factory 이벤트 트리거](how-to-create-event-trigger.md)를 참조하세요.
---
title: '자습서: Azure Data Lake Storage Gen2, Azure Synapse | Microsoft Docs'
description: 이 자습서에서는 Azure Synapse 서버리스 SQL 엔드포인트에서 SQL 쿼리를 실행하여 Azure Data Lake Storage Gen2 스토리지 계정의 데이터에 액세스하는 방법을 보여 줍니다.
author: jovanpop-msft
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/22/2021
ms.author: jovanpop
ms.reviewer: jrasnic
ms.custom: devx-track-sql
ms.openlocfilehash: 6495324c45ca05304b793c5c1bb8c82ab2e89e5e
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132942664"
---
# <a name="tutorial-query-azure-data-lake-storage-gen2-using-sql-language-in-synapse-analytics"></a>자습서: Synapse Analytics에서 SQL 언어를 사용하여 Azure Data Lake Storage Gen2 쿼리

이 자습서에서는 Azure Synapse 서버리스 SQL 풀을 Azure Data Lake Storage Gen2가 활성화된 Azure 스토리지 계정에 저장된 데이터에 연결하는 방법을 보여줍니다.
이 연결을 통해 Azure Storage의 데이터에 대해 SQL 언어를 사용하여 SQL 쿼리 및 분석을 기본적으로 실행할 수 있습니다.

이 자습서에서 다음을 수행합니다.

> [!div class="checklist"]
> - 스토리지 계정으로 데이터 수집
> - Synapse Analytics 작업 영역을 만듭니다(없는 경우).
> - Blob 스토리지의 데이터에 대한 분석 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Data Lake Storage Gen2 계정을 만듭니다.

  [Data Lake Storage Gen2에서 사용할 스토리지 계정 만들기](create-data-lake-storage-account.md)를 참조하세요.

- 사용자 계정에 [Storage Blob 데이터 기여자 역할](assign-azure-role-data-access.md)이 할당되었는지 확인합니다.

- AzCopy v10을 설치합니다. [AzCopy v10을 사용하여 데이터 전송](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

  해당 문서의 단계를 수행할 때 해야 하는 두어 가지 항목이 있습니다.

  > [!IMPORTANT]
  > 역할을 Data Lake Storage Gen2 스토리지 계정의 범위에 할당해야 합니다. 역할은 부모 리소스 그룹 또는 구독에 할당할 수 있지만, 이러한 역할 할당이 스토리지 계정에 전파될 때까지 권한 관련 오류가 발생합니다.

### <a name="download-the-flight-data"></a>비행 데이터 다운로드

이 자습서에서는 Bureau of Transportation Statistics의 비행 데이터를 사용하여 ETL 작업을 수행하는 방법을 보여줍니다. 자습서를 완료하려면 이 데이터를 다운로드해야 합니다.

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ)로 이동합니다.

2. **미리 압축된 파일** 확인란을 선택하여 모든 데이터 필드를 선택합니다.

3. **다운로드** 단추를 선택하고 결과를 컴퓨터에 저장합니다.

4. 압축된 파일을 풀고, 파일 이름 및 파일 경로를 적어 둡니다. 이후 단계에서 이 정보가 필요합니다.

### <a name="copy-source-data-into-the-storage-account"></a>스토리지 계정에 원본 데이터 복사

AzCopy를 사용하여 *.csv* 파일의 데이터를 Data Lake Storage Gen2 계정으로 복사합니다.

1. 명령 프롬프트 창을 열고, 다음 명령을 입력하여 스토리지 계정에 로그인합니다.

   ```bash
   azcopy login
   ```

   명령 프롬프트 창에 나타나는 지침에 따라 사용자 계정을 인증합니다.

2. *.csv* 계정의 데이터를 복사하려면 다음 명령을 입력합니다.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   - `<csv-folder-path>` 자리 표시자 값을 *.csv* 파일의 경로로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   - `<container-name>` 자리 표시자를 스토리지 계정의 컨테이너 이름으로 바꿉니다.

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역 만들기

이 섹션에서는 Azure 작업 영역을 만듭니다.

1. **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

   [![Azure에 배포](../../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

2. 다음 값을 입력하거나 업데이트합니다.

   - **구독**: Azure 스토리지 계정이 있는 Azure 구독을 선택합니다.
   - **리소스 그룹**: Azure Data Lake 스토리지를 배치한 리소스 그룹을 선택합니다.
   - **지역**: Azure Data Lake 스토리지를 배치한 지역(예: **미국 중부**)을 선택합니다.
   - **이름**: Synapse 작업 영역의 이름을 입력합니다.
   - **SQL 관리자 로그인**: SQL Server의 관리자 사용자 이름을 입력합니다.
   - **SQL 관리자 암호**: SQL Server의 관리자 암호를 입력합니다.
   - **태그 값**: 기본값을 적용합니다.
   - **검토 및 만들기**: 선택합니다.
   - **만들기**: 선택합니다.

배포가 완료되면 배포된 리소스 목록에 Azure Synapse Analytics 작업 영역이 표시됩니다. 링크를 따라 작업 영역에 대한 자세한 내용을 확인하고 [Synapse SQL 엔드포인트 이름을 찾을](#find-your-synapse-sql-endpoint-name) 수 있습니다.

## <a name="find-your-synapse-sql-endpoint-name"></a>Synapse SQL 엔드포인트 이름 찾기

다음 예제에서 서버리스 SQL 풀의 서버 이름은 `showdemoweu-ondemand.sql.azuresynapse.net`입니다. 정규화된 서버 이름을 찾으려면:

1. 연결하려는 작업 영역을 선택합니다.
2. 개요로 이동합니다.
3. 전체 서버 이름을 찾습니다.

   ![전체 서버 이름 서버리스 SQL 풀](../../synapse-analytics/sql/media/connect-overview/server-connect-example-sqlod.png)

### <a name="connect-to-synapse-sql-endpoint"></a>Synapse SQL 엔드포인트에 연결

Synapse SQL 엔드포인트를 사용하면 SQL 서버 또는 Azure SQL 데이터베이스에서 T-SQL 쿼리를 실행할 수 있는 도구에 연결할 수 있습니다. 예제는 [SQL Server Management Studio](../../synapse-analytics/sql/get-started-ssms.md), [Azure Data Studio](../../synapse-analytics/sql/get-started-azure-data-studio.md) 또는 [Power BI](../../synapse-analytics/sql/get-started-power-bi-professional.md)입니다.

원하는 도구를 사용하여 SQL 엔드포인트에 연결하고, 서버리스 SQL 서버리스 엔드포인트 이름을 입력하고, 연결할 Azure AD 인증에 연결합니다.

  > [!IMPORTANT]
  > Azure Storage 계정에 액세스하려면 SQL 로그인을 활성화하는 추가 단계가 필요하므로 사용자 이름과 암호로 SQL 인증을 사용하지 마세요.

## <a name="explore-data"></a>데이터 살펴보기

Synapse 엔드포인트에 연결하는 데 사용한 도구를 사용하여 새 SQL 쿼리를 만들고, 다음 쿼리를 배치하고, 경로를 설정합니다.

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK 'https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv',
        FORMAT='CSV',
        PARSER_VERSION='2.0'
    ) AS [result]
```

쿼리를 실행하면 파일의 내용이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Synapse Analytics 작업 영역을 삭제합니다. 일부 추가 전용 SQL 풀이나 Spark 풀이 없는 작업 영역은 사용하지 않을 경우 요금이 청구되지 않으므로 **유지하더라도 요금이 청구되지 않습니다**.
Azure 스토리지 계정을 배치한 리소스 그룹을 선택한 경우 리소스 그룹을 삭제하지 마세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2, Azure Databricks 및 Spark](data-lake-storage-use-databricks-spark.md)

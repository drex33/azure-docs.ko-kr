---
title: '자습서: Pandas를 사용하여 Synapse Analytics의 서버리스 Apache Spark 풀에서 ADLS 데이터 읽기/쓰기'
description: PySpark Notebook에서 Pandas를 사용하여 서버리스 Apache Spark 풀에서 ADLS 데이터를 읽고 쓰는 방법에 대한 자습서입니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4f977ad6a6cc38a269eedbe3e107b166bd74a4f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029589"
---
# <a name="tutorial-use-pandas-to-readwrite-azure-data-lake-storage-gen2-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>자습서: Pandas를 사용하여 Synapse Analytics의 서버리스 Apache Spark 풀에서 Azure Data Lake Storage Gen2 데이터 읽기/쓰기

Pandas를 사용하여 Azure Synapse Analytics의 서버리스 Apache Spark 풀에서 Azure Data Lake Storage Gen2 데이터를 읽고 쓰는 방법을 배웁니다. 이 자습서의 예제에서는 Synapse에서 Pandas를 사용하여 csv 데이터를 읽는 방법뿐만 아니라 Excel 및 Parquet 파일을 읽는 방법을 보여줍니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

   > [!div class="checklist"]
   > - Spark 세션에서 Pandas를 사용하여 ADLS Gen2 데이터를 읽고 씁니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 또는 기본 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- Azure Synapse Analytics 작업 영역의 서버리스 Apache Spark 풀 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../get-started-analyze-spark.md)를 참조하세요.
- [보조 Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md) 계정(Synapse 작업 영역에 대한 기본값이 아님)을 구성합니다. 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- 연결된 서비스 만들기 - Azure Synapse Analytics에서 연결된 서비스는 서비스에 대한 연결 정보를 정의합니다. 이 자습서에서는 Azure Synapse Analytics와 Azure Data Lake Storage Gen2 연결 서비스를 추가합니다.

   1. Azure Synapse Studio를 열고 **관리** 탭을 선택합니다.
   1. **외부 연결** 에서 **연결된 서비스** 를 선택합니다.
   1. 연결된 서비스를 추가하려면 **새로 만들기** 를 선택합니다.
   1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택하고 **계속** 을 선택합니다.
   1. 인증 자격 증명을 입력합니다. 계정 키, 서비스 주체(SP), 자격 증명 및 관리 서비스 ID(Manged Service Identity)는 현재 지원되는 인증 유형입니다. 인증을 위해 선택하기 전에 *Storage BLOB 데이터 기여자* 가 SP 및 MSI용 스토리지에 할당되어 있는지 확인합니다. **연결 테스트** 로 자격 증명이 올바른지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-use-pandas-spark-pool/create-adls-linked-service.png" alt-text="ADLS Gen2 스토리지 액세스 키를 사용하여 연결된 서비스를 만드는 스크린샷.":::


> [!NOTE]
> - Pandas 기능은 Azure Synapse Analytics **Python 3.8** 및 **Spark3** 서버리스 Apache Spark 풀에서 지원됩니다. 
> - 다음 버전에 사용할 수 있는 지원: **pandas 1.2.3, fsspec 2021.10.0, adlfs 0.7.7**
> - **Azure Data Lake Storage Gen2 URI**(abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path) 및 **FSSPEC 짧은 URL**(abfs[s]://container_name/file_path)을 모두 지원하는 기능이 있습니다.


## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="readwrite-data-to-default-adls-storage-account-of-synapse-workspace"></a>Synapse 작업 영역의 기본 ADLS 스토리지 계정에 데이터 읽기/쓰기

Pandas는 파일 경로를 직접 지정하여 ADLS 데이터를 읽고 쓸 수 있습니다.

다음 코드를 실행합니다.

   > [!NOTE]
   > 이 스크립트를 실행하기 전에 파일 URL을 업데이트합니다.

   ```PYSPARK
      #Read data file from URI of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://container_name/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path')
   ```

## <a name="readwrite-data-using-secondary-adls-account"></a>보조 ADLS 계정을 사용하여 데이터 읽기/쓰기

Pandas는 보조 ADLS 계정 데이터를 읽고 쓸 수 있습니다.
   - 연결된 서비스 사용(인증 옵션 사용 - 스토리지 계정 키, 서비스 주체, 서비스 ID 및 자격 증명 관리)
   - 스토리지 옵션을 사용하여 클라이언트 ID 및 비밀, SAS 키, 스토리지 계정 키 및 연결 문자열을 직접 전달합니다.

**연결된 서비스 사용**

다음 코드를 실행합니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트에서 파일 URL 및 연결된 서비스 이름을 업데이트합니다.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'linked_service' : 'linked_service_name'})
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options =       {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'linked_service' :    'linked_service_name'})
   ```


**스토리지 옵션을 사용** 하여 클라이언트 ID 및 비밀, SAS 키, 스토리지 계정 키 및 연결 문자열을 직접 전달합니다.

다음 코드를 실행합니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트에서 파일 URL과 storage_options를 업데이트합니다.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```


## <a name="example-to-readwrite-parquet-file"></a>parquet 파일을 읽고 쓰는 예제 

다음 코드를 실행합니다.

   > [!NOTE]
   > 이 스크립트를 실행하기 전에 파일 URL을 업데이트합니다.

   ```PYSPARK
      import pandas
      
      #read parquet file
      df = pandas.read_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
      print(df)
      
      #write parquet file
      df.to_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
   ```

## <a name="example-to-readwrite-excel-file"></a>Excel 파일을 읽고 쓰는 예제 

다음 코드를 실행합니다.

   > [!NOTE]
   > 이 스크립트를 실행하기 전에 파일 URL을 업데이트합니다.

   ```PYSPARK
      import pandas
      
      #read excel file
      df = pandas.read_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   excel_file_path')
      print(df)
      
      #write excel file
      df.to_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/excel_file_path')
   ```

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../index.yml)

---
title: Azure의 스토리지 서비스에 대한 ID 기반 데이터 액세스
titleSuffix: Machine Learning
description: ID 기반 데이터 액세스를 사용하여 Azure Machine Learning 데이터 저장소 및 Machine Learning Python SDK로 Azure의 스토리지 서비스에 연결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: e82ba7fbab8d82d266fb3a4d86d97598bc9b6703
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722965"
---
# <a name="connect-to-storage-by-using-identity-based-data-access"></a>ID 기반 데이터 액세스를 사용하여 스토리지에 커넥트

이 문서에서는 [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 통해 ID 기반 데이터 액세스 및 Azure Machine Learning 데이터 저장소를 사용하여 Azure의 스토리지 서비스에 연결하는 방법을 알아봅니다.  

일반적으로 데이터 저장소는 자격 증명 기반 데이터 액세스를 사용하여 스토리지 서비스에 액세스할 권한이 있는지 확인합니다. 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 작업 영역과 연결된 [키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)에 유지합니다. ID 기반 데이터 액세스를 사용하는 데이터 저장소를 만들 때 Azure 계정([Azure Active Directory 토큰](../active-directory/fundamentals/active-directory-whatis.md))을 사용하여 스토리지 서비스에 액세스할 권한이 있는지 확인합니다. 이 시나리오에서는 인증 자격 증명이 저장되지 않습니다. 스토리지 계정 정보만 데이터 저장소에 저장됩니다. 

액세스 키 또는 서비스 주체와 같은 자격 증명 기반 인증을 사용하는 데이터 저장소를 만들려면 [Azure의 스토리지 서비스에 연결](how-to-access-data.md)을 참조하세요.

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure Machine Learning에서 ID 기반 데이터 액세스

Azure Machine Learning에서 ID 기반 데이터 액세스를 적용할 수 있는 두 가지 시나리오가 있습니다. 이러한 시나리오는 기밀 데이터로 작업하고 더 세분화된 데이터 액세스 관리가 필요한 경우 ID 기반 액세스에 적합합니다.

- 스토리지 서비스에 액세스
- 프라이빗 데이터를 사용하여 기계 학습 모델 학습

### <a name="accessing-storage-services"></a>스토리지 서비스에 액세스

Azure Machine Learning 데이터 저장소 또는 [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)를 사용하여 ID 기반 데이터 액세스를 통해 스토리지 서비스에 연결할 수 있습니다. 

인증 자격 증명은 일반적으로 데이터 저장소에 보관되며, 스토리지 서비스에 액세스할 수 있는 권한이 있는지 확인하는 데 사용됩니다. 이러한 자격 증명이 데이터 저장소를 통해 등록되면 작업 영역 reader 역할이 있는 모든 사용자가 자격 증명을 검색할 수 있습니다. 이러한 액세스 규모는 일부 조직에서 보안 문제가 될 수 있습니다. [작업 영역 reader 역할에 대해 자세히 알아보세요.](how-to-assign-roles.md#default-roles) 

ID 기반 데이터 액세스를 사용하는 경우, Azure Machine Learning은 데이터 저장소에 자격 증명을 유지하는 대신 데이터 액세스 인증을 위해 Azure Active Directory 토큰을 입력하라는 메시지를 표시합니다. 이 방식을 사용하면 스토리지 수준에서 데이터 액세스를 관리할 수 있고 자격 증명을 기밀로 유지할 수 있습니다. 

다음과 같은 경우 동일한 동작이 적용됩니다.

* [스토리지 URL에서 직접 데이터 세트를 만드는 경우](#use-data-in-storage) 
* 로컬 컴퓨터 또는 [컴퓨팅 인스턴스](concept-compute-instance.md)에서 Jupyter Notebook을 통해 대화형으로 데이터를 사용하는 경우

> [!NOTE]
> 자격 증명 기반 인증을 통해 저장되는 자격 증명에는 구독 ID, SAS(공유 액세스 서명) 토큰, 스토리지 액세스 키 및 서비스 주체 정보(예: 클라이언트 ID 및 테넌트 ID)가 포함됩니다.

### <a name="model-training-on-private-data"></a>프라이빗 데이터에 대한 모델 학습

특정 기계 학습 시나리오에는 프라이빗 데이터로 모델을 학습시키는 과정이 포함됩니다. 이러한 경우 데이터 과학자는 기밀 입력 데이터에 노출되지 않으면서 학습 워크플로를 실행해야 합니다. 이 시나리오에서는 데이터 액세스 인증에 학습 컴퓨팅의 관리 ID가 사용됩니다. 이 방식을 통해 스토리지 관리자는 관리 ID에 Storage Blob 데이터 판독기 액세스 권한을 부여할 수 있습니다. 이 권한은 학습 컴퓨팅이 학습 작업을 실행하는 데 사용됩니다. 개별 데이터 과학자에게 액세스 권한을 부여할 필요가 없습니다. 자세한 내용은 [컴퓨팅 클러스터에서 관리 ID 설정](how-to-create-attach-compute-cluster.md#managed-identity)을 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

- 지원되는 스토리지 형식을 사용하는 Azure 스토리지 계정. 미리 보기에서 지원되는 스토리지 유형은 다음과 같습니다. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/install).

- Azure Machine Learning 작업 영역
  
  [Azure Machine Learning 작업 영역을 생성](how-to-manage-workspace.md)하거나 [Python SDK를 통해 기존 작업 영역](how-to-manage-workspace.md#connect-to-a-workspace)을 사용합니다. 

## <a name="storage-access-permissions"></a>스토리지 액세스 권한

Azure의 스토리지 서비스에 안전하게 연결할 수 있도록, Azure Machine Learning을 사용하려면 해당하는 데이터 스토리지에 액세스할 수 있는 권한이 있어야 합니다.

ID 기반 데이터 액세스는 다음 스토리지 서비스에 대한 연결만 지원합니다.

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

이러한 스토리지 서비스에 액세스하려면 최소한 [스토리지 Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 액세스 권한이 있어야 합니다. 스토리지 계정 소유자만 [Azure Portal을 통해 액세스 수준을 변경](../storage/blobs/assign-azure-role-data-access.md)할 수 있습니다.

원격 컴퓨팅 대상에서 모델을 학습시키는 경우에는 컴퓨팅 ID에 스토리지 서비스에서 최소한 Storage Blob 데이터 판독기 역할을 부여해야 합니다. [컴퓨팅 클러스터에 관리 ID를 설정](how-to-create-attach-compute-cluster.md#managed-identity)하는 방법에 대해 알아보세요.

## <a name="work-with-virtual-networks"></a>가상 네트워크 사용

기본적으로 Azure Machine Learning은 방화벽 뒤에 있거나 가상 네트워크에 있는 스토리지 계정과 통신할 수 없습니다.

특정 가상 네트워크 내에서만 액세스가 가능하도록 스토리지 계정을 구성할 수 있습니다. 이러한 구성에는 데이터가 네트워크 외부로 유출되지 않도록 하기 위한 추가 단계가 필요합니다. 이 동작은 자격 증명 기반 데이터 액세스에서도 동일합니다. 자세한 내용은 [가상 네트워크 시나리오를 구성하는 방법](how-to-access-data.md#virtual-network)을 참조하세요. 

## <a name="create-and-register-datastores"></a>데이터 저장소 만들기 및 등록

Azure의 스토리지 솔루션을 데이터 저장소로 등록하면 해당 데이터 저장소가 자동으로 생성되어 특정 작업 영역에 등록됩니다. 필요한 권한 유형에 대한 지침은 [스토리지 액세스 권한](#storage-access-permissions)을 참조하세요. 가상 네트워크 뒤의 데이터 스토리지에 연결하는 방법에 대한 자세한 내용은 [가상 네트워크 사용](#work-with-virtual-networks)을 참조하세요.

다음 코드에는 `sas_token`, `account_key`, `subscription_id` 및 서비스 주체 `client_id`와 같은 인증 매개 변수가 없습니다. 이러한 생략은 Azure Machine Learning이 인증에 ID 기반 데이터 액세스를 사용하는 것을 나타냅니다. 데이터 저장소 생성은 일반적으로 Notebook에서 또는 스튜디오를 통해 대화형으로 발생합니다. 따라서 Azure Active Directory 토큰이 데이터 액세스 인증에 사용됩니다.

> [!NOTE]
> 데이터 저장소 이름은 소문자, 숫자, 밑줄로만 구성되어야 합니다. 

### <a name="azure-blob-container"></a>Azure Blob 컨테이너

Azure BLOB 컨테이너를 데이터 저장소로 등록하려면 [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)를 사용합니다.

다음 코드는 `credentialless_blob` 데이터 저장소를 만들어 `ws` 작업 영역에 등록하고 `blob_datastore` 변수에 할당합니다. 이 데이터 저장소는 `my-account-name` 스토리지 계정의 `my_container_name` Blob 컨테이너에 액세스합니다.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

[register_azure_data_lake()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-)를 사용하여 Azure Data Lake Storage Gen1에 연결하는 데이터 저장소를 등록합니다.

다음 코드는 `credentialless_adls1` 데이터 저장소를 만들어 `workspace` 작업 영역에 등록하고 `adls_dstore` 변수에 할당합니다. 이 데이터 저장소는 `adls_storage` Azure Data Lake Storage 계정에 액세스합니다.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

[register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-)를 사용하여 Azure Data Lake Storage Gen2에 연결하는 데이터 저장소를 등록합니다.

다음 코드는 `credentialless_adls2` 데이터 저장소를 만들어 `ws` 작업 영역에 등록하고 `adls2_dstore` 변수에 할당합니다. 이 데이터 저장소는 `myadls2` 스토리지 계정의 `tabular` 파일 시스템에 액세스합니다.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```
### <a name="azure-sql-database"></a>Azure SQL 데이터베이스
Azure SQL 데이터베이스의 경우 [register_azure_sql_database()를](/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none--subscription-id-none--resource-group-none--grant-workspace-access-false----kwargs-) 사용하여 Azure SQL 데이터베이스 스토리지에 연결하는 데이터 저장소를 등록합니다.

다음 코드는 데이터 저장소를 만들어 `credentialless_sqldb` 작업 영역에 `ws` 등록하고 변수 에 `sqldb_dstore` 할당합니다. 이 데이터 저장소는 `mydb` SQL DB 서버의 데이터베이스에 `myserver` 액세스합니다.  

```python
# createn sqldatabase datastore without credentials
                                                       
sqldb_dstore = Datastore.register_azure_sql_database(workspace=ws,
                                                       datastore_name='credentialless_sqldb',
                                                       server_name='myserver',
                                                       database_name='mydb')                                                       
                                                   
```

## <a name="use-data-in-storage"></a>스토리지의 데이터 사용

Azure Machine Learning을 사용하여 스토리지의 데이터와 상호 작용할 때 [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)를 사용하는 것이 좋습니다. 

데이터 세트는 학습과 같은 기계 학습 작업을 위해 천천히 평가되는 사용 가능한 개체로 데이터를 패키징합니다. 또한 데이터 세트를 사용하여 Azure Storage 서비스(예: Azure Blob Storage 및 Azure Data Lake Storage)에서 컴퓨팅 대상으로 모든 형식의 파일을 [다운로드하거나 탑재](how-to-train-with-datasets.md#mount-vs-download)할 수 있습니다.


ID 기반 데이터 액세스를 사용하여 데이터 세트를 만드는 경우 다음과 같은 옵션이 있습니다. 이러한 유형의 데이터 세트를 만들 때는 데이터 액세스 인증에 Azure Active Directory 토큰을 사용합니다. 

*  ID 기반 데이터 액세스도 사용하는 데이터 저장소의 경로를 참조합니다. 
<br>다음 예에서 `blob_datastore`는 이미 존재하며 ID 기반 데이터 액세스를 사용합니다.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* 데이터 저장소 만들기를 건너뛰고 스토리지 URL에서 직접 데이터 세트를 만듭니다. 이 기능은 현재 Azure Blob 및 Azure Data Lake Storage Gen1 및 Gen2만 지원합니다.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

ID 기반 데이터 액세스를 사용하여 만든 데이터 세트를 사용하는 학습 작업을 제출하는 경우 학습 컴퓨팅의 관리 ID가 데이터 액세스 인증에 사용됩니다. Azure Active Directory 토큰은 사용되지 않습니다. 이 시나리오의 경우 컴퓨팅의 관리 ID에 최소한 스토리지 서비스의 Storage Blob 데이터 판독기 역할이 부여되었는지 확인합니다. 자세한 내용은 [컴퓨팅 클러스터에서 관리 ID 설정](how-to-create-attach-compute-cluster.md#managed-identity)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning 데이터 세트 만들기](how-to-create-register-datasets.md)
* [데이터 세트로 학습](how-to-train-with-datasets.md)
* [키 기반 데이터 액세스를 사용하여 데이터 저장소 만들기](how-to-access-data.md)
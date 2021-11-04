---
title: Azure에서 스토리지 서비스에 연결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning으로 학습하는 동안 데이터 저장소를 사용하여 Azure 스토리지 서비스에 안전하게 연결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 9d722d5344316582536b3301270ee47f3ec9d3dc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554966"
---
# <a name="connect-to-storage-services-on-azure"></a>Azure에서 스토리지 서비스에 연결

이 문서에서는 Azure Machine Learning 데이터 저장소 및 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)를 사용하여 Azure의 데이터 스토리지 서비스에 연결하는 방법을 알아봅니다.

데이터 저장소는 인증 자격 증명과 원래 데이터 원본의 무결성을 위험에 노출시키지 않고 Azure의 스토리지 서비스에 안전하게 연결합니다. 작업 영역과 연결된 [Key Vault](https://azure.microsoft.com/services/key-vault/)의 구독 ID 및 토큰 권한 부여 같은 연결 정보를 저장하므로, 스크립트에서 이러한 정보를 하드 코딩할 필요 없이 스토리지에 안전하게 액세스할 수 있습니다. [이 Azure 스토리지 솔루션](#matrix)에 연결하는 데이터 저장소를 만들 수 있습니다.

Azure Machine Learning의 데이터 액세스 워크플로 전체에서 데이터 저장소가 적합한 위치를 이해하려면 [안전하게 데이터 액세스](concept-data.md#data-workflow) 문서를 참조하세요.

하위 코드 환경의 경우 [Azure Machine Learning 스튜디오를 사용하여 데이터 저장소를 만들고 등록](how-to-connect-data-ui.md#create-datastores)하는 방법을 참조하세요.

>[!TIP]
> 이 문서에서는 서비스 주체 또는 SAS(공유 액세스 서명) 토큰과 같은 자격 증명 기반 인증 자격 증명을 사용하여 스토리지 서비스에 연결하려는 경우를 가정합니다. 자격 증명이 데이터 저장소에 등록된 경우 작업 영역 *읽기 권한자* 역할을 가진 모든 사용자는 이러한 자격 증명을 검색할 수 있습니다. [작업 영역 *읽기 권한자* 역할에 대해 자세히 알아보세요.](how-to-assign-roles.md#default-roles) <br><br>이 부분이 걱정된다면 [ID 기반 액세스를 사용하여 스토리지 서비스에 연결](how-to-identity-based-data-access.md)하는 방법을 알아봅니다. <br><br>이 기능은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

- [지원되는 스토리지 형식](#matrix)을 사용하는 Azure 스토리지 계정.

- [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro).

- Azure Machine Learning 작업 영역
  
  [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만들거나 Python SDK를 통해 기존 작업 영역을 사용합니다. 

    `Workspace` 및 `Datastore` 클래스를 가져오고, `from_config()` 함수를 사용하여 `config.json` 파일의 구독 정보를 로드합니다. 이 작업은 기본적으로 현재 디렉터리에서 JSON 파일을 찾지만, `from_config(path="your/file/path")`를 사용하여 해당 파일을 가리키도록 경로 매개 변수를 지정할 수도 있습니다.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    작업 영역을 만들면 Azure Blob 컨테이너와 Azure 파일 공유가 자동으로 작업 영역에 데이터 저장소로 등록됩니다. 각각 이름이 `workspaceblobstore` 및 `workspacefilestore`로 지정됩니다. `workspaceblobstore`는 작업 영역 아티팩트와 기계 학습 실험 로그를 저장하는 데 사용됩니다. 또한 **기본 데이터 저장소** 로 설정되며 작업 영역에서 삭제할 수 없습니다. `workspacefilestore`는 [컴퓨팅 인스턴스](./concept-compute-instance.md#accessing-files)를 통해 권한이 부여된 Notebook 및 R 스크립트를 저장하는 데 사용됩니다.
    
    > [!NOTE]
    > 디자이너 홈페이지에서 샘플을 열면 Azure Machine Learning 디자이너가 **azureml_globaldatasets** 라는 데이터 저장소를 자동으로 만듭니다. 이 데이터 저장소는 샘플 데이터 세트만 포함합니다. 기밀 데이터 액세스에는 이 데이터 저장소를 사용하지 **마세요**.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>지원되는 데이터 스토리지 서비스 유형

현재 데이터 저장소는 다음 표에 나열된 스토리지 서비스에 연결 정보를 저장할 수 있습니다. 

> [!TIP]
> **지원되지 않는 스토리지 솔루션의 경우**, ML 실험 중에 데이터 송신 비용을 절약하려면 지원되는 Azure 스토리지 솔루션으로 [데이터를 이동](#move)하세요. 

| 스토리지&nbsp;유형 | 인증&nbsp;유형 | [Azure&nbsp;Machine&nbsp;Learning 스튜디오](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](/python/api/overview/azure/ml/intro) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](/rest/api/azureml/) | VS 코드
---|---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;Storage](../storage/blobs/storage-blobs-overview.md)| 계정 키 <br> SAS 토큰 | ✓ | ✓ | ✓ |✓ |✓
[Azure&nbsp;파일&nbsp;공유](../storage/files/storage-files-introduction.md)| 계정 키 <br> SAS 토큰 | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](../data-lake-store/index.yml)| 서비스 주체| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](../storage/blobs/data-lake-storage-introduction.md)| 서비스 주체| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;Database](../azure-sql/database/sql-database-paas-overview.md)| SQL 인증 <br>서비스 주체| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](../postgresql/overview.md) | SQL 인증| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;for&nbsp;MySQL](../mysql/overview.md) | SQL 인증|  | ✓* | ✓* |✓*|
[Databricks&nbsp;파일&nbsp;시스템](/azure/databricks/data/databricks-file-system)| 인증 없음 | | ✓** | ✓ ** |✓** |

\* MySQL은 [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) 파이프라인에만 지원됩니다.<br />
\*\* Databricks는 [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep) 파이프라인에만 지원됩니다.


### <a name="storage-guidance"></a>스토리지 지침

[Azure Blob 컨테이너](../storage/blobs/storage-blobs-introduction.md)에 대한 데이터 저장소를 만드는 것이 좋습니다. BLOB에 표준 스토리지와 프리미엄 스토리지 둘 다 제공됩니다. 프리미엄 스토리지는 더 비싸지만 처리 속도가 빨라서 학습 실행 속도를 높일 수 있으며, 특히 대량의 데이터 세트를 학습시킬 때 유용합니다. 스토리지 계정 비용에 대한 내용은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)를 참조하세요.

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)는 Azure Blob Storage를 기반으로 하며 엔터프라이즈 빅 데이터 분석용으로 설계되었습니다. Data Lake Storage Gen2의 기본적인 부분은 [계층 구조 네임스페이스](../storage/blobs/data-lake-storage-namespace.md)를 Blob 스토리지에 추가하는 것입니다. 계층 구조 네임스페이스는 효율적인 데이터 액세스를 위해 개체/파일을 디렉터리 계층 구조로 구성합니다.

## <a name="storage-access-and-permissions"></a>스토리지 액세스 및 사용 권한

Azure Storage 서비스에 안전하게 연결하려면 Azure Machine Learning에서 해당하는 데이터 스토리지 컨테이너에 액세스할 권한이 있어야 합니다. 이 액세스 권한은 데이터 저장소를 등록하는 데 사용되는 인증 자격 증명에 따라 다릅니다. 

> [!NOTE]
> 이 참고 자료는 [ID 기반 데이터 액세스 권한으로 생성된 데이터 저장소(미리 보기)](how-to-identity-based-data-access.md)에도 적용됩니다. 

### <a name="virtual-network"></a>가상 네트워크 

Azure Machine Learning은 방화벽 뒤 또는 가상 네트워크 내에 있는 스토리지 계정과 통신하기 위한 추가 구성 단계가 필요합니다. 스토리지 계정이 방화벽 뒤에 있는 경우 [Azure Portal을 통해 IP 주소 나열을 허용](../storage/common/storage-network-security.md#managing-ip-network-rules)할 수 있습니다.

Azure Machine Learning은 가상 네트워크 외부의 클라이언트에서 요청을 받을 수 있습니다. 서비스에서 데이터를 요청하는 엔터티가 안전한지 확인하려면 [작업 영역이 포함된 프라이빗 엔드포인트를 사용](how-to-configure-private-link.md)합니다.

**Python SDK 사용자의 경우** 컴퓨팅 대상의 학습 스크립트를 통해 데이터에 액세스하려면 컴퓨팅 대상이 스토리지의 동일한 가상 네트워크 및 서브넷 내부에 있어야 합니다. 

**Azure Machine Learning 스튜디오 사용자의 경우** 몇 가지 기능이 데이터 세트 미리 보기, 프로필 및 자동화된 Machine Learning 등 데이터 세트에서 데이터를 읽는 기능에 의존합니다. 이러한 기능이 가상 네트워크 뒤에 있는 스토리지에서 작동하도록 하려면 [스튜디오에서 작업 영역 관리 ID](how-to-enable-studio-virtual-network.md)를 사용하여 Azure Machine Learning이 가상 네트워크 외부에서 스토리지 계정에 액세스하도록 허용합니다. 

> [!NOTE]
> 데이터 스토리지가 가상 네트워크 뒤에 있는 Azure SQL Database인 경우 [Azure Portal](https://ms.portal.azure.com/)을 통해 *공용 액세스 거부* 를 **아니요** 로 설정하여 Azure Machine Learning에서 스토리지 계정에 액세스할 수 있도록 해야 합니다.

### <a name="access-validation"></a>액세스 유효성 검사

**초기 데이터 저장소 생성 및 등록 프로세스에서** Azure Machine Learning은 기본 스토리지 서비스가 있고 사용자가 제공한 보안 주체(사용자 이름, 서비스 주체 또는 SAS 토큰)가 지정된 스토리지에 대한 액세스 권한이 있는지 자동으로 유효성을 검사합니다.

**데이터 저장소가 생성된 후** 이 유효성 검사는 기본 스토리지 컨테이너에 액세스해야 하는 메서드에 대해서만 수행되고, 데이터 저장소 개체가 검색될 때마다 **수행되지는 않습니다**. 예를 들어 데이터 저장소에서 파일을 다운로드하려는 경우에는 유효성 검사가 수행되지만, 기본 데이터 저장소만 변경하려는 경우에는 유효성 검사가 수행되지 않습니다.

기본 스토리지 서비스에 대한 액세스를 인증하려면 만들려는 데이터 저장소 유형의 해당 `register_azure_*()` 메서드에 계정 키, SAS(공유 액세스 서명) 토큰 또는 서비스 주체를 제공하면 됩니다. [스토리지 유형 행렬](#matrix)에는 각 데이터 저장소 유형에 해당하는 지원되는 인증 유형이 나열됩니다.

[Azure Portal](https://portal.azure.com)에서 계정 키, SAS 토큰 및 서비스 주체 정보를 찾을 수 있습니다.

* 계정 키 또는 SAS 토큰을 인증에 사용할 계획이면 왼쪽 창에서 **스토리지 계정** 을 선택하고, 등록하려는 스토리지 계정을 선택합니다. 
  * **개요** 페이지에서는 계정 이름, 컨테이너, 파일 공유 이름 등의 정보를 제공합니다. 
      1. 계정 키의 경우 **설정** 창에서 **액세스 키** 로 이동합니다. 
      1. SAS 토큰의 경우 **설정** 창에서 **공유 액세스 서명** 으로 이동합니다.

* 서비스 주체를 인증에 사용할 계획이면 **앱 등록** 으로 이동하여 사용하려는 앱을 선택합니다. 
    * 해당하는 **개요** 페이지에는 테넌트 ID, 클라이언트 ID 등의 필수 정보가 포함되어 있습니다.

> [!IMPORTANT]
> * Azure Storage 계정(계정 키 또는 SAS 토큰)의 액세스 키를 변경해야 하는 경우 새 자격 증명을 작업 영역 및 이 작업 영역에 연결된 데이터 저장소와 동기화해야 합니다. [업데이트된 자격 증명을 동기화](how-to-change-storage-access-key.md)하는 방법을 알아봅니다. 
### <a name="permissions"></a>사용 권한

Azure Blob 컨테이너 및 Azure Data Lake Gen 2 스토리지의 경우, 인증 자격 증명에 **스토리지 Blob 데이터 읽기 권한자** 액세스 권한이 있는지 확인합니다. [스토리지 Blob 데이터 읽기 권한자](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)에 대해 자세히 알아보세요. 계정 SAS 토큰은 기본적으로 권한 없음으로 설정됩니다. 
* 데이터 **읽기 액세스** 의 경우 인증 자격 증명에는 컨테이너 및 개체에 대한 최소한의 나열 및 읽기 권한이 있어야 합니다. 

* 데이터 **쓰기 액세스** 의 경우, 쓰기 및 추가 권한도 필요합니다.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>데이터 저장소 만들기 및 등록

Azure 스토리지 솔루션을 데이터 저장소로 등록하면 해당 데이터 저장소가 자동으로 생성되어 특정 작업 영역에 등록됩니다. [스토리지 액세스 및 사용 권한](#storage-access-and-permissions) 섹션을 검토하여 가상 네트워크 시나리오에 대한 참고 자료와 필요한 인증 자격 증명을 확인할 수 있는 위치를 확인합니다. 

이 섹션에는 Python SDK를 통해 다음 스토리지 형식의 데이터 저장소를 만들고 등록하는 방법에 대한 예제가 나와 있습니다. 이 예제에 제공된 매개 변수는 데이터 저장소를 만들고 등록하는 데 필요한 **필수 매개 변수** 입니다.

* [Azure Blob 컨테이너](#azure-blob-container)
* [Azure 파일 공유](#azure-file-share)
* [Azure Data Lake Storage Generation 2](#azure-data-lake-storage-generation-2)

 지원되는 다른 스토리지 서비스에 대한 데이터 저장소를 만들려면 [해당하는 `register_azure_*` 메서드의 참조 설명서](/python/api/azureml-core/azureml.core.datastore.datastore#methods)를 확인하세요.

하위 코드 환경을 선호하는 경우 [Azure Machine Learning 스튜디오를 사용하여 데이터에 연결](how-to-connect-data-ui.md)을 참조하세요.
>[!IMPORTANT]
> 데이터 저장소의 등록을 취소하고 같은 이름으로 다시 등록할 때 실패하는 경우 작업 영역의 Azure Key Vault에 일시 삭제가 설정되지 않은 것일 수 있습니다. 일시 삭제는 작업 영역에서 만든 키 자격 증명 모음 인스턴스에 대해 기본적으로 사용되지만, 기존 키 자격 증명 모음을 사용하거나 2020년 10월 이전에 작업 영역을 만든 경우에는 일시 삭제가 사용되지 않을 수 있습니다. 일시 삭제를 사용하도록 설정하는 방법에 관한 자세한 내용은 [기존 키 자격 증명 모음에 대해 일시 삭제 설정](../key-vault/general/soft-delete-change.md#turn-on-soft-delete-for-an-existing-key-vault)을 참조하세요.


> [!NOTE]
> 데이터 저장소 이름은 소문자, 숫자, 밑줄만으로 구성되어야 합니다. 

### <a name="azure-blob-container"></a>Azure Blob 컨테이너

Azure BLOB 컨테이너를 데이터 저장소로 등록하려면 [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)를 사용합니다.

다음 코드는 `blob_datastore_name` 데이터 저장소를 만들어 `ws` 작업 영역에 등록합니다. 이 데이터 저장소는 제공된 계정 액세스 키를 사용하여 `my-account-name` 스토리지 계정의 `my-container-name` BLOB 컨테이너에 액세스합니다. [스토리지 액세스 및 사용 권한](#storage-access-and-permissions) 섹션을 검토하여 가상 네트워크 시나리오에 대한 참고 자료와 필요한 인증 자격 증명을 확인할 수 있는 위치를 확인합니다. 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure 파일 공유

Azure 파일 공유를 데이터 저장소로 등록하려면 [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)를 사용합니다. 

다음 코드는 `file_datastore_name` 데이터 저장소를 만들어 `ws` 작업 영역에 등록합니다. 이 데이터 저장소는 제공된 계정 액세스 키를 사용하여 `my-account-name` 스토리지 계정의 `my-fileshare-name` 파일 공유에 액세스합니다. [스토리지 액세스 및 사용 권한](#storage-access-and-permissions) 섹션을 검토하여 가상 네트워크 시나리오에 대한 참고 자료와 필요한 인증 자격 증명을 확인할 수 있는 위치를 확인합니다. 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Azure Data Lake Storage Generation 2(ADLS Gen 2) 데이터 저장소의 경우 [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-)를 사용하여 Azure DataLake Gen 2 스토리지에 연결된 자격 증명 데이터 저장소를 [서비스 주체 권한](../active-directory/develop/howto-create-service-principal-portal.md)에 등록합니다.  

서비스 주체를 활용하려면 [애플리케이션을 등록](../active-directory/develop/app-objects-and-service-principals.md)하고 Azure RBAC(Azure 역할 기반 액세스 제어) 또는 ACL(액세스 제어 목록)을 통해 서비스 주체에 데이터 액세스 권한을 부여해야 합니다. [ADLS Gen 2의 액세스 제어 설정](../storage/blobs/data-lake-storage-access-control-model.md)에 대해 자세히 알아보세요. 

다음 코드는 `adlsgen2_datastore_name` 데이터 저장소를 만들어 `ws` 작업 영역에 등록합니다. 이 데이터 저장소는 제공된 서비스 주체 자격 증명을 사용하여 `account_name` 스토리지 계정의 `test` 파일 시스템에 액세스합니다. [스토리지 액세스 및 사용 권한](#storage-access-and-permissions) 섹션을 검토하여 가상 네트워크 시나리오에 대한 참고 자료와 필요한 인증 자격 증명을 확인할 수 있는 위치를 확인합니다. 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```



## <a name="create-datastores-with-other-azure-tools"></a>다른 Azure 도구를 사용하여 데이터 저장소 만들기
Python SDK 및 스튜디오를 사용하여 데이터 저장소를 만드는 것 외에도 Azure Resource Manager 템플릿 또는 Azure Machine Learning VS Code 확장을 사용할 수 있습니다. 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure Resource Manager

[https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices)에는 데이터 저장소를 만드는 데 사용할 수 있는 여러 가지 템플릿이 있습니다.

해당 템플릿 사용에 관한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning용 작업 영역 만들기 ](how-to-create-workspace-template.md)를 참조하세요.

### <a name="vs-code-extension"></a>VS Code 확장

Azure Machine Learning VS Code 확장을 사용하여 데이터 저장소를 만들고 관리하려면 [VS Code 리소스 관리 방법 가이드](how-to-manage-resources-vscode.md#datastores)를 방문하여 자세히 알아보세요.
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>데이터 저장소의 데이터 사용

데이터 저장소를 만든 후 [Azure Machine Learning 데이터 세트를 만들어](how-to-create-register-datasets.md) 데이터와 상호 작용합니다. 데이터 세트는 학습 같은 기계 학습 태스크를 위해 데이터를 지연 평가되는 소모성 개체로 패키징합니다. 

데이터 세트를 사용하면 컴퓨팅 대상에 대한 모델 학습을 위해 Azure Storage 서비스에서 모든 형식의 파일을 [다운로드하거나 탑재](how-to-train-with-datasets.md#mount-vs-download)할 수 있습니다. [데이터 세트를 사용하여 ML 모델을 학습시키는 방법을 알아보세요](how-to-train-with-datasets.md).

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>작업 영역에서 데이터 저장소 가져오기

현재 작업 영역에 등록된 특정 데이터 저장소를 가져오려면 `Datastore` 클래스에서 [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29#get-workspace--datastore-name-) 정적 메서드를 사용합니다.

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
특정 작업 영역에 등록된 데이터 저장소 목록을 가져오려면 다음과 같이 작업 영역 개체에 대한 [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29#datastores) 속성을 사용합니다.

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

작업 영역의 기본 데이터 저장소를 가져오려면 다음 줄을 사용합니다.

```Python
datastore = ws.get_default_datastore()
```
다음 코드를 사용하여 기본 데이터 저장소를 변경할 수도 있습니다. 이 기능은 SDK를 통해서만 지원됩니다. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>채점 중에 데이터 액세스

Azure Machine Learning은 모델을 채점에 사용할 수 있는 여러 가지 방법을 제공합니다. 이러한 방법 중 일부는 데이터 저장소에 대한 액세스를 제공하지 않습니다. 다음 표를 사용하여 채점 중에 데이터 저장소에 액세스할 수 있는 방법을 알아보세요.

| 방법 | 데이터 저장소 액세스 | Description |
| ----- | :-----: | ----- |
| [일괄 처리 예측](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | 대량의 데이터에 대해 비동기적으로 예측합니다. |
| [웹 서비스](how-to-deploy-and-where.md) | &nbsp; | 모델을 웹 서비스로 배포합니다. |

SDK가 데이터 저장소에 대한 액세스를 제공하지 않는 경우에는 관련 Azure SDK를 사용하여 데이터에 액세스하는 사용자 지정 코드를 만들 수 있습니다. 예를 들어 [Python용 Azure Storage SDK](https://github.com/Azure/azure-storage-python)는 BLOB 또는 파일에 저장된 데이터에 액세스하는 데 사용할 수 있는 클라이언트 라이브러리입니다.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>지원되는 Azure 스토리지 솔루션으로 데이터 이동

Azure Machine Learning은 Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL에서 데이터에 액세스하는 기능을 지원합니다. 지원되지 않는 스토리지를 사용하는 경우 [Azure Data Factory 및 다음 단계](../data-factory/quickstart-create-data-factory-copy-data-tool.md)를 사용하여 지원되는 Azure 스토리지 솔루션으로 데이터를 이동하는 것이 좋습니다. 지원되는 스토리지로 데이터를 이동하면 기계 학습 실험 중에 발생하는 데이터 송신 비용을 절감할 수 있습니다. 

Azure Data Factory는 80개 이상의 미리 작성된 커넥터를 추가 비용 없이 제공하므로 효율적이 고 복원력 있는 데이터 전송이 가능합니다. 이러한 커넥터로는 Azure 데이터 서비스, 온-프레미스 데이터 원본, Amazon S3 및 Redshift, Google BigQuery가 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 기계 학습 데이터 세트 만들기](how-to-create-register-datasets.md)
* [모델 학습](how-to-set-up-training-targets.md)
* [모델 배포](how-to-deploy-and-where.md)
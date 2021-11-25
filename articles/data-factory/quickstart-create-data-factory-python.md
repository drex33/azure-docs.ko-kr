---
title: '빠른 시작: Python을 사용하여 Azure Data Factory 만들기'
description: 데이터 팩터리를 사용하여 Azure Blob 스토리지의 한 위치에서 다른 위치로 데이터를 복사합니다.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.devlang: python
ms.topic: quickstart
ms.date: 05/27/2021
ms.custom: seo-python-october2019, devx-track-python, mode-other
ms.openlocfilehash: f5873d45706490a71a6e26054d45489d3bbfe76e
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050756"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>빠른 시작: Python을 사용하여 데이터 팩터리 및 파이프라인 만들기

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [현재 버전](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 빠른 시작에서는 Python을 사용하여 데이터 팩터리를 만듭니다. 이 데이터 팩터리의 파이프라인은 Azure Blob 스토리지의 한 폴더에서 다른 폴더로 데이터를 복사합니다.

Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위한 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 파이프라인이라는 데이터 기반 워크플로를 만들고 예약할 수 있습니다.

파이프라인은 서로 다른 데이터 저장소에서 데이터를 수집할 수 있습니다. 파이프라인은 Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 컴퓨팅 서비스를 사용하여 데이터를 처리하거나 변환합니다. 파이프라인은 BI(비즈니스 인텔리전스) 애플리케이션용 Azure Synapse Analytics와 같은 데이터 저장소에 출력 데이터를 게시합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.6 이상](https://www.python.org/downloads/).

* [Azure Storage 계정](../storage/common/storage-account-create.md)

* [Azure Storage Explorer](https://storageexplorer.com/)(선택 사항).

* [Azure Active Directory의 애플리케이션](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). 이 링크의 단계에 따라 인증 옵션 2(애플리케이션 비밀)를 사용하여 애플리케이션을 만들고 동일한 문서의 지침에 따라 **기여자** 역할에 애플리케이션을 할당합니다. 이후 단계에서 사용할 문서에 표시된 대로 **애플리케이션(클라이언트) ID, 클라이언트 암호 값 및 테넌트 ID** 값을 기록해 둡니다.

## <a name="create-and-upload-an-input-file"></a>입력 파일 만들기 및 업로드

1. 메모장을 시작합니다. 다음 텍스트를 복사하여 **input.txt** 파일로 디스크에 저장합니다.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  [Azure Storage Explorer](https://storageexplorer.com/)와 같은 도구를 사용하여 **adfv2tutorial** 컨테이너와 컨테이너에 **input** 폴더를 만듭니다. 그런 다음 **input.txt** 파일을 **input** 폴더에 업로드합니다.

## <a name="install-the-python-package"></a>Python 패키지 설치

1. 관리자 권한으로 터미널 또는 명령 프롬프트를 엽니다. 
2. 먼저, Azure 관리 리소스에 대한 Python 패키지를 설치합니다.

    ```python
    pip install azure-mgmt-resource
    ```
3. 데이터 팩터리를 위한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Data Factory용 Python SDK](https://github.com/Azure/azure-sdk-for-python)는 Python 2.7 및 3.6 이상을 지원합니다.

4. Azure ID 인증을 위한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > "azure-identity" 패키지는 일부 공통 종속성에서 "azure-cli"와 충돌할 수 있습니다. 인증 문제가 발생하면 "azure-cli" 및 해당 종속성을 제거하거나 "azure cli" 패키지를 설치하지 않고 정리된 머신을 사용하여 작동하도록 합니다.
    > 소버린 클라우드의 경우 적절한 클라우드 관련 상수를 사용해야 합니다.  [Python 다중 클라우드용 Azure 라이브러리를 사용하여 모든 지역에 연결 | 소버린 클라우드에서 Python을 사용하여 연결하는 지침에 대한 Microsoft Docs](/azure/developer/python/azure-sdk-sovereign-domain)를 참조하세요.
    
    
## <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

  
1. **datafactory.py** 라는 파일을 만듭니다. 다음 문을 추가하여 네임스페이스에 대한 참조를 추가합니다.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. 정보를 출력하는 다음 함수를 추가합니다.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. DataFactoryManagementClient 클래스의 인스턴스를 만드는 **Main** 메서드에 다음 코드를 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링합니다. **subscription_id** 변수를 Azure 구독의 ID로 설정합니다. 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics** 를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

        
    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<Application (client) ID>', client_secret='<client secret value>', tenant_id='<tenant ID>') 
        
        # Specify following for Soverign Clouds, import right cloud constant and then use it to connect.
        # from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD as CLOUD
        # credentials = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)
        
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

**Main** 메서드에 **데이터 팩터리** 를 만드는 다음 코드를 추가합니다. 리소스 그룹이 이미 있는 경우 첫 번째 `create_or_update` 문을 주석으로 처리합니다.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

**Main** 메서드에 **Azure Storage 연결된 서비스** 를 만드는 다음 코드를 추가합니다.

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 복사 원본 및 싱크 저장소 모두에 대해 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다. `<storageaccountname>` 및 `<storageaccountkey>`를 Azure Storage 계정 이름 및 키로 바꿉니다.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>데이터 세트 만들기

이 섹션에서는 원본과 싱크 각각에 대해 하나씩, 두 개의 데이터 세트를 만듭니다.

### <a name="create-a-dataset-for-source-azure-blob"></a>원본 Azure Blob에 대한 데이터 세트 만들기

Azure Blob 데이터 세트를 만드는 Main 메서드에 다음 코드를 추가합니다. Azure Blob 데이터 세트의 속성에 대한 자세한 내용은 [Azure Blob 커넥터](connector-azure-blob-storage.md#dataset-properties) 문서를 참조하세요.

Azure Blob의 원본 데이터를 나타내는 데이터 세트를 정의합니다. 이 Blob 데이터 세트는 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>싱크 Azure Blob에 대한 데이터 세트 만들기

Azure Blob 데이터 세트를 만드는 Main 메서드에 다음 코드를 추가합니다. Azure Blob 데이터 세트의 속성에 대한 자세한 내용은 [Azure Blob 커넥터](connector-azure-blob-storage.md#dataset-properties) 문서를 참조하세요.

Azure Blob의 원본 데이터를 나타내는 데이터 세트를 정의합니다. 이 Blob 데이터 세트는 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```


## <a name="create-a-pipeline"></a>파이프라인 만들기

**Main** 메서드에 **복사 작업이 있는 파이프라인** 을 만드는 다음 코드를 추가합니다.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    
    #Note1: To pass parameters to the pipeline, add them to the json string params_for_pipeline shown below in the format { “ParameterName1” : “ParameterValue1” } for each of the parameters needed in the pipeline.
    #Note2: To pass parameters to a dataflow, create a pipeline parameter to hold the parameter name/value, and then consume the pipeline parameter in the dataflow parameter in the format @pipeline().parameters.parametername.
    
    p_name = 'copyPipeline'
    params_for_pipeline = {}

    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

**Main** 메서드에 **파이프라인 실행** 을 트리거하는 다음 코드를 추가합니다.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

파이프라인 실행을 모니터링하려면 **Main** 메서드에 다음 코드를 추가합니다.

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

이제 프로그램이 실행될 때 **main** 메서드를 호출하기 위해 다음 문을 추가합니다.

```python
# Start the main method
main()
```

## <a name="full-script"></a>전체 스크립트

전체 Python 코드는 다음과 같습니다.

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>코드 실행

애플리케이션을 빌드하고 시작한 다음, 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 세트, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 변수에 지정한 대로 Blob이 "inputBlobPath"에서 "outputBlobPath"로 복사되었는지 확인합니다.

샘플 출력은 다음과 같습니다.

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>리소스 정리

데이터 팩터리를 삭제하려면 프로그램에 다음 코드를 추가합니다.

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>다음 단계

이 샘플의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요.

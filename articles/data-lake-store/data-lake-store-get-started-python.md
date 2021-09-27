---
title: Python을 사용하여 Azure Data Lake Storage Gen1 계정 관리
description: Python SDK를 사용하여 Azure Data Lake Storage Gen1 계정 관리 작업을 수행하는 방법을 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.custom: devx-track-python
ms.openlocfilehash: b2941f2b81826577f0dd2e6d9138ed6bc88d3187
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580342"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Python을 사용한 Azure Data Lake Storage Gen1에서의 계정 관리 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure Data Lake Storage Gen1용 Python SDK를 사용하여 Data Lake Storage Gen1 계정 만들기, Data Lake Storage Gen1 계정 나열 등과 같은 기본 계정 관리 작업을 수행하는 방법을 알아봅니다. Python을 사용하여 Data Lake Storage Gen1에서 파일 시스템 작업을 수행하는 방법에 대한 지침은 [Python을 사용한 Data Lake Storage Gen1의 파일 시스템 작업](data-lake-store-data-operations-python.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* **Python**. Python을 [여기](https://www.python.org/downloads/)에서 다운로드할 수 있습니다. 이 문서에서는 Python 3.6.2를 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure 리소스 그룹** 지침에 대해서는 [Azure 리소스 그룹 만들기](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.

## <a name="install-the-modules"></a>모듈 설치

Python을 통해 Data Lake Storage Gen1을 사용하려면 세 가지 모듈을 설치해야 합니다.

* Active Directory 등 Azure 모듈을 포함하는 `azure-mgmt-resource` 모듈.
* Azure Data Lake Storage Gen1 계정 관리 작업을 포함하는 `azure-mgmt-datalake-store` 모듈. 이 모듈에 대한 자세한 내용은 [Azure Data Lake Storage Gen1 관리 모듈 참조](/python/api/azure-mgmt-datalake-store/)를 참조하세요.
* Azure Data Lake Storage Gen1 파일 시스템 작업을 포함하는 `azure-datalake-store` 모듈. 이 모듈에 대한 자세한 내용은 [azure-datalake-store 파일 시스템 모듈 참조](/python/api/azure-datalake-store/azure.datalake.store.core/)를 참조하세요.

다음 명령을 사용하여 모듈을 설치합니다.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 원하는 IDE에서 **mysample.py** 와 같이 새 Python 애플리케이션을 만듭니다.

2. 다음 코드 조각을 추가하여 필요한 모듈 가져오기

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py의 변경 내용을 저장합니다.

## <a name="authentication"></a>인증

이 섹션에서는 Azure AD로 인증하는 다양한 방법에 대해 설명합니다. 제공되는 옵션은 다음과 같습니다.

* 애플리케이션에 대한 최종 사용자 인증의 경우 [Python을 사용한 Data Lake Storage Gen1의 최종 사용자 인증](data-lake-store-end-user-authenticate-python.md)을 참조하세요.
* 애플리케이션에 대한 서비스 간 인증의 경우 [Python을 사용한 Data Lake Storage Gen1의 서비스 간 인증](data-lake-store-service-to-service-authenticate-python.md)을 참조하세요.

## <a name="create-client-and-data-lake-storage-gen1-account"></a>클라이언트 및 Data Lake Storage Gen1 계정 만들기

다음 코드 조각은 Data Lake Storage Gen1 계정 클라이언트를 먼저 만듭니다. 클라이언트 개체를 사용하여 Data Lake Storage Gen1 계정을 만듭니다. 마지막으로 코드 조각은 파일 시스템 클라이언트 개체를 만듭니다.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Data Lake Storage Gen1 계정 나열

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정 삭제

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>다음 단계
* [을 사용한 Data Lake Storage Gen1에서의 파일 시스템 작업](data-lake-store-data-operations-python.md)

## <a name="see-also"></a>참고 항목

* [azure-datalake-store Python(파일 시스템) 참조](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Azure Data Lake Storage Gen1과 호환되는 오픈 소스 빅 데이터 애플리케이션](data-lake-store-compatible-oss-other-applications.md)
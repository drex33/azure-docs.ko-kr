---
title: '빠른 시작: Python을 사용하여 Purview 계정 만들기'
description: Python을 사용하여 Azure Purview 계정을 만듭니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.custom: mode-other
ms.openlocfilehash: 65de2be754172cebbabc38709295cc51cd3caf58
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041230"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>빠른 시작: Python을 사용하여 Purview 계정 만들기

이 빠른 시작에서는 Python을 사용하여 Purview 계정을 프로그래밍 방식으로 만듭니다. [Purview에 대한 Python 참조](/python/api/azure-mgmt-purview/)를 사용할 수 있지만, 이 문서에서는 Python을 사용하여 계정을 만드는 데 필요한 모든 단계에 대해 설명합니다.

Azure Purview는 데이터 환경을 관리하고 제어하는 데 도움이 되는 데이터 거버넌스 서비스입니다. 온-프레미스, 다중 클라우드 및 SaaS(Software as a Service) 원본에서 데이터에 연결하면 Purview에서 최신 정보 맵을 만듭니다. 중요한 데이터를 식별 및 분류하고 엔드투엔드 계보를 제공합니다. 데이터 소비자는 조직 전체에서 데이터를 검색할 수 있으며, 데이터 관리자는 데이터의 올바른 사용을 감사, 보호 및 보장할 수 있습니다.

Purview에 대한 자세한 내용은 [개요 페이지를 참조](overview.md)하세요. Purview를 조직 전체에 배포하는 방법에 대한 자세한 내용은 [배포 모범 사례를 참조](deployment-best-practices.md)하세요.

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="install-the-python-package"></a>Python 패키지 설치

1. 관리자 권한으로 터미널 또는 명령 프롬프트를 엽니다.
2. 먼저, Azure 관리 리소스에 대한 Python 패키지를 설치합니다.

    ```python
    pip install azure-mgmt-resource
    ```

3. Purview에 대한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```python
    pip install azure-mgmt-purview
    ```

    [Purview용 Python SDK](https://github.com/Azure/azure-sdk-for-python)는 Python 2.7, 3.3, 3.4, 3.5, 3.6 및 3.7을 지원합니다.

4. Azure ID 인증을 위한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```python
    pip install azure-identity
    ```

    > [!NOTE]
    > "azure-identity" 패키지는 일부 공통 종속성에서 "azure-cli"와 충돌할 수 있습니다. 인증 문제가 발생하면 "azure-cli" 및 해당 종속성을 제거하거나 "azure cli" 패키지를 설치하지 않고 정리된 컴퓨터를 사용합니다.

## <a name="create-a-purview-client"></a>purview 클라이언트 만들기

1. **purview.py** 라는 파일을 만듭니다. 다음 문을 추가하여 네임스페이스에 대한 참조를 추가합니다.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. **Main** 메서드에 PurviewManagementClient 클래스의 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 purview 계정을 만들고, purview 계정을 삭제하고, 이름 가용성 및 다른 리소스 공급자 작업을 확인할 수 있습니다.

    ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>purview 계정 만들기

1. **Main** 메서드에 **purview 계정** 을 만드는 다음 코드를 추가합니다. 리소스 그룹이 이미 있는 경우 첫 번째 `create_or_update` 문을 주석으로 처리합니다.

   ```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
      ```

2. 이제 프로그램이 실행될 때 **main** 메서드를 호출하기 위해 다음 문을 추가합니다.

   ```python
   # Start the main method
   main()
   ```

## <a name="full-script"></a>전체 스크립트

전체 Python 코드는 다음과 같습니다.

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>코드 실행

애플리케이션을 빌드하고 시작합니다. 콘솔에서 Purview 계정 만들기에 대한 진행 상황을 출력합니다. 완료될 때까지 기다립니다.
샘플 출력은 다음과 같습니다.

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>출력 확인

Azure Portal에서 **Purview 계정** 페이지로 이동하고 위의 코드를 사용하여 만든 계정을 확인합니다.

## <a name="delete-purview-account"></a>Purview 계정 삭제

purview 계정을 삭제하려면 다음 코드를 프로그램에 추가하고 실행합니다.

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>다음 단계

이 자습서의 코드는 purview 계정을 만들고 purview 계정을 삭제합니다. 이제 python SDK를 다운로드하고 Purview 계정에 대해 수행할 수 있는 다른 리소스 공급자 작업에 대해 알아볼 수 있습니다.

Purview Studio를 탐색하고, 컬렉션을 만들고, Purview에 대해 액세스 권한을 부여하는 방법을 알아보려면 다음 문서를 참조하세요.

* [Purview Studio를 사용하는 방법](use-purview-studio.md)
* [컬렉션 만들기](quickstart-create-collection.md)
* [Azure Purview 계정에 사용자 추가](catalog-permissions.md)

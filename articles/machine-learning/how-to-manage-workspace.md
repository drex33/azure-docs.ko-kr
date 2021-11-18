---
title: 포털 또는 Python SDK에서 작업 영역 관리
titleSuffix: Azure Machine Learning
description: Azure Portal에서 또는 Python용 SDK를 사용하여 Azure Machine Learning 작업 영역을 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 04/22/2021
ms.topic: how-to
ms.custom: fasttrack-edit, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 5a5ef573b4df91e191dac2fa24a09c65aace5cea
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723611"
---
# <a name="manage-azure-machine-learning-workspaces-in-the-portal-or-with-the-python-sdk"></a>Azure Portal에서 또는 Python SDK를 사용하여 Azure Machine Learning 작업 영역 관리

이 문서에서는 Azure Portal이나 [Python용 SDK](/python/api/overview/azure/ml/)를 사용하여 [Azure Machine Learning](overview-what-is-azure-machine-learning.md)에 대한 [**Azure Machine Learning 작업 영역**](concept-workspace.md)을 만들고, 확인하고, 삭제합니다.

변경이 필요하거나 자동화 요구 사항이 증가하면 [CLI를 사용](reference-azure-machine-learning-cli.md)하거나 [VS Code 확장을 통해](how-to-setup-vs-code.md) 작업 영역을 관리할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* Python SDK를 사용하는 경우 [SDK를 설치합니다](/python/api/overview/azure/ml/install).

## <a name="limitations"></a>제한 사항

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

기본적으로 작업 영역을 만들면 ACR(Azure Container Registry)도 만들어집니다.  ACR은 현재 리소스 그룹 이름에서 유니코드 문자를 지원하지 않으므로 이 문자를 포함하지 않는 리소스 그룹을 사용합니다.

## <a name="create-a-workspace"></a>작업 영역 만들기

# <a name="python"></a>[Python](#tab/python)

* **기본 사양입니다.** 기본적으로 종속 리소스 및 리소스 그룹은 자동으로 만들어집니다. 이 코드는 `eastus2`에서 이름이 `myworkspace`인 작업 영역과 이름이 `myresourcegroup`인 리소스 그룹을 만듭니다.
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    작업 영역에 사용하려는 기존 Azure 리소스 그룹이 있는 경우 `create_resource_group`을 False로 설정합니다.

* <a name="create-multi-tenant"></a>**다중 테넌트.**  계정이 여러 개인 경우 사용하려는 Azure Active Directory의 테넌트 ID를 추가합니다.  [Azure Portal](https://portal.azure.com)의 **Azure Active Directory, 외부 ID** 에서 테넌트 ID를 찾습니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[소버린 클라우드](reference-machine-learning-cloud-parity.md)** . 소버린 클라우드에서 작업하는 경우 Azure 인증을 위해 추가 코드가 필요합니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **기존 Azure 리소스 사용**.  Azure 리소스 ID 형식으로 기존 Azure 리소스를 사용하는 작업 영역을 만들 수도 있습니다. Azure Portal 또는 SDK를 사용하여 특정 Azure 리소스 ID를 찾습니다. 이 예제에서는 리소스 그룹, 스토리지 계정, 키 자격 증명 모음, App Insights 및 컨테이너 레지스트리가 이미 있다고 가정합니다.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

자세한 내용은 [작업 영역 SDK 참조](/python/api/azureml-core/azureml.core.workspace.workspace)를 확인하세요.

구독 액세스에 문제가 있는 경우 [Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정](how-to-setup-authentication.md)과 [Azure Machine Learning의 인증](https://aka.ms/aml-notebook-auth) Notebook을 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

1. Azure Portal의 왼쪽 위 모서리에서 **+ 리소스 만들기** 를 선택합니다.

      ![새 리소스 만들기](./media/how-to-manage-workspace/create-workspace.gif)

1. 검색 창을 사용하여 **Machine Learning** 을 찾습니다.

1. **Machine Learning** 을 선택합니다.

1. **Machine Learning** 창에서 **만들기** 를 선택하여 시작합니다.

1. 새 작업 영역을 구성하려면 다음 정보를 제공하세요.

   필드|설명 
   ---|---
   작업 영역 이름 |작업 영역을 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **docs-ws** 를 사용합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요. 작업 영역 이름은 대/소문자를 구분하지 않습니다.
   Subscription |사용할 Azure 구독을 선택합니다.
   Resource group | 구독의 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다. 이 예에서는 **docs-aml** 을 사용합니다. 기존 리소스 그룹을 사용하려면 *기여자* 또는 *소유자* 역할이 필요합니다.  액세스에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 액세스 관리](how-to-assign-roles.md)를 참조하세요.
   지역 | 사용자 및 데이터 리소스와 가장 가까운 Azure 위치를 선택하여 작업 영역을 만듭니다.
   | 스토리지 계정 | 작업 영역의 기본 스토리지 계정입니다. 기본적으로 새로 만듭니다. |
   | Key Vault | 작업 영역에서 사용하는 Azure Key Vault입니다. 기본적으로 새로 만듭니다. |
   | Application Insights | 작업 영역에 대한 Application Insights 인스턴스입니다. 기본적으로 새로 만듭니다. |
   | Container Registry | 작업 영역에 대한 Azure Container Registry입니다. 기본적으로 처음에 작업 영역을 만들 때 새로 생성되지는 _않습니다_. 대신, 학습 또는 배포 중에 Docker 이미지를 만들 때 필요하면 만들어집니다. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="작업 영역을 구성합니다.":::

1. 작업 영역 구성을 마치면 **검토 + 만들기** 를 선택합니다. 필요에 따라 [네트워킹](#networking) 및 [고급](#advanced) 섹션을 사용하여 작업 영역에 대한 추가 설정을 구성합니다.

1. 설정을 검토하고 다른 변경 또는 수정을 합니다. 설정에 만족하면 **만들기** 를 선택합니다.

   > [!Warning] 
   > 클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다.

   프로세스가 완료되면 배포 성공 메시지가 표시됩니다. 
 
 1. 새 작업 영역을 보려면 **리소스로 이동** 을 선택합니다.
 
---



### <a name="networking"></a>네트워킹  

> [!IMPORTANT]  
> 작업 영역에서 프라이빗 엔드포인트 및 가상 네트워크를 사용하는 방법에 대한 자세한 내용은 [가상 네트워크 격리 및 프라이버시](how-to-network-security-overview.md)를 참조하세요.


# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK는 프라이빗 엔드포인트를 사용하는 작업 영역을 만들 때 [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-)와 함께 사용할 수 있는 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) 클래스를 제공합니다. 이 클래스를 사용하려면 기존 가상 네트워크가 필요합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 기본 네트워크 구성은 공용 인터넷에서 액세스할 수 있는 __퍼블릭 엔드포인트__ 를 사용하는 것입니다. 작업 영역에 대한 액세스를 만든 Azure Virtual Network 제한하려면 대신 __프라이빗 엔드포인트를__ __연결 방법으로__ 선택한 __다음, + 추가를__ 사용하여 엔드포인트를 구성할 수 있습니다. 

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="프라이빗 엔드포인트 선택":::  

1. __프라이빗 엔드포인트 만들기__ 양식에서 사용할 위치, 이름 및 가상 네트워크를 설정합니다. 프라이빗 DNS 영역에서 엔드포인트를 사용하려면 __프라이빗 DNS 영역과 통합__ 을 선택하고 __프라이빗 DNS 영역__ 필드를 사용하여 영역을 선택합니다. __확인__ 을 선택하여 엔드포인트를 만듭니다.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="프라이빗 엔드포인트 만들기":::   

1. 네트워킹 구성을 마쳤으면 __검토 + 만들기__ 를 선택하거나 선택적 __고급__ 구성을 진행할 수 있습니다.

---

### <a name="vulnerability-scanning"></a>취약성 검색

Microsoft Defender for Cloud는 하이브리드 클라우드 워크로드 전반에 걸쳐 통합 보안 관리 및 고급 위협 보호를 제공합니다. Microsoft Defender for Cloud가 리소스를 검사하고 권장 사항을 따르도록 허용해야 합니다. 자세한 내용은 [Defender for Cloud의 Azure Container Registry 이미지 검색](../security-center/defender-for-container-registries-introduction.md) 및 Defender [for Cloud와 Azure Kubernetes Services 통합을 참조하세요.](../security-center/defender-for-kubernetes-introduction.md)

### <a name="advanced"></a>고급

기본적으로 작업 영역에 대한 메타데이터는 Microsoft에서 유지 관리하는 Azure Cosmos DB 인스턴스에 저장됩니다. 이 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다.

Microsoft에서 작업 영역에 대해 수집하는 데이터를 제한하려면 포털에서 __높은 비즈니스 영향 작업 영역__ 을 선택하거나 Python에서 `hbi_workspace=true `를 설정합니다. 이 설정에 대한 자세한 내용은 [저장 데이터 암호화](concept-data-encryption.md#encryption-at-rest)를 참조하세요.

> [!IMPORTANT]  
> 높은 비즈니스 영향을 선택하는 작업은 작업 영역을 만들 때만 수행할 수 있습니다. 작업 영역을 만든 후에는 이 설정을 변경할 수 없습니다.   

#### <a name="use-your-own-key"></a>사용자 고유 키 사용

데이터 암호화를 위해 사용자 고유의 키를 제공할 수 있습니다. 이렇게 하면 Azure 구독에 메타데이터를 저장하는 Azure Cosmos DB 인스턴스가 만들어집니다.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

다음 단계를 사용하여 사용자 고유 키를 제공합니다.

> [!IMPORTANT]  
> 이러한 단계를 수행하기 전에 먼저 다음 작업을 수행해야 합니다.   
>
> 1. __Machine Learning 앱__(ID 및 액세스 관리에서)에 구독에 대한 기여자 권한을 부여합니다.  
> 1. [고객 관리형 키 구성](../cosmos-db/how-to-setup-cmk.md)의 단계에 따라 다음을 수행합니다.
>     * Azure Cosmos DB 공급자 등록
>     * Azure Key Vault 만들기 및 구성
>     * 키 생성
>   
>     Azure Cosmos DB 인스턴스는 수동으로 만들 필요가 없으며 작업 영역을 만드는 동안 생성됩니다. 이 Azure Cosmos DB 인스턴스는 `<your-workspace-resource-name>_<GUID>` 패턴을 기반으로 하는 이름을 사용하여 별도의 리소스 그룹에 만들어집니다.   
>   
> 작업 영역을 만든 후에는 이 설정을 변경할 수 없습니다. 작업 영역에서 사용하는 Azure Cosmos DB를 삭제할 경우 해당 항목을 사용하는 작업 영역도 삭제해야 합니다.

# <a name="python"></a>[Python](#tab/python)

`cmk_keyvault` 및 `resource_cmk_uri`를 사용하여 고객 관리형 키를 지정합니다.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[포털](#tab/azure-portal)

1. __고객 관리형 키__ 를 선택한 다음, __키를 선택하려면 클릭__ 을 선택합니다.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="고객 관리형 키":::

1. __Azure Key Vault에서 키 선택__ 양식에서 기존 Azure Key Vault, 그 안에 포함된 키 및 키 버전을 선택합니다. 이 키는 Azure Cosmos DB에 저장된 데이터를 암호화하는 데 사용됩니다. 마지막으로 __선택__ 단추를 사용하여 이 키를 사용합니다.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="키 선택":::

---

### <a name="download-a-configuration-file"></a>구성 파일 다운로드

[컴퓨팅 인스턴스](quickstart-create-resources.md)를 만들려는 경우 이 단계를 건너뜁니다.  컴퓨팅 인스턴스가 이 파일의 사본을 이미 만들었습니다.

# <a name="python"></a>[Python](#tab/python)

이 작업 영역(`ws`)을 참조하는 로컬 환경에서 코드를 사용하려면 다음 구성 파일을 작성합니다.

```python
ws.write_config()
```

# <a name="portal"></a>[포털](#tab/azure-portal)

로컬 환경에서 이 작업 영역을 참조하는 코드를 사용하려면 작업 영역의 **개요** 섹션에서 **config.json 다운로드** 를 선택합니다.  

   ![config.json 다운로드](./media/how-to-manage-workspace/configure.png)

---

Python 스크립트 또는 Jupyter Notebook을 사용하여 파일을 디렉터리 구조에 배치합니다. 동일한 디렉터리, *.azureml* 이라는 하위 디렉터리 또는 부모 디렉터리에 있을 수 있습니다. 컴퓨팅 인스턴스를 만들 때 이 파일이 VM의 정확한 디렉터리에 추가됩니다.

## <a name="connect-to-a-workspace"></a>작업 영역에 연결

Python 코드에서 작업 영역에 연결할 작업 영역 개체를 만듭니다.  이 코드는 구성 파일의 콘텐츠를 읽어 작업 영역을 찾습니다.  아직 인증되지 않은 경우 로그인하라는 메시지가 표시됩니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**다중 테넌트.**  계정이 여러 개인 경우 사용하려는 Azure Active Directory의 테넌트 ID를 추가합니다.  [Azure Portal](https://portal.azure.com)의 **Azure Active Directory, 외부 ID** 에서 테넌트 ID를 찾습니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[소버린 클라우드](reference-machine-learning-cloud-parity.md)** . 소버린 클라우드에서 작업하는 경우 Azure 인증을 위해 추가 코드가 필요합니다.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
구독 액세스에 문제가 있는 경우 [Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정](how-to-setup-authentication.md)과 [Azure Machine Learning의 인증](https://aka.ms/aml-notebook-auth) Notebook을 참조하세요.

## <a name="find-a-workspace"></a><a name="view"></a>작업 영역 찾기

사용할 수 있는 모든 작업 영역 목록을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

[Azure Portal의 구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 페이지에서 구독을 찾습니다. ID를 복사하고 아래 코드에 사용하여 해당 구독에 사용할 수 있는 모든 작업 영역을 확인합니다.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

Workspace.list(..) 메서드는 전체 작업 영역 개체를 반환하지 않습니다. 구독의 기존 작업 영역에 대한 기본 정보만 포함합니다. 특정 작업 영역에 대한 전체 개체를 얻으려면 Workspace.get(..)을 사용합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 위쪽 검색 필드에 **Machine Learning** 을 입력합니다.  

1. **Machine Learning** 을 선택합니다.

   ![Azure Machine Learning 작업 영역 검색](./media/how-to-manage-workspace/find-workspaces.png)

1. 검색된 작업 영역 목록을 살펴봅니다. 구독, 리소스 그룹 및 위치를 기준으로 필터링 할 수 있습니다.  

1. 작업 영역을 선택하여 해당 속성을 표시합니다.

---


## <a name="delete-a-workspace"></a>작업 영역 삭제

작업 영역이 더 이상 필요하지 않으면 삭제합니다.  

[!INCLUDE [machine-learning-delete-workspace](../../includes/machine-learning-delete-workspace.md)]

작업 영역을 실수로 삭제한 경우에도 Notebook을 검색할 수 있습니다. 자세한 내용은 [비즈니스 연속성 및 재해 복구를 위한 장애 조치(failover)를](./how-to-high-availability-machine-learning.md#workspace-deletion)참조하세요.

# <a name="python"></a>[Python](#tab/python)

작업 영역 `ws` 삭제:

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

기본 작업은 작업 영역, 즉 컨테이너 레지스트리, 스토리지 계정, 키 자격 증명 모음 및 애플리케이션 인사이트와 연결된 리소스를 삭제하지 않는 것입니다.  이러한 리소스도 삭제하려면 `delete_dependent_resources`를 True로 설정합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에서 삭제하려는 작업 영역 맨 위에서 **삭제** 를 선택합니다.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="작업 영역 삭제":::

---

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>문제 해결

* **Azure Machine Learning 스튜디오에서 지원되는 브라우저**: 운영 체제와 호환되는 최신 브라우저를 사용하는 것이 좋습니다. 다음과 같은 브라우저가 지원됩니다.
  * Microsoft Edge(새로운 Microsoft Edge, 최신 버전. Microsoft Edge 레거시 아님)
  * Safari(최신 버전, Mac만 해당)
  * Chrome(최신 버전)
  * Firefox(최신 버전)

* **Azure 포털**: 
  * SDK 또는 Azure Portal의 공유 링크를 통해 직접 작업 영역으로 이동할 경우 확장에서 구독 정보가 포함된 표준 **개요** 페이지를 볼 수 없습니다. 이 시나리오에서는 다른 작업 영역으로 전환할 수도 없습니다. 다른 작업 영역을 보려면 [Azure Machine Learning 스튜디오](https://ml.azure.com)로 직접 이동하고 작업 영역 이름을 검색합니다.
  * 모든 자산(데이터 세트, 실험, 컴퓨팅 등)은 [Azure Machine Learning 스튜디오](https://ml.azure.com)에서만 사용할 수 있습니다. Azure Portal에서는 사용할 수 *없습니다*.

### <a name="workspace-diagnostics"></a>작업 영역 진단

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유하는 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry 삭제

Azure Machine Learning 작업 영역에서는 일부 작업에 ACR(Azure Container Registry)을 사용합니다. 먼저 필요한 경우 ACR 인스턴스를 자동으로 만듭니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>예제

작업 영역 만들기 예제:
* Azure Portal을 사용하여 [작업 영역 및 컴퓨팅 인스턴스 만들기](quickstart-create-resources.md)

## <a name="next-steps"></a>다음 단계

작업 영역이 있으면 [모델을 학습시키고 배포](tutorial-train-models-with-aml.md)하는 방법을 알아봅니다.

조직의 요구 사항에 맞게 작업 영역을 계획하는 방법에 대한 자세한 내용은 [Azure Machine Learning 구성 및 설정](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization)을 참조하세요.
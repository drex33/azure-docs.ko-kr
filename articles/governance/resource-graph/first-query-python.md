---
title: '빠른 시작: 첫 번째 Python 쿼리'
description: 이 빠른 시작에서는 단계에 따라 Python용 Resource Graph 라이브러리를 사용하도록 설정하고 첫 번째 쿼리를 실행합니다.
ms.date: 10/01/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 0dfe97eda8244eb0926709d3ed39a514fbb984e9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355468"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>빠른 시작: Python을 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 Python에 필요한 라이브러리가 설치되었는지 확인하는 것입니다. 이 빠른 시작에서는 Python 설치에 라이브러리를 추가하는 과정을 안내합니다.

이 과정이 끝나면 Python 설치에 라이브러리가 추가되고 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Resource Graph 라이브러리 추가

Python을 사용하도록 설정하여 Azure Resource Graph를 쿼리하려면 해당 라이브러리를 추가해야 합니다. 이 라이브러리는 [Windows 10의 bash](/windows/wsl/install-win10) 또는 로컬로 설치된 경우를 포함하여 Python을 사용할 수 있을 때마다 작동합니다.

1. 최신 Python이 설치되었는지 확인합니다(**3.8** 이상). 아직 설치되지 않았으면 [Python.org](https://www.python.org/downloads/)에서 다운로드합니다.

1. 최신 Azure CLI가 설치되었는지 확인합니다(**2.5.1** 이상). 아직 설치되지 않았으면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

   > [!NOTE]
   > 다음 예시에서 **CLI 기반 인증** 을 사용하도록 Python을 설정하려면 Azure CLI가 필요합니다. 다른 옵션들에 대한 자세한 내용은 [Python용 Azure 관리 라이브러리를 사용하여 인증](/azure/developer/python/azure-sdk-authenticate)을 참조하세요.

1. Azure CLI를 통해 인증을 수행합니다.

   ```azurecli
   az login
   ```

1. 선택한 Python 환경에서 Azure Resource Graph에 필요한 라이브러리를 설치합니다.

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core

   # Add the Azure identity library for Python
   pip install azure.identity
   ```

   > [!NOTE]
   > 모든 사용자용으로 Python이 설치된 경우에는 이러한 명령은 관리자 권한 콘솔에서 실행해야 합니다.

1. 라이브러리가 설치되어 있는지 확인 합니다. `azure-mgmt-resourcegraph`는 **2.0.0** 이상이고, `azure-mgmt-resource`는 **9.0.0** 이상이고, `azure-cli-core`는 **2.5.0** 이상이어야 합니다.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core azure.identity
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

원하는 환경에 Python 라이브러리가 추가되었으므로, 이제 간단한 구독 기반 Resource Graph 쿼리를 시도해 볼 수 있습니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식** 과 함께 처음 5개 Azure 리소스를 반환합니다. [관리 그룹](../management-groups/overview.md)을 기준으로 쿼리하려면 `management_groups`매개 변수를 `QueryRequest`와 함께 사용합니다.

1. 설치된 라이브러리 및 `resources` 메서드를 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다.

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg

   # Import specific methods and models from other libraries
   from azure.mgmt.resource import SubscriptionClient
   from azure.identity import AzureCliCredential

   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       credential = AzureCliCredential()
       subsClient = SubscriptionClient(credential)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))

       # Create Azure Resource Graph client and set options
       argClient = arg.ResourceGraphClient(credential)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")

       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)

       # Run query
       argResults = argClient.resources(argQuery)

       # Show Python object
       print(argResults)

   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다.

1. `getresources` 호출을 업데이트하고 **Name** 속성을 `order by`하도록 쿼리를 변경합니다.

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. `getresources` 호출을 업데이트하고 **Name** 속성에 먼저 `order by`를 수행하고 상위 5개 결과로 `limit`하도록 쿼리를 변경합니다.

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Python 환경에서 설치된 라이브러리를 제거하려면 다음 명령을 사용할 수 있습니다.

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core azure.identity
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph 라이브러리를 Python 환경에 추가하고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)

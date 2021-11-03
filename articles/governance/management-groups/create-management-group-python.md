---
title: '빠른 시작: Python을 사용하여 관리 그룹 만들기'
description: 이 빠른 시작에서는 Python을 사용하여 리소스를 리소스 계층 구조로 구성하는 관리 그룹을 만듭니다.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-python, mode-api
ms.openlocfilehash: 571e59e058b5cade33be8e883f5f1b1bab4169f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028256"
---
# <a name="quickstart-create-a-management-group-with-python"></a>빠른 시작: Python을 사용하여 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 테넌트의 모든 Azure AD 사용자는 [계층 구조 보호](./how-to/protect-resource-hierarchy.md#setting---require-authorization)가 활성화되지 않은 경우 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹 또는 [기본 관리 그룹](./how-to/protect-resource-hierarchy.md#setting---default-management-group)의 자식이 되며 작성자에게 "소유자" 역할 할당이 부여됩니다. 관리 그룹 서비스는 이 기능을 허용하므로 루트 수준에서 역할 할당이 필요하지 않습니다. 루트 관리 그룹이 생성될 때 사용자는 액세스할 수 있는 권한이 없습니다. 관리 그룹 사용을 시작하기 위해 Azure AD 전역 관리자를 찾는 장애물을 방지하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Resource Graph 라이브러리 추가

Python에서 관리 그룹을 관리할 수 있도록 하려면 라이브러리를 추가해야 합니다. 이 라이브러리는 [Windows 10의 bash](/windows/wsl/install-win10) 또는 로컬로 설치된 경우를 포함하여 Python을 사용할 수 있을 때마다 작동합니다.

1. 최신 Python이 설치되었는지 확인합니다(**3.8** 이상). 아직 설치되지 않았으면 [Python.org](https://www.python.org/downloads/)에서 다운로드합니다.

1. 최신 Azure CLI가 설치되었는지 확인합니다(**2.5.1** 이상). 아직 설치되지 않았으면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

   > [!NOTE]
   > 다음 예시에서 **CLI 기반 인증** 을 사용하도록 Python을 설정하려면 Azure CLI가 필요합니다. 다른 옵션들에 대한 자세한 내용은 [Python용 Azure 관리 라이브러리를 사용하여 인증](/azure/developer/python/azure-sdk-authenticate)을 참조하세요.

1. Azure CLI를 통해 인증을 수행합니다.

   ```azurecli
   az login
   ```

1. 선택한 Python 환경에서 관리 그룹에 필요한 라이브러리를 설치합니다.

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > 모든 사용자용으로 Python이 설치된 경우에는 이러한 명령은 관리자 권한 콘솔에서 실행해야 합니다.

1. 라이브러리가 설치되어 있는지 확인 합니다. `azure-mgmt-managementgroups`는 **0.2.0** 이상이고, `azure-mgmt-resource`는 **9.0.0** 이상이고, `azure-cli-core`는 **2.5.0** 이상이어야 합니다.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>관리 그룹 만들기

1. Python 스크립트를 만들고 `mgCreate.py`로 다음 원본을 저장합니다.

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI

   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient

   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))

       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}

       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)

       # Show results
       print(mg)

   createmanagementgroup("MyNewMG")
   ```

1. `az login`을 사용하여 Azure CLI로 인증합니다.

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   py mgCreate.py
   ```

관리 그룹 생성 결과는 콘솔에 `LROPoller` 개체로 출력됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Python 환경에서 설치된 라이브러리를 제거하려면 다음 명령을 사용할 수 있습니다.

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 계층 구조를 구성하기 위한 관리 그룹을 만들었습니다. 관리 그룹은 구독 또는 기타 관리 그룹을 포함할 수 있습니다.

관리 그룹 및 리소스 계층 구조를 관리하는 방법에 대해 자세히 알아보려면 다음을 계속 진행하세요.

> [!div class="nextstepaction"]
> 관리 그룹으로 리소스 관리

---
title: Azure Automation에서 Python 2 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 2 패키지를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 08/13/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2d5cf0a3d577949edd9e8174ef74e5fede96e67e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965692"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation에서 Python 2 패키지 관리

Azure Automation을 사용하면 Linux Hybrid Runbook Worker 및 Azure에서 Python 2 Runbook을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 이 문서에서는 Azure Automation에서 Python 패키지를 관리하고 사용하는 방법을 설명합니다.

## <a name="import-packages"></a>패키지 가져오기

Automation 계정의 **공유 리소스** 아래에서 **Python 2 패키지** 를 선택합니다. **+ Python 2 패키지 추가** 를 클릭합니다.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Python 2 패키지 페이지의 스크린샷은 왼쪽 메뉴에서 Python 2 패키지와 강조 표시된 Python 2 패키지 추가를 표시합니다.":::

Python 2 패키지 추가 페이지에서 업로드할 로컬 패키지를 선택합니다. 패키지는 **.whl** 또는 **.tar.gz** 파일일 수 있습니다. 패키지를 선택한 다음 **확인** 을 클릭하여 업로드합니다.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="스크린샷은 업로드된 tar.gz 파일이 선택된 Python 2 패키지 추가 페이지 추가를 표시합니다.":::

가져온 패키지는 Automation 계정의 Python 2 패키지 페이지에 나열됩니다. 패키지를 제거해야 하는 경우 해당 패키지를 선택하고 **삭제** 를 선택합니다.

:::image type="content" source="media/python-packages/package-list.png" alt-text="스크린샷은 패키지를 가져온 후의 Python 2 패키지 페이지를 표시합니다.":::

## <a name="import-packages-with-dependencies"></a>종속성이 있는 패키지 가져오기

Azure Automation은 가져오기 프로세스 중에 Python 패키지에 대한 종속성을 확인하지 않습니다. 모든 종속성이 있는 패키지를 가져오는 방법은 두 가지입니다. 다음 단계 중 하나만 사용하여 패키지를 Automation 계정으로 가져올 수 있습니다.

### <a name="manually-download"></a>수동 다운로드

[Python2.7](https://www.python.org/downloads/release/latest/python2) 및 [pip](https://pip.pypa.io/en/stable/)가 설치된 Windows 64비트 컴퓨터에서 다음 명령을 실행하여 패키지와 모든 종속성을 다운로드합니다.

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

패키지 다운로드가 완료되면 이를 Automation 계정으로 가져올 수 있습니다.

### <a name="runbook"></a>Runbook

 Runbook을 획득하려면, Azure Automation GitHub 조직에서 사용자의 Automation 계정으로 [Python 2 패키지를 pypi에서 Azure Automation 계정으로 Python 2 패키지를 가져옵니다](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account). 실행 설정이 **Azure** 로 설정되어 있고 매개 변수가 포함된 Runbook을 시작해야 합니다. Automation 계정이 작동하려면 Runbook에 실행 계정이 필요 합니다. 각 매개 변수에 대해 다음 목록과 이미지에 표시된 것처럼 스위치를 사용하여 시작해야 합니다.

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="스크린샷은 오른쪽에 있는 Runbook 시작 창을 사용하여 import_py2package_from_pypi에 대한 개요 페이지를 표시합니다.":::

Runbook을 사용하여 다운로드할 패키지를 지정할 수 있습니다. 예를 들어 `Azure` 매개 변수를 사용하면 모든 Azure 모듈과 모든 종속성(약 105개)이 다운로드됩니다.

Runbook이 완료되면 Automation 계정의 **공유 리소스** 에서 **Python 2 패키지** 를 검사하여 패키지를 올바르게 가져왔는지 확인해야 합니다.

## <a name="use-a-package-in-a-runbook"></a>Runbook에서 패키지 사용

패키지를 가져오면 Runbook에서 이를 사용할 수 있습니다. 다음 예제에서는 [ Azure Automation 유틸리티 패키지](https://github.com/azureautomation/azure_automation_utility)를 사용합니다. 이 패키지가 있으면 Azure Automation에서 Python을 더욱 쉽게 사용할 수 있습니다. 패키지를 사용하려면 GitHub 리포지토리의 지침에 따라 Runbook에 추가합니다. 예를 들어 `from azure_automation_utility import get_automation_runas_credential`을 사용하여 실행 계정을 검색하는 함수를 가져올 수 있습니다.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

> [!NOTE]
> Python `automationassets` 패키지는 pypi.org에서 사용할 수 없으므로 Windows 컴퓨터로 가져올 수 없습니다.

## <a name="develop-and-test-runbooks-offline"></a>오프라인에서 Runbook 개발 및 테스트

오프라인에서 Python 2 Runbook을 개발하고 테스트하려는 경우 GitHub의 [Azure Automation python 에뮬레이트된 자산](https://github.com/azureautomation/python_emulated_assets) 모듈을 사용할 수 있습니다. 이 모듈에서는 자격 증명, 변수, 연결, 인증서 등의 공유 리소스를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Python Runbook을 준비하려면 [Python Runbook 만들기](learn/automation-tutorial-runbook-textual-python2.md)를 참조하세요.

---
title: Azure Automation에서 Python 2 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 2 패키지를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 302ed99a2032200d770f371d24388c92a28a97e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470817"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation에서 Python 2 패키지 관리

이 문서에서는 Azure 샌드박스 환경 및 Hybrid Runbook Worker에서 실행 되는 Azure Automation에서 Python 2 패키지를 가져오고, 관리 하 고, 사용 하는 방법을 설명 합니다. Runbook을 간소화 하는 데 도움이 되도록 Python 패키지를 사용 하 여 필요한 모듈을 가져올 수 있습니다.

Python 3 패키지를 관리 하는 방법에 대 한 자세한 내용은 [python 3 패키지 관리](./python-3-packages.md)를 참조 하세요.

## <a name="import-packages"></a>패키지 가져오기

1. Automation 계정의 **공유 리소스** 아래에서 **Python 패키지** 를 선택합니다. **+ Python 패키지 추가를** 클릭 합니다.

    :::image type="content" source="media/python-packages/add-python-package.png" alt-text="Python 패키지 페이지의 스크린샷 왼쪽 메뉴에 Python 패키지를 표시 하 고 강조 표시 된 Python 패키지를 추가 합니다.":::

2. **Python 패키지 추가** 페이지에서 업로드할 로컬 패키지를 선택 합니다. 패키지는 **.whl** 또는 **.tar.gz** 파일일 수 있습니다. 
3. 이름을 입력 하 고 **런타임 버전** 을 2.x로 선택 합니다. x. x
4. **가져오기** 를 선택합니다.

   :::image type="content" source="media/python-packages/upload-package.png" alt-text="스크린샷 release.tar.gz 파일이 선택 된 Python 패키지 추가 페이지를 표시 합니다.":::

패키지를 가져온 후에는 Automation 계정의 **Python 패키지** 페이지에 나열 됩니다. 패키지를 제거 하려면 패키지를 선택 하 고 **삭제** 를 클릭 합니다.

:::image type="content" source="media/python-packages/package-list.png" alt-text="패키지를 가져온 후의 스크린샷에는 Python 2.7. x 패키지 페이지가 표시 됩니다.":::

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

Runbook이 완료 되 면 Automation 계정의 **공유 리소스** 아래에 있는 **Python 패키지** 를 확인 하 여 패키지를 올바르게 가져왔는지 확인할 수 있습니다.

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

Python Runbook을 준비하려면 [Python Runbook 만들기](./learn/automation-tutorial-runbook-textual-python-3.md)를 참조하세요.
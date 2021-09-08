---
title: Azure Automation에서 Python 3 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 3 패키지(미리 보기)를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 08/13/2021
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: b7393f06df77b0057925e20a7cf055fddb0a7bf2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965293"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Azure Automation에서 Python 3 패키지(미리 보기) 관리

Azure Automation을 사용하면 Azure Sandbox 환경과 Linux Hybrid Runbook Worker에서 Python 3 Runbook(미리 보기)을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 단일 패키지를 가져오려면 [패키지 가져오기](#import-a-package)를 참조하세요. 여러 패키지가 포함된 패키지를 가져오려면 [종속성이 있는 패키지 가져오기](#import-a-package-with-dependencies)를 참조하세요. 이 문서에서는 Azure Automation에서 Python 3 패키지(미리 보기)를 관리하고 사용하는 방법을 설명합니다.

## <a name="packages-as-source-files"></a>원본 파일 형태의 패키지

Azure Automation은 Python 코드만 있고 다른 언어 확장이나 다른 언어로 작성된 코드는 포함하지 않은 Python 패키지만 지원합니다. 그러나 Azure 샌드박스 환경에 C/C++ 바이너리에 필요한 컴파일러가 없을 수 있으므로 [whl 파일](https://pythonwheels.com/)을 대신 사용하는 것이 좋습니다. [PyPI(Python Package Index)](https://pypi.org/)는 Python 프로그래밍 언어용 소프트웨어의 리포지토리입니다. PyPI에서 Automation 계정으로 가져올 Python 3 패키지를 선택할 때는 다음 파일 이름 부분에 유의하세요.

| 파일 이름 부분 | 설명 |
|---|---|
|cp38|Automation은 클라우드 작업에 **Python 3.8.x** 를 지원합니다.|
|amd64|Azure 샌드박스 프로세스는 **Windows 64비트** 아키텍처입니다.|

예를 들어 pandas를 가져오려면 이름이 `pandas-1.2.3-cp38-win_amd64.whl`과 유사한 whl 파일을 선택할 수 있습니다.

PyPI에서 사용할 수 있는 일부 Python 패키지는 whl 파일을 제공하지 않습니다. 이 경우 소스(.zip 또는 .tar.gz 파일)를 다운로드하고 `pip`를 사용하여 whl 파일을 생성합니다. 예를 들어 Python 3.8. x 및 whl 패키지가 설치된 64비트 컴퓨터를 사용하여 다음 단계를 수행합니다.

1. 소스 파일 `pandas-1.2.4.tar.gz`를 다운로드합니다.
1. `pip wheel --no-deps pandas-1.2.4.tar.gz` 명령으로 pip를 실행하여 whl 파일을 가져옵니다.

## <a name="import-a-package"></a>패키지 가져오기

Automation 계정의 **공유 리소스** 아래에서 **Python 패키지** 를 선택합니다. 그런 다음, **+ Python 패키지 추가** 를 선택합니다.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Python 3 패키지 페이지의 스크린샷은 왼쪽 메뉴에서 Python 3 패키지와 강조 표시된 Python 2 패키지 추가를 표시합니다.":::

**Python 패키지 추가** 페이지에서 **버전** 에 **python 3** 를 선택하고 업로드할 로컬 패키지를 선택합니다. 패키지는 **.whl** 또는 **.tar.gz** 파일일 수 있습니다. 패키지를 선택한 다음, **확인** 을 클릭하여 업로드합니다.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="스크린샷은 업로드된 tar.gz 파일이 선택된 Python 3 패키지 추가 페이지 추가를 표시합니다.":::

패키지를 가져오면 해당 패키지가 해당 Automation 계정에서 Python 패키지 페이지의 **Python 3 패키지(미리 보기)** 탭 아래 나열됩니다. 패키지를 제거하려면 선택하고 **삭제** 를 선택합니다.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="스크린샷은 패키지를 가져온 후의 Python 3 패키지 페이지를 표시합니다.":::

### <a name="import-a-package-with-dependencies"></a>종속성이 있는 패키지 가져오기

다음 Python 스크립트를 Python 3 Runbook에 가져온 다음, 실행하여 Python 3 패키지와 종속성을 가져올 수 있습니다.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Runbook에 스크립트 가져오기
Runbook 가져오기에 대한 정보는 [Azure Portal에서 Runbook 가져오기](manage-runbooks.md#import-a-runbook-from-the-azure-portal)를 참조하세요. 가져오기를 실행하기 전에, GitHub의 파일을 포털이 액세스할 수 있는 스토리지에 복사합니다.

**Runbook 가져오기** 페이지는 기본적으로 스크립트 이름과 일치하도록 Runbook 이름을 설정합니다. 필드에 대한 액세스 권한이 있는 경우 이름을 변경할 수 있습니다. **Runbook 유형** 은 기본적으로 **Python 2** 일 수 있습니다. 이 경우에는 **Python 3** 로 변경해야 합니다.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="스크린샷은 Python 3 Runbook 가져오기 페이지를 표시합니다.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Runbook을 실행하여 패키지 및 종속성 가져오기

Runbook을 만들고 게시한 후에는 실행하여 패키지를 가져옵니다. Runbook 실행에 대한 세부 정보는 [Azure Automation에서 Runbook 시작](start-runbooks.md)을 참조하세요.

스크립트(`import_py3package_from_pypi.py`)에는 다음 매개 변수가 필요합니다.

| 매개 변수 | 설명 |
|---------------|-----------------|
|subscription_id | Automation 계정의 구독 ID |
| resource_group | Automation 계정이 정의된 리소스 그룹의 이름 |
| automation_account | Automation 계정 이름 |
| module_name | `pypi.org`에서 가져올 모듈의 이름 |

Runbook에서의 매개 변수 사용에 대한 자세한 정보는 [Runbook 매개 변수 작업](start-runbooks.md#work-with-runbook-parameters)을 참조하세요.

## <a name="use-a-package-in-a-runbook"></a>Runbook에서 패키지 사용

패키지를 가져오면 Runbook에서 사용할 수 있습니다. Azure 구독의 모든 리소스 그룹을 나열하는 다음 코드를 추가합니다.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

> [!NOTE]
> Python `automationassets` 패키지는 pypi.org에서 사용할 수 없으므로 Windows 머신으로 가져올 수 없습니다.

## <a name="next-steps"></a>다음 단계

Python Runbook을 준비하려면 [Python Runbook 만들기](learn/automation-tutorial-runbook-textual-python-3.md)를 참조하세요.

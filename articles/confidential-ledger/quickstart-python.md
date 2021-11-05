---
title: 빠른 시작 - Microsoft Azure 기밀 원장 Python 클라이언트 라이브러리
description: Python용 Microsoft Azure 기밀 원장 클라이언트 라이브러리를 사용하는 방법 알아보기
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/27/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 292c298f3af1b693574ecf9e053de14373d4d597
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476322"
---
# <a name="quickstart-microsoft-azure-confidential-ledger-client-library-for-python"></a>빠른 시작: Python용 Microsoft Azure 기밀 원장 클라이언트 라이브러리

Python용 Microsoft Azure 기밀 원장 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다.

Microsoft Azure 기밀 원장은 중요한 데이터 레코드를 관리하는 데 사용하는 매우 안전한 새로운 서비스입니다. 권한 있는 블록체인 모델을 기반으로 하는 Azure 기밀 원장은 불변성(원장을 추가 전용으로 만듦) 및 변조 방지(모든 레코드가 그대로 유지되도록)와 같은 고유한 데이터 무결성 이점을 제공합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[API 참조 설명서](/python/api/overview/azure/keyvault-secrets-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/confidentialledger) | [패키지(Python 패키지 인덱스) 관리 라이브러리](https://pypi.org/project/azure-mgmt-confidentialledger/)| [패키지(Python 패키지 인덱스) 클라이언트 라이브러리](https://pypi.org/project/azure-confidentialledger/)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="set-up"></a>설정

이 빠른 시작에서는 Azure CLI 또는 Azure PowerShell과 함께 Azure ID 라이브러리를 사용하여 사용자를 Azure Services에 인증합니다. 개발자는 Visual Studio 또는 Visual Studio Code를 사용하여 해당 호출을 인증할 수도 있습니다. 자세한 내용은 [Azure ID 클라이언트 라이브러리를 사용하여 클라이언트 인증](/python/api/overview/azure/identity-readme)을 참조하세요.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [Sign in to Azure](../../includes/confidential-ledger-sign-in-azure.md)]

### <a name="install-the-packages"></a>패키지 설치

터미널 또는 명령 프롬프트에서 적절한 프로젝트 폴더를 만든 다음, [Python 가상 환경 사용](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)에 설명된 대로 Python 가상 환경을 만들고 활성화합니다.

Azure Active Directory ID 클라이언트 라이브러리를 설치합니다.

```terminal
pip install azure-identity
```

Azure 기밀 원장 컨트롤 플레인 클라이언트 라이브러리를 설치합니다.

```terminal
pip install azure.mgmt.confidentialledger
```

Azure 기밀 원장 데이터 평면 클라이언트 라이브러리를 설치합니다.

```terminal
pip install azure.confidentialledger 
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](../../includes/confidential-ledger-rg-create.md)]

### <a name="register-the-microsoftconfidentialledger-resource-provider"></a>microsoft.ConfidentialLedger 리소스 공급자 등록

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

## <a name="create-your-python-app"></a>python 앱 만들기

### <a name="initialization"></a>초기화

이제 python 애플리케이션 작성을 시작할 수 있습니다.  먼저 필요한 패키지를 가져옵니다.

```python
# Import the Azure authentication library

from azure.identity import DefaultAzureCredential

## Import the control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import the data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient
```

다음으로 [DefaultAzureCredential 클래스](/python/api/azure-identity/azure.identity.defaultazurecredential)를 사용하여 앱을 인증합니다.

```python
credential = DefaultAzureCredential()
```

애플리케이션에서 사용할 몇 가지 변수를 설정하여 설치를 완료합니다. 리소스 그룹(myResourceGroup), 만들려는 원장 이름 및 데이터 평면 클라이언트 라이브러리에서 사용할 두 개의 url을 설정합니다.

  > [!Important]
  > 각 원장에는 전역적으로 고유한 이름이 있어야 합니다. 다음 예제에서 \<your-unique-keyvault-name\>을 원장 이름으로 바꿉니다.

```python
resource_group = "myResourceGroup"
ledger_name = "<your-unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".confidential-ledger.azure.com"
```

### <a name="use-the-control-plane-client-library"></a>컨트롤 플레인 클라이언트 라이브러리 사용

컨트롤 플레인 클라이언트 라이브러리(azure.mgmt.confidentialledger)를 사용하면 원장에서 생성, 수정 및 삭제와 같은 작업을 수행하고, 구독과 연결된 원장을 나열하고, 특정 원장의 세부 정보를 가져올 수 있습니다.

이 코드에서는 먼저 ConfidentialLedgerAPI 자격 증명 변수와 Azure 구독 ID(둘 다 위에 설정됨)를 전달하여 컨트롤 플레인 클라이언트를 만듭니다.  

```python
confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, subscription_id
)
```

이제 `begin_create`를 사용하여 원장을 만들 수 있습니다. `begin_create` 함수에는 세 개의 매개 변수, 즉 리소스 그룹, 원장 이름 및 "properties" 개체가 필요합니다.  

다음 키와 값을 사용하여 `properties` 사전을 만들고, 변수에 할당합니다.

```python
properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)
```

이제 리소스 그룹, 원장의 이름 및 속성 개체를 `begin_create`에 전달합니다.

```python
confidential_ledger_mgmt.ledger.begin_create(resource_group, ledger_name, ledger_properties)
```

원장이 성공적으로 생성되었는지 확인하려면 `get` 함수를 사용하여 세부 정보를 봅니다.

```python
myledger = confidential_ledger_mgmt.ledger.get(resource_group, ledger_name)

print("Here are the details of your newly created ledger:")
print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

```

### <a name="use-the-data-plane-client-library"></a>데이터 평면 클라이언트 라이브러리 사용

이제 원장이 있으므로 데이터 평면 클라이언트 라이브러리(azure.confidentialledger)를 사용하여 원장과 상호 작용합니다. 

먼저 기밀 원장 인증서를 생성하고 저장합니다.  

```python
identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)
```

이제 원장 URL 및 자격 증명과 함께 네트워크 인증서를 사용하여 기밀 원장 클라이언트를 만들 수 있습니다.

```python
ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)
```

원장에 쓸 준비가 되어 있습니다.  `append_to_ledger` 함수를 사용하여 수행합니다.

```python
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)
```

인쇄 함수는 원장에 쓴 트랜잭션 ID를 원장에 반환합니다. 이 ID는 원장에 쓴 메시지를 검색하는 데 사용할 수 있습니다.

```python
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

인쇄 함수는 트랜잭션 ID에 해당하는 원장의 메시지인 "Hello world!"를 반환합니다.

## <a name="full-sample-code"></a>전체 샘플 코드

```python
from azure.identity import DefaultAzureCredential

## Import control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

# Set variables

rg = "myResourceGroup"
ledger_name = "<unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".eastus.cloudapp.azure.com"

# Authentication

# Need to do az login to get default credential to work

credential = DefaultAzureCredential()

# Control plane (azure.mgmt.confidentialledger)
# 
# initialize endpoint with credential and subscription

confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, "<subscription-id>"
)

# Create properties dictionary for begin_create call 

properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)

# Create a ledger

foo = confidential_ledger_mgmt.ledger.begin_create(rg, ledger_name, ledger_properties)

# Get the details of the ledger you just created

print(f"{rg} / {ledger_name}")
 
print("Here are the details of your newly created ledger:")
myledger = confidential_ledger_mgmt.ledger.get(rg, ledger_name)

print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

# Data plane (azure.confidentialledger)
#
# Create a CL client

identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)


ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)

# Write to the ledger
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)

# Read from the ledger
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure 기밀 원장 문서도 이 빠른 시작을 이용해 작성할 수 있습니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

그렇지 않으면 이 문서에서 만든 리소스를 완료한 후 Azure CLI [az group delete](/cli/azure/group?#az_group_delete) 명령을 사용하여 리소스 그룹과 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 기밀 원장 개요](overview.md)

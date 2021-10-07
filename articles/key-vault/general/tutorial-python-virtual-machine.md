---
title: 자습서 - Python에서 가상 머신이 있는 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽도록 가상 머신을 Python 애플리케이션으로 구성합니다.
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 463474d13f0df50aebd775e119918e0db7ad65c2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597319"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>자습서: Python에서 가상 머신이 있는 Azure Key Vault 사용

Azure Key Vault는 API 키 및 데이터베이스 연결 문자열과 같은 키, 비밀 및 인증서를 보호하는 데 도움이 됩니다.

이 자습서에서는 Azure 리소스의 관리 ID를 사용하여 Azure Key Vault에서 정보를 읽도록 Python 애플리케이션을 설정합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 키 자격 증명 모음 만들기
> * Key Vault에 비밀 저장
> * Azure Linux 가상 머신 만들기
> * 가상 머신에 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정
> * 콘솔 애플리케이션이 Key Vault에서 데이터를 읽는 데 필요한 권한 부여
> * Key Vault에서 비밀 검색

시작하기 전에 [Key Vault 기본 개념](basic-concepts.md)을 읽어보세요. 

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Windows, Mac 및 Linux:
  * [Git](https://git-scm.com/downloads)
  * 이 자습서에서는 Azure CLI를 로컬로 실행해야 합니다. Azure CLI 버전 2.0.4 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>비밀로 키 자격 증명 모음 채우기

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

다음 방법 중 하나를 사용하여 **myVM** 이라는 VM을 만듭니다.

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) | [Azure 포털](../../virtual-machines/windows/quick-create-portal.md) |

Azure CLI를 사용하여 Linux VM를 만들려면 [az vm create](/cli/azure/vm) 명령을 사용합니다.  다음 예제에서는 *azureuser* 라는 사용자 계정을 추가합니다. `--generate-ssh-keys` 매개 변수는 SSH 키를 자동으로 생성하고 이를 기본 키 위치( *~/.ssh*)에 배치하는 데 사용됩니다. 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

출력의 `publicIpAddress` 값을 기록해 둡니다.

## <a name="assign-an-identity-to-the-vm"></a>VM에 ID 할당

Azure CLI [az vm identity assign](/cli/azure/vm/identity#az_vm_identity_assign) 명령을 사용하여 가상 머신의 시스템 할당 ID를 만듭니다.

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

다음 코드에 표시되는 시스템 할당 ID를 기록해 둡니다. 위 명령의 출력은 다음과 같습니다. 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID에 사용 권한을 할당합니다.

이제 다음 명령을 실행하여 이전에 만든 ID 사용 권한을 Key Vault에 할당할 수 있습니다.

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM에 로그인

가상 머신에 로그인하려면 [Linux를 실행하는 Azure 가상 머신에 연결 및 로그인](../../virtual-machines/linux/login-using-aad.md) 또는 [Windows를 실행하는 Azure 가상 머신에 연결 및 로그인](../../virtual-machines/windows/connect-logon.md)의 지침을 따르세요.


Linux VM에 로그인하려면 다음과 같이 [가상 머신 만들기](#create-a-virtual-machine) 단계에서 지정한 \<publicIpAddress\>와 함께 ssh 명령을 사용하면 됩니다.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>VM에 Python 라이브러리 설치

Python 스크립트에서 사용할 두 개의 Python 라이브러리 `azure-keyvault-secrets` 및 `azure.identity`를 가상 머신에 설치합니다.  

예를 들어 Linux VM에서는 다음과 같이 `pip3`를 사용하여 설치할 수 있습니다.

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>샘플 Python 스크립트 만들기 및 편집

가상 머신에서 **sample.py** 라고 하는 Python 파일을 만듭니다. 다음 코드를 포함하도록 파일을 편집하고 \<your-unique-keyvault-name\>을 키 자격 증명 모음의 이름으로 바꿉니다.

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>샘플 Python 앱 실행

마지막으로 **sample.py** 파일을 실행합니다. 모두 정상적으로 처리되었으면 다음과 같은 비밀 값이 반환됩니다.

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 가상 머신 및 Key Vault를 삭제합니다.  간단하게 리소스가 속한 리소스 그룹을 삭제하면 됩니다.

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[Azure Key Vault REST API](/rest/api/keyvault/)
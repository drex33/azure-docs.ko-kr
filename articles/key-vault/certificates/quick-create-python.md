---
title: 빠른 시작 – Azure Key Vault Python 클라이언트 라이브러리 - 인증서 관리
description: Python 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 인증서를 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli, mode-other
ms.openlocfilehash: a87db89fd252f133f7a7976637774fcb21b2c9bf
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133067482"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>빠른 시작: Python용 Azure Key Vault 인증서 클라이언트 라이브러리

Python용 Azure Key Vault 인증서 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다. Key Vault를 사용하여 인증서를 저장하면 코드에 인증서를 저장하지 않아도 되므로 앱의 보안이 강화됩니다.

[API 참조 설명서](/python/api/overview/azure/keyvault-certificates-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7 이상 또는 3.6 이상](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

이 빠른 시작에서는 Linux 터미널 창에서 [Azure CLI](/cli/azure/install-azure-cli)를 실행하고 있다고 가정합니다.

## <a name="set-up-your-local-environment"></a>로컬 환경 설정

이 빠른 시작에서는 Azure CLI와 함께 Azure ID 라이브러리를 사용하여 사용자를 Azure Services에 인증합니다. 또한 개발자는 Visual Studio 또는 Visual Studio Code를 사용하여 호출을 인증할 수도 있습니다. 자세한 내용은 [Azure ID 클라이언트 라이브러리를 사용하여 클라이언트 인증](/python/api/overview/azure/identity-readme)을 참조하세요.

### <a name="sign-in-to-azure"></a>Azure에 로그인

1. `login` 명령을 실행합니다.

    ```azurecli-interactive
    az login
    ```

    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

    그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.

2. 브라우저에서 계정 자격 증명으로 로그인합니다.

### <a name="install-the-packages"></a>패키지 설치

1. 터미널 또는 명령 프롬프트에서 적절한 프로젝트 폴더를 만든 다음, [Python 가상 환경 사용](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)에 설명된 대로 Python 가상 환경을 만들고 활성화합니다.

1. Azure Active Directory ID 라이브러리를 설치합니다.

    ```terminal
    pip install azure.identity
    ```


1. Key Vault 인증서 클라이언트 라이브러리를 설치합니다.

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

인증서 권한을 사용자 계정에 부여하는 키 자격 증명 모음에 대한 액세스 정책을 만듭니다.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>환경 변수 설정

이 애플리케이션은 키 자격 증명 모음 이름을 `KEY_VAULT_NAME`이라는 환경 변수로 사용합니다.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS 또는 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>샘플 코드 만들기

Python용 Azure Key Vault 인증서 클라이언트 라이브러리를 사용하면 인증서를 관리할 수 있습니다. 다음 코드 샘플에서는 클라이언트 생성, 인증서 설정, 인증서 검색 및 인증서 삭제 방법을 보여줍니다.

이 코드가 포함된 *kv_certificates.py* 라는 파일을 만듭니다.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>코드 실행

이전 섹션의 코드가 *kv_certificates.py* 라는 파일에 있는지 확인합니다. 그런 다음, 다음 명령을 사용하여 코드를 실행합니다.

```terminal
python kv_certificates.py
```

- 권한 오류가 발생한 경우 [`az keyvault set-policy` 명령](#grant-access-to-your-key-vault)을 실행했는지 확인합니다.
- 동일한 키 이름으로 코드를 다시 실행하면 "(충돌) 인증서 \<name\>이 현재 삭제되었지만 복구 가능한 상태에 있습니다."라는 오류가 발생할 수 있습니다. 다른 키 이름을 사용합니다.

## <a name="code-details"></a>코드 세부 정보

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

이 빠른 시작에서 로그인한 사용자는 로컬 개발에서 기본적으로 설정되는 방법인 키 자격 증명 모음에 인증하는 데 사용됩니다. Azure에 배포된 애플리케이션의 경우 관리 ID를 App Service 또는 Virtual Machine에 할당해야 합니다. 자세한 내용은 [관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

아래 예제에서 키 자격 증명 모음 이름은 `https://\<your-key-vault-name\>.vault.azure.net` 형식의 키 자격 증명 모음 URI로 확장됩니다. 이 예제는 ID를 제공하는 다양한 옵션이 있는 서로 다른 환경에서 동일한 코드를 사용할 수 있도록 하는 ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential) 클래스를 사용합니다. 자세한 내용은 [기본 Azure 자격 증명 인증](/python/api/overview/azure/identity-readme)을 참조하세요. 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>인증서 저장

키 자격 증명 모음에 대한 클라이언트 개체를 가져온 후에는 [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) 메서드를 사용하여 인증서를 만들 수 있습니다. 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

여기에서 인증서에는 [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) 메서드를 사용하여 가져온 정책이 필요합니다.

`begin_create_certificate` 메서드를 호출하면 키 자격 증명 모음용 Azure REST API에 대한 비동기 호출이 생성됩니다. 비동기 호출은 폴러 개체를 반환합니다. 작업의 결과를 대기하려면 폴러의 `result` 메서드를 호출합니다.

요청을 처리할 때 Azure는 클라이언트에 제공한 자격 증명 개체를 사용하여 호출자의 ID(서비스 주체)를 인증합니다.


### <a name="retrieve-a-certificate"></a>인증서 검색

Key Vault에서 인증서를 읽으려면 [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) 메서드를 사용합니다.

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Azure CLI 명령 [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show)를 사용하여 인증서가 설정되었는지 확인할 수도 있습니다.

### <a name="delete-a-certificate"></a>인증서 삭제

인증서를 삭제하려면 [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) 메서드를 사용합니다.

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate` 메서드는 비동기식이며 폴러 개체를 반환합니다. 폴러의 `result` 메서드를 호출하면 작업이 완료될 때까지 대기합니다.

Azure CLI 명령 [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show)를 사용하여 인증서가 삭제되었는지 확인할 수 있습니다.

인증서가 삭제되었지만 당분간 복구 가능한 상태로 유지됩니다. 코드를 다시 실행하는 경우 다른 인증서 이름을 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

[비밀](../secrets/quick-create-python.md) 및 [키](../keys/quick-create-python.md)로도 실험하려면 이 문서에서 만든 Key Vault를 다시 사용할 수 있습니다.

그렇지 않으면 이 문서에서 만든 리소스를 완료한 후 다음 명령을 사용하여 리소스 그룹과 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개요](../general/overview.md)
- [Key vault에 대한 액세스 보안](../general/security-features.md)
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md)
- [Key Vault 보안 개요](../general/security-features.md)
- [Key Vault로 인증](../general/authentication.md)

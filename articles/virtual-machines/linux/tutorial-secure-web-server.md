---
title: '자습서: TLS/SSL 인증서로 웹 서버 보호'
description: 이 자습서에서는 Azure CLI를 사용하여 Azure Key Vault에 저장된 SSL 인증서로 NGINX 웹 서버를 실행하는 Linux 가상 머신을 보호하는 방법을 설명합니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fbe2aabbcf9595cbb7520d160f72d17b58872293
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698971"
---
# <a name="tutorial-use-tlsssl-certificates-to-secure-a-web-server"></a>자습서: TLS/SSL 인증서를 사용하여 웹 서버 보호


**적용 대상:** :heavy_check_mark: Linux VM 

웹 서버를 보호하기 위해 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(Transport Layer Security) 인증서를 사용하여 웹 트래픽을 암호화할 수 있습니다. 이러한 TLS/SSL 인증서는 Azure Key Vault에 저장될 수 있으며 Azure에서 Linux VM(가상 머신)에 인증서의 보안 배포를 허용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Key Vault 만들기
> * Key Vault에 인증서 생성 또는 업로드
> * VM 만들기 및 NGINX 웹 서버 설치
> * VM에 인증서를 삽입하고 TLS 바인딩으로 NGINX 구성

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 [Azure Cloud Shell](../../cloud-shell/overview.md) 내의 CLI를 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.


## <a name="overview"></a>개요
Azure Key Vault는 암호화 키 및 비밀(인증서 또는 암호)을 보호합니다. Key Vault를 사용하면 인증서 관리 프로세스를 간소화하고 해당 인증서에 액세스하는 키의 제어를 유지할 수 있습니다. Key Vault 내에 자체 서명된 인증서를 만들거나 이미 소유하고 있는 기존의 신뢰할 수 있는 인증서를 업로드할 수 있습니다.

내재된 인증서를 포함하는 사용자 지정 VM 이미지를 사용하는 대신 실행 중인 VM에 인증서를 삽입합니다. 이 프로세스를 통해 배포하는 동안 가장 최신 인증서가 웹 서버에 설치됩니다. 인증서를 갱신하거나 바꾸는 경우 새 사용자 지정 VM 이미지를 만들 필요가 없습니다. 최신 인증서는 추가 VM을 만들 때 자동으로 삽입됩니다. 전체 프로세스 동안 인증서는 Azure 플랫폼에서 벗어나거나 스크립트, 명령줄 기록 또는 템플릿에 노출되지 않습니다.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기
Key Vault 및 인증서를 만들려면 먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupSecureWeb* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

다음으로 [az keyvault create](/cli/azure/keyvault)를 사용하여 Key Vault를 만들고 VM 배포 시에 사용할 수 있도록 설정합니다. 각 Key Vault에는 고유한 이름이 필요하며 모두 소문자여야 합니다. 다음 예제에서 *\<mykeyvault>* 를 사용자 고유의 Key Vault 이름으로 바꿉니다.

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>인증서 생성 및 Key Vault에 저장
프로덕션 사용을 위해 [az keyvault certificate import](/cli/azure/keyvault/certificate)를 사용하여 신뢰할 수 있는 공급자가 서명한 유효한 인증서를 가져와야 합니다. 이 자습서에서는 다음 예제를 통해 [az keyvault certificate create](/cli/azure/keyvault/certificate)를 사용하여 기본 인증서 정책을 사용하는 자체 서명된 인증서를 생성할 수 있는 방법을 보여 줍니다.

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>VM에 사용할 인증서 준비
VM 만들기 프로세스 동안 인증서를 사용하려면 [az keyvault secret list-versions](/cli/azure/keyvault/secret)를 사용하여 인증서 ID를 가져옵니다. [az vm secret format](/cli/azure/vm/secret#az_vm_secret_format)을 사용하여 인증서를 변환합니다. 다음 예제에서는 다음 단계의 사용 편의성을 위해 변수에 이러한 명령의 출력을 할당합니다.

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>NGINX를 보호할 cloud-init 구성 만들기
[Cloud-init](https://cloudinit.readthedocs.io)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 실행되면 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.

VM을 만들 때 인증서와 키는 보호되는 */var/lib/waagent/* 디렉터리에 저장됩니다. VM에 인증서 추가 및 웹 서버 구성을 자동화하려면 cloud-init를 사용합니다. 이 예제에서는 NGINX 웹 서버를 설치하고 구성합니다. 동일한 프로세스를 사용하여 Apache를 설치하고 구성할 수 있습니다. 

*cloud-init-web-server.txt* 라는 파일을 만들고 다음 구성을 붙여 넣습니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>보안 VM 만들기
이제 [az vm create](/cli/azure/vm)로 VM을 만듭니다. 인증서 데이터는 `--secrets` 매개 변수를 사용하여 Key Vault에서 삽입됩니다. `--custom-data` 매개 변수를 사용하여 cloud-init 구성에서 전달합니다.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

VM을 만들고 패키지를 설치하고 앱을 시작하는 데 몇 분 정도 걸립니다. VM이 만들어지면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소는 웹 브라우저에서 사이트에 액세스할 때 사용됩니다.

보안 웹 트래픽이 VM에 도달하도록 허용하려면 [az vm open-port](/cli/azure/vm)를 사용하여 인터넷에서 포트 443을 엽니다.

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>보안 웹앱 테스트
이제 웹 브라우저를 열고 주소 표시줄에 *https:\/\/\<publicIpAddress>* 를 입력할 수 있습니다. VM 만들기 프로세스에서 사용자 고유의 공용 IP 주소를 제공합니다. 자체 서명된 인증서를 사용하는 경우 보안 경고를 허용합니다.

![웹 브라우저 보안 경고 허용](./media/tutorial-secure-web-server/browser-warning.png)

그러면 보안 NGINX 사이트가 다음 예제와 같이 표시됩니다.

![실행 중인 보안 NGINX 사이트 보기](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Key Vault에 저장된 TLS/SSL 인증서를 사용하여 NGINX 웹 서버를 보호했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Key Vault 만들기
> * Key Vault에 인증서 생성 또는 업로드
> * VM 만들기 및 NGINX 웹 서버 설치
> * VM에 인증서를 삽입하고 TLS 바인딩으로 NGINX 구성

미리 빌드된 가상 머신 스크립트 샘플을 보려면 이 링크를 따릅니다.

> [!div class="nextstepaction"]
> [Linux 가상 머신 스크립트 샘플](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
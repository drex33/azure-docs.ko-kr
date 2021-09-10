---
title: 자습서 - Azure의 cloud-init를 사용하여 Linux VM 사용자 지정
description: 이 자습서에서는 cloud-init 및 Key Vault를 사용하여 Azure에서 처음 부팅 시 Linux VM을 사용자 지정하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 98723a6390958f38acec4909d6635adadc65ff13
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692058"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>자습서 - cloud-init를 사용하여 첫 번째 부팅 시 Azure에서 Linux 가상 머신을 사용자 지정하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이전 자습서에서는 VM(가상 머신)에 SSH를 적용하고 NGINX를 수동으로 설치하는 방법에 대해 알아보았습니다. 빠르고 일관된 방식으로 VM을 만들려면 일반적으로 자동화 기능이 필요합니다. 처음 부팅 시 VM을 사용자 지정하는 일반적인 방법은 [cloud-init](https://cloudinit.readthedocs.io)를 사용하는 것입니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * cloud-init 구성 파일 만들기
> * cloud-init 파일을 사용하는 VM 만들기
> * VM을 만든 후에 실행 중인 Node.js 앱 보기
> * Key Vault를 사용하여 안전하게 인증서 저장
> * cloud-init를 사용하여 NGINX 배포 자동화

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="cloud-init-overview"></a>Cloud-init 개요
[Cloud-init](https://cloudinit.readthedocs.io)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 실행되면 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.

Cloud-init는 배포에서도 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install** 은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. cloud-init에서 선택한 배포판의 기본 패키지 관리 도구를 자동으로 사용합니다.

당사는 파트너와 협력하여 파트너가 Azure에 제공하는 이미지에 cloud-init를 포함하고 이러한 이미지에서 cloud-init가 작동하도록 설정하고 있습니다. 각 배포의 cloud-init 지원에 대한 자세한 내용은 [Azure의 VM에 대한 cloud-init 지원](using-cloud-init.md)을 참조하세요.


## <a name="create-cloud-init-config-file"></a>cloud-init 구성 파일 만들기
cloud-init의 실제 동작을 확인하려면 NGINX를 설치하고 간단한 'Hello World' Node.js 앱을 실행하는 VM을 만듭니다. 다음 cloud-init 구성은 필요한 패키지를 설치하고 Node.js 앱을 만든 다음 앱을 초기화하고 시작합니다.

bash 프롬프트 또는 Cloud Shell에서 *cloud-init.txt* 라는 파일을 만들고 다음 구성을 붙여넣습니다. 예를 들어 `sensible-editor cloud-init.txt`를 입력하여 파일을 만들고 사용 가능한 편집기의 목록을 봅니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

cloud-init 구성 옵션에 대한 자세한 내용은 [cloud-init 구성 예제](https://cloudinit.readthedocs.io/en/latest/topics/examples.html)를 참조하세요.

## <a name="create-virtual-machine"></a>가상 머신 만들기
VM을 만들려면 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupAutomate* 라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

이제 [az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. `--custom-data` 매개 변수를 사용하여 cloud-init 구성 파일을 전달합니다. 현재 작업 디렉터리 외부에 파일을 저장한 경우 *cloud-init.txt* 구성의 전체 경로를 제공합니다. 다음 예제에서는 *myVM* 이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

VM을 만들고 패키지를 설치하고 앱을 시작하는 데 몇 분 정도 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. 앱에 액세스하려면 몇 분이 걸릴 수 있습니다. VM이 만들어지면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소는 웹 브라우저를 통해 Node.js 앱에 액세스할 때 사용됩니다.

웹 트래픽이 VM에 도달하도록 허용하려면 [az vm open-port](/cli/azure/vm#az_vm_open_port)를 사용하여 인터넷에서 포트 80을 엽니다.

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Web App 테스트
이제 웹 브라우저를 열고 주소 표시줄에 *http:\/\/\<publicIpAddress>* 을 입력할 수 있습니다. VM 만들기 프로세스에서 사용자 고유의 공용 IP 주소를 제공합니다. Node.js 앱은 다음 예제와 같이 표시됩니다.

![실행 중인 NGINX 사이트 보기](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Key Vault의 인증서 삽입
이 선택적 섹션에서는 Azure Key Vault에 안전하게 인증서를 저장하고 VM 배포 중에 이 인증서를 삽입할 수 있는 방법을 보여 줍니다. 내재된 인증서를 포함하는 사용자 지정 이미지를 사용하는 대신 이 프로세스를 통해 처음 부팅 시 가장 최신 인증서를 VM에 삽입합니다. 프로세스 동안 인증서는 Azure 플랫폼에서 벗어나거나 스크립트, 명령줄 기록 또는 템플릿에 노출되지 않습니다.

Azure Key Vault는 암호화 키 및 비밀(인증서 또는 암호)을 보호합니다. Key Vault를 사용하면 키 관리 프로세스를 간소화하고 데이터를 액세스하고 암호화하는 키의 제어를 유지할 수 있습니다. 이 시나리오는 Key Vault를 사용하는 방법에 대한 자세한 개요는 아니지만 인증서를 만들고 사용할 수 있는 Key Vault의 몇 가지 개념을 소개합니다.

다음 단계에서는 다음과 같은 작업을 수행할 수 있는 방법을 설명합니다.

- Azure Key Vault 만들기
- Key Vault에 인증서 생성 또는 업로드
- VM에 삽입할 인증서의 비밀 만들기
- VM 만들기 및 인증서 삽입

### <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기
먼저 [az keyvault create](/cli/azure/keyvault#az_keyvault_create)를 사용하여 Key Vault를 만들고 VM 배포 시에 사용할 수 있도록 설정합니다. 각 Key Vault에는 고유한 이름이 필요하며 모두 소문자여야 합니다. 다음 예제에서 `mykeyvault`를 사용자 고유의 Key Vault 이름으로 바꿉니다.

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>인증서 생성 및 Key Vault에 저장
프로덕션 사용을 위해 [az keyvault certificate import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import)를 사용하여 신뢰할 수 있는 공급자가 서명한 유효한 인증서를 가져와야 합니다. 이 자습서에서는 다음 예제를 통해 [az keyvault certificate create](/cli/azure/keyvault/certificate#az_keyvault_certificate_create)를 사용하여 기본 인증서 정책을 사용하는 자체 서명된 인증서를 생성할 수 있는 방법을 보여 줍니다.

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>VM에 사용할 인증서 준비
VM 만들기 프로세스 동안 인증서를 사용하려면 [az keyvault secret list-versions](/cli/azure/keyvault/secret#az_keyvault_secret_list_versions)를 사용하여 인증서 ID를 가져옵니다. VM에는 부팅 시 삽입하는 특정 형식의 인증서가 필요하므로 [az vm secret format](/cli/azure/vm)을 사용하여 인증서를 변환합니다. 다음 예제에서는 다음 단계의 사용 편의성을 위해 변수에 이러한 명령의 출력을 할당합니다.

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>NGINX를 보호할 cloud-init 구성 만들기
VM을 만들 때 인증서와 키는 보호되는 */var/lib/waagent/* 디렉터리에 저장됩니다. VM에 인증서 추가 및 NGINX 구성을 자동화하기 위해 이전 예제에서 업데이트된 cloud-init 구성을 사용할 수 있습니다.

*cloud-init-secured.txt* 라는 파일을 만들고 다음 구성을 붙여 넣습니다. Cloud Shell을 사용하는 경우 로컬 머신이 아닌 해당 위치에서 cloud-init 구성 파일을 만듭니다. 예를 들어 `sensible-editor cloud-init-secured.txt`를 입력하여 파일을 만들고 사용 가능한 편집기의 목록을 봅니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>보안 VM 만들기
이제 [az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 인증서 데이터는 `--secrets` 매개 변수를 사용하여 Key Vault에서 삽입됩니다. 이전 예제와 마찬가지로 `--custom-data` 매개 변수를 사용하여 cloud-init 구성을 전달합니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

VM을 만들고 패키지를 설치하고 앱을 시작하는 데 몇 분 정도 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. 앱에 액세스하려면 몇 분이 걸릴 수 있습니다. VM이 만들어지면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소는 웹 브라우저를 통해 Node.js 앱에 액세스할 때 사용됩니다.

보안 웹 트래픽이 VM에 도달하도록 허용하려면 [az vm open-port](/cli/azure/vm#az_vm_open_port)를 사용하여 인터넷에서 포트 443을 엽니다.

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>보안 Web App 테스트
이제 웹 브라우저를 열고 주소 표시줄에 *https:\/\/\<publicIpAddress>* 를 입력할 수 있습니다. 이전의 VM 생성 프로세스 출력에 표시된 대로 공용 IP 주소를 제공합니다. 자체 서명된 인증서를 사용하는 경우 보안 경고를 허용합니다.

![웹 브라우저 보안 경고 허용](./media/tutorial-automate-vm-deployment/browser-warning.png)

그러면 보안 NGINX 사이트와 Node.js 앱이 다음 예제와 같이 표시됩니다.

![실행 중인 보안 NGINX 사이트 보기](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 cloud-init를 사용하여 처음 부팅할 때 VM을 구성했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * cloud-init 구성 파일 만들기
> * cloud-init 파일을 사용하는 VM 만들기
> * VM을 만든 후에 실행 중인 Node.js 앱 보기
> * Key Vault를 사용하여 안전하게 인증서 저장
> * cloud-init를 사용하여 NGINX 배포 자동화

사용자 지정 VM 이미지를 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [사용자 지정 VM 이미지 만들기](./tutorial-custom-images.md)

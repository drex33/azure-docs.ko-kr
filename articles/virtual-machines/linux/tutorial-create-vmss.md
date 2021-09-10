---
title: '자습서: Linux 가상 머신 확장 집합 만들기'
description: Azure CLI를 사용하여 가상 머신 확장 집합을 사용하는 Linux VM에서 고가용성 애플리케이션을 만들고 배포하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.collection: linux
ms.date: 06/01/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: e20622d48132172387e78d2e4db6ef808e68bf12
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697713"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli"></a>자습서: Azure CLI를 통해 Linux에서 가상 머신 확장 집합 만들기 및 고가용성 앱 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 균일한 확장 집합

가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정하거나 CPU와 같은 리소스 사용량, 메모리 요구량 또는 네트워크 트래픽을 기반으로 자동으로 크기를 조정하는 규칙을 정의할 수도 있습니다. 이 자습서에서는 Azure에서 가상 머신 확장 집합을 배포합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * cloud-init를 사용하여 크기를 조정하는 앱 만들기
> * 가상 머신 확장 집합 만들기
> * 확장 집합의 인스턴스 수 증가 또는 감소
> * 자동 크기 조정 규칙 만들기
> * 확장 집합 인스턴스에 대한 연결 정보 보기
> * 확장 집합에 데이터 디스크 사용

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 [Azure Cloud Shell](../../cloud-shell/overview.md) 내의 CLI를 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="scale-set-overview"></a>확장 집합 개요
가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM은 하나 이상의 *배치 그룹* 에서 논리 장애 도메인 및 업데이트 도메인에 분산됩니다. 이러한 항목은 비슷하게 구성된 VM의 그룹으로 [가용성 집합](tutorial-availability-sets.md)과 비슷합니다.

VM은 필요에 따라 확장 집합에 생성됩니다. 사용자는 확장 집합에서 VM이 추가되거나 제거되는 방법 및 시기를 제어하는 자동 크기 조정 규칙을 정의합니다. 이러한 규칙은 메트릭(예: CPU 부하, 메모리 사용량 또는 네트워크 트래픽)을 기반으로 트리거할 수 있습니다.

확장 집합은 Azure 플랫폼 이미지를 사용하는 경우 최대 1,000개의 VM을 지원합니다. 중요한 설치 또는 VM 사용자 지정이 필요한 워크로드의 경우 [사용자 지정 VM 이미지를 만들 수 있습니다](tutorial-custom-images.md). 사용자 지정 이미지를 사용하는 경우 확장 집합에 최대 300대의 VM을 만들 수 있습니다.


## <a name="create-an-app-to-scale"></a>크기를 조정하는 앱 만들기
프로덕션 사용을 위해 설치되고 구성된 애플리케이션을 포함하는 [사용자 지정 VM 이미지 만들기](tutorial-custom-images.md) 작업이 필요할 수 있습니다. 이 자습서에서는 처음 부팅 시 VM을 사용자 지정하여 확장 집합의 실제 동작을 신속하게 확인합니다.

이전 자습서에서 cloud-init를 사용하여 [처음 부팅 시 Linux 가상 머신을 사용자 지정하는 방법](tutorial-automate-vm-deployment.md)을 배웠습니다. 동일한 cloud-init 구성 파일을 사용하여 NGINX를 설치하고 간단한 'Hello World' Node.js 앱을 실행할 수 있습니다.

현재 셸에서 *cloud-init.txt* 라는 파일을 만들고 다음 구성을 붙여 넣습니다. 예를 들어 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. `sensible-editor cloud-init.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
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
  - path: /home/azureuser/myapp/index.js
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


## <a name="create-a-scale-set"></a>확장 집합 만들기
확장 집합을 만들려면 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupScaleSet* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupScaleSet --location eastus
```

이제 [az vmss create](/cli/azure/vmss#az_vmss_create)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 *myScaleSet* 이라는 확장 집합을 만들고, cloud-init 파일을 사용하여 VM을 사용자 지정하고, SSH 키가 없는 경우 SSH 키를 생성합니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. 앱에 액세스하려면 몇 분이 걸릴 수 있습니다.


## <a name="allow-web-traffic"></a>웹 트래픽 허용
부하 분산 장치는 가상 머신 확장 집합의 일부로 자동으로 생성되었습니다. 부하 분산 장치는 부하 분산 장치 규칙을 사용하여 정의된 VM 집합 전역에 트래픽을 분산시킵니다. 다음 자습서 [Azure에서 Virtual Machines의 부하를 분산하는 방법](tutorial-load-balancer.md)에서 부하 분산 장치 개념 및 구성에 대해 자세히 알아볼 수 있습니다.

트래픽이 Web App에 도달하도록 허용하려면 [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 규칙을 만듭니다. 다음 예제는 *myLoadBalancerRuleWeb* 이라는 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>앱 테스트
웹에서 Node.js 앱을 보려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myScaleSetLBPublicIP* 의 IP 주소를 가져옵니다.

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

웹 브라우저에 공용 IP 주소를 입력합니다. 앱이 표시되고 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름이 표시됩니다.

![Node.js 앱 실행](./media/tutorial-create-vmss/running-nodejs-app.png)

확장 집합의 실제 동작을 확인하려면 웹 브라우저에서 새로 고침을 실행하여 부하 분산 장치가 앱이 실행되는 모든 VM으로 트래픽을 분산시키는 것을 확인합니다.


## <a name="management-tasks"></a>관리 작업
확장 집합의 수명 주기 내내 하나 이상의 관리 작업을 실행해야 합니다. 또한 다양한 수명 주기 작업을 자동화하는 스크립트를 만들어야 하는 경우가 있습니다. Azure CLI는 이러한 작업을 수행할 수 있는 빠른 방법을 제공합니다. 다음은 몇 가지 일반적인 작업입니다.

### <a name="view-vms-in-a-scale-set"></a>확장 집합의 VM 보기
확장 집합에서 실행 중인 VM 목록을 보려면 다음과 같이 [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)를 사용합니다.

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

다음 예제와 유사하게 출력됩니다.

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="manually-increase-or-decrease-vm-instances"></a>수동으로 VM 인스턴스 증가 또는 감소
현재 확장 집합의 인스턴스 수를 보려면 [az vmss show](/cli/azure/vmss#az_vmss_show)를 사용하여 *sku.capacity* 를 쿼리합니다.

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

그런 다음[az vmss scale](/cli/azure/vmss#az_vmss_scale)을 사용하여 확장 집합의 Virtual Machines 수를 수동으로 증가 또는 감소시킬 수 있습니다. 다음 예제는 확장 집합의 VM 수를 *3* 으로 설정합니다.

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```

### <a name="get-connection-info"></a>연결 정보 가져오기
확장 집합의 VM에 대한 연결 정보를 가져오려면 [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info)를 사용합니다. 이 명령은 SSH와 연결할 수 있도록 각 VM의 공용 IP 주소 및 포트를 출력합니다.

```azurecli-interactive
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>확장 집합으로 데이터 디스크 사용
확장 집합으로 데이터 디스크를 사용할 수 있습니다. OS 디스크 대신 데이터 디스크에 앱을 빌드하는 모범 사례 및 성능 향상에 대해 설명하는 이전 자습서에서는 [Azure 디스크를 관리하는 방법](tutorial-manage-disks.md)을 배웠습니다.

### <a name="create-scale-set-with-data-disks"></a>데이터 디스크로 확장 집합 만들기
확장 집합을 만들고 데이터 디스크를 연결하려면 [az vmss create](/cli/azure/vmss#az_vmss_create) 명령에 `--data-disk-sizes-gb` 매개 변수를 추가합니다. 다음 예제에서는 각 인스턴스에 *50* Gb 데이터 디스크가 연결된 확장 집합을 만듭니다.

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

확장 집합에서 인스턴스가 제거되면 연결된 데이터 디스크도 제거됩니다.

### <a name="add-data-disks"></a>데이터 디스크 추가
확장 집합의 인스턴스에 데이터 디스크를 추가하려면 [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach) 명령을 사용합니다. 다음 예제는 각 인스턴스에 *50* Gb 디스크를 추가합니다.

```azurecli-interactive
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>데이터 디스크 분리
확장 집합의 인스턴스에서 데이터 디스크를 제거하려면 [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach) 명령을 사용합니다. 다음 예제는 각 인스턴스의 LUN *2* 에서 데이터 디스크를 제거합니다.

```azurecli-interactive
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 가상 머신 확장 집합을 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * cloud-init를 사용하여 크기를 조정하는 앱 만들기
> * 가상 머신 확장 집합 만들기
> * 확장 집합의 인스턴스 수 증가 또는 감소
> * 자동 크기 조정 규칙 만들기
> * 확장 집합 인스턴스에 대한 연결 정보 보기
> * 확장 집합에 데이터 디스크 사용

Virtual Machines의 부하 분산 개념에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Virtual Machines 부하 분산](tutorial-load-balancer.md)

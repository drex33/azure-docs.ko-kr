---
title: '자습서: 가상 머신 확장 집합 만들기 및 관리 – Azure CLI'
description: Azure CLI를 사용하여 인스턴스를 시작하고 중지하는 방법, 확장 집합 용량을 변경하는 방법 등의 몇 가지 일반적인 관리 작업과 함께 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 1b95d997bfea9703f302b6ebfdc8dcbf1f514589
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693074"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 가상 머신 확장 집합 만들기 및 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 가상 머신 확장 집합의 수명 주기 동안 하나 이상의 관리 작업을 실행해야 합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 머신 확장 집합 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 인스턴스 크기 보기 및 사용
> * 수동으로 확장 집합 크기 조정
> * 일반적인 확장 집합 관리 작업 수행

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.29 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 


## <a name="create-a-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신 확장 집합보다 먼저 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *eastus* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

리소스 그룹 이름은 이 자습서에서 확장 집합 만들거나 수정할 때 지정됩니다.


## <a name="create-a-scale-set"></a>확장 집합 만들기
[az vmss create](/cli/azure/vmss) 명령을 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 *myScaleSet* 이라는 확장 집합을 만들고, SSH 키가 없는 경우 이 키를 생성합니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

확장 집합 리소스와 VM 인스턴스를 모두 만들고 구성하는 데 몇 분 정도 걸립니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다.


## <a name="view-the-vm-instances-in-a-scale-set"></a>확장 집합의 VM 인스턴스 보기
확장 집합의 VM 인스턴스 목록을 보려면 다음과 같이 [az vmss list-instances](/cli/azure/vmss)를 사용합니다.

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

다음 예제 출력에서는 확장 집합의 두 VM 인스턴스를 보여 줍니다.

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


출력의 첫 번째 열에는 *InstanceId* 가 표시됩니다. 특정 VM 인스턴스에 대한 추가 정보를 보려면 `--instance-id` 매개 변수를 [az vmss get-instance-view](/cli/azure/vmss)에 추가합니다. 다음 예제에서는 *1* VM 인스턴스에 대한 정보가 표시됩니다.

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>연결 정보 나열
트래픽을 개별 VM 인스턴스로 라우팅하는 부하 분산 장치에 공용 IP 주소가 할당됩니다. NAT(Network Address Translation) 규칙은 기본적으로 지정된 포트의 각 VM에 원격 연결 트래픽을 전달하는 Azure 부하 분산 장치에 추가됩니다. 확장 집합의 VM 인스턴스에 연결하려면 할당된 공용 IP 주소와 포트 번호에 대한 원격 연결을 만듭니다.

확장 집합의 VM 인스턴스에 연결할 주소와 포트를 나열하려면 [az vmss list-instance-connection-info](/cli/azure/vmss)를 사용합니다.

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

다음 예제 출력에서는 NAT 규칙에서 트래픽을 전달하는 인스턴스 이름, 부하 분산 장치의 공용 IP 주소 및 포트 번호를 보여 줍니다.

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH를 첫 번째 VM 인스턴스에 연결합니다. 앞의 명령과 같이 `-p` 매개 변수를 사용하여 공용 IP 주소와 포트 번호를 지정합니다.

```console
ssh azureuser@13.92.224.66 -p 50001
```

VM 인스턴스에 로그인한 후 필요에 따라 일부 구성 변경을 수동으로 수행할 수 있습니다. 지금은 정상적으로 SSH 세션을 닫습니다.

```console
exit
```


## <a name="understand-vm-instance-images"></a>VM 인스턴스 이미지 이해
자습서의 시작 부분에서 확장 집합을 만들 때 VM 인스턴스에 대해 *UbuntuLTS* 의 `--image`가 지정되었습니다. Azure Marketplace에는 VM 인스턴스를 만드는 데 사용할 수 있는 많은 이미지가 포함되어 있습니다. 가장 일반적으로 사용되는 이미지 목록을 보려면 [az vm image list](/cli/azure/vm/image) 명령을 사용하세요.

```azurecli-interactive
az vm image list --output table
```

다음 예제 출력에서는 Azure에서 가장 일반적인 VM 이미지를 보여 줍니다. 확장 집합을 만들 때 *UrnAlias* 를 사용하여 이러한 일반 이미지 중 하나를 지정할 수 있습니다.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

전체 목록을 보려면 `--all` 인수를 추가합니다. 이미지 목록은 `--publisher` 또는 `–-offer`로 필터링할 수도 있습니다. 다음 예제에서는 *CentOS* 와 일치하는 제품이 있는 모든 이미지에 대한 목록이 필터링됩니다.

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

압축된 다음 출력에서는 사용할 수 있는 CentOS 7.3 이미지 중 일부를 보여 줍니다.

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

특정 이미지를 사용하는 확장 집합을 배포하려면 *Urn* 열의 값을 사용합니다. 이미지를 지정할 때 이미지 버전 번호는 최신 버전의 배포를 선택하도록 *latest* 로 대체될 수 있습니다. 다음 예제에서는 CentOS 7.3 이미지의 최신 버전을 지정하기 위해 `--image` 인수를 사용합니다.

> [!IMPORTANT]
> *최신* 이미지 버전을 사용하는 것이 좋습니다. 배포 시 사용할 수 있는 최신 버전의 이미지를 사용하려면 '최신'을 지정합니다. '최신'을 사용하더라도 배포 시간이 지나면 새 버전을 사용할 수 있게 되는 경우에도 VM 이미지가 자동으로 업데이트되지 않습니다.

모든 확장 집합 리소스와 VM 인스턴스를 만들고 구성하는 데 몇 분이 걸리기 때문에 다음 확장 집합을 배포할 필요가 없습니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>VM 인스턴스 크기 이해
VM 인스턴스 크기 또는 *SKU* 에 따라 VM 인스턴스에 사용할 수 있는 컴퓨팅 리소스(예: CPU, GPU, 메모리)의 양이 결정됩니다. 확장 집합의 VM 인스턴스 크기는 예상 작업에 맞게 적절히 조정되어야 합니다.

### <a name="vm-instance-sizes"></a>VM 인스턴스 크기
다음 표에서는 일반적인 VM 크기를 사용 사례로 분류하고 있습니다.

| Type                     | 일반적인 크기           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [범용](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU 대 메모리 비율이 적당합니다. 개발/테스트와 소규모에서 중간 정도의 애플리케이션 및 데이터 솔루션에 적합합니다.  |
| [컴퓨팅 최적화](../virtual-machines/sizes-compute.md)   | Fs, F             | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 애플리케이션, 네트워크 어플라이언스 및 일괄 처리 프로세스에 적합합니다.        |
| [메모리에 최적화](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | 메모리 대 코어 비율이 높습니다. 관계형 데이터베이스, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| [Storage에 최적화](../virtual-machines/sizes-storage.md)      | Ls                | 높은 디스크 처리량 및 IO 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | 대량의 그래픽 렌더링 및 비디오 편집에 적합한 전문 VM입니다.       |
| [고성능](../virtual-machines/sizes-hpc.md) | H, A8-11          | 당사의 가장 강력한 CPU VM으로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. 

### <a name="find-available-vm-instance-sizes"></a>사용 가능한 VM 인스턴스 크기 찾기
특정 지역에서 사용할 수 있는 VM 인스턴스 크기의 목록을 보려면 [az vm list-sizes](/cli/azure/vm) 명령을 사용합니다.

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

출력은 압축된 다음 예제와 비슷하며, 각 VM 크기에 할당된 리소스를 보여 줍니다.

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>특정 VM 인스턴스 크기로 확장 집합 만들기
자습서의 시작 부분에서 확장 집합을 만들 때 VM 인스턴스에 대해 *Standard_D1_v2* 의 기본 VM SKU가 제공되었습니다. [az vm list-sizes](/cli/azure/vm)의 출력에 따라 다른 VM 인스턴스 크기를 지정할 수 있습니다. 다음 예제에서는 *Standard_F1* 의 VM 인스턴스 크기를 지정하는 `--vm-sku` 매개 변수를 사용하여 확장 집합을 만듭니다. 모든 확장 집합 리소스와 VM 인스턴스를 만들고 구성하는 데 몇 분이 걸리기 때문에 다음 확장 집합을 배포할 필요가 없습니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>확장 집합의 용량 변경
자습서의 시작 부분에서 확장 집합을 만들 때 두 개의 VM 인스턴스가 기본적으로 배포되었습니다. [az vmss create](/cli/azure/vmss)에 `--instance-count` 매개 변수를 지정하여 확장 집합으로 만든 인스턴스의 수를 변경할 수 있습니다. 기존 확장 집합의 VM 인스턴스 수를 늘리거나 줄이려면 용량을 수동으로 변경할 수 있습니다. 확장 집합은 필요한 수의 VM 인스턴스를 만들거나 제거한 다음, 부하 분산 장치에서 트래픽을 분산하도록 구성합니다.

확장 집합의 VM 인스턴스 수를 수동으로 늘리거나 줄이려면 [az vmss scale](/cli/azure/vmss)을 사용합니다. 다음 예제에서는 확장 집합의 VM 인스턴스 수를 *3* 으로 설정합니다.

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

확장 집합의 용량을 업데이트하는 데 몇 분 정도가 걸립니다. 현재 확장 집합의 인스턴스 수를 보려면 [az vmss show](/cli/azure/vmss)를 사용하고 *sku.capacity* 에 대해 쿼리합니다.

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>일반적인 관리 작업
이제 확장 집합을 만들고, 연결 정보를 나열하고, VM 인스턴스에 연결할 수 있습니다. VM 인스턴스에 대해 다른 OS 이미지를 사용하거나, 다른 VM 크기를 선택하거나, 인스턴스 수를 수동으로 조정하는 방법을 알아보았습니다. 일상적인 관리의 일환으로, 확장 집합에서 VM 인스턴스를 중지, 시작 또는 다시 시작해야 할 수 있습니다.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>확장 집합에서 VM 인스턴스 중지 및 할당 취소
확장 집합에서 하나 이상의 VM 인스턴스를 중지하려면 [az vmss stop](/cli/azure/vmss)을 사용합니다. `--instance-ids` 매개 변수를 사용하면 중지할 VM 인스턴스를 하나 이상 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합의 모든 VM 인스턴스가 중지됩니다. 다음 예제에서는 *1* 인스턴스를 중지합니다.

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

중지된 VM 인스턴스는 할당된 상태로 유지되며 컴퓨팅 요금이 계속 발생합니다. 대신 VM 인스턴스의 할당을 취소하고 스토리지 요금만 발생하도록 하려면 [az vmss deallocate](/cli/azure/vmss)를 사용합니다. 다음 예제에서는 *1* 인스턴스를 중지하고 할당을 취소합니다.

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>확장 집합에서 VM 인스턴스 시작
확장 집합에서 하나 이상의 VM 인스턴스를 시작하려면 [az vmss start](/cli/azure/vmss)를 사용합니다. `--instance-ids` 매개 변수를 사용하면 시작할 VM 인스턴스를 하나 이상 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합의 모든 VM 인스턴스가 시작됩니다. 다음 예제에서는 *1* 인스턴스를 시작합니다.

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>확장 집합에서 VM 인스턴스 다시 시작
확장 집합에서 하나 이상의 VM 인스턴스를 다시 시작하려면 [az vmss restart](/cli/azure/vmss)를 사용합니다. `--instance-ids` 매개 변수를 사용하면 다시 시작할 VM 인스턴스를 하나 이상 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합의 모든 VM 인스턴스가 다시 시작됩니다. 다음 예제에서는 *1* 인스턴스를 다시 시작합니다.

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹을 삭제하면 VM 인스턴스, 가상 네트워크 및 디스크와 같이 포함된 리소스도 모두 삭제됩니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure CLI를 사용하여 몇 가지 기본적인 확장 집합 만들기 및 관리 작업을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 가상 머신 확장 집합 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * 수동으로 확장 집합 크기 조정
> * 일반적인 확장 집합 관리 작업 수행

확장 집합 디스크에 대해 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합으로 데이터 디스크 사용](tutorial-use-disks-cli.md)

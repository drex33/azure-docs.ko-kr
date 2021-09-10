---
title: CLI를 사용하여 Marketplace 구매 계획 정보 찾기 및 사용
description: Azure CLI를 사용하여 이미지 URN 및 Marketplace VM 이미지에 대한 구매 계획 매개 변수(Publisher, Offer, SKU 및 Version)를 찾는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: b0df8d446716363f21c468838523ffe4804f2e9c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691908"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Marketplace 이미지 정보 찾기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 항목에서는 Azure CLI를 사용하여 Azure Marketplace에서 VM 이미지를 찾는 방법을 설명합니다. CLI, Resource Manager 템플릿 또는 기타 도구를 사용하여 프로그래밍 방식으로 VM을 생성할 때 이 정보를 사용하여 Marketplace 이미지를 지정합니다.

또한 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 또는 [Azure PowerShell](../windows/cli-ps-findimage.md)을 사용하여 사용할 수 있는 이미지와 제품을 찾을 수 있습니다. 

## <a name="terminology"></a>용어

Azure의 Marketplace 이미지에는 다음과 같은 특성이 있습니다.

* **Publisher**: 이미지를 만든 조직입니다. 예: Canonical, MicrosoftWindowsServer
* **Offer**: 게시자가 만든 관련 이미지 그룹의 이름입니다. 예: UbuntuServer, WindowsServer
* **SKU**: 제공의 인스턴스(예: 배포의 주 릴리스)입니다. 예: 18.04-LTS, 2019-Datacenter
* **Version**: 이미지 SKU의 버전 번호입니다. 

이러한 값은 콜론(:)으로 구분된 값을 결합하여 개별적으로 또는 이미지 *URN* 으로 전달할 수 있습니다. 예: *Publisher*:*Offer*:*Sku*:*Version*. URN에서 버전 번호를 `latest`로 바꿔 이미지의 최신 버전을 사용할 수 있습니다. 

이미지 게시자가 추가 라이선스 및 추가 약관을 제공하는 경우 이에 동의해야 이미지를 사용할 수 있습니다.  자세한 내용은 [구매 계획 정보 확인](#check-the-purchase-plan-information)을 참조하세요.



## <a name="list-popular-images"></a>인기 있는 이미지 나열

`--all` 옵션을 사용하지 않고 [az vm image list](/cli/azure/vm/image) 명령을 실행하여 Azure Marketplace에서 인기있는 VM 이미지의 목록을 확인합니다. 예를 들어, 다음 명령을 실행하여 테이블 형식으로 캐시된 인기있는 이미지 목록을 표시합니다.

```azurecli
az vm image list --output table
```

출력에는 이미지 URN이 포함됩니다. *UbuntuLTS* 와 같은 인기 있는 이미지를 위해 만든 단축 버전인 *UrnAlias* 를 사용할 수도 있습니다.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>특정 이미지 찾기

Marketplace에서 특정 VM 이미지를 찾으려면 `az vm image list` 명령에 `--all` 옵션을 사용합니다. 명령의 이 버전은 완료하는 데 다소 시간이 걸리며 긴 출력을 생성할 수 있으므로 일반적으로 `--publisher` 또는 다른 매개 변수를 기준으로 목록을 필터링합니다. 

예를 들어, 다음 명령은 모든 Debian 제품을 표시합니다(`--all` 스위치 없이는 공용 이미지의 로컬 캐시만을 검색함).

```azurecli
az vm image list --offer Debian --all --output table 
```

부분 출력: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>사용할 수 있는 모든 이미지 보기
 
위치에서 이미지를 찾는 또 다른 방법은 [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) 및 [az vm image list-skus](/cli/azure/vm/image)의 순서로 명령을 실행하는 것입니다. 이러한 명령을 사용하여 값을 결정합니다.

1. 위치에 대한 이미지 게시자를 나열합니다. 이 예제에서는 *미국 서부* 지역을 살펴 봅니다.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. 지정된 게시자에 제안을 나열합니다. 이 예제에서는 *Canonical* 을 게시자로 추가합니다.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. 지정된 제안에 SKU를 나열합니다. 이 예제에서는 *UbuntuServer* 를 제품으로 추가합니다.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. 지정된 게시자, 제품 및 SKU의 경우 이미지의 모든 버전을 표시합니다. 이 예제에서는 *18.04-LTS* 를 SKU로 추가합니다.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

[az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만드는 경우 `--image` 매개 변수로 이 URN 열의 값을 전달합니다. URN에서 버전 번호를 "최신"으로 바꾸어 이미지의 최신 버전을 사용할 수도 있습니다. 

Resource Manager 템플릿을 사용하여 VM을 배포하는 경우 `imageReference` 속성에 이미지 매개 변수를 개별적으로 설정합니다. [템플릿 참조](/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.


## <a name="check-the-purchase-plan-information"></a>구매 계획 정보 확인

Azure Marketplace의 일부 VM 이미지를 프로그래밍 방식으로 배포하려면 동의가 필요한 추가 라이선스 및 구매 약관이 있습니다.  

이러한 이미지에서 VM을 배포하려면 처음 사용할 때 구독당 한 번씩 이미지의 약관에 동의해야 합니다. 또한 해당 이미지에서 VM을 배포하려면 *구매 계획* 매개 변수를 지정해야 합니다.

이미지의 구매 계획 정보를 보려면 이미지의 URN으로 [az vm image show](/cli/azure/image) 명령을 실행합니다. 출력의 `plan` 속성이 `null`이 아닌 경우, 이미지에는 프로그램 방식으로 배포하기 전에 동의해야 하는 약관이 있습니다.

예를 들어, Canonical Ubuntu Server 18.04 LTS 이미지는 `plan` 정보가 `null`이기 때문에 추가 약관이 없습니다.

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

출력:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

RabbitMQ Certified by Bitnami 이미지에 유사한 명령을 실행하면 `name`, `product` 및 `publisher`와 같은 `plan` 속성이 표시됩니다. (일부 이미지에는 `promotion code` 속성도 있습니다.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
출력:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

이 이미지를 배포하려면 해당 이미지를 사용하여 VM 배포 시 약관에 동의하고 구매 플랜 매개 변수를 제공해야 합니다.

## <a name="accept-the-terms"></a>약관에 동의

사용 조건을 확인하고 동의하려면 [az vm image terms](/cli/azure/vm/image/terms) 명령을 사용합니다. 약관에 동의하면 구독에서 프로그래밍 방식 배포를 사용하도록 설정됩니다. 이미지의 구독마다 약관에 한 번만 동의하면 됩니다. 예를 들어:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

출력에는 사용 조건에 대한 `licenseTextLink`가 포함되며 `accepted`의 값이 `true`임을 나타냅니다.

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

약관에 동의하려면 다음을 입력하세요.

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>이미지 매개 변수를 사용하여 새 VM 배포

이미지에 대한 정보와 함께 `az vm create` 명령을 사용하여 이를 배포할 수 있습니다. 

Canonical에서 최신 Ubuntu Server 18.04 이미지와 같은 계획 정보가 없는 이미지를 배포하려면 `--image`에 대한 URN을 전달합니다.

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


RabbitMQ Certified by Bitnami 이미지와 같은 구매 플랜 매개 변수가 포함된 이미지의 경우 `--image`에 대한 URN을 전달하고 구매 플랜 매개 변수도 제공합니다.

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

이미지 약관에 동의하는 방법에 대한 메시지가 표시되면 [약관에 동의](#accept-the-terms)섹션을 검토합니다. `az vm image accept-terms` 출력이 이미지에 대한 약관을 동의했음을 보여주는 `"accepted": true,` 값을 반환하는지 확인합니다.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>구매 계획 정보와 함께 기존 VHD 사용

유료 Azure Marketplace 이미지를 사용하여 만든 VM의 기존 VHD가 있는 경우 해당 VHD에서 새 VM을 만들 때 구매 계획 정보를 제공해야 할 수 있습니다. 

원본 VM이 아직 있거나 동일한 Marketplace 이미지를 사용하여 만든 다른 VM이 있는 경우 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)를 사용하여 계획 이름, 게시자 및 제품 정보를 가져올 수 있습니다. 이 예제에서는 *myResourceGroup* 리소스 그룹에서 *myVM* 이라는 VM을 가져온 다음 구매 계획 정보를 표시합니다.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

원본 VM을 삭제하기 전에 계획 정보를 가져오지 않은 경우 [지원 요청](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출할 수 있습니다. VM 이름, 구독 ID 및 삭제 작업의 타임 스탬프가 필요합니다.

계획 정보를 받으면 `--attach-os-disk` 매개 변수를 사용하여 새 VM을 만들어 VHD를 지정할 수 있습니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>다음 단계
이미지 정보를 사용하여 가상 머신을 빠르게 만들려면 [Azure CLI로 Linux VM 만들기 및 관리](tutorial-manage-vm.md)를 참조하세요.

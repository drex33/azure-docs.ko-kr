---
title: Linux 가상 머신 확장 집합에 대한 Azure 하이브리드 혜택
description: Azure에서 실행 중인 Linux 가상 머신에서 비용을 절감하기 위해 Azure 하이브리드 혜택을 가상 머신 확장 집합에 적용하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: d8671cab8e81b47316d14c9cabb4b803111643be
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768484"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set"></a>Linux 가상 머신 확장 집합에 대한 Azure 하이브리드 혜택

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 균일 확장 집합

**Linux 가상 머신 확장 집합에 대한 Azure 하이브리드 혜택이 현재 GA 상태입니다**. AHB 혜택은 RHEL 및 SLES [가상 머신 확장 집합](./overview.md) 실행 비용을 줄이는 데 도움이 될 수 있습니다.

이 혜택을 사용하면 확장 집합의 인프라 비용만 지불하면 됩니다. 이 혜택은 모든 RHEL 및 SLES Marketplace PAYG(종량제) 이미지에 제공됩니다.


>[!NOTE]
> 이 문서에서는 Linux VMSS에 대한 Azure 하이브리드 혜택을 설명합니다. [여기에서 사용할 수 있는 개별 문서, [Linux VM용 AHB](../virtual-machines/linux/azure-hybrid-benefit-linux.md)가 있으며, 2020년 11월부터 Azure 고객이 이미 사용할 수 있습니다.

## <a name="benefit-description"></a>혜택 설명
Azure 하이브리드를 사용하면 Red Hat이나 SUSE의 기존 클라우드 액세스 라이선스를 사용하고 가상 머신 확장 집합 인스턴스를 BYOS(Bring-Your-Own-Subscription) 청구로 유연하게 변환할 수 있습니다. 

PAYG 마켓플레이스 이미지에서 배포된 가상 머신 확장 집합은 인프라와 소프트웨어 요금을 모두 부과합니다. AHB를 사용하면 PAYG 인스턴스를 다시 배포하지 않고 BYOS 청구 모델로 변환할 수 있습니다.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Linux VM에 대한 Azure 하이브리드 혜택의 비용 시각화.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Linux에 대한 Azure 하이브리드 혜택 자격의 범위
Azure 하이브리드 혜택은 Azure Marketplace의 모든 RHEL 및 SLES PAYG 이미지에 사용할 수 있습니다. Azure Marketplace의 RHEL 또는 SLES BYOS 이미지나 사용자 지정 이미지에는 아직 혜택이 제공되지 않습니다.

Linux VM으로 이미 혜택을 사용하고 있다면 Azure Dedicated Host 인스턴스와 SQL 하이브리드 혜택은 Azure 하이브리드 혜택에 적격하지 않습니다.

## <a name="get-started"></a>시작

### <a name="red-hat-customers"></a>Red Hat 고객

RHEL에 대한 Azure 하이브리드 혜택은 다음 두 가지 기준을 모두 충족하는 Red Hat 고객에게 제공됩니다.

- Azure에서 사용하기에 적격한 사용 또는 미사용 중인 RHEL 구독 보유
- [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 프로그램을 사용하여 Azure에서 사용할 수 있도록 이러한 구독을 하나 이상 활성화

> [!IMPORTANT]
> [클라우드 액세스](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 프로그램에서 올바른 구독을 사용하도록 설정했는지 확인합니다.

Red Hat에 대한 혜택을 사용하려면 다음을 수행합니다.

1. [Red Hat Cloud Access 고객 인터페이스](https://access.redhat.com/management/cloud)를 사용하여 Azure에서 적격 RHEL 구독을 사용으로 설정합니다.

   그러면 Red Hat Cloud Access 사용 설정 프로세스 중에 제공하는 Azure 구독이 Azure 하이브리드 혜택 기능을 사용하도록 허용됩니다.
1. 기존 및 새 RHEL PAYG 가상 머신 확장 집합에 Azure 하이브리드 혜택을 적용합니다. Azure Portal 또는 Azure CLI를 사용하여 혜택을 사용하도록 설정할 수 있습니다.
1. RHEL VM에 대한 업데이트 소스 구성 및 RHEL 구독 규정 준수 지침을 위해 권장되는 [다음 단계](https://access.redhat.com/articles/5419341)를 따르세요.


### <a name="suse-customers"></a>SUSE 고객

SUSE에 대한 혜택을 사용하려면 다음을 수행합니다.

1. SUSE 퍼블릭 클라우드 프로그램에 등록합니다.
1. Azure Portal 또는 Azure CLI를 통해 새로 만들거나 기존의 가상 머신 확장 집합에 혜택을 적용합니다.
1. 별도의 업데이트 소스로 혜택을 받는 VM을 등록합니다.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Azure Portal에서 혜택 사용 및 사용 안 함 설정 
### <a name="azure-portal-example-to-enable-the-benefit-during-creation"></a>만드는 동안 Azure Portal에서 혜택을 사용하도록 설정하는 예제:
1. [Microsoft Azure Portal](https://portal.azure.com/)을 방문합니다.
1. 포털에서 '가상 머신 확장 집합 만들기' 페이지로 이동합니다.
 ![VMSS를 만드는 동안의 AHB](./media/azure-hybrid-benefit-linux/create-vmss-ahb.png)
1. AHB 변환을 사용하도록 설정하고 Cloud Access 라이선스를 사용하려면 확인란을 클릭합니다.
 ![VMSS 확인란을 만드는 동안의 AHB](./media/azure-hybrid-benefit-linux/create-vmss-ahb-checkbox.png)
1. 다음과 같은 일련의 지침에 따라 가상 머신 확장 집합을 만듭니다.
1. **구성** 블레이드를 확인하면 사용하도록 설정된 옵션이 보입니다. 
![만든 후 AHB OS 블레이드](./media/azure-hybrid-benefit-linux/create-vmss-ahb-os-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-virtual-machine-scale-set"></a>Azure Portal에서 기존 가상 머신 확장 집합에 대한 혜택을 사용하도록 설정하는 예제:
1. [Microsoft Azure Portal](https://portal.azure.com/)을 방문합니다.
1. 변환을 적용하려는 '가상 머신 확장 집합' 페이지를 엽니다.
1. 왼쪽의 **운영 체제** 옵션으로 이동합니다. 라이선스 섹션이 표시됩니다. AHB 변환을 사용하도록 설정하려면 ‘예’ 라디오 단추를 선택하고 확인란을 선택합니다.
![생성 후 AHB 구성 블레이드](./media/azure-hybrid-benefit-linux/create-vmss-ahb-os-blade.png)



## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Azure CLI를 사용하여 혜택 사용 및 사용 안 함 설정

`az vmss update` 명령을 사용하여 기존 VM을 업데이트할 수 있습니다. RHEL VM의 경우 `RHEL_BYOS`의 `--license-type` 매개 변수를 사용하여 명령을 실행합니다. SLES VM의 경우 `SLES_BYOS`의 `--license-type` 매개 변수를 사용하여 명령을 실행합니다.

### <a name="cli-example-to-enable-the-benefit"></a>혜택을 사용하도록 설정하는 CLI 예시
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>혜택을 사용하지 않도록 설정하는 CLI 예시
혜택을 사용하지 않도록 설정하려면 `None`의 `--license-type` 값을 사용하세요.

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> 확장 집합에는 VM이 최신 확장 집합 모델로 최신 상태를 유지하는 방법을 결정하는 [“업그레이드 정책”](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)이 있습니다. 따라서 VMSS에 ‘자동’ 업그레이드 정책이 있으면 VM 인스턴스가 업데이트될 때 AHB 혜택이 자동으로 적용됩니다. VMSS에 ‘롤링’ 업그레이드 정책이 있으면 예약된 업데이트에 따라 AHB가 적용됩니다.
‘수동’ 업그레이드 정책의 경우 기존 VM마다 “수동 업그레이드”를 수행해야 합니다.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>“수동 업그레이드” 정책의 경우 가상 머신 확장 집합 인스턴스를 업그레이드하는 CLI 예제 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>가상 머신 확장 집합을 만들 때 Azure 하이브리드 혜택 적용 
Azure 하이브리드 혜택을 기존 종량제 가상 머신 확장 집합에 적용하는 것 외에도 가상 머신 확장 집합을 만들 때 호출할 수 있습니다. 이를 통해 일석삼조의 혜택을 누릴 수 있습니다.
- 같은 이미지와 프로세스를 사용하여 PAYG와 BYOS 가상 머신 확장 집합 인스턴스를 모두 프로비저닝할 수 있습니다.
- BYOS 전용 이미지를 사용하는 경우에는 불가능한 향후 라이선스 모드 변경을 할 수 있습니다.
- 가상 머신 확장 집합 인스턴스는 기본적으로 RHUI(Red Hat 업데이트 인프라)에 연결되어 최신 상태로 안전하게 유지됩니다. 배포 후 업데이트된 메커니즘을 언제든지 변경할 수 있습니다.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>AHB 혜택을 사용하여 가상 머신 확장 집합을 만드는 CLI 예제
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>다음 단계
* [Azure CLI를 사용하여 Azure 하이브리드 혜택에 대한 VM을 만들고 업데이트하고 라이선스 형식(RHEL_BYOS, SLES_BYOS)을 추가하는 방법 알아보기](/cli/azure/vmss)
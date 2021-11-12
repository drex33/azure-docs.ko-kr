---
title: 가상 머신의 Azure 기밀 컴퓨팅 솔루션
description: 가상 머신의 Azure 기밀 컴퓨팅 솔루션에 대해 알아봅니다.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1805b153d33915c2ec252c0e4a932eceb710b59f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331902"
---
# <a name="solutions-on-azure-for-intel-sgx"></a>Intel SGX용 Azure의 솔루션

이 문서에서는 Intel SGX VM 배포에 대한 정보를 다룹니다.

### <a name="current-available-sizes-and-regions"></a>현재 사용 가능한 크기 및 지역

사용 가능한 지역 및 가용성 영역에서 Intel SGX VM 크기 목록을 얻으려면 [Azure CLI](/cli/azure/install-azure-cli-windows)다음 명령을 실행합니다.

```azurecli-interactive
az vm list-skus `
    --size Standard_DC `
    --all `
    --output table
```

### <a name="dedicated-host-requirements"></a>전용 호스트 요구 사항

**Standard_DC8_v2**, **Standard_DC48s_v3** **Standard_DC48ds_v3** 가상 머신을 배포하면 전체 호스트가 차지하며 다른 테넌트 또는 구독과 공유되지 않습니다. 이 VM SKU 제품군은 일반적으로 전용 호스트 서비스가 있으면 충족되는 규정 준수 및 보안 규정 요구 사항을 충족하려면 필요할 수 있는 격리를 제공합니다. 이러한 크기를 선택하면 물리적 호스트 서버는 EPC 메모리를 포함하여 사용 가능한 모든 하드웨어 리소스를 가상 머신에만 할당합니다. 이 배포는 다른 Azure VM 제품군에서 제공하는 [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) 서비스와 다릅니다.


## <a name="deployment-considerations"></a>배포 고려 사항

빠른 시작 자습서에 따라 10분 이내에 DCsv2 시리즈 가상 머신을 배포합니다. 

- **Azure 구독** - 기밀 컴퓨팅 VM 인스턴스를 배포하려면 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우 적절한 Azure 계산 코어 양에 대한 할당량이 없습니다.

- **가격 책정 및 지역별 가용성** - Virtual Machine 가격 책정 페이지에서 DCsv2, DCsv3 및 DCdsv3 VM에 대한 [가격 책정을 찾습니다.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 에서 Azure 지역의 가용성을 확인하세요.

- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 구독에 따라서도 DCsv2 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-portal/supportability/per-vm-quota-requests.md) 합니다. 기본 제한은 구독 범주에 따라 달라질 수 있습니다.

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **크기 조정** – 특수 하드웨어로 인해 동일한 크기 제품군 내에서만 이러한 인스턴스의 크기를 조정 할 수 있습니다. 예를 들어 DCsv2 시리즈 VM 크기는 한 DCsv2 시리즈에서 다른 DCsv2 시리즈로만 조정할 수 있습니다. 

- **이미지** - 기밀 컴퓨팅 인스턴스에 Intel SGX(Software Guard Extension) 지원을 제공하기 위해 모든 배포를 2세대 이미지에서 실행해야 합니다. Azure 기밀 컴퓨팅은 Ubuntu 20.04 Gen 2, Ubuntu 18.04 Gen 2 및 Windows Server 2019 Gen 2에서 실행되는 워크로드를 지원합니다. 지원되는 시나리오 및 지원되지 않는 시나리오에 대한 자세한 내용은 [Azure의 2세대 VM](../virtual-machines/generation-2.md) 지원에 대해 알아보세요. 

- **Storage** – DCsv2 시리즈는 DC8_v2 제외하고 표준 SSD 및 Premium SSD를 지원합니다. DCsv3 및 DCdsv3 시리즈는 SSD 및 Ultra Disk Premium 표준 SSD 지원합니다.

- **디스크 암호화** - 기밀 컴퓨팅 인스턴스는 현재 디스크 암호화를 지원하지 않습니다. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항

Azure에서 가상 머신을 사용하는 경우 모든 가동 중지 시간을 방지하기 위해 높은 가용성과 재해 복구 솔루션을 구현해야 합니다. 

Azure 기밀 컴퓨팅은 현재 가용성 영역을 통해 영역 중복성을 지원하지 않습니다. 기밀 컴퓨팅에 대한 최고 가용성 및 중복성을 위해 [가용성 집합](../virtual-machines/availability-set-overview.md)을 사용합니다. 하드웨어 제한으로 인해 기밀 컴퓨팅 인스턴스의 가용성 집합에는 최대 10개의 업데이트 도메인만 사용할 수 있습니다. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿을 사용하여 배포

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 구독에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 배포 이후 액세스 제어, 잠금 및 태그와 같은 관리 기능을 사용하여 리소스를 보호하고 구성할 수 있습니다.

ARM 템플릿에 대한 자세한 내용은 [템플릿 배포 개요](../azure-resource-manager/templates/overview.md)를 참조하세요.

ARM 템플릿을 사용하여 배포하려면 Virtual Machine 리소스 를 [활용합니다.](../virtual-machines/windows/template-description.md) **vmSize** 및 **imageReference** 에 대한 올바른 속성을 지정해야 합니다.

### <a name="vm-size"></a>VM 크기

Virtual Machine 리소스의 ARM 템플릿에서 다음 크기 중 하나를 지정합니다. 이 문자열은 **속성** 에서 **vmSize** 로 배치됩니다.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2",
        "Standard_DC1s_v3",
        "Standard_DC2s_v3",
        "Standard_DC4s_v3",
        "Standard_DC8s_v3",
        "Standard_DC16s_v3",
        "Standard_DC24s_v3",
        "Standard_DC32s_v3",
        "Standard_DC48s_v3",
        "Standard_DC1ds_v3",
        "Standard_DC2ds_v3",
        "Standard_DC4ds_v3",
        "Standard_DC8ds_v3",
        "Standard_DC16ds_v3",
        "Standard_DC24ds_v3",
        "Standard_DC32ds_v3",
        "Standard_DC48ds_v3",
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 이미지

**속성** 아래의 **storageProfile** 에서 이미지를 참조해야 합니다. **imageReference** 에 대해 다음 이미지 중 하나만 사용합니다.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>다음 단계 

이 문서에서는 Intel SGX VM을 만들 때 필요한 자격 및 구성에 대해 배웠습니다.

> [!div class="nextstepaction"]
> [Azure Marketplace 사용하여 Intel SGX VM 만들기](quick-create-marketplace.md)

---
title: AMD 프로세서의 Azure 기밀 가상 머신 옵션 (미리 보기)
description: Azure 기밀 컴퓨팅은 심각도-.SNP 기술로 지원 되는 AMD 프로세서에서 실행 되는 기밀 가상 컴퓨터에 대 한 여러 옵션을 제공 합니다.
author: edendcohen
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: edcohen
ms.openlocfilehash: 26664d86462c3edda222f63807a47fe84b895bb5
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132943474"
---
# <a name="azure-confidential-vm-options-on-amd-preview"></a>AMD의 Azure 기밀 VM 옵션 (미리 보기)

> [!IMPORTANT]
> Azure 기밀 컴퓨팅의 기밀 Vm (가상 머신)은 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 기밀 컴퓨팅은 [Amd Secure Encrypted Virtualization-Secure 중첩 페이징 (심각도)](https://www.amd.com/system/files/TechDocs/SEV-SNP-strengthening-vm-isolation-with-integrity-protection-and-more.pdf) 기술로 지원 되는 amd 프로세서에서 실행 되는 기밀 vm에 대 한 여러 옵션을 제공 합니다.

## <a name="sizes"></a>크기

다음 크기 제품군의 AMD 프로세서에서 실행 되는 기밀 Vm을 만들 수 있습니다.

| 크기 패밀리          | Description                                                                         |
| ------------------ | ----------------------------------------------------------------------------------- |
| **DCasv5 시리즈**  | 원격 저장소를 사용 하는 기밀 VM. 로컬 임시 책상이 없습니다.                  |
| **DCadsv5 시리즈** | 로컬 임시 디스크를 사용 하는 기밀 VM.                                        |
| **ECasv5 시리즈**  | 원격 저장소를 사용 하는 메모리 액세스에 최적화 된 기밀 VM. 로컬 임시 디스크가 없습니다. |
| **ECadsv5 시리즈** | 로컬 임시 디스크를 사용 하는 메모리 액세스에 최적화 된 기밀 VM.                       |

> [!NOTE]
> 메모리 액세스에 최적화 된 기밀 Vm은 vCPU 수 당 메모리 비율을 두 배로 제공 합니다.

## <a name="azure-cli-commands"></a>Azure CLI 명령

[Azure Command-Line 인터페이스 (Azure CLI)](/cli/azure/install-azure-cli) 를 기밀 vm과 함께 사용할 수 있습니다.

기밀 VM 크기 목록을 보려면 다음 명령을 실행 합니다. `<vm-series>`사용할 계열로 대체 합니다. 예: `DCASv5`, `ECASv5`, `DCADSv5` 또는 `ECADSv5`. 출력은 사용 가능한 지역 및 가용성 영역에 대 한 정보를 표시 합니다.

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standard<vm-series>Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

자세한 목록을 보려면 대신 다음 명령을 실행 합니다.

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standard<vm-series>Family']" 
```

## <a name="deployment-considerations"></a>배포 고려 사항

기밀 Vm을 배포 하기 전에 다음 설정 및 선택 사항을 고려 하십시오.

### <a name="azure-subscription"></a>Azure 구독

기밀 VM 인스턴스를 배포 하려면 종 량 제 구독 또는 기타 구매 옵션을 고려 하세요. [Azure 무료 계정을](https://azure.microsoft.com/free/)사용 하는 경우 할당량은 적절 한 양의 azure 계산 코어를 허용 하지 않습니다.

기본값에서 Azure 구독의 코어 할당량을 늘려야 할 수도 있습니다. 기본 제한은 구독 범주에 따라 달라 집니다. 또한 구독은 기밀 VM 크기를 포함 하 여 특정 VM 크기 제품군에 배포할 수 있는 코어 수를 제한할 수 있습니다. 

할당량 증가를 요청 하려면 [온라인 고객 지원 요청을 여세요](../azure-portal/supportability/per-vm-quota-requests.md). 

대규모 용량이 필요한 경우 Azure 지원에 문의 하세요. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 사용 하는 코어에 대해서만 요금이 부과 됩니다.

### <a name="pricing"></a>가격 책정

가격 책정 옵션은 [Linux Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)을 참조 하세요. 

### <a name="regional-availability"></a>국가별 가용성

가용성 정보는 [Azure 지역에서 사용할 수 있는 VM 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 참조 하세요.

### <a name="resizing"></a>크기 조정

기밀 Vm은 특수 한 하드웨어에서 실행 되므로 동일한 제품군 내 에서만 인스턴스 크기를 조정할 수 있습니다. 예를 들어 DCasv5 시리즈 VM이 있는 경우 다른 DCasv5 시리즈 인스턴스 유형 으로만 크기를 조정할 수 있습니다.

기밀 vm에 대 한 비 기밀 VM의 크기를 조정할 수 없습니다.

### <a name="disk-encryption"></a>디스크 암호화

기밀 Vm에 대 한 OS 이미지는 특정 보안 및 호환성 요구 사항을 충족 해야 합니다. 정규화 된 이미지는 보안 탑재, 증명, 선택적인 [전체 디스크 암호화](confidential-vm-overview.md#full-disk-encryption)및 기본 클라우드 인프라의 격리를 지원 합니다. 이러한 이미지는 다음과 같습니다.

- Ubuntu 20.04 Gen 2
- Windows Server 2019 Gen 2
- Windows Server 2022 Gen 2

지원 되거나 지원 되지 않는 VM 시나리오에 대 한 자세한 내용은 [Azure의 2 세대 vm에 대 한 지원](../virtual-machines/generation-2.md)을 참조 하세요. 

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

기밀 Vm에 대 한 고가용성 및 재해 복구 솔루션을 만들 책임이 있습니다. 이러한 시나리오에 대 한 계획은 가동 중지 시간을 최소화 하는 데 도움이 됩니다.

### <a name="deployment-with-arm-templates"></a>ARM 템플릿을 사용 하 여 배포

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. 다음을 수행할 수 있습니다.

- 액세스 제어, 잠금 및 태그와 같은 관리 기능을 사용 하 여 배포 후 리소스를 보호 하 고 구성 합니다. 
- 관리 계층을 사용 하 여 Azure 구독에서 리소스를 만들고, 업데이트 하 고, 삭제 합니다.
- [Azure Resource Manager 템플릿 (ARM 템플릿)](../azure-resource-manager/templates/overview.md) 을 사용 하 여 AMD 프로세서에 기밀 vm을 배포 합니다. [기밀 vm에](https://aka.ms/CVMTemplate)사용할 수 있는 ARM 템플릿이 있습니다. 

매개 변수 섹션 ()에서 VM에 대 한 다음 속성을 지정 해야 합니다 `parameters` . 

- VM 크기 ( `vmSize` ). 다른 [기밀 VM 제품군 및 크기](#sizes)를 선택 합니다.
- OS 이미지 이름 ( `osImageName` )입니다. [정규화 된 OS 이미지](#disk-encryption)를 선택 합니다.
- 디스크 암호화 유형 ( `securityType` ). VMGS 전용 암호화 ( `VMGuestStateOnly` ) 또는 전체 OS 디스크 사전 암호화 () 중에서 선택 `DiskWithVMGuestState` 하면 프로 비전 시간이 길어질 수 있습니다.

## <a name="next-steps"></a>다음 단계 

> [!div class="nextstepaction"]
> [Azure Portal에서 AMD에 기밀 VM 배포](quick-create-confidential-vm-portal-amd.md)

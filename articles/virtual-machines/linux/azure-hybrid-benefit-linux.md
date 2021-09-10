---
title: Azure 하이브리드 혜택 및 Linux VM
description: Azure 하이브리드 혜택이 Azure에서 실행되는 Linux Virtual Machines의 비용을 절감하는 데 어떻게 도움이 되는지 알아보세요.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: c794d3e18eaf25241cb9c55c14a32e4e5c9ff7b0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692260"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Azure 하이브리드 혜택을 Linux Virtual Machines에 적용하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure 하이브리드 혜택은 클라우드에서 RHEL(Red Hat Enterprise Linux) 및 SLES(SUSE Linux Enterprise Server) VM(가상 머신)을 실행하는 비용을 크게 절감할 수 있도록 돕는 라이선스 혜택입니다. 이 혜택으로 RHEL 또는 SLES 구독에 소프트웨어 비용이 포함되므로 VM의 인프라 요금만 지불하면 됩니다. 이 혜택은 모든 RHEL 및 SLES Marketplace PAYG(종량제) 이미지에 제공됩니다.

이제 Linux VM을 위한 Azure 하이브리드 혜택을 공개적으로 사용할 수 있습니다.

## <a name="benefit-description"></a>혜택 설명

Azure 하이브리드 혜택을 통해 Azure의 기존 RHEL 및 SLES PAYG VM을 BYOS(Bring Your Own Subscription) 청구로 변환하여 온-프레미스 RHEL 및 SLES 서버를 Azure로 마이그레이션할 수 있습니다. 일반적으로 Azure의 PAYG 이미지에서 배포된 VM은 인프라 요금과 소프트웨어 요금을 모두 청구합니다. Azure 하이브리드 혜택을 사용하면 PAYG VM을 재배치하지 않고 BYOS 청구 모델로 변환할 수 있으므로 가동 중지 시간 위험을 방지할 수 있습니다.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Linux VM에 대한 Azure 하이브리드 혜택의 비용 시각화.":::

RHEL 또는 SLES VM에서 혜택을 사용하도록 설정한 후에는 일반적으로 PAYG VM에서 발생하는 추가 소프트웨어 요금이 더 이상 청구되지 않습니다. 대신 VM에 BYOS 요금이 부과되기 시작하며, 이는 컴퓨팅 하드웨어 요금만 포함하고 소프트웨어 요금은 포함하지 않습니다.

혜택을 사용하도록 설정한 VM을 다시 PAYG 청구 모델로 변환할 수도 있습니다.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux VM에 대한 Azure 하이브리드 혜택 자격의 범위

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

1. [Red Hat Cloud Access 고객 인터페이스](https://access.redhat.com/management/cloud)를 사용하여 Azure에서 사용할 수 있도록 하나 이상의 적격 RHEL 구독을 활성화합니다.

   그러면 Red Hat Cloud Access 사용 설정 프로세스 중에 제공하는 Azure 구독이 Azure 하이브리드 혜택 기능을 사용하도록 허용됩니다.
1. Azure Marketplace PAYG 이미지를 통해 배포한 기존 RHEL PAYG VM 및 새로운 RHEL VM에 Azure 하이브리드 혜택을 적용합니다. Azure Portal 또는 Azure CLI를 사용하여 혜택을 사용하도록 설정할 수 있습니다.
1. RHEL VM에 대한 업데이트 소스 구성 및 RHEL 구독 규정 준수 지침을 위해 권장되는 [다음 단계](https://access.redhat.com/articles/5419341)를 따르세요.


### <a name="suse-customers"></a>SUSE 고객

다음이 있는 고객은 SUSE에 대한 Azure 하이브리드 혜택을 사용할 수 있습니다.

- Azure에서 사용할 수 있는 사용되지 않는 SUSE 구독
- Azure로 이동해야 하는 온-프레미스에서 사용할 하나 이상의 활성 SUSE 구독
- Azure에서 사용하기 위해 SUSE 고객 센터에서 활성화한 구매한 구독 

> [!IMPORTANT]
> Azure에서 사용할 올바른 구독을 선택해야 합니다.

SUSE에 대한 혜택을 사용하려면 다음을 수행합니다.

1. SUSE 또는 SUSE 배포자에서 구매한 구독을 [SUSE 고객 센터](https://scc.suse.com)에 등록합니다.
2. SUSE 고객 센터에서 구독을 활성화합니다.
3. 혜택을 받는 VM을 SUSE 고객 센터에 등록하여 SUSE 고객 센터에서 업데이트를 받습니다.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Azure Portal에서 혜택 사용 및 사용 안 함 설정

Azure Portal에서 기존 VM의 혜택을 사용하도록 설정하거나 VM을 만들 때 새 VM의 혜택을 사용하도록 설정할 수 있습니다.

### <a name="enable-the-benefit-for-an-existing-vm-in-the-azure-portal"></a>Azure Portal에서 기존 VM의 혜택을 사용하도록 설정

기존 VM의 혜택을 사용하도록 설정하려면

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.
1. 변환을 적용할 가상 머신 페이지를 엽니다.
1. 왼쪽의 **구성** 옵션으로 이동합니다. 라이선스 섹션이 표시됩니다. AHB 변환을 사용하도록 설정하려면 ‘예’ 라디오 단추를 선택하고 확인란을 선택합니다.
![생성 후 AHB 구성 블레이드](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="enable-the-benefit-when-you-create-the-vm-in-the-azure-portal"></a>Azure Portal에서 VM을 만들 때 혜택을 사용하도록 설정

VM을 만들 때 혜택을 사용하도록 설정하려면(SUSE 워크플로는 여기에 표시된 RHEL 예제와 동일함)

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.
1. Portal에서 ‘가상 머신 만들기’ 페이지로 이동합니다.
 ![VM을 만드는 동안의 AHB](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. AHB 변환을 사용하도록 설정하고 Cloud Access 라이선스를 사용하려면 확인란을 클릭합니다.
 ![VM 확인란을 만드는 동안의 AHB](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. 다음 지침에 따라 가상 머신을 만듭니다.
1. **구성** 블레이드를 확인하면 사용하도록 설정된 옵션이 보입니다. 
![생성 후 AHB 구성 블레이드](./media/azure-hybrid-benefit/create-configuration-blade.png)

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Azure CLI에서 혜택 사용 및 사용 안 함 설정

`az vm update` 명령을 사용하여 기존 VM을 업데이트할 수 있습니다. RHEL VM의 경우 `RHEL_BYOS`의 `--license-type` 매개 변수를 사용하여 명령을 실행합니다. SLES VM의 경우 `SLES_BYOS`의 `--license-type` 매개 변수를 사용하여 명령을 실행합니다.

### <a name="cli-example-to-enable-the-benefit"></a>혜택을 사용하도록 설정하는 CLI 예시
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>혜택을 사용하지 않도록 설정하는 CLI 예시
혜택을 사용하지 않도록 설정하려면 `None`의 `--license-type` 값을 사용하세요.

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>많은 수의 VM에서 혜택을 사용하도록 설정하는 CLI 예시
많은 수의 VM에서 혜택을 사용하도록 설정하기 위해 Azure CLI에서 `--ids` 매개 변수를 사용할 수 있습니다.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

다음 예제에서는 리소스 ID 목록을 가져오는 두 가지 메서드를 보여 줍니다. 하나는 리소스 그룹 수준에서, 다른 하나는 구독 수준에서 가져옵니다.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>VM 생성 시점에 Azure 하이브리드 혜택 적용하기
Azure 하이브리드 혜택은 기존의 종량제 VM에 적용할 수도 있지만, VM 생성 시점에 호출할 수도 있습니다. 이를 통해 일석삼조의 혜택을 누릴 수 있습니다.
- 동일한 이미지와 프로세스를 사용하여 PAYG 및 BYOS VM을 모두 프로비저닝할 수 있습니다.
- BYOS 전용 이미지를 사용하거나 자체 VM을 가져오는 경우에는 불가능한 향후 라이선스 모드 변경을 할 수 있습니다.
- VM은 기본적으로 RHUI(Red Hat 업데이트 인프라)에 연결되어 최신 상태로 안전하게 유지됩니다. 배포 후 업데이트된 메커니즘을 언제든지 변경할 수 있습니다.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>VM의 Azure 하이브리드 혜택 상태 확인
Azure CLI를 사용하거나 Azure Instance Metadata Service를 사용하여 VM의 Azure 하이브리드 혜택 상태를 볼 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

이와 같은 목적으로 `az vm get-instance-view` 명령을 사용할 수 있습니다. 응답에서 `licenseType` 필드를 찾습니다. `licenseType`필드가 존재하고 값이 `RHEL_BYOS` 또는 `SLES_BYOS`이면, VM에서 혜택을 사용할 수 있습니다.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

VM 자체 내에서 Azure Instance Metadata Service의 증명된 메타데이터를 쿼리하여 VM의 `licenseType` 값을 확인할 수 있습니다. `RHEL_BYOS` 또는 `SLES_BYOS`의 `licenseType` 값은 VM이 혜택을 사용하도록 설정된 것을 의미합니다. [증명된 메타데이터에 대해 자세히 알아보세요](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>규정 준수

### <a name="red-hat"></a>Red Hat

RHEL에 대한 Azure 하이브리드 혜택을 사용하는 고객은 Azure Marketplace RHEL 제공과 관련된 표준 [법적 약관](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) 및 [개인정보처리방침](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf)에 동의합니다.

RHEL에 대한 Azure 하이브리드 혜택을 사용하는 고객에게는 해당 VM에 소프트웨어 업데이트 및 패치를 제공하는 다음 세 가지 옵션이 있습니다.

- [Red Hat 업데이트 인프라](../workloads/redhat/redhat-rhui.md)(기본 옵션)
- Red Hat 위성 서버
- Red Hat 구독 관리자

RHUI 옵션을 선택한 고객은 Azure 하이브리드 혜택 RHEL VM을 위한 주 업데이트 소스로 해당 VM에 RHEL 구독을 연결하지 않고도 계속해서 RHUI를 사용할 수 있습니다. RHUI 옵션을 선택한 고객은 RHEL 구독 규정 준수를 보장할 책임이 있습니다.

Red Hat 위성 서버 또는 Red Hat 구독 관리자를 선택한 고객은 RHUI 구성을 제거한 후 Cloud Access 사용 가능 RHEL 구독을 Azure 하이브리드 혜택 RHEL VM에 연결해야 합니다.  

Red Hat 구독 규정 준수, 소프트웨어 업데이트, Azure 하이브리드 혜택 RHEL VM을 위한 소스에 대한 자세한 내용은 [Azure 하이브리드 혜택과 함께 RHEL 구독 사용에 대한 Red Hat 문서](https://access.redhat.com/articles/5419341)를 참조하세요.

### <a name="suse"></a>SUSE

Azure 하이브리드 혜택을 사용하는 고객은 클라우드 업데이트 인프라를 해당 VM에 소프트웨어 업데이트 및 패치를 제공하는 세 가지 옵션 중 하나로 이동해야 합니다.
- [SUSE 고객 센터](https://scc.suse.com)
- SUSE 관리자
- SUSE RMT(리포지토리 미러링 도구) 

SLES VM에 Azure 하이브리드 혜택을 사용하고 SLES PAYG에서 BYOS로 전환하거나 SLES BYOS에서 PAYG로 전환하는 방법에 대한 자세한 내용은 [SUSE Linux Enterprise 및 Azure 하이브리드 혜택](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)을 참조하세요. 

## <a name="azure-hybrid-benefit-on-reserved-instances"></a>예약 인스턴스에 대한 Azure 하이브리드 혜택 

Azure Reservations(Azure Reserved Virtual Machine Instances)를 통해 여러 제품에 1년 또는 3년 플랜을 선택함으로써 비용을 절감할 수 있습니다. [여기에서 예약 인스턴스](../../cost-management-billing/reservations/save-compute-costs-reservations.md)에 대해 자세히 알아볼 수 있습니다. Azure 하이브리드 혜택은 [RI(예약 가상 머신 인스턴스)](../../cost-management-billing/reservations/save-compute-costs-reservations.md#charges-covered-by-reservation)에 대해 사용할 수 있습니다. 

즉, RI를 사용하여 컴퓨팅 비용을 할인된 요금으로 구매한 경우, 거기에 더해 RHEL과 SUSE의 라이선스 비용에도 AHB 혜택을 적용할 수 있습니다. AHB 혜택을 RI 인스턴스에 적용하는 단계는 일반 VM에 적용하는 단계와 정확히 동일합니다.
![RIs를 위한 AHB](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Azure Marketplace에서 이미 RHEL 또는 SUSE PAYG 소프트웨어 예약을 구매한 경우, Azure 하이브리드 혜택을 사용하기 전에 예약 기간이 완료될 때까지 기다려 주세요.


## <a name="frequently-asked-questions"></a>질문과 대답
Q: 라이선스 유형 `RHEL_BYOS`를 SLES 이미지와 사용하거나 혹은 그 반대로 할 수 있나요?

A: 아니요, 불가능합니다. VM에서 실행 중인 배포와 일치하지 않는 라이선스 유형을 입력하려고 하면 청구 메타데이터가 업데이트되지 않습니다. 그러나 잘못된 라이선스 유형을 실수로 입력한 경우 VM을 올바른 라이선스 유형으로 다시 업데이트하면 혜택을 계속 사용할 수 있습니다.

Q: Red Hat Cloud Access에 등록했지만 내 RHEL VM에서 여전히 혜택을 사용할 수 없습니다. 어떻게 해야 하나요?

A: Red Hat Cloud Access 구독 등록이 Red Hat에서 Azure로 전파되는 데 시간이 걸릴 수 있습니다. 영업일로 하루가 지난 후에도 오류가 표시되면 Microsoft 고객 지원팀에 문의하세요.

Q: RHEL BYOS “골든 이미지”를 사용하여 VM을 배포했습니다. 해당 이미지에 대한 청구서를 BYOS에서 PAYG로 변환할 수 있나요?

A: 아니요, 불가능합니다. Azure 하이브리드 혜택은 종량제 이미지에서만 변환을 지원합니다.

Q: 온-프레미스에서(Azure Migrate, Azure Site Recovery 등을 통해) Azure로 자체 RHEL 또는 SLES 이미지를 업로드했습니다. 해당 이미지에 대한 청구를 BYOS에서 PAYG로 변환할 수 있나요?

A: 아니요, 불가능합니다. Azure 하이브리드 혜택 기능은 현재 Azure Marketplace의 RHEL 및 SLES 이미지에만 사용 가능합니다. 

Q: 온-프레미스에서(Azure Migrate, Azure Site Recovery 등을 통해) Azure로 자체 RHEL 또는 SLES 이미지를 업로드했습니다. Azure 하이브리드 혜택에서 혜택을 누리려면 수행해야 하는 작업이 있나요?

A: 아니요, 없습니다. 업로드하는 RHEL 또는 SLES 이미지는 이미 BYOS로 간주되며, Azure 인프라 비용에 대해서만 요금이 부과됩니다. 온-프레미스 환경과 마찬가지로 RHEL 구독 비용만 부담하면 됩니다. 

Q: Azure Marketplace RHEL 및 SLES SAP 이미지에서 배포된 VM에서 Azure 하이브리드 혜택을 사용할 수 있나요?

A: 예, 가능합니다. 라이선스 유형 `RHEL_BYOS`을 RHEL VM에 사용할 수 있고, `SLES_BYOS`을 Azure Marketplace RHEL 및 SLES SAP 이미지에서 배포된 VM을 변환하는 데 사용할 수 있습니다.

Q: RHEL 및 SLES용 Virtual Machine Scale Sets에서 Azure 하이브리드 혜택을 사용할 수 있나요?

A: 예, RHEL 및 SLES용 Virtual Machine Scale Sets의 Azure 하이브리드 혜택은 모든 사용자가 사용할 수 있습니다. [여기에서 해당 혜택과 사용 방법에 대한 자세한 정보](../../virtual-machine-scale-sets/azure-hybrid-benefit-linux.md)를 알아볼 수 있습니다. 

Q: RHEL 및 SLES에 대한 예약 인스턴스에서 Azure 하이브리드 혜택을 사용할 수 있나요?

A: 예, RHEL 및 SLES용 예약 인스턴스의 Azure 하이브리드 혜택은 모든 사용자가 사용할 수 있습니다. [여기에서 해당 혜택과 사용 방법에 대한 자세한 정보](#azure-hybrid-benefit-on-reserved-instances)를 알아볼 수 있습니다.

Q: RHEL 이미지에서 SQL Server를 위해 배포된 가상 머신에서 Azure 하이브리드 혜택을 사용할 수 있나요?

A: 아니요, 불가능합니다. 해당 가상 머신을 지원할 계획이 없습니다.

Q: 내 RHEL 가상 데이터 센터 구독에서 Azure 하이브리드 혜택을 사용할 수 있나요?

A: 아니요, 불가능합니다. AHB를 포함하여 Azure에서는 VDC가 전혀 지원되지 않습니다.  
 

## <a name="common-problems"></a>일반적인 문제
이 섹션에서는 발생할 수 있는 일반적인 문제와 완화 방법 단계를 나열합니다.

| Error | 완화 방법 |
| ----- | ---------- |
| “Azure 구독에서 Red Hat Cloud Access를 성공적으로 활성화하지 못한 것으로 기록되어 있기 때문에 작업을 완료할 수 없습니다.” | RHEL VM에서 혜택을 사용하려면 먼저 [Red Hat Cloud Access를 사용하여 Azure 구독을 등록](https://access.redhat.com/management/cloud)해야 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure CLI를 사용하여 Azure 하이브리드 혜택에 대한 VM을 만들고 업데이트하고 라이선스 형식(RHEL_BYOS, SLES_BYOS)을 추가하는 방법 알아보기](/cli/azure/vm)
* RHEL 및 SLES용 Virtual Machine Scale Sets의 Azure 하이브리드 혜택은 모든 사용자가 사용할 수 있습니다. [여기에서 해당 혜택과 사용 방법에 대한 자세한 정보](../../virtual-machine-scale-sets/azure-hybrid-benefit-linux.md)를 알아볼 수 있습니다.

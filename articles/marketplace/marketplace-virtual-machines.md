---
title: 가상 머신 제품 플랜 - Microsoft 상업용 Marketplace
description: 이 문서에서는 Azure Marketplace에 가상 머신 제품을 게시하기 위한 요구 사항에 대해 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 11/17/2021
ms.openlocfilehash: f354accdd001f9fb49897e74009407c05598747e
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759266"
---
# <a name="plan-a-virtual-machine-offer"></a>가상 머신 제품 계획

이 문서에서는 VM(가상 머신) 제품을 상업용 Marketplace에 게시하기 위한 다양한 옵션 및 요구 사항에 대해 설명합니다. VM 제품은 Azure Marketplace를 통해 배포되고 청구되는 거래 가능한 제품입니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만들고](create-account.md) 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

> [!TIP]
> 상업용 Marketplace에서의 구매에 대한 고객의 보기를 보려면 [구매 Azure Marketplace](/marketplace/azure-purchasing-invoicing)참조하세요.

### <a name="technical-fundamentals"></a>기술 기본 사항

제품 설계, 작성, 테스트 프로세스에는 시간이 걸리고, 제품을 빌드하는 데 사용되는 Azure 플랫폼과 기술 모두에 대한 전문 지식이 필요합니다. 엔지니어링 팀은 [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage), [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)에 대한 실무 지식과 함께 [Azure 애플리케이션의 디자인 및 아키텍처](https://azure.microsoft.com/solutions/architecture/)에 대한 숙련도를 갖추어야 합니다. 다음 추가 기술 리소스를 참조하세요.

- 자습서
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- 샘플
  - [Linux VM용 Azure CLI 샘플](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Linux VM용 Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Windows VM용 Azure CLI 샘플](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Windows VM용 Azure PowerShell 샘플](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>기술 요구 사항

VM 제품의 기술 요구 사항은 다음과 같습니다.

- 하나의 운영 체제 VHD(가상 하드 디스크)를 준비해야 합니다. 데이터 디스크 VHD는 선택 사항입니다. 아래에 더 자세히 설명되어 있습니다.
- 제품에 대한 플랜을 하나 이상 만들어야 합니다. 플랜은 선택한 [라이선스 모델에](#licensing-models) 따라 가격이 책정됩니다.
   > [!IMPORTANT]
   > 계획의 모든 VM 이미지에는 동일한 수의 데이터 디스크가 있어야 합니다.

VM에는 다음 두 가지 구성 요소가 포함됩니다.

- **운영 VHD** – 제품과 함께 배포하는 솔루션과 운영 체제가 포함됩니다. VHD가 Linux 기반, Windows 기반, 사용자 지정 기반 VM인지 여부에 따라 VHD를 준비하는 프로세스가 달라집니다.
- **데이터 디스크 VHD**(선택 사항) - VM의 영구적 전용 스토리지입니다. 운영 체제 VHD(예: C: 드라이브)를 사용하여 영구 정보를 저장하지 마세요. 
    - 최대 16개의 데이터 디스크를 포함할 수 있습니다.
    - 디스크가 비어 있는 경우에도 데이터 디스크당 하나의 VHD를 사용합니다.

    > [!NOTE]
    > 사용 중인 운영 체제에 상관없이 솔루션에 필요한 최소 개수의 데이터 디스크만 추가합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만, 배포 중 또는 배포 후에 언제든지 디스크를 추가할 수 있습니다.

기술 자산 준비에 대한 자세한 지침을 보려면 [승인된 기본을 사용하여 가상 머신 만들기](azure-vm-use-approved-base.md) 또는 [고유한 이미지를 사용하여 가상 머신 만들기](azure-vm-use-own-image.md)를 참조하세요.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 대상 사용자는 엔드 투 엔드 기능을 테스트하기 위해 마켓플레이스에서 라이브로 게시되기 전에 해당 제안에 액세스할 수 있습니다. 미리 **보기 대상** 페이지에서 Azure 구독 ID를 사용하여 제한된 미리 보기 대상을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 미리 보기 대상은 제안을 테스트하고 유효성을 검사할 수 있는 구독 ID 목록입니다. 여기에는 사용자가 사용할 수 있게 되기 전에 프라이빗 플랜이 포함됩니다. 반면, 제품을 비공개로 만들 때는 프라이빗 대상을 지정하여 선택한 고객에게 제안 표시를 제한해야 합니다. 프라이빗 대상 그룹(각 플랜의 **가격 책정 및 가용성** 페이지에 정의됨)은 제안이 라이브 상태인 후 특정 플랜에 액세스할 수 있는 구독 ID 및/또는 테넌트 ID 목록입니다.

## <a name="plans-pricing-and-trials"></a>계획, 가격 책정 및 평가판

VM 제품에는 하나 이상의 플랜이 필요합니다. 플랜은 솔루션 범위, 한도, 관련 가격 책정을 정의합니다. 고객에게 다양한 기술 및 라이선스 옵션과 평가판 기회를 제공하기 위해 제안에 대한 여러 플랜을 만들 수 있습니다. 가격 책정 모델, 평가판, 프라이빗 플랜을 포함한 플랜에 대한 일반적인 지침은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md)을 참조하세요.

VM은 사용량 기반 종량제 또는 BYOL(사용자 라이선스 가져오기) 라이선스 모델을 사용하여 완벽하게 상거래가 가능합니다. Microsoft는 사용자 대신, 상거래를 주도하고 고객에게 요금을 청구합니다. 사용자는 기업계약을 비롯하여 고객과 Microsoft 간에 선호하는 결제 방식을 채택할 수 있습니다. 자세한 내용은 [상업용 Marketplace 거래 기능](./marketplace-commercial-transaction-capabilities-and-considerations.md)을 참조하세요.

> [!NOTE]
> 기업계약과 관련된 Azure 선불(이전의 현금 약정 금액)은 VM의 Azure 사용량에 대해 사용할 수 있지만 소프트웨어 라이선스 요금에는 사용할 수 없습니다.

### <a name="private-plans"></a>비공개 플랜

프라이빗 플랜은 솔루션의 검색 및 배포를 선택한 특정 고객 집합으로 제한하고 사용자 지정된 소프트웨어, 사용 약관 및 가격을 제공합니다. 이러한 사용자 지정된 사용 약관을 사용하면 사용자가 전문화된 가격 책정 및 조건뿐 아니라 제한된 릴리스 소프트웨어에 대한 초기 액세스를 사용하여 선도적인 거래를 포함한 다양한 시나리오를 강조 표시할 수 있습니다.

자세한 내용은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md) 및 [Microsoft 상업용 Marketplace의 프라이빗 제품](private-offers.md)을 참조하세요.

### <a name="licensing-models"></a>라이선스 모델

새 제품 게시를 준비할 때 적절한 라이선스 모델을 선택하여 가격 책정 관련 결정을 내려야 합니다.

VM 제품에 사용할 수 있는 라이선스 옵션은 다음과 같습니다.

| 라이선스 모델 | 트랜잭션 프로세스 |
| --- | --- |
| 사용량 기준 | 종량제라고도 합니다. 이 라이선스 모델을 사용하면 다양한 가격 책정 옵션을 통해 시간당 고객에게 요금을 청구할 수 있습니다. |
| BYOL | 사용자 라이선스 필요 옵션을 사용하면 고객이 기존 소프트웨어 라이선스를 Azure로 가져올 수 있습니다. * |
|

`*` 게시자는 주문, 처리, 계량, 청구, 송장, 결제, 수금을 포함하지만 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원합니다.

다음 예에서는 Azure Marketplace에서 사용량 기반 가격 책정을 제공하는 VM 제품을 보여 줍니다.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="샘플 VM 제품 화면":::

### <a name="trials"></a>평가판

다음은 잠재 고객을 식별하는 데 도움이 되도록 구성할 수 있는 평가판 유형입니다. 이러한 평가판을 통해 잠재 고객은 선택한 라이선스 모델을 통해 구매하기 전에 제품을 조작할 수 있습니다.

| 평가판 | 트랜잭션 프로세스 |
| ------------ | ------------- |
| 평가판 | 1개월, 3개월, 6개월 평가판을 고객에게 제공합니다. |
| 시험 사용 | 이 옵션을 사용하면 고객이 추가 비용 없이 솔루션을 평가할 수 있습니다. 기존 Azure 고객이 아니어도 평가판을 경험해 볼 수 있습니다. [시험사에](#test-drive)대해 자세히 알아보세요. |
|

> [!NOTE]
> 선택한 평가판 기회와 함께 라이선스 모델에 따라 파트너 센터 제안을 만들 때 제공해야 하는 추가 정보가 결정됩니다.

## <a name="test-drive"></a>시험 사용

고객이 고정된 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 구매하기 전에 제안을 사용해 볼 수 있는 시험 사용을 사용하도록 설정하여 정규화된 잠재 고객과 전환율이 높아질 수 있습니다. 시험판은 제안 유형 및 마켓플레이스에 따라 다릅니다. 시험판 유형 및 작동 방식에 대한 자세한 내용은 [시험 구동이란?을 참조하세요.](what-is-test-drive.md) VM 제안용 시험사용 드라이브에 대한 자세한 내용은 [VM 시험 드라이브 구성을 참조하세요.](azure-vm-test-drive.md)

## <a name="customer-leads"></a>잠재 고객

파트너 센터를 통해 제품을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 테스트 드라이브를 사용하도록 설정하려면 CRM에 연결해야 합니다(이전 섹션 참조). 그렇지 않으면 CRM에 연결하는 것은 선택 사항입니다.

## <a name="legal-contracts"></a>법적 계약

제안에 대한 사용 약관을 정의하는 두 가지 옵션이 있습니다.
- 수정안 옵션이 있는 표준 계약 사용
- 사용자 고유의 사용 약관 사용

표준 계약에 대한 자세한 내용은 [Microsoft 상업용 Marketplace의 표준 계약](standard-contract.md)을 참조하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF를 다운로드할 수 있습니다(팝업 차단이 해제되어 있는지 확인).

## <a name="cloud-solution-providers"></a>클라우드 솔루션 공급자

파트너 센터에서 제품을 만들 때 **CSP를 통해 재판매** 탭이 표시됩니다. 이 옵션을 사용하면 Microsoft CSP(클라우드 솔루션 공급자) 프로그램에 속한 파트너가 번들 제품의 일부로 VM을 재판매할 수 있습니다. 모든 BYOL(사용자 라이선스 필요) 플랜은 자동으로 프로그램에 옵트인됩니다. 또한 비 BYOL 플랜을 옵트인하도록 선택할 수도 있습니다. 자세한 내용은 [클라우드 솔루션 공급자 프로그램](cloud-solution-providers.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 제안에 대해 만든 이미지가 아직 없는 경우 [승인된 기본을 사용하여 가상 머신 만들기](azure-vm-use-approved-base.md) 또는 사용자 [고유의 이미지를 사용하여 가상 머신 만들기를 참조하세요.](azure-vm-use-own-image.md)
- 이미지가 준비되면 [Azure Marketplace 가상 머신 제안 만들기를](azure-vm-offer-setup.md) 참조하세요.

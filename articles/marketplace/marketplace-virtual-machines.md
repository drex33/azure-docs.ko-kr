---
title: 가상 머신 제품 플랜 - Microsoft 상업용 Marketplace
description: 이 문서에서는 Azure Marketplace에 가상 머신 제품을 게시하기 위한 요구 사항에 대해 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/26/2021
ms.openlocfilehash: 56b2ce9b8b392a25c50ff4ea2d47da0237363a53
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719247"
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
- 고객은 언제든지 제품을 취소할 수 있습니다.
- 제품에 대한 플랜을 하나 이상 만들어야 합니다. 선택한 [라이선스 옵션](#licensing-options)에 따라 플랜에 대한 가격이 책정됩니다.
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

[!INCLUDE [Test drives section](includes/preview-audience.md)]

## <a name="plans-and-pricing"></a>플랜 및 가격 책정

VM 제품에는 하나 이상의 플랜이 필요합니다. 플랜은 솔루션 범위, 한도, 관련 가격 책정을 정의합니다. 제품에 대한 여러 플랜을 만들어 고객에게 다양한 기술 및 라이선스 옵션과 평가판을 제공할 수 있습니다. 가격 책정 모델, 평가판, 프라이빗 플랜을 포함한 플랜에 대한 일반적인 지침은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md)을 참조하세요. 

VM은 종량제 또는 BYOL(사용자 라이선스 필요) 라이선스 모델을 사용하여 완전하게 거래될 수 있습니다. Microsoft는 사용자 대신, 상거래를 주도하고 고객에게 요금을 청구합니다. 사용자는 기업계약을 비롯하여 고객과 Microsoft 간에 선호하는 결제 방식을 채택할 수 있습니다. 자세한 내용은 [상업용 Marketplace 거래 기능](./marketplace-commercial-transaction-capabilities-and-considerations.md)을 참조하세요.

> [!NOTE]
> 기업계약과 관련된 Azure 선불(이전의 현금 약정 금액)은 VM의 Azure 사용량에 대해 사용할 수 있지만 소프트웨어 라이선스 요금에는 사용할 수 없습니다.

### <a name="licensing-options"></a>라이선싱 옵션

새 제품 게시를 준비할 때 선택할 라이선싱 옵션을 결정해야 합니다. 파트너 센터에서 제품을 만들 때 나중에 제공해야 할 추가 정보를 결정합니다.

VM 제품에 사용할 수 있는 라이선스 옵션은 다음과 같습니다.

| 라이선싱 옵션 | 트랜잭션 프로세스 |
| --- | --- |
| 평가판 | 1개월, 3개월, 6개월 평가판을 고객에게 제공합니다. |
| 시험 사용 | 이 옵션을 사용하면 고객은 추가 비용 없이 VM을 평가할 수 있습니다. 기존 Azure 고객이 아니어도 평가판을 경험해 볼 수 있습니다. 자세한 내용은 [시험 사용이란?](./what-is-test-drive.md)을 참조하세요. |
| BYOL | 사용자 라이선스 필요 옵션을 사용하면 고객이 기존 소프트웨어 라이선스를 Azure로 가져올 수 있습니다.\* |
| 사용량 기반 | 종량제라고도 하는 이 옵션을 사용하면 고객이 시간당 요금을 지불할 수 있습니다. |
| 대화형 데모  | 대화형 데모를 사용하여 솔루션의 안내 방식 환경을 고객에게 제공합니다. 복잡한 솔루션을 복잡하게 설정하지 않고도 평가판 경험을 제공할 수 있다는 이점이 있습니다. |
|

\* 게시자는 주문, 처리, 계량, 청구, 송장, 결제, 수금을 포함하지만 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원합니다.

다음 예에서는 Azure Marketplace에서 사용량 기반 가격 책정을 제공하는 VM 제품을 보여 줍니다.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="샘플 VM 제품 화면":::

### <a name="private-plans"></a>프라이빗 플랜

이미지를 게시하고 프라이빗 플랜으로 가격 책정하여 특정 고객 집합에 대한 VM의 검색 및 배포를 제한할 수 있습니다. 프라이빗 플랜을 통해 가장 가까운 고객을 위한 독점 제품을 만들고 사용자 지정 소프트웨어 및 약관을 제공할 수 있습니다. 이러한 사용자 지정된 사용 약관을 사용하면 사용자가 전문화된 가격 책정 및 조건뿐 아니라 제한된 릴리스 소프트웨어에 대한 초기 액세스를 사용하여 선도적인 거래를 포함한 다양한 시나리오를 강조 표시할 수 있습니다. 프라이빗 플랜을 통해 제한된 고객 집합에 특정 가격 책정 또는 제품을 제공할 수 있습니다.

자세한 내용은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md) 및 [Microsoft 상업용 Marketplace의 프라이빗 제품](private-offers.md)을 참조하세요.

## <a name="test-drive"></a>시험 사용

[!INCLUDE [Test drives section](includes/test-drives.md)]

이제 VM 제안용 시험사용 드라이브에 대해 자세히 알아보려면 [VM 시험 드라이브 구성을](azure-vm-test-drive.md)참조하세요.

## <a name="customer-leads"></a>잠재 고객

파트너 센터를 통해 제품을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 테스트 드라이브를 사용하도록 설정하려면 CRM에 연결해야 합니다(이전 섹션 참조). 그렇지 않으면 CRM에 연결하는 것은 선택 사항입니다.

## <a name="legal-contracts"></a>법적 계약

[!INCLUDE [Legal contracts section](includes/legal-contracts-intro.md)]
[!INCLUDE [Legal section for Standard contracts](includes/legal-contracts-standard.md)]

## <a name="cloud-solution-providers"></a>클라우드 솔루션 공급자

파트너 센터에서 제품을 만들 때 **CSP를 통해 재판매** 탭이 표시됩니다. 이 옵션을 사용하면 Microsoft CSP(클라우드 솔루션 공급자) 프로그램에 속한 파트너가 번들 제품의 일부로 VM을 재판매할 수 있습니다. 모든 BYOL(사용자 라이선스 필요) 플랜은 자동으로 프로그램에 옵트인됩니다. 또한 비 BYOL 플랜을 옵트인하도록 선택할 수도 있습니다. 자세한 내용은 [클라우드 솔루션 공급자 프로그램](cloud-solution-providers.md)을 참조하세요. 

> [!NOTE]
> 이제 CSP(클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대한 자세한 내용은 [**클라우드 솔루션 공급자**](./cloud-solution-providers.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Marketplace에서 Azure 가상 머신 제품 만들기](azure-vm-offer-setup.md)
- [승인된 기본을 사용하여 가상 머신 만들기](azure-vm-use-approved-base.md) 또는 [고유한 이미지를 사용하여 가상 머신 만들기](azure-vm-use-own-image.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)

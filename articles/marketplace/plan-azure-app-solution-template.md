---
title: Azure 애플리케이션 제품에 대한 솔루션 템플릿 계획
description: Microsoft 파트너 센터에서 상용 마켓플레이스 포털을 사용하여 새 Azure 애플리케이션 제품에 대한 솔루션 템플릿 플랜을 만드는 데 필요한 사항에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: b2119a4168dcc1addec0bff1d046ac90e9530a8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308279"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Azure 애플리케이션 제품에 대한 솔루션 템플릿 계획

이 문서에서는 Azure 애플리케이션 제품에 대한 솔루션 템플릿 플랜을 게시하기 위한 요구 사항을 설명합니다. 솔루션 템플릿 플랜은 Azure 애플리케이션 제품에서 지원하는 두 가지 유형의 플랜 중 하나입니다. 이러한 두 플랜 유형 간 차이점에 대한 자세한 내용은 [플랜 유형](plan-azure-application-offer.md#plans)을 참조하세요. 아직 수행하지 않은 경우 [Azure 애플리케이션 제품 계획](plan-azure-application-offer.md)을 참조하세요.

솔루션 템플릿 플랜 유형에는 솔루션 인프라를 자동으로 배포하기 위해 [ARM 템플릿(Azure Resource Manager 템플릿)](../azure-resource-manager/templates/overview.md)이 필요합니다.

## <a name="solution-template-requirements"></a>솔루션 템플릿 요구 사항

| 요구 사항 | 세부 정보 |
| ------------ | ------------- |
| 청구 및 계량 | 솔루션 템플릿 플랜은 거래 가능하지 않지만 Microsoft 상업용 마켓플레이스를 통해 청구되는 유료 VM 제품을 배포하는 데 사용할 수 있습니다. 솔루션의 ARM 템플릿에서 배포하는 리소스는 고객의 Azure 구독에 설정됩니다. 종량제 가상 머신은 Microsoft를 통해 고객과 거래되고 고객의 Azure 구독을 통해 청구됩니다. <br><br> BYOL(사용자 라이선스 필요) 청구의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하지만 소프트웨어 라이선스 요금은 고객과 직접 거래합니다. |
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요.<ul><li>[Azure VM 기술 자산 만들기](./azure-vm-create-certification-faq.yml#address-a-vulnerability-or-an-exploit-in-a-vm-offer)(Windows VHD의 경우)</li><li>[Azure에서 보증된 Linux 배포](../virtual-machines/linux/endorsed-distros.md)(Linux VHD용)</li></ul> |
| 고객 사용량 특성 | 고객 사용량 특성 활성화는 Azure Marketplace에 게시된 모든 솔루션 템플릿에 필요합니다. 고객 사용 특성 및 사용 설정 방법에 대한 자세한 내용은 [Azure 파트너 고객 사용 특성](azure-partner-customer-usage-attribution.md)을 참조하세요. |
| 관리 디스크 사용 | [관리 디스크](../virtual-machines/managed-disks-overview.md)는 Azure에서 IaaS(Infrastructure as a Service) VM의 영구 디스크에 대한 기본 옵션입니다. 솔루션 템플릿에서 관리 디스크를 사용해야 합니다.<ul><li>솔루션 템플릿을 업데이트하려면 [Azure Resource Manager 템플릿에서 관리 디스크 사용](../virtual-machines/using-managed-disks-template-deployments.md)의 지침을 따르고 제공된 [샘플](https://github.com/Azure/azure-quickstart-templates)을 사용합니다.</li><li>Azure Marketplace 이미지로 VHD를 게시하려면 [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) 또는 [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) 중 하나를 사용하여 관리 디스크의 기본 VHD를 스토리지 계정으로 가져옵니다.</ul> |
| 배포 패키지 | 고객이 플랜을 배포할 수 있도록 하는 배포 패키지가 필요합니다. 동일한 기술 구성이 필요한 여러 플랜을 만드는 경우 동일한 플랜 패키지를 사용할 수 있습니다. 자세한 내용은 다음 섹션: 배포 패키지를 참조하세요. |
|||

## <a name="deployment-package"></a>배포 패키지

배포 이 패키지에는 플랜에 필요한 모든 템플릿 파일뿐만 아니라 파일로 패키징된 추가 리소스가 포함됩니다.

모든 Azure 애플리케이션은 .zip 보관 파일의 루트 폴더에 다음 두 개의 파일을 포함해야 합니다.

- [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)이라는 Resource Manager 템플릿 파일. 이 템플릿은 고객의 Azure 구독에 배포할 리소스를 정의합니다. Resource Manager 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조하세요.
- [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)이라는 Azure 애플리케이션 만들기 환경의 사용자 인터페이스 정의. 사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

지원되는 최대 파일 크기는 다음과 같습니다.

- 전체 압축 .zip 보관 파일 크기로 최대 1GB
- .zip 보관 파일 내에서 압축되지 않은 개별 파일에 대해 최대 1GB

모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다.

## <a name="azure-regions"></a>Azure 지역

Azure 공용 지역이나 Azure Government 지역 또는 둘 다에 플랜을 게시할 수 있습니다. [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)에 게시하기 전에 특정 엔드포인트가 다를 수 있으므로 환경에서 플랜을 테스트하고 유효성을 검사합니다. 플랜을 설정하고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청합니다.

게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

상업용 마켓플레이스에서 지원되는 국가 및 지역 목록은 [지리적 가용성 및 통화 지원](marketplace-geo-availability-currencies.md)을 참조하세요.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 관한 설명에 연결되는 링크일 수 있습니다. 이 링크는 Azure Government 고객에게만 표시됩니다.

## <a name="choose-who-can-see-your-plan"></a>플랜을 볼 수 있는 사용자 선택

모든 사용자(퍼블릭) 또는 선택한 특정 대상 그룹(프라이빗)에만 표시되도록 각 플랜을 구성할 수 있습니다. 최대 100개의 플랜을 만들 수 있으며 여기서 최대 45개의 플랜을 프라이빗으로 지정할 수 있습니다. 특정 고객에게 다양한 가격 책정 옵션이나 기술 구성을 제공하기 위해 프라이빗 플랜을 만들 수 있습니다.

사용자가 할당한 각 구독 ID에 대한 설명을 포함하는 옵션과 함께 Azure 구독 ID를 사용하여 프라이빗 플랜에 대한 액세스 권한을 부여합니다. 최대 10개의 구독 ID를 수동으로 추가하거나 또는 .CSV 파일을 사용하여 최대 1만개의 구독 ID를 추가할 수 있습니다. Azure 구독 ID는 GUID로 표시되며 문자는 소문자여야 합니다.

> [!NOTE]
> 프라이빗 플랜을 게시하는 경우 나중에 표시 유형을 퍼블릭으로 변경할 수 있습니다. 그러나 퍼블릭 플랜을 게시한 후에는 표시 유형을 프라이빗으로 변경할 수 없습니다.

솔루션 템플릿 플랜의 경우 Azure Marketplace에서 해당 플랜을 숨기도록 선택할 수도 있습니다. 플랜이 다른 솔루션 템플릿 또는 관리형 애플리케이션을 통해서만 간접적으로 배포되는 경우 이 작업을 수행하는 것이 좋습니다.

> [!NOTE]
> CSP(클라우드 솔루션 공급자 프로그램)의 재판매인을 통해 설정된 Azure 구독은 프라이빗 플랜을 지원하지 않습니다.

자세한 내용은 [Microsoft 상업용 마켓플레이스의 프라이빗 제품](private-offers.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 제품 만들기](azure-app-offer-setup.md)
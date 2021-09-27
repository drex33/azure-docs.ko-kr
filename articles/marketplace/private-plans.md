---
title: Microsoft 상업용 Marketplace의 프라이빗 플랜
description: 앱 및 서비스 게시자를 위한 Microsoft 상업용 Marketplace(Azure Marketplace)의 프라이빗 플랜입니다.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.date: 08/13/2021
ms.openlocfilehash: cc7f4e983ab766577af41497c2e270694361605a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784758"
---
# <a name="private-plans-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 Marketplace의 프라이빗 플랜

게시자는 대상 고객에게만 표시되도록 프라이빗 플랜을 사용합니다. 이 문서에서는 프라이빗 플랜의 옵션과 혜택을 설명합니다.

## <a name="unlock-enterprise-deals-with-private-plans"></a>프라이빗 플랜을 통해 엔터프라이즈 거래 공개

프라이빗 플랜을 통해 게시자는 엔터프라이즈에 필요한 역량을 가진 대상 고객에게 사용자 지정된 프라이빗 솔루션을 제공할 수 있습니다.

- ‘협상된 가격 책정’을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- *프라이빗 사용 약관* 을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- *특수화된 구성* 을 통해 게시자가 Virtual Machines, Azure 애플리케이션, SaaS(Software as a Service)를 개별 고객의 요구 사항에 맞게 조정할 수 있습니다. 이 옵션을 사용하면 게시자가 새 제품 기능을 모든 고객에게 제공하기 전에 해당 기능에 대한 미리 보기 액세스를 제공할 수도 있습니다.

프라이빗 플랜을 통해 게시자가 사용자 지정 거래와 구성을 협상하고 제공하는 데 필요한 유연성과 컨트롤을 포함한 퍼블릭 마켓플레이스의 규모와 글로벌 가용성을 활용할 수 있습니다. 이제 엔터프라이즈는 기대하는 방법으로 제품을 구매하고 판매할 수 있습니다.

## <a name="create-private-plans"></a>프라이빗 클라우드 플랜

‘플랜이 있는 새 제품이나 기존 제품’의 경우 게시자는 새 플랜(이전의 SKU)을 만들고 프라이빗으로 표시하여 새로운 프라이빗 변형을 쉽게 만들 수 있습니다. 각 제품에는 최대 45개의 프라이빗 플랜이 있을 수 있습니다.

<!--- [Private SKUs]() --->

프라이빗 플랜은 다음 제품 유형에 사용할 수 있습니다.

- Azure Virtual Machine
- Azure 애플리케이션(솔루션 템플릿 또는 관리형 애플리케이션으로 구현됨)
- 관리형 서비스
- SaaS 제품

프라이빗 플랜은 제품의 구성 요소이며 대상 고객에게만 표시되고 해당 고객만 구매할 수 있습니다. 프라이빗 플랜은 대상 고객에게만 표시되고 해당 고객만 구매할 수 있습니다. 프라이빗 플랜은 Azure Global과 Azure Government에 있는 고객에게 제공될 수 있습니다.

프라이빗 플랜은 기본 이미지를 다시 사용하거나 퍼블릭 플랜에 이미 게시된 메타데이터를 제공할 수 있습니다. 이 옵션을 통해 게시자가 동일한 기본 이미지의 여러 버전을 게시하고 메타데이터를 제공하지 않고 공용 제품의 여러 프라이빗 변형을 만들 수 있습니다. Azure Virtual Machine 및 Azure 애플리케이션 제품의 경우에만, 프라이빗 플랜이 기본 이미지를 퍼블릭 플랜과 공유하는 경우 제품의 기본 이미지의 모든 변경 내용이 해당 기본 이미지를 사용하는 모든 퍼블릭 및 프라이빗 플랜에서 전파됩니다.

*프라이빗 플랜만 포함하는 새 제품* 의 경우 게시자는 해당 제품을 다른 제품으로 만든 다음, 플랜을 프라이빗으로 표시할 수 있습니다. 비공개 요금제만 있는 제안은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 에서 검색 되거나 액세스할 수 없으며 제품에 연결 되지 않은 고객에 의해 [Microsoft AppSource](https://appsource.microsoft.com/) 됩니다.

>[!NOTE]
>비공개 계획만 포함 된 제안은 공용 Azure Marketplace에 표시 되지 않습니다.

## <a name="target-customers-with-private-plans"></a>프라이빗 플랜을 사용하는 대상 고객

새로운 프라이빗 플랜과 기존 프라이빗 플랜의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. Azure Virtual Machine, Azure 애플리케이션, 관리형 서비스 제품의 경우 게시자는 프라이빗 플랜의 가용성을 개별 Azure 구독 ID로 제한하거나 최대 10,000개에 달하는 Azure 구독 ID의 CSV를 업로드할 수 있습니다. SaaS 제품의 경우 게시자는 수동 또는 CSV 업로드 접근 방식을 사용하여 Azure Active Directory 테넌트 ID를 연결하여 프라이빗 플랜의 가용성을 제한할 수 있습니다.

제품이 인증되고 게시된 후 프라이빗 구독 동기화 기능을 사용하여 플랜에서 고객을 업데이트하거나 제거할 수 있습니다. 이 기능을 사용하면 게시자가 제품을 다시 인증하거나 게시하지 않고 프라이빗 플랜을 제공할 고객의 목록을 쉽고 빠르게 업데이트할 수 있습니다.

## <a name="deploying-a-private-plan"></a>프라이빗 플랜 배포

Azure Portal에 로그인한 후 고객은 이 단계에 따라 프라이빗 플랜을 선택할 수 있습니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
1. **Azure 서비스** 에서 **리소스 만들기** 를 선택합니다.
1. **신규** 페이지에서 **Azure Marketplace** 옆에 있는 **모두 보기** 를 선택합니다. Marketplace 페이지가 나타납니다.
1. 왼쪽 탐색에서 **프라이빗 플랜** 을 선택합니다.

> [!NOTE]
> 개인 계획은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 또는 [Microsoft AppSource](https://appsource.microsoft.com/)에서만 검색할 수 있습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com)에는 표시 되지 않습니다. 다른 상업용 Marketplace 온라인 스토어에 게시하는 방법에 관한 자세한 내용은 [목록 옵션 소개](./determine-your-listing-type.md)를 참조하세요.

프라이빗 플랜은 검색 결과에 나타나고 다른 제품과 같이 명령줄과 Azure Resource Manager 템플릿을 통해 배포될 수 있습니다.

[![[프라이빗 제품이 검색 결과에 표시됨]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

>[!Note]
>프라이빗 플랜은 CSP(클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정된 구독에서는 지원되지 않습니다.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->

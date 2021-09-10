---
title: Microsoft 상업용 마켓플레이스의 프라이빗 제품
description: 앱 및 서비스 게시자용 Microsoft 상업용 마켓플레이스의 프라이빗 제품.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.date: 02/22/2021
ms.openlocfilehash: 9828d5e52f06f54026894ad70d6c16aaebe8d2fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567310"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 마켓플레이스의 프라이빗 제품

프라이빗 제품(프라이빗 요금제)을 통해, 게시자는 대상 고객에게만 표시되는 계획을 생성할 수 있습니다. 이 문서에서는 프라이빗 제품의 옵션 및 이점에 대해 설명합니다.

## <a name="unlock-enterprise-deals-with-private-offers"></a>프라이빗 제품에서 엔터프라이즈 거래 잠금 해제

프라이빗 제품을 생성하면, 게시자는 개별적으로 엔터프라이즈가 요구하는 기능을 포함한 사용자 지정 솔루션을 대상 고객에게 제공할 수 있습니다.

- *가격 책정 협상* 을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- *프라이빗 사용 약관* 을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- *특수화된 구성* 을 통해 게시자가 Virtual Machines, Azure 애플리케이션, SaaS(Software as a Service)를 개별 고객의 요구 사항에 맞게 조정할 수 있습니다. 이 옵션을 사용하면 게시자가 새 제품 기능을 모든 고객에게 제공하기 전에 해당 기능에 대한 미리 보기 액세스를 제공할 수도 있습니다.

프라이빗 제품을 통해 게시자가 사용자 지정 거래와 구성을 협상하고 제공하는 데 필요한 유연성과 컨트롤을 포함한 퍼블릭 마켓플레이스의 규모와 글로벌 가용성을 활용할 수 있습니다. 이제 엔터프라이즈는 기대하는 방법으로 제품을 구매하고 판매할 수 있습니다.

## <a name="create-private-offers-using-plans"></a>요금제를 사용하여 프라이빗 제품 생성하기

*플랜이 있는 새 제품이나 기존 제품* 의 경우 게시자는 새 플랜을 생성하고 프라이빗으로 표시하여 새로운 프라이빗 변형을 쉽게 만들 수 있습니다. 각 제품에는 최대 45개의 프라이빗 플랜이 있을 수 있습니다.

<!--- [Private SKUs]() --->

프라이빗 플랜은 다음 제품 유형에 사용할 수 있습니다.

- Azure Virtual Machine
- Azure 애플리케이션(솔루션 템플릿 또는 관리형 애플리케이션으로 구현됨)
- 관리형 서비스
- SaaS 제품

프라이빗 플랜은 제품의 구성 요소이며 대상 고객에게만 표시되고 해당 고객만 구매할 수 있습니다. 프라이빗 플랜은 대상 고객에게만 표시되고 해당 고객만 구매할 수 있습니다. 프라이빗 플랜은 Azure Global과 Azure Government에 있는 고객에게 제공될 수 있습니다.

프라이빗 플랜은 기본 이미지를 다시 사용하거나 퍼블릭 플랜에 이미 게시된 메타데이터를 제공할 수 있습니다. 이 옵션을 통해 게시자가 동일한 기본 이미지의 여러 버전을 게시하고 메타데이터를 제공하지 않고 공용 제품의 여러 프라이빗 변형을 만들 수 있습니다. Azure Virtual Machine 및 Azure 애플리케이션 제품의 경우에만, 프라이빗 플랜이 기본 이미지를 퍼블릭 플랜과 공유하는 경우 제품의 기본 이미지의 모든 변경 내용이 해당 기본 이미지를 사용하는 모든 퍼블릭 및 프라이빗 플랜에서 전파됩니다.

*프라이빗 플랜만 포함하는 새 제품* 의 경우 게시자는 해당 제품을 다른 제품으로 만든 다음, 플랜을 프라이빗으로 표시할 수 있습니다. 프라이빗 플랜만 있는 제품은 해당 제품과 연결되지 않은 고객이 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 검색하거나 액세스할 수 없습니다.

>[!NOTE]
>프라이빗 플랜만 포함된 제품은 퍼블릭 Azure Marketplace 또는 AppSource에 표시되지 않습니다.

## <a name="target-customers-with-private-offers"></a>프라이빗 제품을 사용하는 대상 고객

새로운 프라이빗 제품 및 기존 전용 제품의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. Azure Virtual Machine, Azure 애플리케이션 및 관리형 서비스 제품의 경우 게시자는 프라이빗 플랜의 가용성을 개별 Azure 구독 ID로 제한하거나 최대 10,000개 Azure 구독 ID의 CSV를 업로드할 수 있습니다. SaaS 제품의 경우 게시자는 수동 또는 CSV 업로드 접근 방식을 사용하여 Azure Active Directory 테넌트 ID를 연결하여 프라이빗 플랜의 가용성을 제한할 수 있습니다.

제품이 인증되고 게시된 후 프라이빗 구독 동기화 기능을 사용하여 플랜에서 고객을 업데이트하거나 제거할 수 있습니다. 이 기능을 사용하면 게시자가 제품을 다시 인증하거나 게시하지 않고 프라이빗 플랜을 제공할 고객의 목록을 쉽고 빠르게 업데이트할 수 있습니다.

## <a name="deploying-private-offers"></a>프라이빗 제품 배포

Azure Portal에 로그인한 후 고객은 이 단계에 따라 프라이빗 제품을 선택할 수 있습니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
1. **Azure 서비스** 에서 **리소스 만들기** 를 선택합니다.
1. **신규** 페이지에서 **Azure Marketplace** 옆에 있는 **모두 보기** 를 선택합니다. Marketplace 페이지가 나타납니다.
1. 왼쪽 탐색에서 **프라이빗 제품** 을 선택합니다.

> [!NOTE]
> 프라이빗 제품은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서만 검색할 수 있습니다. 이는 [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com)에 표시되지 않습니다. 다른 상용 마켓플렛이스 온라인 스토어에 게시하는 방법에 관한 자세한 내용은 [목록 옵션 소개](./determine-your-listing-type.md)를 참조하세요.

프라이빗 제품은 검색 결과에 나타나고 다른 제품과 같이 명령줄 및 Azure Resource Manager 템플릿을 통해 배포될 수 있습니다.

[![[프라이빗 제품이 검색 결과에 표시됨]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

>[!Note]
>CSP(클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정된 구독은 프라이빗 제품을 지원하지 않습니다.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->

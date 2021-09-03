---
title: Azure 애플리케이션 제품에 대한 계획 만들기
description: 파트너 센터(Azure Marketplace)에서 Azure 애플리케이션 제품에 대한 플랜을 만듭니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: cd771baabad58cba4beca02f56e428730051aa96
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542891"
---
# <a name="create-plans-for-an-azure-application-offer"></a>Azure 애플리케이션 제품에 대한 계획 만들기

Microsoft의 상업용 Marketplace를 통해 판매되는 제품에는 상업용 Marketplace에 제품을 나열하기 위한 플랜이 하나 이상 있어야 합니다. 동일한 제품 내에서 다양한 옵션을 사용하여 여러 가지 플랜을 만들 수 있습니다. 이러한 플랜(SKU라고도 함)은 플랜 유형(솔루션 템플릿 또는 관리형 애플리케이션), 수익 창출 또는 대상 측면에 따라 다를 수 있습니다.  플랜에 대한 일반적인 지침은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md)을 참조하세요.

## <a name="create-a-plan"></a>계획 만들기

1. **플랜 개요** 탭의 위에 있는 **+ 새 플랜 만들기** 를 선택합니다.
1. 표시되는 대화 상자의 **플랜 ID** 상자에 고유한 플랜 ID를 입력합니다. 이 ID는 고객에게 제품 URL로 표시됩니다. 최대 50자의 영숫자 소문자, 대시 또는 밑줄을 사용합니다. **만들기** 를 선택한 후에는 플랜 ID를 수정할 수 없습니다.
1. **플랜 이름** 상자에 이 플랜의 고유한 이름을 입력합니다. 고객이 제품 내에서 선택할 플랜을 확인할 때 이 이름이 표시됩니다. 최대 50자를 사용할 수 있습니다.
1. **만들기** 를 선택합니다.

## <a name="define-the-plan-setup"></a>플랜 설정 정의

**플랜 설정** 탭을 사용하면 플랜 유형, 플랜이 다른 플랜의 기술 구성을 다시 사용하는지 여부, 플랜을 사용할 수 있는 Azure 지역을 설정할 수 있습니다. 이 탭에 있는 답변은 해당 플랜에 대해 다른 탭에 표시되는 필드에 영향을 미칩니다.

### <a name="select-the-plan-type"></a>플랜 유형 선택

- **플랜 유형** 목록에서 **솔루션 템플릿** 또는 **관리형 애플리케이션** 중 하나를 선택합니다.

**솔루션 템플릿** 플랜은 고객이 완전히 관리합니다. **관리형 애플리케이션** 플랜을 사용하면 게시자가 고객 대신 애플리케이션을 관리할 수 있습니다. 두 가지 플랜 유형에 대한 자세한 내용은 [플랜 유형](plan-azure-application-offer.md#types-of-plans)을 참조하세요.

#### <a name="to-re-use-a-technical-configuration"></a>기술 구성을 다시 사용하려면 다음을 수행합니다.

1. **이 플랜은 동일한 유형의 다른 플랜에서 기술 구성을 다시 사용합니다.** 확인란을 선택합니다.
1. 표시되는 목록에서 원하는 기본 플랜을 선택합니다.

> [!NOTE]
> 다른 플랜의 패키지를 다시 사용하면 전체 **기술 구성** 탭이 해당 플랜에 표시되지 않습니다. 향후 적용할 업데이트를 포함하여 다른 플랜의 기술 구성 세부 정보가 해당 플랜에도 사용됩니다.

### <a name="select-azure-regions-clouds"></a>Azure 지역 선택(클라우드)

플랜은 하나 이상의 Azure 지역에서 사용할 수 있어야 합니다. 플랜이 게시되고 특정 Azure 지역에서 사용할 수 있게 되면 제품에서 해당 지역을 제거할 수 없습니다.

#### <a name="azure-global-region"></a>Azure 글로벌 지역

**Azure 글로벌** 확인란은 기본적으로 선택됩니다. 이를 통해 상업용 Marketplace 통합이 있는 모든 Azure 글로벌 지역의 고객이 플랜을 사용할 수 있습니다. 관리형 애플리케이션 플랜의 경우 플랜을 사용할 수 있도록 하려는 시장에서 선택할 수 있습니다.

해당 지역에서 제품을 제거하려면 **Azure 글로벌** 확인란의 선택을 취소합니다.

#### <a name="azure-government-region"></a>Azure Government 지역

해당 지역은 미국 연방, 주, 지방 또는 부족 단체의 고객뿐만 아니라 관련 고객에게 서비스를 제공할 수 있는 파트너에게도 액세스를 제어합니다. 게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이러한 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 대한 설명에 연결되는 링크일 수 있습니다. 이러한 링크는 Azure Government 고객에게만 표시됩니다.

##### <a name="to-select-the-azure-government-region"></a>Azure Government 지역을 선택하려면 다음을 수행합니다.

1. **Azure Government** 확인란을 선택합니다.
1. **Azure Government 인증** 아래 **+ 인증 추가(최대 100)** 를 선택합니다.
1. 표시되는 상자에서 인증에 대한 이름과 링크를 제공합니다.
1. 다른 인증을 추가하려면 2단계와 3단계를 반복합니다.

다음 탭(플랜 목록)으로 진행하기 전에 **초안 저장** 을 선택합니다.

## <a name="define-the-plan-listing"></a>플랜 목록 정의

**플랜 목록** 탭에서는 플랜의 세부 정보 목록을 구성할 수 있습니다. 이 탭에는 동일한 제품의 플랜 간 차이점을 보여 주는 구체적인 정보가 표시됩니다. 상업용 Marketplace에 표시하려는 대로 플랜 이름, 요약, 설명을 정의할 수 있습니다.

1. **플랜 이름** 상자에는 이 플랜에 대해 이전에 제공한 이름이 표시됩니다. 이는 언제든지 변경할 수 있습니다. 이름은 상업용 Marketplace에 제품 소프트웨어 플랜의 제목으로 표시되며 100자로 제한됩니다.
1. **플랜 요약** 상자에 플랜(제품이 아님)을 간략하게 요약합니다. 이 요약은 100자로 제한됩니다.
1. **플랜 설명** 상자에 이 소프트웨어 플랜의 고유한 특징과 제품 내 다른 소프트웨어 플랜과의 차이점을 설명합니다. 제품이 아닌 계획을 설명해야 합니다. 이 설명에는 최대 2,000자까지 포함할 수 있습니다.
1. 계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

다음 중 하나를 수행합니다.

- [솔루션 템플릿 플랜 구성](azure-app-solution.md)
- [관리형 애플리케이션 플랜 구성](azure-app-managed.md)

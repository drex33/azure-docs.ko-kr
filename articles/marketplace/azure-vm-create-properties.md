---
title: Azure Marketplace에서 가상 머신 제품 속성 구성
description: Azure Marketplace에서 가상 머신 제품 속성을 구성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
msreviewer: amhindma
ms.date: 11/10/2021
ms.openlocfilehash: 44f500f59169398910b7ede32ebb1c96ecf6fdbc
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134888"
---
# <a name="configure-virtual-machine-offer-properties"></a>가상 머신 제품 속성 구성

**속성** 페이지(파트너 센터 왼쪽 탐색 메뉴에서 선택)에서 Azure Marketplace VM(가상 머신) 제안을 그룹화하는 데 사용되는 범주와 제안을 지원하는 법적 계약을 정의합니다.

## <a name="select-a-category"></a>범주 선택

범주와 하위 범주를 선택하여 적절한 Azure Marketplace 검색 영역에 제품을 배치합니다. 나중에 제품이 해당 범주를 지원하는 방법을 제품 설명에 입력해야 합니다.

- 기본 범주를 선택합니다.
- 두 번째 선택적 범주(보조)를 추가하려면 **+범주** 링크를 선택합니다.
- 기본 및/또는 보조 범주에 하위 범주를 최대 두 개까지 선택합니다. Ctrl+클릭을 사용하여 두 번째 하위 범주를 선택합니다. 제안에 적용할 수 있는 하위 범주가 없으면 **해당 없음이** 자동으로 선택됩니다.

[상업용 마켓플레이스의](categories.md)범주 및 하위 범주에서 범주 및 하위 범주의 전체 목록을 참조하세요. 가상 머신 제품은 항상 Azure Marketplace의 **컴퓨팅** 범주 아래에 표시됩니다.

## <a name="provide-terms-and-conditions"></a>사용 약관 만들기

**법률 정보** 에서 제품에 대한 사용 약관을 제공합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- [수정안 옵션이 있는 표준 계약 사용](#use-the-standard-contract)
- [사용자 고유의 사용 약관 사용](#use-your-own-terms-and-conditions)

표준 계약에 대한 자세한 내용은 [Microsoft 상업용 Marketplace의 표준 계약](standard-contract.md)을 참조하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF를 다운로드할 수 있습니다(팝업 차단이 해제되어 있는지 확인).

### <a name="use-the-standard-contract"></a>표준 계약 사용

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 Marketplace에서 제품에 사용할 수 있는 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번 읽고 동의만 하면 되고 사용자 지정 사용 약관을 만들 필요는 없습니다. 검색 및 구매 프로세스 중에 발생하는 고객 경험에 대한 자세한 내용은 [Microsoft 상업용 Marketplace용 표준 계약 참조하세요.](standard-contract.md#customer-experience)

1. **Microsoft의 상업용 Marketplace의 표준 계약 사용** 확인란을 선택합니다.

1. **확인** 대화 상자에서 **동의** 를 선택합니다. 화면 크기에 따라 확인하려면 위로 스크롤해야 할 수도 있습니다.
1. 계속하기 전에 **초안 저장** 을 선택합니다.

   > [!NOTE]
   > 상업용 Marketplace 표준 계약을 사용하여 제품이 게시되면 사용자 고유의 사용 약관을 사용할 수 없습니다. 수정안 옵션이 있는 표준 계약 또는 사용자 고유의 사용 약관에 따라 솔루션을 제공합니다.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>표준 계약에 수정안 추가(선택 사항)

사용 가능한 수정계약에는 *범용* 및 *사용자 지정* 의 두 가지 종류가 있습니다.

##### <a name="add-universal-amendment-terms"></a>범용 수정계약 사용 약관 추가

**Microsoft의 상업용 Marketplace 표준 계약에 대한 범용 수정계약 조건** 상자에서 범용 수정계약 사용 약관을 입력합니다. 이 상자에는 문자를 개수 제한 없이 입력할 수 있습니다. 이러한 조건은 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에게 표시됩니다.

##### <a name="add-one-or-more-custom-amendments"></a>하나 이상의 사용자 지정 수정안 추가

사용자 지정 수정은 Azure 테넌트 ID를 사용하는 특정 고객을 대상으로 하는 표준 계약 대한 특별 수정입니다. 테넌트 고객만 제안을 구매할 때 사용자 지정 수정 조건이 제공됩니다.

> [!NOTE]
> 사용자 지정 수정을 대상으로 하는 고객은 구매 중에 표준 계약 대한 범용 수정(있는 경우)도 받게 됩니다.

1. **Microsoft의 상업용 Marketplace 표준 계약에 대한 사용자 지정 수정안 사용 약관** 에서 **사용자 지정 수정안 사용 약관 추가(최대 10개)** 링크를 선택합니다.
2. 상자에 **사용자 지정 수정안 사용 약관** 을 입력합니다.
3. 상자에 **테넌트 ID** 를 입력합니다.

   > [!TIP]
   > 테넌트 ID는 Azure에서 고객을 식별합니다. 게시자는 고객에게 이 ID를 요청할 수 있으며 고객은 [ **https://portal.azure.com**](https://portal.azure.com) > **Azure Active Directory** > **속성** 으로 이동하여 ID를 확인할 수 있습니다. 디렉터리 ID 값은 테넌트 ID(예: `50c464d3-4930-494c-963c-1e951d15360e`)입니다. [내 Microsoft Azure 및 Office 365 테넌트 ID는 무엇인가요?](https://www.whatismytenantid.com/)에서 도메인 이름 URL을 사용하여 고객의 조직 테넌트 ID를 조회할 수도 있습니다.

4. 필요한 경우 테넌트 ID에 간단한 **설명** 을 입력합니다. 이 설명은 수정안으로 대상 고객을 식별하는 데 도움이 됩니다.
5. 다른 테넌트 ID를 추가하려면 **고객의 테넌트 ID 추가(최대 10개)** 링크를 선택하고 3단계와 4단계를 반복합니다. 최대 10개의 테넌트 ID를 추가할 수 있습니다.
6. 다른 수정안 사용 약관을 추가하려면 1 ~ 5단계를 반복합니다. 제품당 최대 10개의 사용자 지정 수정 조건을 제공할 수 있습니다.
7. 계속하기 전에 **초안 저장** 을 선택합니다.

### <a name="use-your-own-terms-and-conditions"></a>사용자 고유의 사용 약관 사용

표준 계약을 사용하는 대신 사용자 고유의 사용 약관을 제공할 수 있습니다. 고객은 이 약관에 동의해야 제품을 사용할 수 있습니다.

1. **법적 정보** 에서 **Microsoft의 상업용 Marketplace의 표준 계약 사용** 확인란을 선택 취소합니다.
1. **사용 약관** 상자에 최대 1만 문자 텍스트를 입력합니다.

   > [!NOTE]
   > 긴 설명이 필요하면 사용 약관을 찾을 수 있는 위치를 가리키는 단일 웹 주소를 입력합니다. 이는 고객에게 활성 링크로 표시됩니다.

1. 왼쪽 탐색 메뉴 **제품 목록** 에서 다음 탭을 계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [VM 제품 목록 구성](azure-vm-create-listing.md)

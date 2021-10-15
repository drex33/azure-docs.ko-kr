---
title: 상업용 Marketplace에 대한 컨설팅 서비스 가격 책정 및 가용성 구성
description: 파트너 센터 사용하여 Microsoft 상업용 Marketplace에서 컨설팅 서비스 제안 가격 정보 및 시장 가용성을 구성합니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: cf6290632a1c9d78265961ccaa47b25fc1734a1b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065961"
---
# <a name="configure-consulting-service-offer-pricing-and-availability"></a>컨설팅 서비스 제품 가격 책정 및 가용성 구성

이 문서에서는 Microsoft 상용 마켓플레이스에서 컨설팅 서비스 제품에 대한 시장 가용성 및 가격 책정 세부 정보를 정의하는 방법을 설명합니다.

> [!NOTE]
> 컨설팅 서비스 제품은 목록 형태로만 제공됩니다. 사용자와 상용 마켓플레이스 외부 고객 간의 모든 트랜잭션을 관리해야 합니다.

## <a name="markets"></a>시장

**시장** 섹션에서 컨설팅 서비스를 사용할 수 있는 국가 또는 지역을 선택합니다.

1. **시장** 에서 **시장 편집** 링크를 선택합니다.
2. 표시되는 대화 상자에서 제품을 사용할 수 있도록 할 시장 위치를 선택합니다. 시장은 1개 이상, 141개 이하로 선택해야 합니다.
3. **저장** 을 선택하여 대화 상자를 닫습니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

컨설팅 서비스 제품을 게시하거나 업데이트하면 파트너 센터는 목록의 미리 보기 버전을 만듭니다. 이 미리 보기는 **숨기기 키** 가 있는 사용자에게만 표시됩니다.

**숨기기 키** 필드에 제품의 미리 보기 버전에 액세스하는 데 사용할 영숫자 문자열을 입력합니다.

## <a name="pricing-informational-only"></a>가격 책정(정보용)

**가격 책정** 섹션에서 무료 제품인지 또는 유료 제품인지를 정의합니다.

유료 제품의 경우 가격을 고정할지 변동할지를 지정합니다. 가격이 변동되면 제품 설명에서 가격에 영향을 주는 요인을 설명해야 합니다.

**시장** 섹션에서 단일 국가 또는 지역을 선택한 경우 해당 시장에서 지원되는 통화로 가격을 제공하고 **초안 저장** 을 선택합니다. 지원되는 통화 목록은 [상용 마켓플레이스의 지리적 가용성 및 통화 지원](./marketplace-geo-availability-currencies.md)을 참조하세요.

**시장** 섹션에서 여러 국가 또는 지역을 선택한 경우 가격을 USD(미국 달러)로 제공하고 **초안 저장** 을 선택합니다. 파트너 센터는 초안을 저장할 때 사용할 수 있는 환율을 사용하여 해당 가격을 선택한 모든 시장의 현지 통화로 변환합니다.

변환이 유효한지 검사하거나 개별 시장에서 사용자 지정 가격을 설정하려면 가격 책정 스프레드시트를 내보내고 수정한 후 가져와야 합니다.

1. **가격 책정** 에서 **가격 책정 데이터 내보내기** 링크를 선택합니다. 그러면 파일이 디바이스로 다운로드됩니다.
1. Microsoft Excel에서 exportedPrice.xlsx 파일을 엽니다.
1. 스프레드시트에서 각 시장의 가격과 통화를 조정할 수 있습니다. 지원되는 통화 목록은 [상용 마켓플레이스의 지리적 가용성 및 통화 지원](./marketplace-geo-availability-currencies.md)을 참조하세요. 작업을 완료한 후 파일을 저장합니다.
1. 파트너 센터의 **가격 책정** 에서 **가격 책정 데이터 가져오기** 링크를 선택합니다. 가져온 파일이 이전 가격 책정 정보를 덮어씁니다.

> [!IMPORTANT]
> 파트너 센터에서 정의한 가격은 고정되어 있으며 환율 변동을 따르지 않습니다. 게시 후 하나 이상의 시장에서 가격을 변경하려면 파트너 센터에서 제품을 업데이트한 후 다시 제출하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [검토 및 게시](review-publish-offer.md)

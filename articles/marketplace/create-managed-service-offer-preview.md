---
title: 관리되는 서비스 제품의 미리 보기 대상 그룹 추가
description: Azure Marketplace에서 관리되는 서비스 제품의 미리 보기 대상 그룹을 추가합니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 7/16/2021
ms.openlocfilehash: 38fbf04f9e8f961cca99bd3e0b23e05e664f4848
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649703"
---
# <a name="add-a-preview-audience-for-a-managed-service-offer"></a>관리되는 서비스 제품의 미리 보기 대상 그룹 추가

이 문서에서는 파트너 센터를 사용하여 상업적 마켓플레이스에서 관리되는 서비스 제품에 대한 미리 보기 대상을 구성하는 방법을 설명합니다. 미리 보기 대상은 라이브 상태가 되기 전에 제품을 검토할 수 있습니다.

## <a name="define-a-preview-audience"></a>미리 보기 대상 정의

**미리 보기 대상** 페이지에서 관리되는 서비스 제품을 광범위한 마켓플레이스 대상 그룹에 라이브로 게시하기 전에 검토할 수 있는 제한된 대상을 정의합니다. Azure 구독 ID와 각각에 대한 선택적 설명과 함께 미리 보기 대상을 정의합니다. 해당 필드는 고객에게 표시되지 않습니다. Azure 구독 ID는 Azure Portal의 **구독** 페이지에서 확인할 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로 추가하거나(최대 10개), CSV 파일을 업로드하여(최대 100개) 라이브로 게시하기 전에 제품을 미리 볼 수 있는 대상을 정의합니다. 제품이 이미 라이브 상태인 경우 제품에 대한 업데이트를 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> *미리 보기* 대상 그룹은 *프라이빗* 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 제품이 온라인 스토어에 라이브로 게시되기 ‘전에’ 해당 제품에 액세스할 수 있습니다. 제품이 마켓플레이스에 완전히 게시된 후에 미리 보기 대상 그룹에서 모든 플랜(프라이빗 대상 그룹만 사용할 수 있는 플랜 포함)을 보고 유효성을 검사할 수 있습니다. 전용 대상에게만 계획을 제공할 수 있습니다. 프라이빗 대상 그룹(계획 가용성 탭에서 정의됨)은 특정 플랜에 단독으로 액세스할 수 있습니다.

## <a name="add-email-addresses-manually"></a>수동으로 이메일 주소 추가

1. **미리 보기 대상** 페이지에서 제공된 상자에 단일 Azure 구독 ID와 설명(선택 사항)을 추가합니다.
2. 다른 이메일 주소를 추가하려면 **ID 추가(최대 10개)** 링크를 선택합니다.
3. 다음 탭으로 진행하기 전에 **초안 저장** 을 선택합니다.

## <a name="add-email-addresses-using-a-csv-file"></a>CSV 파일을 사용하여 이메일 주소 추가

1. **미리 보기 대상** 페이지에서 **대상 내보내기(csv)** 링크를 선택합니다.
2. CSV 파일을 엽니다. **Id** 열에서 미리 보기 대상에 추가할 Azure 구독 ID를 입력합니다.
3. **설명** 열에는 각 항목에 대한 설명을 추가할 수 있는 옵션이 있습니다.
4. **유형** 열에서 ID가 있는 각 행에 **SubscriptionId** 를 추가합니다.
5. 파일을 CSV 파일로 저장합니다.
6. **미리 보기 대상** 페이지에서 **대상 가져오기(csv)** 링크를 선택합니다.
7. **확인** 대화 상자에서 **예** 를 선택한 다음 CSV 파일을 업로드합니다.

다음 탭인 **플랜 개요** 로 이동하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [계획 만들기](create-managed-service-offer-plans.md)

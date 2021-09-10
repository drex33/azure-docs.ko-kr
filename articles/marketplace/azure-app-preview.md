---
title: Azure 애플리케이션 제품에 대한 미리 보기 대상 추가
description: 파트너 센터(Azure Marketplace)에서 Azure 애플리케이션 제품에 대한 미리 보기 대상을 추가합니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 74f270922694dd0416638b8d429eca31cebf2609
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542876"
---
# <a name="add-a-preview-audience-for-an-azure-application-offer"></a>Azure 애플리케이션 제품에 대한 미리 보기 대상 추가

이 문서에서는 상업용 Marketplace에서 Azure 애플리케이션 제품에 대한 미리 보기 대상을 구성하는 방법에 관해 설명합니다. 제품 목록이 라이브로 전환되기 전에 이를 검토할 수 있는 미리 보기 대상 그룹을 정의해야 합니다.

## <a name="define-a-preview-audience"></a>미리 보기 대상 정의

**미리 보기 대상** 페이지에서 광범위한 마켓플레이스 대상에게 라이브로 게시하기 전에 Azure 애플리케이션 제품을 검토할 수 있는 제한된 사용자를 정의할 수 있습니다. Azure 구독 ID와 각각에 대한 선택적 설명과 함께 미리 보기 대상을 정의합니다. 해당 필드는 고객에게 표시되지 않습니다. Azure 구독 ID는 Azure Portal의 **구독** 페이지에서 찾을 수 있습니다.

개별적으로(최대 10개) 또는 CSV 파일을 업로드하여(최대 100개) 최소 1개에서 최대 10개의 Azure 구독 ID를 추가하고 라이브로 게시하기 전에 제품을 미리 볼 수 있는 사람을 정의합니다. 제품이 이미 라이브 상태인 경우 제품의 변경 내용 또는 업데이트를 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 제품이 온라인 스토어에 라이브로 게시되기 ‘전에’ 해당 제품에 액세스할 수 있습니다. 제품이 마켓플레이스에 완전히 게시된 후에 미리 보기 대상 그룹에서 모든 플랜(프라이빗 대상 그룹만 사용할 수 있는 플랜 포함)을 보고 유효성을 검사할 수 있습니다. 전용 대상에게만 계획을 제공할 수 있습니다. 프라이빗 대상 그룹(플랜의 **가용성** 탭에서 정의됨)은 특정 플랜에 독점적으로 액세스할 수 있습니다.

### <a name="add-subscription-ids-manually"></a>수동으로 구독 ID 추가

1. **미리 보기 대상** 페이지에서 제공된 상자에 단일 Azure 구독 ID와 설명(선택 사항)을 추가합니다.
1. 다른 ID를 추가하려면 **ID 추가(최대 10개)** 링크를 선택합니다.
1. 다음 탭(기술 구성)으로 진행하기 전에 **초안 저장** 을 선택합니다.
1. [다음 단계](#next-steps)로 이동합니다.

### <a name="add-subscription-ids-with-a-csv-file"></a>CSV 파일을 사용하여 구독 ID 추가

1. **미리 보기 대상** 페이지에서 **대상 내보내기(csv)** 링크를 선택합니다.
1. Microsoft Excel과 같은 적절한 애플리케이션에서 .CSV 파일을 엽니다.
1. .CSV 파일의 **ID** 열에서 미리 보기 대상 그룹에 추가하려는 Azure 구독 ID를 입력합니다.
1. **설명** 열에서 각 메일 주소에 대한 설명을 추가할 수 있습니다(선택 사항).
1. 열 B에 입력한 각 구독 ID에 대해 "SubscriptionID"의 열 A에 **Type** 을 입력합니다.
1. .CSV 파일로 저장합니다.
1. **미리 보기 대상** 페이지에서 **대상 가져오기(csv)** 링크를 선택합니다.
1. **확인** 대화 상자에서 **예** 를 선택합니다.
1. .CSV 파일을 선택한 다음, **열기** 를 선택합니다.
1. 다음 탭(기술 구성)으로 진행하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [이 제품에 기술 세부 정보 추가](azure-app-technical-configuration.md)

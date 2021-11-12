---
title: Azure Marketplace에서 가상 머신 제품에 대한 미리 보기 대상 그룹 추가
description: Azure Marketplace에서 가상 머신 제품에 대한 미리 보기 대상 그룹을 추가합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.reviewer: amhindma
ms.date: 11/12/2021
ms.openlocfilehash: b4ae30a1573534fbf0ead8b913a6a397e9b0cd27
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400489"
---
# <a name="add-a-preview-audience-for-a-virtual-machine-offer"></a>가상 머신 제품에 대한 미리 보기 대상 그룹 추가

**사용자 미리 보기** 페이지에서 (파트너 센터의 왼쪽 탐색 메뉴에서 선택), 더 광범위 한 상업적 marketplace 대상에 게시 하기 전에 제품의 유효성을 검사 하도록 제한 된 *미리 보기 대상 그룹* 을 구성 합니다. 제품을 게시할 때 지정한 미리 보기 사용자의 구성원이 미리 보기 링크를 사용할 수 있게 됩니다. 사용자가 구성 하는 미리 보기 대상 에게만 미리 보기 링크에 액세스할 수 있으며, **로그인 하기 전에** 제품 미리 보기의 세부 정보를 확인할 수 있습니다.

숨겨진 계획에는 미리 보기 링크를 사용할 수 없습니다. 숨겨진 계획의 경우 미리 보기 대상 사용자는 명령 프롬프트를 통해 제품을 테스트할 수 있습니다.

미리 보기 대상 사용자는 각각에 대 한 선택적 **설명과** 함께 **Azure 구독 id** 로 식별 됩니다. 해당 필드는 고객에게 표시되지 않습니다. Azure Portal의 **[구독](https://go.microsoft.com/fwlink/?LinkId=2122490)** 페이지에서 AZURE 구독 ID를 찾을 수 있습니다.

개별적으로 또는 CSV 파일을 업로드 하 여 하나 이상의 Azure 구독 ID를 추가 합니다. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용이 나 업데이트를 테스트 하기 위해 미리 보기 대상을 수정할 수 있습니다.

> [!IMPORTANT]
> 제품에 대 한 미리 보기 대상에 대 한 변경 내용은 저장 해야 하며 제품을 다시 게시 한 후에만 적용 됩니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 미리 보기 대상은 제품을 테스트 하 고 유효성을 검사할 수 있는 구독 Id의 목록입니다. 여기에는 사용자가 사용할 수 있게 되기 전에 모든 비공개 계획이 포함 됩니다. 이와 대조적으로 제품을 비공개로 설정 하는 경우 사용자가 선택한 고객에 대 한 제품의 표시 여부를 제한 하는 개인 사용자를 지정 해야 합니다. 각 요금제에 대 한 **가격 책정 및 가용성** 페이지에 정의 된 개인 사용자는 제품이 라이브 된 후 특정 요금제에 액세스할 수 있는 구독 id 및/또는 테 넌 트 id의 목록입니다.

미리 보기 대상 그룹을 구성한 후에는 왼쪽 탐색 메뉴의 **계획 개요** 에서 다음 탭으로 진행 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [계획 만들기](azure-vm-create-plans.md)

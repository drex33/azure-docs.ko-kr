---
title: EDIFACT 메시지의 UNH 2.5 세그먼트
description: 엔터프라이즈 통합이 포함된 팩 Azure Logic Apps의 UNH2.5 세그먼트를 사용하여 EDIFACT 메시지 해결
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divyaswarnkar, azla
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: d69515947c406677724746ab37b8cddef87ff6d0
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236883"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps에서 UNH2.5 세그먼트를 사용하여 EDIFACT 문서를 처리합니다.

EDIFACT 문서에 UNH2.5 세그먼트가 있는 경우, 해당 세그먼트는 스키마 조회에 사용됩니다. 예를 들어 이 샘플 EDIFACT 메시지에서 UNH 필드는 `EAN008`입니다.

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

이 메시지를 처리하려면 아래에 설명된 다음 단계를 수행하세요.

1. 스키마를 업데이트하세요.

1. 규약 설정을 확인하세요.

## <a name="update-the-schema"></a>스키마 업데이트

메시지를 처리하려면 UNH 2.5 루트 노드 이름을 가진 스키마를 배포해야 합니다. 예를 들어 샘플 UNH 필드의 스키마 루트 이름은 `EFACT_D03B_ORDERS_EAN008`입니다. UNH2.5 세그먼트가 다른 각각의 `D03B_ORDERS`에 대해 개별 스키마를 배포해야 합니다.

## <a name="add-schema-to-edifact-agreement"></a>EDIFACT 규약에 스키마 추가

### <a name="edifact-decode"></a>EDIFACT 디코딩

들어오는 메시지를 디코딩하려면 EDIFACT 규약의 수신 설정에서 스키마를 설정하세요.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 여세요.

1. 통합 계정에 스키마를 추가하세요.

1. EDIFACT 규약의 수신 설정에서 스키마를 구성하세요.

1. EDIFACT 규약을 선택하고 **JSON으로 편집** 을 선택하세요. UNH2.5 값을 수신 규약의 섹션 `schemaReferences`에 추가하세요.

   ![UNH2.5를 추가하여 규약 수신](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 인코딩

들어오는 메시지를 인코딩하려면 EDIFACT 규약 전송 설정에서 스키마를 구성하세요.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 여세요.

1. 통합 계정에 스키마를 추가하세요.

1. EDIFACT 규약의 전송 설정에서 스키마를 구성하세요.

1. EDIFACT 규약을 선택하고 **JSON으로 편집** 을 선택하세요. UNH2.5 값을 전송 규약의 섹션 `schemaReferences`에 추가하세요.

   ![UNH2.5를 추가하여 규약 전송](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>다음 단계

* [통합 계정 규약](../logic-apps/logic-apps-enterprise-integration-agreements.md)에 대해 자세히 알아보기

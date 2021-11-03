---
title: 질문 답변 서비스 구성
description: 이 문서에서는 사용자 지정 질문 답변 사용 리소스에 대한 고급 구성을 간략하게 설명합니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2eae7e38422aa597c9b093dbefaef553cb084d09
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102911"
---
# <a name="configure-custom-question-answering-enabled-resources"></a>사용자 지정 질문 답변 사용 리소스 구성

다른 Cognitive Search 리소스를 사용하도록 질문 답변을 구성할 수 있습니다.

## <a name="change-cognitive-search-resource"></a>Cognitive Search 리소스 변경

> [!WARNING]
> 언어 리소스와 연결된 Azure Search Service 변경하면 이미 있는 모든 프로젝트/기술 자료에 액세스할 수 없게 됩니다. Azure Search Service 변경하기 전에 기존 프로젝트를 내보내야 합니다.

Azure Portal 통해 언어 리소스 및 해당 의존성(예: 검색)을 만드는 경우 Search Service 만들어지고 언어 리소스에 연결됩니다. 이러한 리소스를 만든 후 **기능** 탭에서 검색 리소스를 업데이트할 수 있습니다.

1.  Azure Portal 언어 리소스로 이동합니다.

2.  **기능을** 선택하고 언어 리소스와 연결하려는 Azure Cognitive Search 서비스를 선택합니다.

> [!div class="mx-imgBorder"]
> ![TA에 QnA 추가](../media/configure-resources/update-custom-feature.png)

3.  **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [저장 데이터 암호화](./encrypt-data-at-rest.md)

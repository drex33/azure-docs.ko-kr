---
title: LUIS, QnA Maker 및 Text Analytics 언어용 Azure Cognitive Service로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 LUIS, QnA Maker 및 Text Analytics 애플리케이션을 마이그레이션해야 하는지 알아봅니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1e2a2945700e0537b7ae4b220f97eafb1ce19a69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052940"
---
# <a name="migrating-to-azure-cognitive-service-for-language"></a>언어용 Azure Cognitive Service로 마이그레이션

2021년 11월 2일에 Azure Cognitive Service for Language가 공개 미리 보기로 릴리스되었습니다. 이 언어 서비스는 Text Analytics, QnA Maker 및 LUIS 서비스 제품을 통합하고 몇 가지 새로운 기능도 제공합니다. 

## <a name="do-i-need-to-migrate-to-the-language-service-if-i-am-using-text-analytics"></a>Text Analytics 사용하는 경우 언어 서비스로 마이그레이션해야 합니까?

Text Analytics 언어 서비스에 통합되었으며 해당 기능을 계속 사용할 수 있습니다. Text Analytics 사용하는 경우 애플리케이션은 주요 변경 없이 계속 작동해야 합니다. 이전 애플리케이션을 업데이트해야 하는 경우 [Text Analytics v2 마이그레이션 가이드](migrate-from-text-analytics-v2.md)을 참조할 수도 있습니다. 

사용 가능한 빠른 시작 문서 중 하나를 사용하여 서비스 엔드포인트 및 API 호출에 대한 최신 정보를 확인하는 것이 좋습니다. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-luis"></a>LUIS를 사용하는 경우 언어 서비스로 마이그레이션할 어떻게 할까요? 있나요?

LUIS(Language Understanding)를 사용하는 경우 [LUIS JSON 파일을](../conversational-language-understanding/concepts/backwards-compatibility.md) 새 대화형 언어 이해 기능으로 가져올 수 있습니다. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-qna-maker"></a>QnA Maker 사용하는 경우 언어 서비스로 마이그레이션할 어떻게 할까요? 있나요?

QnA Maker 사용하는 경우 QnA Maker 질문 답변으로 기술 자료 마이그레이션에 대한 자세한 내용은 [마이그레이션 가이드를](../question-answering/how-to/migrate-qnamaker.md) 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Service for Language 개요](../overview.md)
* [대화형 Language Understanding 개요](../conversational-language-understanding/overview.md)
* [질문 답변 개요](../question-answering/overview.md)

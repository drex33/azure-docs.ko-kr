---
title: LUIS, QnA Maker 및 Text Analytics에서 언어에 대 한 Azure 인식 서비스로 마이그레이션합니다.
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 LUIS, QnA Maker 및 Text Analytics에서 응용 프로그램을 마이그레이션해야 하는지 여부를 알아봅니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 12/03/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 304a9f8b6fe3e33c1cc751443b34169a83d1618f
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542518"
---
# <a name="migrating-to-azure-cognitive-service-for-language"></a>언어에 대 한 Azure 인식 서비스로 마이그레이션

11 월 2 2021에는 언어에 대 한 Azure 인식 서비스가 공개 미리 보기로 출시 되었습니다. 이 언어 서비스는 Text Analytics, QnA Maker 및 LUIS 서비스 제공 기능을 통합 하 고 여러 가지 새로운 기능을 제공 합니다. 

## <a name="do-i-need-to-migrate-to-the-language-service-if-i-am-using-text-analytics"></a>Text Analytics를 사용 하는 경우 언어 서비스로 마이그레이션해야 하나요?

Text Analytics 언어 서비스로 통합 되었으며 해당 기능을 계속 사용할 수 있습니다. Text Analytics 사용 하는 경우 응용 프로그램은 주요 변경 내용 없이 계속 작동 해야 합니다. 이전 응용 프로그램을 업데이트 해야 하는 경우 [Text Analytics 마이그레이션 가이드](migrate-language-service-latest.md)도 볼 수 있습니다. 

서비스 끝점 및 API 호출에 대 한 최신 정보를 보려면 사용 가능한 빠른 시작 문서 중 하나를 사용 하는 것이 좋습니다. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-luis"></a>LUIS를 사용 하는 경우 언어 서비스로 어떻게 할까요? 마이그레이션할 수 있나요?

Language Understanding (LUIS)를 사용 하는 경우 [LUIS JSON 파일](../conversational-language-understanding/concepts/backwards-compatibility.md) 을 새로운 대화형 언어 이해 기능으로 가져올 수 있습니다. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-qna-maker"></a>QnA Maker을 사용 하는 경우 언어 서비스로 어떻게 할까요? 마이그레이션

QnA Maker를 사용 하는 경우 QnA Maker에서 질문 응답으로 기술 자료를 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 가이드](../question-answering/how-to/migrate-qnamaker.md) 를 참조 하세요.

## <a name="see-also"></a>참조

* [Azure Cognitive Service for Language 개요](../overview.md)
* [대화형 Language Understanding 개요](../conversational-language-understanding/overview.md)
* [질문에 대 한 답변 개요](../question-answering/overview.md)

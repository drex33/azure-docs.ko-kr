---
title: Azure Cognitive Service for Language의 엔터티 링크 설정이란 무엇인가요?
titleSuffix: Azure Cognitive Services
description: 텍스트에서 엔터티를 추출하고 온라인 기술 자료에 대한 링크를 제공하는 Azure Cognitive Services의 엔터티 링크 설정에 대한 개요입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: bbd4483702055a4bebb05251606fb705b1e0deb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487470"
---
# <a name="what-is-entity-linking-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language의 엔터티 링크 설정이란 무엇인가요?

엔터티 링크 설정은 [Azure Cognitive Service for Language](../overview.md)에서 제공하는 기능 중 하나입니다. 이 기능은 서면 언어와 관련된 지능형 애플리케이션을 개발하기 위한 클라우드의 기계 학습 및 AI 알고리즘 모음입니다. 엔터티 링크 설정은 텍스트에서 찾은 엔터티의 ID를 식별하고 명확하게 합니다. 예를 들어 "*We go to Seattle last week.* "문장에서 "*Seattle*"이라는 단어는 Wikipedia의 추가 정보 링크와 함께 식별됩니다.

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.

* [**빠른 시작**](quickstart.md) 은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](how-to/call-api.md)에는 보다 구체적인 방법으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

결과는 온라인 기술 자료로 Wikipedia에 대한 URL과 함께 텍스트에서 인식된 엔터티의 모음이 됩니다. 

## <a name="responsible-ai"></a>Responsible AI 

AI 시스템에는 기술뿐만 아니라 기술을 사용하는 사람, 영향을 받는 사람, 배포되는 환경이 포함됩니다. 시스템에서 책임감 있는 AI 사용 및 배포에 대해 알아보려면 [엔터티 링크 설정에 대한 투명성 참고사항](/legal/cognitive-services/language-service/transparency-note?context=/azure/cognitive-services/language-service/context/context)을 읽어보세요. 자세한 내용은 다음 문서를 참조하세요.

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>다음 단계

엔터티 연결 기능을 사용하여 시작하는 방법에는 두 가지가 있습니다.
* [Language Studio](../language-studio.md): 코드를 작성할 필요 없이 여러 Azure Cognitive Service for Language 기능을 사용해 볼 수 있는 웹 기반 플랫폼입니다.
* REST API 및 클라이언트 라이브러리 SDK를 사용하여 서비스에 요청하는 방법에 대한 지침은 [빠른 시작 문서](quickstart.md)를 참조하세요.  

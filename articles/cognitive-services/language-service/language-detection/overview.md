---
title: Azure Cognitive Service for Language의 언어 탐지란 무엇인가요?
titleSuffix: Azure Cognitive Services
description: 언어 코드를 반환하여 텍스트가 작성된 언어를 탐지하는 데 도움이 되는 Azure Cognitive Services의 언어 탐지에 대한 개요입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 5ca0304ca413e17a70d39641f145272a2c58a792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101701"
---
# <a name="what-is-language-detection-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language의 언어 탐지란 무엇인가요?

언어 탐지는 [Azure Cognitive Service for Language](../overview.md)에서 제공하는 기능 중 하나입니다. 이 기능은 서면 언어와 관련된 지능형 애플리케이션을 개발하기 위한 클라우드의 기계 학습 및 AI 알고리즘 모음입니다. 언어 탐지는 문서가 작성된 언어를 탐지하고 다양한 언어, 변형, 방언 및 일부 지역/문화 언어에 대한 언어 코드를 반환합니다. 

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.

* [**빠른 시작**](quickstart.md) 은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](how-to/call-api.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Docker 컨테이너를 사용하여 온-프레미스 배포

사용 가능한 Docker 컨테이너를 사용하여 [이 기능을 온-프레미스에 배포](how-to/use-containers.md)합니다. 이러한 Docker 컨테이너를 사용하면 규정 준수, 보안 또는 기타 운영상의 이유로 서비스를 데이터에 더 가깝게 가져올 수 있습니다.

## <a name="responsible-ai"></a>Responsible AI 

AI 시스템에는 기술뿐만 아니라 기술을 사용하는 사람, 영향을 받는 사람, 배포되는 환경이 포함됩니다. [언어 감지에 대한 투명도 참고](/legal/cognitive-services/language-service/transparency-note-language-detection?context=/azure/cognitive-services/language-service/context/context)를 읽고 시스템에서 책임감 있는 AI 사용 및 배포에 대해 알아봅니다. 자세한 내용은 다음 문서에서도 확인할 수 있습니다.

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>다음 단계

엔터티 연결 기능을 사용하여 시작하는 방법에는 두 가지가 있습니다.
* [Language Studio](../language-studio.md): 코드를 작성할 필요 없이 여러 Azure Cognitive Service for Language 기능을 사용해 볼 수 있는 웹 기반 플랫폼입니다.
* REST API 및 클라이언트 라이브러리 SDK를 사용하여 서비스에 요청하는 방법에 대한 지침은 [빠른 시작 문서](quickstart.md)를 참조하세요.  

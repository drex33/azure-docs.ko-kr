---
title: URL 도구를 사용하여 기술 자료에서 답변 가져오기 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 cURL 또는 Postman과 같은 URL 테스트 도구를 사용하여 기술 자료에서 답변을 가져오는 과정을 안내합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
zone_pivot_groups: URL-test-interface
ms.topic: how-to
ms.date: 07/16/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 87a49dd023da073cf8e66bac16251db97aaa9d0d
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128914"
---
# <a name="get-an-answer-from-a-qna-maker-knowledge-base"></a>QNA Maker 기술 자료로부터 답변 받기

[!INCLUDE [Custom question answering](../includes/new-version.md)]

> [!NOTE]
> 이 설명서는 최신 릴리스에는 적용되지 않습니다. 최신 질문 답변 API를 사용하는 방법에 대한 자세한 내용은 질문 [답변 작성 가이드](../../language-service/question-answering/how-to/authoring.md)를 참조하세요.

::: zone pivot="url-test-tool-curl"

[!INCLUDE [Get answer using cURL](../includes/quickstart-test-tool-curl.md)]

::: zone-end

::: zone pivot="url-test-tool-postman"

[!INCLUDE [Get answer using Postman](../includes/quickstart-test-tool-Postman.md)]

::: zone-end


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [배치 파일을 사용하여 기술 자료 테스트](../how-to/test-knowledge-base.md#batch-test-with-tool)

메타데이터에 대해 자세히 알아봅니다.
* [제작 - QnA 쌍에 메타데이터 추가](../How-To/edit-knowledge-base.md#add-metadata)
* [쿼리 예측 - 메타데이터를 통한 응답 필터링](../How-To/query-knowledge-base-with-metadata.md)

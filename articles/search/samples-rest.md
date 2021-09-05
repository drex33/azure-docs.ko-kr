---
title: REST 샘플
titleSuffix: Azure Cognitive Search
description: 검색 또는 관리 REST API를 사용하는 Azure Cognitive Search 데모 REST 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 8e25c921b390211f159e7f845dca9c7cbc9168c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528828"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search용 REST 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 REST API 샘플에 대해 알아봅니다. 이러한 샘플은 [**검색 REST API**](/rest/api/searchservice)를 사용합니다.

REST는 Azure Cognitive Search에 대한 결정적인 프로그래밍 인터페이스이며, 프로그래밍 방식으로 호출할 수 있는 모든 작업은 먼저 REST에서 사용 가능하고, 그런 다음, SDK에서 사용할 수 있습니다. 이러한 이유로 설명서의 대부분의 예제에서는 REST API를 활용하여 중요한 개념을 설명합니다.

REST 샘플은 일반적으로 Postman에서 개발되고 테스트되지만 HTTP 호출을 지원하는 모든 클라이언트를 사용할 수 있습니다.

+ HTTP 호출을 작성하는 데 도움이 되는 [빠른 시작: REST API를 사용하여 Azure Cognitive Search 인덱스 만들기](search-get-started-rest.md)로 시작하세요.
+ Visual Studio Code에서 작업하는 경우 [Azure Cognitive Search용 Visual Studio Code 확장](search-get-started-vs-code.md)(현재 미리 보기 상태)을 사용해 보세요.

## <a name="doc-samples"></a>문서 샘플

Cognitive Search 팀의 코드 샘플은 기능 및 워크플로를 보여줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조됩니다. GitHub의 [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples)에서 이러한 샘플을 찾을 수 있습니다.

| 샘플 | 아티클 |
|---------|---------|
| [빠른 시작](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | [빠른 시작: REST API를 사용하여 검색 인덱스 만들기](search-get-started-rest.md)에 대한 소스 코드입니다. 이 문서에서는 샘플 데이터를 사용하여 검색 인덱스를 만들고, 로드하고, 쿼리하는 기본 워크플로를 다룹니다. |
| [자습서](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | [자습서: REST 및 AI를 사용하여 Azure Blob에서 검색 가능한 콘텐츠 생성](cognitive-search-tutorial-blob.md)에 대한 소스 코드입니다. 이 문서에서는 정보를 추출하고 구조를 유추하기 위해 Azure Blob을 반복하는 기술 세트를 만드는 방법을 보여 줍니다.|
| [디버그 세션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | [자습서: 기술 세트에 대한 변경 내용 진단, 수정 및 적용](cognitive-search-tutorial-debug-sessions.md)에 대한 소스 코드입니다. 이 문서에서는 Azure Portal에서 기술 세트 디버그 세션을 사용하는 방법을 보여 줍니다. REST는 디버그하는 동안 사용되는 개체를 만드는 데 사용됩니다.|
| [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | [자습서: 전화 번호에 대한 사용자 지정 분석기 만들기](tutorial-create-custom-analyzer.md)에 대한 소스 코드입니다. 이 문서에서는 분석기를 사용하여 검색 가능한 콘텐츠에서 패턴 및 특수 문자를 유지하는 방법을 설명합니다.|
| [knowledge-store](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | [REST 및 Postman을 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)에 대한 소스 코드입니다. 이 문서에서는 지식 마이닝 워크플로에 사용되는 기술 자료 저장소를 채우는 데 필요한 단계를 설명합니다. |
| [projections](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | [지식 저장소의 프로젝션 정의](knowledge-store-projections-examples.md)에 대한 소스 코드. 이 문서에서는 기술 자료 저장소에서 물리적 데이터 구조를 지정하는 방법을 설명합니다.|
| [index-encrypted-blobs](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | [Blob 인덱서 및 기술 세트를 사용하여 암호화된 Blob을 인덱싱하는 방법](search-howto-index-encrypted-blobs.md)에 대한 소스 코드입니다. 이 문서에서는 Azure Key Vault를 사용하여 이전에 암호화된 Azure Blob Storage의 문서를 인덱싱하는 방법을 보여 줍니다. |

> [!Tip]
> [샘플 브라우저](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search)를 통해 제품, 서비스 및 언어별로 필터링하여 GitHub에서 Microsoft 코드 샘플을 검색해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플은 Cognitive Search 팀에서도 게시하지만 설명서에서 참조되지 않습니다. 관련된 추가 정보 파일은 사용 지침을 제공합니다.

| 샘플 | Description |
|---------|-------------|
| [Query-examples](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | 유사 항목 검색, RegEx 및 와일드카드 검색, 자동 완성 등을 비롯한 다양한 쿼리 기법을 보여 주는 Postman 컬렉션입니다. |

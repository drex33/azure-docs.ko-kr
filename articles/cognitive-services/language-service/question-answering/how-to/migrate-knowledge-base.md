---
title: 프로젝트 및 기술 자료 마이그레이션 - 사용자 지정 질문 답변
description: 사용자 지정 질문 응답 프로젝트를 마이그레이션하려면 한 리소스에서 프로젝트를 내보낸 다음 다른 리소스로 가져와야 합니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 46c092fa096c6f68299f6e0ac8254bfd25c7ce75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103004"
---
# <a name="migrate-projects-and-question-answer-sources"></a>프로젝트 및 질문 답변 원본 마이그레이션

다음과 같은 여러 가지 이유로 프로젝트의 복사본을 만들 수 있습니다.

* 백업 및 복원 프로세스를 구현하려면
* CI/CD 파이프라인과 통합
* 데이터를 다른 지역으로 이동하려는 경우

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.
* Azure Portal 사용자 지정 질문 답변 기능이 활성화된 [언어 리소스입니다.](https://aka.ms/create-language-resource) 리소스를 만들 때 선택한 Azure Active Directory ID, 구독 및 언어 리소스 이름을 기억하세요.

## <a name="export-a-project"></a>프로젝트 내보내기

프로젝트를 내보내면 단일 프로젝트에 포함된 모든 원본 질문 답변 원본을 마이그레이션하거나 백업할 수 있습니다.

1. Language Studio 에 [로그인합니다.](https://language.azure.com/)
1. 프로젝트를 마이그레이션할 언어 리소스를 선택합니다.
1. **프로젝트** 페이지에는 Excel 또는 TSV의 두 가지 형식으로 내보낼 수 있는 옵션이 있습니다. 그러면 파일의 내용이 결정됩니다. 파일 자체는 모든 기술 자료가 포함된 .zip 내보냅니다.

## <a name="import-a-project"></a>프로젝트 가져오기  

1. 이전에 내보낸 프로젝트의 대상이 될 언어 리소스를 선택합니다.
1. **프로젝트** 페이지에서 **가져오기를** 선택하고 내보내기를 선택할 때 사용되는 형식을 선택합니다. 그런 다음 내보낸 프로젝트가 포함된 로컬 .zip 파일을 찾습니다. 새로 가져온 프로젝트의 이름을 입력하고 **완료를** 선택합니다.

## <a name="export-question-and-answers"></a>질문 및 답변 내보내기

1. 개별 질문 답변 원본을 마이그레이션할 언어 리소스를 선택합니다.
1. 내보내려는 질문 및 답변 원본이 포함된 프로젝트를 선택합니다.
1. 기술 자료 편집 페이지의 도구 모음에서 서식 있는 텍스트 사용 오른쪽에 있는 말줄임표( `...` ) 아이콘을  선택합니다. Excel 또는 TSV에서 내보낼 수 있는 옵션이 있습니다.

## <a name="import-question-and-answers"></a>질문 및 답변 가져오기

1. 이전에 내보낸 질문 및 답변 원본의 대상이 될 언어 리소스를 선택합니다.
1. 질문 및 답변 원본을 가져오려는 프로젝트를 선택합니다.
1. 기술 자료 편집 페이지의 도구 모음에서 서식 있는 텍스트 사용 오른쪽에 있는 말줄임표( `...` ) 아이콘을  선택합니다. Excel 또는 TSV 파일을 가져올 수 있습니다.
1. 파일 선택 옵션을 사용하여 파일의 로컬 위치로 **이동한** 후 **완료를** 선택합니다.

<!-- TODO: Replace Link-->
### <a name="test"></a>테스트

**테스트** 패널을 시작할 **기술 자료 편집** 페이지의 도구 모음에서 **테스트** 옵션을 선택하여 질문 답변 원본을 테스트합니다. [기술 자료 테스트](../../../qnamaker/How-To/test-knowledge-base.md) 방법을 알아보세요.

### <a name="deploy"></a>배포

<!-- TODO: Replace Link-->
기술 자료 **배포** 및 채팅 봇 만들기 기술 자료 를 [배포하는](../../../qnamaker/Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)방법을 알아봅니다.

## <a name="chat-logs"></a>채팅 로그

프로젝트 또는 기술 자료로 채팅 로그를 마이그레이션할 수 있는 방법은 없습니다. 진단 로그를 사용하도록 설정하면 채팅 로그가 연결된 Azure Monitor 리소스에 저장됩니다.

## <a name="next-steps"></a>다음 단계

<!-- TODO: Replace Link-->
> [!div class="nextstepaction"]
> [기술 자료 편집](../../../qnamaker/How-To/edit-knowledge-base.md)

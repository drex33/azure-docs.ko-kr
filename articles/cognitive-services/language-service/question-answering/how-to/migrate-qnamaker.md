---
title: QnA Maker 기술 자료를 사용자 지정 질문 답변으로 마이그레이션
description: 최신 기능을 사용 하려면 레거시 QnAMaker 기술 자료를 사용자 지정 질문 답변으로 마이그레이션합니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.author: diagarw
author: DishaAgarwal
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2ffd43a2747d462257b4223285b12b5f81e3e4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103027"
---
# <a name="migrate-from-qna-maker-to-custom-question-answering"></a>QnA Maker에서 사용자 지정 질문 답변으로 마이그레이션

사용자 지정 질문 답변은 심층 학습 ranker, 정확한 답변 및 종단 간 지역 지원을 통해 향상 된 관련성을 비롯 한 몇 가지 새로운 기능을 갖춘 2021 년 5 월에 도입 되었습니다. 각 사용자 지정 질문 응답 프로젝트는 QnA Maker의 기술 자료와 동일 합니다. QnA Maker 리소스의 기술 자료를 [언어 리소스](https://aka.ms/create-language-resource)내의 사용자 지정 질문 응답 프로젝트로 쉽게 마이그레이션할 수 있습니다. 여러 QnA Maker 리소스의 기술 자료를 특정 언어 리소스로 마이그레이션하도록 선택할 수도 있습니다.

기술 자료를 성공적으로 마이그레이션하려면 **마이그레이션을 수행 하는 계정에 선택한 QnA Maker 및 언어 리소스에 대 한 참가자 액세스 권한이 있어야** 합니다. 기술 자료가 마이그레이션되면 다음과 같은 세부 정보가 새 사용자 지정 질문 응답 프로젝트에 복사 됩니다.

- QnA 쌍은 활성 학습 제안을 포함 합니다.
- QnA Maker 리소스의 동의어 및 기본 대답입니다.
- 기술 자료 이름이 프로젝트 설명 필드에 복사 됩니다.

RBAC (역할 기반 액세스 제어)와 같은 리소스 수준 설정은 새 리소스로 마이그레이션되지 않습니다. 이러한 리소스 수준 설정은 언어 리소스 사후 마이그레이션에 맞게 다시 구성 해야 합니다. 또한 언어 리소스에 대 한 [분석을 다시 사용 하도록 설정](analytics.md) 해야 합니다.

## <a name="steps-to-migrate"></a>마이그레이션 단계

아래 단계에 따라 기술 자료를 마이그레이션할 수 있습니다.

1. 미리 설정 된 사용자 지정 질문 응답을 사용 하 여 [언어 리소스](https://aka.ms/create-language-resource) 를 만듭니다. Azure Portal에서 언어 리소스를 만들 때 사용자 지정 질문 응답을 사용 하도록 설정 하는 옵션이 표시 됩니다. 이 옵션을 선택 하 고 계속 하면 기술 자료를 저장 하는 Azure Search 세부 정보를 묻는 메시지가 표시 됩니다.

2. 여러 언어의 기술 자료를 언어 리소스에 추가 하려면 [Language Studio](https://lanuage.azure.com) 를 방문 하 여 첫 번째 사용자 지정 질문 응답 프로젝트를 만들고 아래와 같이 첫 번째 옵션을 선택 합니다. 언어 리소스의 언어 설정은 프로젝트를 만들 때만 지정할 수 있습니다. 기존 기술 자료를 단일 언어로 언어 리소스로 마이그레이션하려는 경우에는이 단계를 건너뛸 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![언어 UI 화면 선택 스크린샷](../media/migrate-qnamaker/choose-language.png)

3. [https://www.qnamaker.ai](https://www.qnamaker.ai)을 방문 하 여 기술 자료 페이지의 마이그레이션 메모에서 **마이그레이션 시작** 을 선택 합니다. 마이그레이션을 시작 하는 대화 상자가 열립니다.

   :::image type="content" source="../media/migrate-qnamaker/start-migration.png" alt-text="Qnamaker.ai의 배너에 표시 되는 마이그레이션 시작 단추" lightbox="../media/migrate-qnamaker/start-migration.png":::

4. 마이그레이션을 시작 하는 데 필요한 세부 정보를 입력 합니다. 테 넌 트가 자동으로 선택 됩니다. 테 넌 트를 전환 하도록 선택할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![테 넌 트 선택 옵션 주위에 빨간색 선택 상자를 사용 하 여 QnAMaker 마이그레이션](../media/migrate-qnamaker/tenant-selection.png)

5. 마이그레이션할 기술 자료를 포함 하는 QnA Maker 리소스를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![QnAMaker 리소스 선택 옵션 주위에 빨간색 선택 상자를 사용 하 여 QnAMaker 마이그레이션](../media/migrate-qnamaker/select-resource.png)

6. 기술 자료를 마이그레이션하려는 언어 리소스를 선택 합니다. 사용자 지정 질문 응답을 사용 하는 해당 언어 리소스만 볼 수 있습니다. 언어 리소스의 언어 설정이 옵션에 표시 됩니다. 언어 설정이 지정 되지 않은 경우 여러 언어의 기술 자료를 QnA Maker 리소스에서 언어 리소스로 마이그레이션할 수 없습니다.

   > [!div class="mx-imgBorder"]
   > ![언어 리소스 옵션에 대 한 빨간색 선택 상자를 사용 하 여 QnAMaker 마이그레이션 현재 선택한 리소스에는 해당 언어가 지정 되지 않은 정보가 포함 됩니다.](../media/migrate-qnamaker/language-setting.png)

    여러 언어의 기술 자료를 언어 리소스로 마이그레이션하려면 언어 리소스에 대 한 첫 번째 사용자 지정 질문 응답 프로젝트를 만들 때 여러 언어 설정을 사용 하도록 설정 해야 합니다. #2 단계의 지침에 따라이 작업을 수행할 수 있습니다. **언어 리소스의 언어 설정이 지정 되지 않은 경우 선택한 QnA Maker 리소스의 언어가 할당 됩니다**.

7. 마이그레이션할 모든 기술 자료를 선택 하 > **다음** 을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![기술 자료 선택 옵션 주위에 빨간색 선택 상자를 사용 하 여 QnAMaker를 세 가지 기술 자료 이름을 표시 하는 드롭다운으로 마이그레이션합니다.](../media/migrate-qnamaker/select-knowledge-bases.png)

8. 마이그레이션하려는 기술 자료를 검토할 수 있습니다. 사용자 지정 질문 응답 프로젝트에 대 한 보다 엄격한 유효성 검사 규칙을 따르기 때문에 프로젝트 이름에 유효성 검사 오류가 있을 수 있습니다. 

    > [!CAUTION]
    > 대상 언어 리소스에 이미 있는 프로젝트와 동일한 이름으로 기술 자료를 마이그레이션하는 경우 **프로젝트의 콘텐츠** 는 선택한 기술 자료의 내용에 의해 재정의 됩니다.

    > [!div class="mx-imgBorder"]
    > ![프로젝트 이름을 시작 하는 오류 메시지의 스크린샷 특수 문자를 포함할 수 없음](../media/migrate-qnamaker/special-characters.png)

9. 유효성 검사 오류를 해결 한 후 **다음** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![특수 문자가 제거 된 스크린 샷](../media/migrate-qnamaker/validation-errors.png)

10. 마이그레이션이 진행 되는 데 몇 분 정도 걸릴 수 있습니다. 진행 중인 동안에는 마이그레이션을 취소 하지 마십시오. 마이그레이션 후 [언어 스튜디오](https://lanuage.azure.com) 에서 마이그레이션된 프로젝트로 이동할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![언어 스튜디오를 사용 하 여 게시할 수 있는 정보를 사용 하 여 성공적으로 마이그레이션된 기술 자료의 스크린샷](../media/migrate-qnamaker/migration-success.png)

    기술 자료가 사용자 지정 질문 응답 프로젝트로 마이그레이션하지 못한 경우 오류가 표시 됩니다. 가장 일반적인 마이그레이션 오류는 다음과 같은 경우에 발생 합니다.
    
    - 원본 및 대상 리소스가 잘못 되었습니다.
    - 빈 KB (기술 자료)를 마이그레이션하려고 합니다.
    - 대상 리소스에 연결 된 Azure Search 인스턴스에 대 한 제한에 도달 했습니다.

    > [!div class="mx-imgBorder"]
    > ![예제 오류가 발생 한 마이그레이션 실패의 스크린샷](../media/migrate-qnamaker/migration-errors.png)

    이러한 오류를 해결 한 후 마이그레이션을 다시 실행할 수 있습니다.

11. 마이그레이션은 기술 자료의 테스트 인스턴스만 복사 합니다. 마이그레이션이 완료 되 면 기술 자료를 수동으로 배포 하 여 테스트 인덱스를 프로덕션 인덱스에 복사 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 진단 로그](analytics.md)를 사용 하 여 분석을 다시 사용 하도록 설정 하는 방법에 대해 알아봅니다.

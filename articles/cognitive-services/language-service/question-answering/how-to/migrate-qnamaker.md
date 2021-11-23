---
title: QnA Maker 기술 자료에서 사용자 지정 질문 답변으로 마이그레이션
description: 최신 기능을 활용하려면 레거시 QnAMaker 기술 자료 를 사용자 지정 질문 답변으로 마이그레이션합니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.author: diagarw
author: DishaAgarwal
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 571f1dd77083080369f397e24d703220059b187a
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132939446"
---
# <a name="migrate-from-qna-maker-to-custom-question-answering"></a>QnA Maker 사용자 지정 질문 답변으로 마이그레이션

사용자 지정 질문 답변은 딥 러닝 순위 매기기, 정확한 답변 및 엔드 투 엔드 지역 지원을 사용하여 향상된 관련성을 포함하여 몇 가지 새로운 기능으로 2021년 5월에 도입되었습니다. 각 사용자 지정 질문 답변 프로젝트는 QnA Maker 기술 자료와 동일합니다. QnA Maker 리소스에서 언어 리소스 내의 사용자 지정 질문 답변 프로젝트로 기술 자료를 쉽게 마이그레이션할 수 [있습니다.](https://aka.ms/create-language-resource) 여러 QnA Maker 리소스에서 특정 언어 리소스로 기술 자료를 마이그레이션하도록 선택할 수도 있습니다.

기술 자료를 성공적으로 마이그레이션하려면 **마이그레이션을 수행하는 계정에 선택한 QnA Maker 및 언어 리소스 에 대한 기여자 액세스 권한이 필요합니다.** 기술 자료가 마이그레이션되면 다음 세부 정보가 새 사용자 지정 질문 답변 프로젝트에 복사됩니다.

- 활성 학습 제안을 포함하는 QnA 쌍
- QnA Maker 리소스의 동의어 및 기본 대답입니다.
- 기술 자료 이름이 프로젝트 설명 필드에 복사됩니다.

RBAC(역할 기반 액세스 제어)와 같은 리소스 수준 설정은 새 리소스로 마이그레이션되지 않습니다. 이러한 리소스 수준 설정은 마이그레이션 후 언어 리소스에 대해 다시 구성해야 합니다. 또한 언어 리소스에 대한 [분석을 다시 사용하도록 설정해야](analytics.md) 합니다.

## <a name="steps-to-migrate"></a>마이그레이션 단계

아래 단계에 따라 기술 자료 마이그레이션을 수행할 수 있습니다.

1. 사용자 지정 질문 답변이 미리 활성화된 [언어 리소스를](https://aka.ms/create-language-resource) 만듭니다. Azure Portal 언어 리소스를 만들 때 사용자 지정 질문 대답을 사용하도록 설정하는 옵션이 표시됩니다. 해당 옵션을 선택하고 계속 진행하면 기술 자료 저장을 위한 Azure Search 세부 정보가 표시됩니다.

2. 언어 리소스에 여러 언어로 된 기술 자료를 추가하려면 [Language Studio를](https://language.azure.com/) 방문하여 첫 번째 사용자 지정 질문 답변 프로젝트를 만들고 아래와 같이 첫 번째 옵션을 선택합니다. 언어 리소스에 대한 언어 설정은 프로젝트를 만들 때만 지정할 수 있습니다. 단일 언어의 기존 기술 자료를 언어 리소스로 마이그레이션하려는 경우 이 단계를 건너뛸 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![언어 UI 선택 화면의 스크린샷"](../media/migrate-qnamaker/choose-language.png)

3. [https://www.qnamaker.ai](https://www.qnamaker.ai)를 방문하여 기술 자료 페이지의 마이그레이션 참고 사항에서 마이그레이션 **시작을** 선택합니다. 마이그레이션을 시작하기 위한 대화 상자가 열립니다.

   :::image type="content" source="../media/migrate-qnamaker/start-migration.png" alt-text="qnamaker.ai 배너에 표시되는 마이그레이션 시작 단추" lightbox="../media/migrate-qnamaker/start-migration.png":::

4. 마이그레이션을 시작하는 데 필요한 세부 정보를 입력합니다. 테넌트는 자동으로 선택됩니다. 테넌트 전환을 선택할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![테넌트 선택 옵션 주위에 빨간색 선택 상자가 있는 QnAMaker 마이그레이션](../media/migrate-qnamaker/tenant-selection.png)

5. 마이그레이션할 기술 자료가 포함된 QnA Maker 리소스를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![QnAMaker 리소스 선택 옵션 주위에 빨간색 선택 상자가 있는 QnAMaker 마이그레이션](../media/migrate-qnamaker/select-resource.png)

6. 기술 자료를 마이그레이션할 언어 리소스를 선택합니다. 사용자 지정 질문 답변이 활성화된 언어 리소스만 볼 수 있습니다. 언어 리소스에 대한 언어 설정이 옵션에 표시됩니다. 언어 설정이 지정되지 않은 경우 여러 언어로 된 기술 자료를 QnA Maker 리소스에서 언어 리소스로 마이그레이션할 수 없습니다.

   > [!div class="mx-imgBorder"]
   > ![현재 선택한 리소스에 언어가 지정되지 않은 정보가 포함된 언어 리소스 옵션 주위에 빨간색 선택 상자가 있는 QnAMaker 마이그레이션](../media/migrate-qnamaker/language-setting.png)

    여러 언어로 된 기술 자료를 언어 리소스로 마이그레이션하려면 언어 리소스에 대한 첫 번째 사용자 지정 질문 답변 프로젝트를 만들 때 여러 언어 설정을 사용하도록 설정해야 합니다. 2단계의 지침에 따라 수행할 수 있습니다. **언어 리소스에 대한 언어 설정을 지정하지 않으면 선택한 QnA Maker 리소스 의 언어가 할당됩니다.**

7. 마이그레이션하려는 기술 자료 Select all > **다음을** 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![기술 자료 선택 옵션 주위에 3개의 기술 자료 이름을 표시하는 드롭다운이 있는 빨간색 선택 상자가 있는 QnAMaker 마이그레이션](../media/migrate-qnamaker/select-knowledge-bases.png)

8. 마이그레이션하려는 기술 자료는 검토할 수 있습니다. 사용자 지정 질문에 응답하는 프로젝트에 대해 더 엄격한 유효성 검사 규칙을 따르기 때문에 프로젝트 이름에 몇 가지 유효성 검사 오류가 있을 수 있습니다. 

    > [!CAUTION]
    > 대상 언어 리소스에 이미 있는 프로젝트와 동일한 이름의 기술 자료를 마이그레이션하는 경우 선택한 기술 **자료의 콘텐츠로 프로젝트의 콘텐츠가 재정의됩니다.**

    > [!div class="mx-imgBorder"]
    > ![프로젝트 이름에 특수 문자를 포함할 수 없는 오류 메시지의 스크린샷](../media/migrate-qnamaker/special-characters.png)

9. 유효성 검사 오류를 해결한 후 **다음을** 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![특수 문자가 제거된 스크린샷](../media/migrate-qnamaker/validation-errors.png)

10. 마이그레이션이 발생하는 데 몇 분 정도 걸립니다. 진행 중인 마이그레이션을 취소하지 마십시오. 마이그레이션 후 [Language Studio](https://language.azure.com/) 내에서 마이그레이션된 프로젝트로 이동할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![Language Studio를 사용하여 게시할 수 있는 정보가 있는 성공적으로 마이그레이션된 기술 자료의 스크린샷](../media/migrate-qnamaker/migration-success.png)

    기술 자료가 사용자 지정 질문 응답 프로젝트로 마이그레이션하지 못하면 오류가 표시됩니다. 가장 일반적인 마이그레이션 오류는 다음과 같은 경우에 발생합니다.
    
    - 원본 및 대상 리소스가 잘못되었습니다.
    - 빈 기술 자료(KB)를 마이그레이션하려고 합니다.
    - 대상 리소스에 연결된 Azure Search 인스턴스에 대한 제한에 도달했습니다.

    > [!div class="mx-imgBorder"]
    > ![예제 오류가 있는 실패한 마이그레이션의 스크린샷](../media/migrate-qnamaker/migration-errors.png)

    이러한 오류를 해결하면 마이그레이션을 다시 실행하면 됩니다.

11. 마이그레이션은 기술 자료의 테스트 인스턴스만 복사합니다. 마이그레이션이 완료되면 기술 자료 를 수동으로 배포하여 테스트 인덱스를 프로덕션 인덱스에 복사해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 진단 로그를](analytics.md)사용하여 분석을 다시 사용하도록 설정하는 방법을 알아봅니다.

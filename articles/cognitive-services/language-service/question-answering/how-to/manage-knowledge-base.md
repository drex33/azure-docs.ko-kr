---
title: 기술 자료 관리-질문과 대답
description: 사용자 지정 질문에 대 한 답변을 통해 프로젝트 설정 및 콘텐츠에 대 한 액세스를 제공 하 여 프로젝트를 관리할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 1a28a0a4bf66824cc1c25d73512415e2a5613827
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103006"
---
# <a name="create-and-manage-project-settings"></a>프로젝트 설정 만들기 및 관리

질문에 대 한 답변을 통해 프로젝트 설정 및 데이터 소스에 대 한 액세스를 제공 하 여 프로젝트/기술 자료를 관리할 수 있습니다. 질문 응답 프로젝트를 만들지 않은 경우 [시작 문서](create-test-deploy.md)를 시작 하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.
> * Azure Portal에서 사용 하도록 설정 된 사용자 지정 질문 응답 기능이 있는 [언어 리소스](https://aka.ms/create-language-resource) 입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독 및 언어 리소스 이름을 명심 하세요.

## <a name="create-a-project"></a>프로젝트 만들기

1. Azure 자격 증명을 사용 하 여 [Language Studio](https://language.azure.com/) 포털에 로그인 합니다.

2. [질문 답변](https://language.azure.com/languageStudio/questionAnswering/projects) 페이지를 엽니다.

3. **새 프로젝트 만들기** 를 선택 합니다.

4. 언어 리소스와 연결 된 첫 번째 프로젝트를 만드는 경우 동일한 리소스에 대해 여러 언어를 사용 하 여 향후 프로젝트를 만들 수 있습니다. 첫 번째 프로젝트에서 언어를 단일 언어로 명시적으로 설정 하도록 선택 하는 경우 나중에이 설정을 수정할 수 없으며 해당 리소스에 대 한 모든 후속 프로젝트는 첫 번째 프로젝트를 만드는 동안 선택한 언어를 사용 합니다.

    > [!div class="mx-imgBorder"]
    > ![언어 선택 UI의 스크린샷](../media/manage-knowledge-base/choose-language-option.png)

5. 기본 프로젝트 설정 입력:

    |설정| 값|
    |-------|------|
    |**이름** | 여기에 고유한 프로젝트 이름 ("project" 및 "기술 자료")을 입력 합니다. |
    |**설명** | 프로젝트에 대 한 설명 입력 |
    |**원본 언어** | 이 값이 회색으로 표시 되는지 여부는 언어 리소스와 연결 된 첫 번째 프로젝트를 만들 때 선택한 내용에 따라 달라 집니다.  |
    |**기본 답변** | 질문에 대 한 답변을 찾을 수 없는 경우 시스템에서 전송 하는 기본 대답입니다. 이 설정은 Project 설정에서 언제 든 지 변경할 수 있습니다.

## <a name="manage-projects"></a>프로젝트 관리

언어 스튜디오의 주요 질문 응답 페이지에서 다음을 수행할 수 있습니다.

- 프로젝트 만들기
- 프로젝트 삭제
- 백업 하거나 다른 언어 리소스로 마이그레이션하기 위해 기존 프로젝트 내보내기
- 프로젝트/기술 자료를 가져옵니다. 예상 되는 파일 형식은 `.zip` 또는 형식으로 내보낸 프로젝트/기술 자료가 포함 된 파일입니다 `excel` `.tsv` .
- **마지막으로 수정한** 날짜 또는 마지막으로 **게시** 된 날짜를 기준으로 프로젝트를 정렬할 수 있습니다.

## <a name="manage-sources"></a>원본 관리

1. 왼쪽 탐색 모음에서 **원본 관리** 를 선택 합니다.

1.  원본에는 **url**, **파일** 및 **chitchat** 의 세 가지 유형이 있습니다.

       |목표|작업|
       |--|--|
       |원본 추가|**원본 > 추가** 를 선택 하 고 **url**, **파일** 또는 **chitchat** 를 선택 하 여 프로젝트에 새 원본 및 FAQ 콘텐츠를 추가할 수 있습니다.|
       |원본 삭제|원본의 왼쪽을 선택 하 여 기존 원본을 삭제할 수 있습니다. 그러면 확인 표시가 있는 파란색 원이 표시 되 > 휴지통 아이콘을 선택할 수 있습니다. |
       |콘텐츠를 구조화되지 않은 것으로 표시|업로드 된 파일 콘텐츠를 비구조적 것으로 표시 하려면 원본을 추가할 때 드롭다운에서 **비구조적 콘텐츠** 를 선택 합니다.|
       |자동 검색| 질문과 대답을 통해 콘텐츠가 구조화 되는지 비구조적 지를 확인 하려고 합니다.|

## <a name="manage-large-projects"></a>대량 프로젝트 관리

**기술 자료 편집 페이지** 에서 다음을 수행할 수 있습니다.

* **프로젝트 검색**: 질문 답변 패널의 맨 위에 있는 텍스트 상자에를 입력 하 여 프로젝트를 검색할 수 있습니다. Enter 키를 눌러 질문, 대답 또는 메타 데이터 콘텐츠를 검색 합니다.

* **페이지 매김**: 데이터 소스를 빠르게 이동 하 여 규모가 많은 프로젝트를 관리 합니다. 페이지 번호는 UI 아래쪽에 표시 되며 화면에 표시 되지 않는 경우도 있습니다.

## <a name="delete-project"></a>프로젝트 삭제

프로젝트를 삭제 하는 작업은 영구적입니다. 실행을 취소할 수 없습니다. 프로젝트를 삭제 하기 전에 언어 스튜디오 내에서 주 질문 응답 페이지의 프로젝트를 내보내야 합니다.

프로젝트를 협력자와 공유 하 고 나중에 삭제 하는 경우 모든 사용자가 프로젝트에 대 한 액세스 권한을 잃게 됩니다.

## <a name="next-steps"></a>다음 단계

* [리소스 구성](./configure-resources.md)

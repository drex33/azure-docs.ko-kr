---
title: 문제 해결-질문과 대답
description: 질문에 대 한 질문과 대답에 대 한 가장 자주 묻는 질문의 큐 레이트 목록은 기능을 더 빠르게 채택 하 고 더 나은 결과를 생성 하는 데 도움이 됩니다.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: dfafd5185c62d3011dfef66eb9d244ebe8a9b549
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482880"
---
# <a name="troubleshooting-for-question-answering"></a>질문 답변에 대 한 문제 해결

질문에 대 한 질문과 대답에 대 한 가장 자주 묻는 질문의 큐 레이트 목록은 기능을 더 빠르게 채택 하 고 더 나은 결과를 생성 하는 데 도움이 됩니다.

## <a name="manage-predictions"></a>예측 관리

<details>
<summary><b>쿼리 예측의 처리량 성능을 향상하려면 어떻게 해야 하나요?</b></summary>

**답변**: 처리량 성능 문제는 Cognitive Search를 스케일 업해야 함을 의미합니다. 성능을 향상시키려면 Cognitive Search에 복제본을 추가하는 것이 좋습니다.

[가격 책정 계층](../Concepts/azure-resources.md)에 대해 자세히 알아보세요.
</details>

## <a name="manage-your-project"></a>프로젝트 관리

<details>
<summary><b>URL/파일이 질문-답변 쌍을 추출하지 않는 이유는 무엇인가요?</b></summary>

**답변**: 질문 응답은 유효한 FAQ url에서 일부 QnA (질문 및 답변) 콘텐츠를 자동으로 추출할 수 없습니다. 이러한 경우 QnA 콘텐츠를 .txt 파일에 붙여넣고 도구에서 수집할 수 있는지 확인할 수 있습니다. 또는 [언어 스튜디오 포털](https://language.azure.com)을 통해 프로젝트/기술 자료에 콘텐츠를 editorially 추가할 수 있습니다.

</details>

<details>
<summary><b>만들 수 있는 기술 자료 크기는 얼마인가요?</b></summary>

**답변**: 기술 자료 크기는 QnA Maker 서비스를 만들 때 선택한 Azure Search의 SKU에 따라 다릅니다. 자세한 내용은 [여기](../concepts/azure-resources.md)를 참조하세요.

</details>

<details>
<summary><b>기술 자료를 다른 사용자와 공유하려면 어떻게 하나요?</b></summary>

**답변**: 공유 언어 리소스 수준에서 작동 합니다. 즉, 언어 리소스와 연결 된 모든 기술 자료를 공유할 수 있습니다.
</details>

<details>
<summary><b>동일한 Azure Active Directory 테넌트에 있지 않은 기여자와 기술 자료를 공유하여 기술 자료를 수정할 수 있나요?</b></summary>

**답변**: 공유는 azure 역할 기반 액세스 제어 (azure 역할 기반 액세스 제어)를 기반으로 합니다. Azure의 _모든_ 리소스를 다른 사용자와 공유할 수 있는 경우 질문 응답을 공유할 수도 있습니다.

</details>

<details>
<summary><b>서로 다른 5 명의 사용자에 게 읽기/쓰기 권한을 할당 하 여 각 사용자가 질문에 대 한 대답을 1 개만 액세스할 수 있나요?</b></summary>

**답변**: 개별 기술 자료가 아니라 전체 언어 리소스를 공유할 수 있습니다.

</details>

<details>
<summary><b>기술 자료에 대 한 업데이트는 프로덕션에 반영 되지 않습니다. 왜 안 돼요?</b></summary>

**답변**: 테이블 업데이트, 테스트 또는 설정에 관계 없이 모든 편집 작업을 배포 하기 전에 저장 해야 합니다. 변경한 후에는 **저장** 을 선택 하 고 해당 변경 내용이 프로덕션에 반영 되도록 프로젝트를 다시 배포 해야 합니다.

</details>

<details>
<summary><b>기술 자료는 풍부한 데이터 또는 멀티미디어를 지원하나요?</b></summary>

**응답**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>파일 및 URL에 대한 멀티미디어 자동 추출

* URL - 제한된 HTML-마크다운 변환 기능입니다.
* 파일 - 지원되지 않음

#### <a name="answer-text-in-markdown"></a>마크다운 형식의 답변 텍스트

QnA 쌍이 기술 자료에 있으면 답변의 마크다운 텍스트를 편집하여 공용 URL에서 사용할 수 있는 미디어에 대한 링크를 포함할 수 있습니다.

</details>

<details>
<summary><b>질문에 대 한 대답은 영어가 아닌 언어를 지원 하나요?</b></summary>

**답변**: [지원되는 언어](../language-support.md)에 대한 자세한 내용을 참조하세요.

여러 언어의 콘텐츠가 있는 경우 각 언어에 대 한 별도의 프로젝트를 만들어야 합니다.

</details>

## <a name="manage-service"></a>서비스 관리

<details>
<summary><b>기존 Search 서비스를 삭제했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?</b></summary>

**답변**: Azure Cognitive Search 인덱스를 삭제한 경우 작업이 최종이며 인덱스를 복구할 수 없습니다.

</details>

<details>
<summary><b>Search 서비스에서 `testkbv2` 인덱스를 삭제했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?</b></summary>

**답변**: 검색 서비스에서 `testkbv2` 인덱스를 삭제한 경우 마지막으로 게시된 KB에서 데이터를 복원할 수 있습니다. GitHub에서 사용할 수 있는 복구 도구 [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd) 을 사용 합니다.

</details>

<details>
<summary><b>여러 언어를 사용하여 기술 자료에 대한 동일한 Azure Cognitive Search 리소스를 사용할 수 있나요?</b></summary>

**답변**: 여러 언어와 여러 기술 자료를 사용 하려면 사용자는 각 언어에 대 한 프로젝트를 만들어야 하며, 언어 리소스에 대해 만든 첫 번째 프로젝트는 **이 리소스에서 프로젝트를 만들 때 언어를 선택** 하는 옵션을 선택 해야 합니다. 이렇게 하면 언어당 별도의 Azure Search 서비스를 만들게 됩니다.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Bot을 비롯한 다른 서비스와의 통합

<details>
<summary><b>질문 응답을 사용 하려면 Bot Framework를 사용 해야 하나요?</b></summary>

**답변**: 아니요, 질문 및 답변에 [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) 를 사용할 필요가 없습니다. 그러나 질문 대답은 [Azure Bot Service](/azure/bot-service/)의 여러 템플릿 중 하나로 제공 됩니다. Bot Service는 Microsoft Bot Framework를 통해 지능형 봇의 신속한 개발을 사용하도록 설정하고, 서버리스 환경에서 실행됩니다.

</details>

<details>
<summary><b>질문에 대 한 응답으로 새 봇을 만들려면 어떻게 해야 하나요?</b></summary>

**답변**: [이](../tutorials/bot-service.md) 문서의 지침에 따라 Azure Bot Service를 사용하여 봇을 만듭니다.

</details>

<details>
<summary><b>기존 Azure 봇 서비스에서 다른 기술 자료를 사용하려면 어떻게 해야 하나요?</b></summary>

**답변**: 기술 자료에 대한 다음 정보가 필요합니다.

* 기술 자료 ID.
* 기술 자료의 게시된 엔드포인트 사용자 지정 하위 도메인 이름으로, `host`라고 하며, 게시 후 **설정** 페이지에서 확인할 수 있습니다.
* 기술 자료의 게시된 엔드포인트 키 - 게시 후 **설정** 페이지에서 확인할 수 있습니다.

이 정보를 확인했으면 Azure Portal에서 봇의 App Service로 이동합니다. **설정 -> 구성 -> 애플리케이션 설정** 에서 해당 값을 변경합니다.

ABS 서비스에서 기술 자료의 엔드포인트 키에는 `QnAAuthkey`라는 레이블이 지정되어 있습니다.

</details>

<details>
<summary><b>두 개 이상의 클라이언트 애플리케이션에서 기술 자료를 공유할 수 있나요?</b></summary>

**답변**: 예, 클라이언트의 개수에 관계없이 기술 자료를 쿼리할 수 있습니다.

</details>

<details>
<summary><b>웹 사이트에서 질문에 대 한 답변을 포함할 어떻게 할까요? 있나요?</b></summary>

**대답**: 다음 단계에 따라 웹 사이트에 질문 응답 서비스를 웹 채팅 컨트롤로 포함 합니다.

1. [여기](../tutorials/bot-service.md)에 제공된 지침을 따라 FAQ 봇을 만듭니다.
2. [여기](../tutorials/bot-service.md#integrate-the-bot-with-channels)에 제공된 단계를 따라 웹 채팅을 사용하도록 설정합니다.

## <a name="data-storage"></a>데이터 스토리지

<details>
<summary><b>어떤 데이터가 저장되고 어디에 저장되나요?</b></summary>

**응답**:

질문에 대 한 답변을 위한 언어 리소스를 만들 때 Azure 지역을 선택 했습니다. 기술 자료 및 로그 파일에는 이 지역에 저장됩니다.

</details>

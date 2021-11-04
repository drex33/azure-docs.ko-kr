---
title: 사용자 지정 명령 애플리케이션 작성 시 오류 디버그(미리 보기)
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 애플리케이션을 작성할 때 오류를 디버깅하는 방법에 대해 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0e4f00440b89117ad2c247a485e29709c336cac3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504024"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>사용자 지정 명령 애플리케이션 작성 시 오류 디버그

이 문서에서는 사용자 지정 명령 애플리케이션을 빌드하는 동안 오류가 표시될 때 디버깅하는 방법에 대해 설명합니다. 

## <a name="errors-when-creating-an-application"></a>애플리케이션을 만들 때 발생하는 오류
사용자 지정 명령은 사용자 지정 명령 애플리케이션을 만들 때 [LUIS](https://www.luis.ai/)에서도 애플리케이션을 만듭니다. 

[LUIS는 작성 리소스당 500개의 애플리케이션으로 제한합니다](../luis/luis-limits.md). 이미 500개의 애플리케이션이 있는 작성 리소스를 사용하는 경우 LUIS 애플리케이션 생성에 실패할 수 있습니다. 

선택한 LUIS 작성 리소스에 500개 미만의 애플리케이션이 있는지 확인합니다. 그렇지 않은 경우 새 LUIS 작성 리소스를 만들거나, 다른 리소스로 전환하거나, LUIS 애플리케이션을 정리할 수 있습니다.  

## <a name="errors-when-deleting-an-application"></a>애플리케이션을 만들 때 발생하는 오류
### <a name="cant-delete-luis-application"></a>LUIS 애플리케이션을 삭제할 수 없음
사용자 지정 명령 애플리케이션을 삭제할 때 해당 애플리케이션과 연결된 LUIS 애플리케이션을 삭제하려고 할 수도 있습니다.

LUIS 애플리케이션 삭제에 실패한 경우 [LUIS](https://www.luis.ai/) 계정으로 이동하여 수동으로 삭제합니다.

### <a name="toomanyrequests"></a>TooManyRequests
한 번에 많은 수의 애플리케이션을 삭제하려고 하면 'TooManyRequests' 오류가 나타날 수 있습니다. 이러한 오류는 Azure에서 삭제 요청이 제한됨을 의미합니다. 

페이지를 새로 고치고 더 적은 수의 애플리케이션을 삭제해 보세요.

## <a name="errors-when-modifying-an-application"></a>애플리케이션을 수정할 때 발생하는 오류

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>매개 변수나 웹 엔드포인트를 삭제할 수 없음
매개 변수가 사용 중인 경우에는 해당 매개 변수를 삭제할 수 없습니다. 모든 음성 응답, 샘플 문장, 조건, 동작에서 매개 변수 참조를 제거하고 다시 시도하세요.

### <a name="cant-delete-a-web-endpoint"></a>웹 엔드포인트를 삭제할 수 없음
웹 엔드포인트가 사용 중인 경우에는 해당 웹 엔드포인트를 삭제할 수 없습니다. 웹 엔드포인트를 제거하기 전에 이 웹 엔드포인트를 사용하는 모든 **웹 엔드포인트 호출** 작업을 제거합니다.

## <a name="errors-when-training-an-application"></a>애플리케이션을 학습할 때 발생하는 오류
### <a name="built-in-intents"></a>기본 의도
LUIS에는 기본으로 제공되는 Yes/No 의도가 있습니다. 샘플 문장에 "yes", "no"만 포함된 경우에는 학습에 실패합니다. 

| 키워드 | 변형 | 
| ------- | --------- | 
| 예 | Sure, OK |
| 예 | Nope, Not | 

### <a name="common-sample-sentences"></a>일반적인 샘플 문장
사용자 지정 명령은 서로 다른 명령 간에 공유되는 공통 샘플 문장을 허용하지 않습니다. 한 명령의 일부 샘플 문장이 다른 명령에 이미 정의되어 있는 경우 애플리케이션 학습이 실패할 수 있습니다. 

서로 다른 명령 간에 공유되는 공통 샘플 문장이 없는지 확인합니다. 

여러 명령에서 샘플 문장의 균형을 맞추는 모범 사례는 [LUIS 모범 사례](../luis/luis-concept-best-practices.md)를 참조하세요.

### <a name="empty-sample-sentences"></a>빈 샘플 문장
각 명령에는 하나 이상의 샘플 문장이 있어야 합니다.

### <a name="undefined-parameter-in-sample-sentences"></a>샘플 문장에 있는 정의되지 않은 매개 변수
하나 이상의 매개 변수가 샘플 문장에서 사용되었지만 정의되지 않았습니다.

### <a name="training-takes-too-long"></a>학습 시간이 너무 오래 걸림
LUIS 학습은 적은 예를 사용하여 빠르게 학습시킵니다. 너무 많은 문장 예를 추가하지 마세요. 

유사한 문장 예가 많은 경우 매개 변수를 정의하고 패턴으로 추상화한 다음 문장 예에 추가합니다.

예를 들어 아래 문장 예에서는 매개 변수 {vehicle}을 정의하고 문장 예에 "Book a {vehicle}"만 추가할 수 있습니다.

| 문장 예 | 패턴 | 
| ------- | ------- | 
| Book a car | Book a {vehicle} | 
| 항공권 예약 | Book a {vehicle} |
| Book a taxi | Book a {vehicle} |

LUIS 학습의 모범 사례는 [LUIS 모범 사례](../luis/luis-concept-best-practices.md)를 참조하세요.

## <a name="cant-update-luis-key"></a>LUIS 키를 업데이트할 수 없음
### <a name="reassign-to-e0-authoring-resource"></a>E0 작성 리소스에 다시 할당
LUIS는 LUIS 애플리케이션을 E0 작성 리소스에 재할당하는 기능을 지원하지 않습니다.

작성 리소스를 F0에서 E0으로 변경하거나 다른 E0 리소스로 변경해야 하는 경우 애플리케이션을 다시 만듭니다. 

기존 애플리케이션을 빠르게 내보내고 새 애플리케이션으로 가져오려면 [Azure DevOps를 사용한 지속적인 배포](./how-to-custom-commands-deploy-cicd.md)를 참조하세요.

### <a name="save-button-is-disabled"></a>저장 단추가 비활성화됩니다.
LUIS 예측 리소스를 애플리케이션에 할당하지 않는 경우 예측 리소스를 추가하지 않고 작성 리소스를 변경하려고 하면 저장 단추가 사용하지 않도록 설정됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub 샘플 참조](https://aka.ms/speech/cc-samples)

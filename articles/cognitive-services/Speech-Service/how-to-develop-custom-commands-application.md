---
title: '방법: 사용자 지정 명령 애플리케이션 개발 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명령 애플리케이션을 개발하고 사용자 지정하는 방법을 알아봅니다. 음성 명령 앱은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: eur
ms.openlocfilehash: ee68e7d529b0c1cebefa8eb9fc809522ca932ab8
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132761343"
---
# <a name="develop-custom-commands-applications"></a>사용자 지정 명령 애플리케이션 개발

이 방법 문서에서는 사용자 지정 명령 애플리케이션을 개발하고 구성하는 방법을 알아봅니다. 사용자 지정 명령 기능을 사용하면 음성 우선 상호 작용 환경에 최적화된 풍부한 음성 명령 앱을 빌드할 수 있습니다. 이 기능은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합합니다. 특히 IoT(사물 인터넷) 디바이스와 앰비언트 및 헤드리스 디바이스에 적합합니다.

이 문서에서는 TV를 켜고 끌 수 있는 애플리케이션을 만들고, 온도를 설정하고, 알람을 설정합니다. 이러한 기본 명령을 만든 후에는 명령을 사용자 지정하는 다음 옵션에 대해 알아봅니다.

* 명령에 매개 변수 추가
* 명령 매개 변수에 구성 추가
* 상호 작용 규칙 작성
* 음성 응답을 위한 언어 생성 템플릿 만들기
* Custom Voice 도구 사용

## <a name="create-an-application-by-using-simple-commands"></a>간단한 명령을 사용하여 애플리케이션 만들기

먼저 빈 사용자 지정 명령 애플리케이션을 만듭니다. 자세한 내용은 [빠른 시작](quickstart-custom-commands-application.md)을 참조하세요. 이 애플리케이션에서는 프로젝트를 가져오는 대신 빈 프로젝트를 만듭니다.

1. **이름** 상자에 프로젝트 이름 *Smart-Room-Lite*(또는 원하는 다른 이름)를 입력합니다.
1. **언어** 목록에서 **영어(미국)** 을 선택합니다.
1. LUIS 리소스를 선택하거나 만듭니다.

   > [!div class="mx-imgBorder"]
   > !["새 프로젝트" 창을 보여주는 스크린샷](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUIS 리소스 업데이트(선택 사항)

**새 프로젝트** 창에서 선택한 작성 리소스를 업데이트할 수 있습니다. 예측 리소스를 설정할 수도 있습니다.

예측 리소스는 사용자 지정 명령 애플리케이션이 게시될 때 인식에 사용됩니다. 개발 및 테스트 단계에서는 예측 리소스가 필요 없습니다.

### <a name="add-a-turnon-command"></a>TurnOn 명령 추가

앞에서 만든 빈 Smart-Room-Lite 사용자 지정 명령 애플리케이션에서 명령을 추가합니다. 이 명령은 발화 `Turn on the tv`를 처리합니다. 애플리케이션은 `Ok, turning the tv on` 메시지로 응답합니다.

1. 왼쪽 창의 맨 위에서 **새 명령** 을 선택하여 새 명령을 만듭니다. **새 명령** 창이 열립니다.
1. **이름** 필드에 `TurnOn` 값을 입력합니다.
1. **만들기** 를 선택합니다.

가운데 창에 명령의 속성이 나열됩니다.

다음 표에서는 명령의 구성 속성에 대해 설명합니다. 자세한 내용은 [사용자 지정 명령 개념 및 정의](./custom-commands-references.md)를 참조하세요.

| 구성            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 예제 문장 | 사용자가 음성으로 이 명령을 트리거할 수 있는 예제 발화입니다.                                                                 |
| 매개 변수       | 명령을 완료하는 데 필요한 정보입니다.                                                                                |
| 완료 규칙 | 명령을 이행하려면 수행해야 하는 작업입니다. 예: 사용자에게 응답하거나 웹 서비스와 통신합니다. |
| 상호 작용 규칙   | 보다 구체적이거나 복잡한 상황을 처리하는 기타 규칙입니다.                                                              |


> [!div class="mx-imgBorder"]
> ![명령을 만드는 위치를 보여주는 스크린샷](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>예제 문장 추가

**예제 문장** 섹션에서 사용자가 말할 수 있는 문장의 예를 제공합니다.

1. 가운데 창에서 **예제 문장** 을 선택합니다.
1. 오른쪽 창에서 예제를 추가합니다.

    ```
    Turn on the tv
    ```

1.  창 맨 위에서 **저장** 을 선택합니다.

아직 매개 변수가 없으므로 **완료 규칙** 섹션으로 이동할 수 있습니다.

#### <a name="add-a-completion-rule"></a>완료 규칙 추가

다음으로, 명령에 완료 규칙이 필요합니다. 이 규칙은 처리 작업이 수행되고 있다는 것을 사용자에게 알립니다.

규칙 및 완료 규칙에 대한 자세한 내용은 [사용자 지정 명령 개념 및 정의](./custom-commands-references.md)를 참조하세요.

1. 기본 완료 규칙 **완료** 를 선택합니다. 그리고 다음과 같이 편집합니다.


    | 설정    | 제안 값                          | Description                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **이름**       | `ConfirmationResponse`                  | 규칙의 목적을 설명하는 이름          |
    | **조건** | 없음                                     | 규칙을 실행할 수 있는 시기를 결정하는 조건    |
    | **actions**    | **음성 응답 보내기** > **간단한 편집기** > `Ok, turning the tv on` | 규칙 조건이 true일 때 수행되는 작업 |

   > [!div class="mx-imgBorder"]
   > ![음성 응답을 만드는 위치를 보여주는 스크린샷](media/custom-commands/create-speech-response-action.png)

1. **저장** 을 선택하여 작업을 저장합니다.
1. **완료 규칙** 섹션으로 돌아가서 **저장** 을 선택하여 모든 변경 내용을 저장합니다.

    > [!NOTE]
    > 명령과 함께 제공되는 기본 완성 규칙을 꼭 사용해야 하는 것은 아닙니다. 기본 완성 규칙을 삭제하고 고유한 규칙을 추가할 수 있습니다.

### <a name="add-a-settemperature-command"></a>SetTemperature 명령 추가

이제 `SetTemperature` 명령을 추가합니다. 이 명령은 단일 발화 `Set the temperature to 40 degrees`를 사용하고, `Ok, setting temperature to 40 degrees` 메시지로 응답합니다.

새 명령을 만들려면 `TurnOn` 명령에 사용한 단계를 따르되, 예제 문장 `Set the temperature to 40 degrees`를 사용합니다.

그런 다음, 기존의 **완료** 완료 규칙을 다음과 같이 편집합니다.

| 설정    | 제안 값                          |
| ---------- | ---------------------------------------- |
| **이름**  | `ConfirmationResponse`                  |
| **조건** | 없음                                     |
| **actions**    | **음성 응답 보내기** > **단순한 편집기** > **첫 번째 변형** > `Ok, setting temperature to 40 degrees` |

**저장** 을 선택하여 모든 명령 변경 내용을 저장합니다.

### <a name="add-a-setalarm-command"></a>SetAlarm 명령 추가

새 `SetAlarm` 명령을 만듭니다. 예제 문장 `Set an alarm for 9 am tomorrow`를 사용합니다. 그런 다음, 기존의 **완료** 완료 규칙을 다음과 같이 편집합니다.

| 설정    | 제안 값                          |
| ---------- | ---------------------------------------- |
| **이름**  | `ConfirmationResponse`                  |
| **조건** | 없음                                     |
| **actions**    | **음성 응답 보내기** > **단순한 편집기** > **첫 번째 변형** > `Ok, setting an alarm for 9 am tomorrow` |

**저장** 을 선택하여 모든 명령 변경 내용을 저장합니다.

### <a name="try-it-out"></a>기능 직접 사용해 보기

테스트 창을 사용하여 애플리케이션의 동작을 테스트합니다.

1. 창의 오른쪽 위 모서리에서 **학습** 아이콘을 선택합니다.
1. 학습이 완료되면 **테스트** 를 선택합니다.

음성 또는 텍스트를 사용하여 다음 발화 예제를 사용해 봅니다.

- 입력: *set the temperature to 40 degrees*
- 예상 응답: Ok, setting temperature to 40 degrees
- 입력: *turn on the tv*
- 예상 응답: Ok, turning the tv on
- 입력: *set an alarm for 9 am tomorrow*
- 예상 응답: Ok, setting an alarm for 9 am tomorrow

> [!div class="mx-imgBorder"]
> ![웹 채팅 인터페이스의 테스트를 보여주는 스크린샷](media/custom-commands/create-basic-test-chat-no-mic.png)

> [!TIP]
> 테스트 창에서 **세부 정보 켜기** 를 선택하면 이 음성 입력 또는 텍스트 입력이 어떻게 처리되었는지 자세히 알아볼 수 있습니다.

## <a name="add-parameters-to-commands"></a>명령에 매개 변수 추가

이 섹션에서는 명령에 매개 변수를 추가하는 방법을 알아봅니다. 작업을 완료하려면 명령에 매개 변수가 필요합니다. 복잡한 시나리오에서는 매개 변수를 사용하여 사용자 지정 작업을 트리거하는 조건을 정의할 수 있습니다.

### <a name="configure-parameters-for-a-turnon-command"></a>TurnOn 명령에 대한 매개 변수 구성

먼저 여러 디바이스를 켜고 끄도록 기존의 `TurnOn` 명령을 편집합니다.

1. 이제 이 명령이 켜기 및 끄기 시나리오를 모두 처리하므로 명령 이름을 *TurnOnOff* 로 바꿉니다.
   1. 왼쪽 창에서 **TurnOn** 명령을 선택합니다. 그런 다음, 창 맨 위에서 **새 명령** 옆에 있는 편집 단추를 선택합니다.

   1. **명령 이름 바꾸기** 창에서 이름을 *TurnOnOff* 로 변경합니다.

1. 명령에 새 매개 변수를 추가합니다. 이 매개 변수는 사용자가 디바이스를 켜려고 하는지 아니면 끄려고 하는지를 나타냅니다.
   1. 가운데 창의 맨 위에서 **추가** 를 선택합니다. 드롭다운 메뉴에서 **매개 변수** 를 선택합니다.
   1. 오른쪽 창의 **매개 변수** 섹션에 있는 **이름** 상자에 `OnOff`를 추가합니다.
   1. **필수** 를 선택합니다. **매개 변수에 대한 응답 추가** 창에서 **단순한 편집기** 를 선택합니다. **첫 번째 변형** 필드에서 *On or Off?* 를 추가합니다.
   1. **업데이트** 를 선택합니다.

       > [!div class="mx-imgBorder"]
       > !['단순한 편집기' 탭이 선택된 '필수 매개 변수에 대한 응답 추가' 섹션을 보여주는 스크린샷](media/custom-commands/add-required-on-off-parameter-response.png)

   1. 다음 표를 사용하여 매개 변수의 속성을 구성합니다. 명령의 모든 구성 속성에 대한 자세한 내용은 [사용자 지정 명령 개념 및 정의](./custom-commands-references.md)를 참조하세요.


       | 구성      | 제안 값     | Description                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **이름**               | `OnOff`           | 매개 변수의 구체적인 이름                                                                           |
       | **필수**           | 선택됨         | 명령이 완료되기 전에 이 매개 변수의 값이 필요한지 여부를 나타내는 확인란입니다. |
       | **필수 매개 변수에 대한 응답**      |**단순한 편집기** > `On or Off?`      | 이 매개 변수의 값을 알 수 없을 때 값을 요청하는 프롬프트입니다. |
       | **형식**               | **String**          | 숫자, 문자열, 날짜 시간 또는 지리와 같은 매개 변수 유형입니다.   |
       | **Configuration**      | **내부 카탈로그의 미리 정의된 입력 값 허용** | 문자열의 경우 이 설정은 입력을 가능한 값 세트로 제한합니다. |
       | **미리 정의된 입력 값**     | `on`, `off`           | 가능한 값 및 값의 별칭 세트입니다.         |


   1. 미리 정의된 입력 값을 추가하려면 **미리 정의된 입력 추가** 를 선택합니다. 위의 표에 나와 있는 것처럼 **새 항목** 창에 *이름* 을 입력합니다. 여기서는 별칭을 사용하지 않으므로 이 필드를 비워 둘 수 있습니다.

      > [!div class="mx-imgBorder"]
      > ![매개 변수를 만드는 방법을 보여주는 스크린샷](media/custom-commands/create-on-off-parameter.png)

   1. **저장** 을 선택하여 모든 매개 변수 구성을 저장합니다.

#### <a name="add-a-subjectdevice-parameter"></a>SubjectDevice 매개 변수 추가

1. 이 명령을 사용하여 제어할 수 있는 디바이스 이름을 나타내는 두 번째 매개 변수를 추가하려면 **추가** 를 선택합니다. 다음 구성을 사용합니다.


    | 설정            | 제안 값       |
    | ------------------ | --------------------- |
    | **이름**               | `SubjectDevice`         |
    | **필수**           | 선택됨               |
    | **필수 매개 변수에 대한 응답**     | **단순한 편집기** > `Which device do you want to control?`    | 
    | **형식**               | **String**                |     
    | **Configuration**      | **내부 카탈로그의 미리 정의된 입력 값 허용** | 
    | **미리 정의된 입력 값** | `tv`, `fan`               |
    | **별칭**(`tv`)      | `television`, `telly`     |

1. **저장** 을 선택합니다.

#### <a name="modify-example-sentences"></a>예제 문장 수정

매개 변수를 사용하는 명령의 경우 가능한 모든 조합을 다루는 예제 문장을 추가하는 것이 좋습니다. 예:

* 완전한 매개 변수 정보: `turn {OnOff} the {SubjectDevice}`
* 부분 매개 변수 정보: `turn it {OnOff}`
* 매개 변수 정보 없음: `turn something`

다양한 수준의 정보를 활용하는 예제 문장을 사용하면 사용자 지정 명령 애플리케이션에서 부분 정보를 사용하여 일회성 해결과 다중 턴 해결을 모두 확인할 수 있습니다.

이 정보를 염두에 두고 다음과 같은 제안된 매개 변수를 사용하도록 예제 문장을 편집합니다.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

**저장** 을 선택합니다.

> [!TIP]
> 예제 문장 편집기에서 중괄호를 사용하여 매개 변수를 참조합니다. 예들 들어 `turn {OnOff} the {SubjectDevice}`입니다.
> 이전에 만든 매개 변수에서 지원하는 자동 완성을 위한 탭을 사용합니다.

#### <a name="modify-completion-rules-to-include-parameters"></a>매개 변수를 포함하도록 완료 규칙 수정

기존 완료 규칙 `ConfirmationResponse`를 수정합니다.

1. **조건** 섹션에서 **조건 추가** 를 선택합니다.
1. **새 조건** 창의 **형식** 목록에서 **필수 매개 변수** 를 선택합니다. 이어지는 목록에서 **OnOff** 및 **SubjectDevice** 를 모두 선택합니다.
1. **만들기** 를 선택합니다.
1. **작업** 섹션에서 마우스로 **음성 응답 보내기** 작업을 가리키고 편집 단추를 선택하여 편집합니다. 이번에는 새로 만든 `OnOff` 및 `SubjectDevice` 매개 변수를 사용합니다.

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. **저장** 을 선택합니다.

오른쪽 창 맨 위에 있는 **학습** 아이콘을 선택하여 변경을 시도해 봅니다.

학습이 완료되면 **테스트** 를 선택합니다. **애플리케이션 테스트** 창이 나타납니다. 다음 상호 작용을 시도합니다.

- 입력: *turn off the tv*
- 출력: Ok, turning off the tv
- 입력: *turn off the television*
- 출력: Ok, turning off the tv
- 입력: *turn it off*
- 출력: Which device do you want to control?
- 입력: *the tv*
- 출력: Ok, turning off the tv

### <a name="configure-parameters-for-a-settemperature-command"></a>SetTemperature 명령에 대한 매개 변수 구성

사용자의 지시에 따라 온도를 설정하도록 `SetTemperature` 명령을 수정합니다.

`TemperatureValue` 매개 변수를 추가합니다. 다음 구성을 사용합니다.

| 구성      | 제안 값     |
| ------------------ | ----------------|
| **이름**               | `TemperatureValue`           |
| **필수**           | 선택됨         |
| **필수 매개 변수에 대한 응답**      | **단순한 편집기** > `What temperature would you like?`
| **유형**               | `Number`          |


다음 값을 사용하도록 예제 발화를 편집합니다.

```
set the temperature to {TemperatureValue} degrees
change the temperature to {TemperatureValue}
set the temperature
change the temperature
```

기존 완료 규칙을 편집합니다. 다음 구성을 사용합니다.

| 구성      | 제안 값     |
| ------------------ | ----------------|
| **조건**         | **필수 매개 변수** > **TemperatureValue**           |
| **actions**           | **음성 응답 보내기** > `Ok, setting temperature to {TemperatureValue} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>SetAlarm 명령에 대한 매개 변수 구성

`DateTime`이라는 매개 변수를 추가합니다. 다음 구성을 사용합니다.

   | 설정                           | 제안 값                     |
   | --------------------------------- | ----------------------------------------|
   | **이름**                              | `DateTime`                               |
   | **필수**                          | 선택됨                                 |
   | **필수 매개 변수에 대한 응답**   | **단순한 편집기** > `For what time?`            |
   | **유형**                              | **DateTime**                                |
   | **날짜 기본값**                     | 날짜가 누락되었으면 오늘 날짜를 사용합니다.            |
   | **시간 기본값**                     | 시간이 누락되었으면 해당 날짜가 시작되는 시간을 사용합니다.     |


> [!NOTE]
> 이 문서에서는 대부분 문자열, 숫자 및 날짜/시간 매개 변수 형식을 사용합니다. 지원되는 모든 매개 변수 형식 및 속성 목록은 [사용자 지정 명령 개념 및 정의](./custom-commands-references.md)를 참조하세요.


예제 발화를 편집합니다. 다음 값을 사용합니다.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

기존 완료 규칙을 편집합니다. 다음 구성을 사용합니다.

   | 설정    | 제안 값                               |
   | ---------- | ------------------------------------------------------- |
   | **actions**    | **음성 응답 보내기** > `Ok, alarm set for {DateTime}`  |

다른 명령과 관련된 발언을 사용하여 세 가지 명령을 함께 테스트합니다. (명령 간에 전환할 수 있습니다.)

- 입력: *Set an alarm*
- 출력: For what time?
- 입력: *Turn on the tv*
- 출력: Ok, turning the tv on
- 입력: *Set an alarm*
- 출력: For what time?
- 입력: *5 pm*
- 출력: Ok, alarm set for 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>명령 매개 변수에 구성 추가

이 섹션에서는 다음을 포함하여 고급 매개 변수 구성에 대해 자세히 알아봅니다.

 - 매개 변수 값이 사용자 지정 명령 애플리케이션 외부에서 정의된 세트에 속할 수 있는 방법
 - 매개 변수 값에 유효성 검사 절을 추가하는 방법

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>매개 변수를 외부 카탈로그 엔터티로 구성

사용자 지정 명령 기능을 사용하면 웹 엔드포인트를 통해 호스트되는 외부 카탈로그를 참조하도록 문자열 형식 매개 변수를 구성할 수 있습니다. 따라서 사용자 지정 명령 애플리케이션을 편집하지 않고도 외부 카탈로그를 독립적으로 업데이트할 수 있습니다. 이 방법은 카탈로그 항목이 많은 경우에 유용합니다.

`TurnOnOff` 명령의 `SubjectDevice` 매개 변수를 다시 사용합니다. 이 매개 변수의 현재 구성은 **내부 카탈로그의 미리 정의된 입력 허용** 입니다. 이 구성은 매개 변수 구성의 고정 디바이스 목록을 나타냅니다. 이 콘텐츠를 독립적으로 업데이트할 수 있는 외부 데이터 원본으로 이동합니다.

이 콘텐츠를 이동하려면 먼저 새 웹 엔드포인트를 추가합니다. 왼쪽 창에서 **웹 엔드포인트** 섹션으로 이동합니다. 새 웹 끝점 URL을 추가 합니다. 다음 구성을 사용합니다.

| 설정 | 제안 값 |
|----|----|
| **이름** | `getDevices` |
| **URL** | `<Your endpoint of getDevices.json>` |
| **방법** | **GET** |

그런 다음, 제어할 수 있는 장치를 나열 하는 JSON 파일을 반환 하는 웹 끝점을 구성 하 고 호스팅합니다. 웹 끝점은이 예제와 같이 서식이 지정 된 JSON 파일을 반환 해야 합니다.

```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

다음으로, **SubjectDevice** 매개 변수 설정 페이지로 이동합니다. 다음 속성을 설정합니다.

| 설정 | 제안 값 |
| ----| ---- |
| **Configuration** | **외부 카탈로그의 미리 정의된 입력 허용** |                               
| **카탈로그 엔드포인트** | `getDevices` |
| **방법** | **GET** |

그런 다음 **저장** 을 선택합니다.

> [!IMPORTANT]
> 왼쪽 창의 **웹 엔드포인트** 섹션에서 웹 엔드포인트를 설정하지 않았으면 외부 카탈로그의 입력을 허용하도록 매개 변수를 구성하는 옵션이 표시되지 않습니다.

**학습** 을 선택하여 시도해 보세요. 학습이 완료되면 **테스트** 를 선택하고 몇 가지 상호 작용을 시도해 봅니다.

* 입력: *turn on*
* 출력: Which device do you want to control?
* 입력: *lights*
* 출력: Ok, turning the lights on

> [!NOTE]
> 이제 웹 엔드포인트에 호스트되는 모든 디바이스를 제어할 수 있습니다. 그러나 아직 새 변경 내용을 테스트한 후 애플리케이션을 다시 게시하도록 애플리케이션을 학습시켜야 합니다.

### <a name="add-validation-to-parameters"></a>매개 변수에 유효성 검사 추가

*유효성 검사* 는 매개 변수 값에 대한 제약 조건을 구성할 수 있는 특정 매개 변수 형식에 적용되는 구문입니다. 값이 제약 조건의 범위를 벗어나면 값을 수정하라는 메시지가 표시됩니다. 유효성 검사 구문을 확장하는 매개 변수 형식 목록은 [사용자 지정 명령 개념 및 정의](./custom-commands-references.md)를 참조하세요.

`SetTemperature` 명령을 사용하여 유효성 검사를 테스트합니다. `Temperature` 매개 변수에 대한 유효성 검사를 추가하려면 다음 단계를 수행합니다.

1. 왼쪽 창에서 **SetTemperature** 명령을 선택합니다.
1. 가운데 창에서 **Temperature** 를 선택합니다.
1. 오른쪽 창에서 **유효성 검사 추가** 를 선택합니다.
1. 새 **유효성 검사** 창에서 다음 표처럼 유효성 검사를 구성합니다. 그런 다음 **생성** 를 선택합니다.


    | 매개 변수 구성 | 제안 값 | Description |
    | ---- | ---- | ---- |
    | **최솟값** | `60` | 숫자 매개 변수인 경우 이 매개 변수가 가정할 수 있는 최솟값입니다. |
    | **최댓값** | `80` | 숫자 매개 변수인 경우 이 매개 변수가 가정할 수 있는 최댓값입니다. |
    | **실패 응답** |  **단순한 편집기** > **첫 번째 변형** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 유효성 검사에 실패할 경우 새 값을 요청하는 프롬프트 |

    > [!div class="mx-imgBorder"]
    > ![범위 유효성 검사를 추가하는 방법을 보여주는 스크린샷](media/custom-commands/add-validations-temperature.png)

오른쪽 창 맨 위에 있는 **학습** 아이콘을 선택하여 시도해 봅니다. 학습이 완료되면 **테스트** 를 선택합니다. 다음과 같은 몇 가지 상호 작용을 시도합니다.

- 입력: *Set the temperature to 72 degrees*
- 출력: Ok, setting temperature to 72 degrees
- 입력: *Set the temperature to 45 degrees*
- 출력: Sorry, I can only set temperature between 60 and 80 degrees
- 입력: *make it 72 degrees instead*
- 출력: Ok, setting temperature to 72 degrees

## <a name="add-interaction-rules"></a>상호 작용 규칙 추가

상호 작용 규칙은 구체적이거나 복잡한 상황을 처리하는 *추가* 규칙입니다. 자체적인 상호 작용 규칙을 자유롭게 작성할 수 있지만, 이 예제에서는 다음 시나리오에 대한 상호 작용 규칙을 사용합니다.

* 명령 확인
* 명령에 1단계 수정 추가

상호 작용 규칙에 대한 자세한 내용은 [사용자 지정 명령 개념 및 정의](./custom-commands-references.md)를 참조하세요.

### <a name="add-confirmations-to-a-command"></a>명령에 확인 추가

확인을 추가하려면 `SetTemperature` 명령을 사용합니다. 확인을 받으려면 다음 단계에 따라 상호 작용 규칙을 만듭니다.

1. 왼쪽 창에서 **SetTemperature** 명령을 선택합니다.
1. 가운데 창에서 **추가** 를 선택하여 상호 작용 규칙을 추가합니다. 그런 다음, **상호 작용 규칙** > **명령 확인** 을 선택합니다.

    이 작업을 수행하면 세 가지 상호 작용 규칙이 추가됩니다. 이러한 규칙은 사용자에게 알람의 날짜 및 시간을 확인하도록 요청합니다. 이러한 규칙은 다음 순서에 대한 확인(예 또는 아니요)이 필요합니다.

    1. 다음 구성을 사용하여 **명령 확인** 상호 작용 규칙을 수정합니다.
        1. 이름을 **온도 확인** 으로 바꿉니다.
        1. **모든 필수 매개 변수** 조건이 이미 추가되었습니다.
        1. 새 작업을 추가합니다(**형식** > **음성 응답 보내기** > **Are you sure you want to set the temperature as {TemperatureValue} degrees?** ).
        1. **예상** 섹션에서 기본값 **사용자의 확인 필요** 를 그대로 둡니다.

         > [!div class="mx-imgBorder"]
         > ![필수 매개 변수 응답을 만드는 방법을 보여주는 스크린샷](media/custom-speech-commands/add-confirmation-set-temperature.png)


    1. 성공한 확인(사용자가 예라고 대답)을 처리하도록 **확인 성공함** 상호 작용 규칙을 수정합니다.

          1. 이름을 **온도 확인 성공함** 으로 바꿉니다.
          1. 기존 조건 **확인 성공함** 을 그대로 둡니다.
          1. 새 조건을 추가합니다(**형식** > **필수 매개 변수** > **TemperatureValue**).
          1. **실행 완료 규칙** 을 기본값인 **실행 후 상태** 로 둡니다.

    1. 확인이 거부되는(사용자가 아니요라고 대답) 시나리오를 처리하도록 **확인 거부됨** 상호 작용 규칙을 수정합니다.

          1. 이름을 **온도 확인 거부됨** 으로 바꿉니다.
          1. 기존 조건 **확인 거부됨** 을 그대로 둡니다.
          1. 새 조건을 추가합니다(**형식** > **필수 매개 변수** > **TemperatureValue**).
          1. 새 작업을 추가합니다(**형식** > **음성 응답 보내기** > **No problem. What temperature then?** ).
          1. 기본값 **실행 후 상태** 를 **사용자 입력 대기** 로 변경합니다.

> [!IMPORTANT]
> 이 문서에서는 기본 제공 확인 기능을 사용합니다. 상호 작용 규칙을 하나씩 수동으로 추가할 수도 있습니다.

**학습** 을 선택하여 변경해 보세요. 학습이 완료되면 **테스트** 를 선택합니다.

- **입력**: *Set temperature to 80 degrees*
- **출력**: are you sure you want to set the temperature as 80 degrees?
- **입력**: *No*
- **출력**: No problem. What temperature then?
- **입력**: *72 degrees*
- **출력**: are you sure you want to set the temperature as 72 degrees?
- **입력**: *Yes*
- **출력**: OK, setting temperature to 72 degrees

### <a name="implement-corrections-in-a-command"></a>명령에서 수정 구현

이 섹션에서는 1단계 수정을 구성합니다. 이 수정 작업은 처리 작업이 실행된 후에 사용됩니다. 또한 명령이 아직 처리되지 않은 경우 기본적으로 수정을 사용하도록 설정하는 방법의 예를 볼 수 있습니다. 명령이 완료되지 않은 경우 수정을 추가하려면 새 매개 변수 `AlarmTone`을 추가합니다.

왼쪽 창에서 **SetAlarm** 명령을 선택합니다. 그런 다음, 새 매개 변수 **AlarmTone** 을 추가합니다.

- **이름** > `AlarmTone`
- **형식** > **문자열**
- **기본값** > **벨소리**
- **구성** > **내부 카탈로그의 미리 정의된 입력 값 허용**
- **미리 정의된 입력 값** > **벨소리**, **징글벨** 및 **에코**(이러한 값은 미리 정의된 개별 입력)


다음으로, **DateTime** 매개 변수에 대한 응답을 **Ready to set alarm with tone as {AlarmTone}. For what time?** 으로 업데이트합니다. 그 후 완료 규칙을 다음과 같이 수정합니다.

1. 기존 완료 규칙 **ConfirmationResponse** 를 선택합니다.
1. 오른쪽 창에서 기존 작업을 마우스로 가리키고 **편집** 을 선택합니다.
1. 음성 응답을 `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}`으로 업데이트합니다.

> [!IMPORTANT]
> 진행 중인 명령에서 명시적인 구성이 없어도 알람 톤이 변경될 수 있습니다. 예를 들어 명령이 아직 완료되지 않은 경우 알람 톤이 변경될 수 있습니다. 명령이 아직 처리되지 않은 경우 순서 번호에 관계없이 모든 명령 매개 변수에 대해 *기본적으로* 수정을 사용하도록 설정됩니다.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>명령이 완료되면 수정 구현

사용자 지정 명령 플랫폼은 명령이 완료된 경우에도 1단계 수정이 가능합니다. 이 기능은 기본적으로 사용되지 않습니다. 명시적으로 구성해야 합니다.

다음 단계에 따라 1단계 수정을 구성합니다.

1. **SetAlarm** 명령에서, 이전에 설정한 알람을 업데이트하는 **이전 명령 업데이트** 형식의 상호 작용 규칙을 추가합니다. 상호 작용 규칙의 이름을 **이전 명령 업데이트** 로 바꿉니다.
1. 기본 조건 **이전 명령을 업데이트해야 함** 을 그대로 둡니다.
1. 새 조건을 추가합니다(**형식** > **필수 매개 변수** > **DateTime**).
1. 새 작업을 추가합니다(**형식** > **음성 응답 보내기** > **단순한 편집기** > **이전 알람 시간을 {DateTime}으로 업데이트**).
1. 기본 **실행 후 상태** 값을 **명령 완료됨** 으로 그대로 둡니다.

**학습** 을 선택하여 변경해 보세요. 학습이 완료될 때까지 기다렸다가 **테스트** 를 선택합니다.

- **입력**: *Set an alarm.*
- **출력**: Ready to set alarm with tone as Chimes. For what time?
- **입력**: *Set an alarm with the tone as Jingle for 9 am tomorrow.*
- **출력**: OK, alarm set for 2020-05-21 09:00:00. The alarm tone is Jingle.
- **입력**: *No, 8 am.*
- **출력**: Updating previous alarm time to 2020-05-29 08:00.

> [!NOTE]
> 실제 애플리케이션에서는 이 수정 규칙의 **작업** 섹션에서 다시 클라이언트에 활동을 보내거나 HTTP 엔드포인트를 호출하여 시스템의 알람 시간을 업데이트해야 합니다. 이 작업은 알람 시간 업데이트만 담당해야 합니다. 명령의 다른 특성을 담당하면 안 됩니다. 여기서는 이 특성이 알람 톤입니다.

## <a name="add-language-generation-templates-for-speech-responses"></a>음성 응답을 위한 언어 생성 템플릿 추가

LG(언어 생성) 템플릿을 사용하여 클라이언트로 전송되는 응답을 사용자 지정할 수 있습니다. 이 템플릿을 사용하여 응답을 변형할 수 있습니다. 다음을 사용하여 언어를 생성할 수 있습니다.

* 언어 생성 템플릿
* 적응형 식

사용자 지정 명령 템플릿은 Bot Framework의 [LG 템플릿](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)을 기반으로 합니다. 사용자 지정 명령 기능은 (매개 변수 또는 작업의 음성 응답을 위해) 필요할 때 새 LG 템플릿을 만들기 때문에 LG 템플릿의 이름을 지정할 필요가 없습니다.

따라서 다음과 같이 템플릿을 정의할 필요가 없습니다.

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

대신 다음과 같이 이름 없이 템플릿 본문을 정의할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![템플릿 편집기 예제를 보여주는 스크린샷](./media/custom-commands/template-editor-example.png)


이러한 변경으로 인해 클라이언트에 전송되는 음성 응답이 변경됩니다. 발화의 경우 제공된 옵션 중에서 해당하는 음성 응답이 임의로 선택됩니다.

LG 템플릿의 장점을 활용하면 적응형 식을 사용하여 명령에 대한 복잡한 음성 응답을 정의할 수도 있습니다. 자세한 내용은 [LG 템플릿 형식](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)을 참조하세요.

기본적으로 사용자 지정 명령 기능은 모든 기능을 지원하며 다음과 같은 사소한 차이점이 있습니다.

* LG 템플릿에서는 엔터티가 `${entityName}`으로 표시됩니다 사용자 지정 명령 기능에서는 엔터티를 사용하지 않습니다. 하지만 매개 변수를 `${parameterName}` 표현 또는 `{parameterName}` 표현과 함께 변수로 사용할 수 있습니다.
* 사용자가 *.lg* 파일을 직접 편집하지 않으므로 사용자 지정 명령 기능은 템플릿 컴퍼지션 및 확장을 지원하지 않습니다. 사용자는 자동으로 생성된 템플릿의 응답만 편집합니다.
* 사용자 지정 명령 기능은 LG에서 삽입하는 사용자 지정 함수를 지원하지 않습니다. 미리 정의된 함수는 지원됩니다.
* 사용자 지정 명령 기능은 `strict`, `replaceNull` 및 `lineBreakStyle`과 같은 옵션을 지원하지 않습니다.

### <a name="add-template-responses-to-a-turnonoff-command"></a>TurnOnOff 명령에 템플릿 응답 추가

새 매개 변수를 추가하려면 `TurnOnOff` 명령을 수정합니다. 다음 구성을 사용합니다.

| 설정            | 제안 값       |
| ------------------ | --------------------- |
| **이름**               | `SubjectContext`         |
| **필수**           | 선택 취소               |
| **형식**               | **String**                |
| **기본값**      | `all` |
| **Configuration**      | **내부 카탈로그의 미리 정의된 입력 값 허용** |
| **미리 정의된 입력 값** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>완료 규칙 수정

기존 완료 규칙 **ConfirmationResponse** 의 **작업** 섹션을 편집합니다. 작업 **편집** 창에서 **템플릿 편집기** 로 전환합니다. 텍스트를 다음 예제로 바꿉니다.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

다음 입력 및 출력을 사용하여 애플리케이션을 학습시키고 테스트합니다. 응답의 변형을 확인합니다. 변형은 템플릿 값의 여러 대안과 적응형 식을 사용하여 만들어졌습니다.

* 입력: *turn on the tv*
* 출력: Ok, turning the tv on
* 입력: *turn on the tv*
* 출력: Done, turned on the tv
* 입력: *turn off the lights*
* 출력: Ok, turning all the lights off
* 입력: *turn off room lights*
* 출력: Ok, turning off the room lights

## <a name="use-a-custom-voice"></a>사용자 지정 음성 사용

사용자 지정 명령 응답을 사용자 지정하는 또 다른 방법은 출력 음성을 선택하는 것입니다. 다음 단계에 따라 기본 음성을 사용자 지정 음성으로 전환합니다.

1. 사용자 지정 명령 애플리케이션의 왼쪽 창에서 **설정** 을 선택합니다.
1. 가운데 창에서 **Custom Voice** 를 선택합니다.
1. 표에서 사용자 지정 음성 또는 공용 음성을 선택합니다.
1. **저장** 을 선택합니다.

> [!div class="mx-imgBorder"]
> ![샘플 문장과 매개 변수를 보여주는 스크린샷](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> 공용 음성의 경우 특정 지역에서만 인공신경망 형식을 사용할 수 있습니다. 자세한 내용은 [Speech Service 지원 지역](./regions.md#neural-and-standard-voices)을 참조하세요.
>
> **Custom Voice** 프로젝트 페이지에서 사용자 지정 음성을 만들 수 있습니다. 자세한 내용은 [Custom Voice 시작](./how-to-custom-voice.md)을 참조하세요.

이제 애플리케이션이 기본 음성 대신 선택한 음성으로 응답합니다.

## <a name="next-steps"></a>다음 단계

* Speech SDK를 사용하여 [사용자 지정 명령 애플리케이션을 클라이언트 앱과 통합](how-to-custom-commands-setup-speech-sdk.md)하는 방법을 알아봅니다.
* Azure DevOps를 사용하여 사용자 지정 명령 애플리케이션에 대한 [지속적인 배포를 설정](how-to-custom-commands-deploy-cicd.md)합니다.

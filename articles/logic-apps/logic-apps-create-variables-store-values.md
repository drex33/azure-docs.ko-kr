---
title: 값 저장 및 전달에 대한 변수 만들기와 관리
description: Azure Logic Apps를 사용하여 만든 자동화된 작업 및 워크플로의 변수를 사용하여 값을 저장, 관리, 사용 및 전달하는 방법에 대해 알아봅니다
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333905"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Azure Logic Apps에서 변수를 사용하여 값 저장 및 관리

이 문서에서는 논리 앱에 값을 저장하는 데 사용하는 변수를 만들고 작업하는 방법을 보여줍니다. 예를 들어, 변수는 루프가 실행되는 횟수 계산에 유용할 수 있습니다. 배열을 반복하거나 배열에서 특정 항목을 확인하는 경우, 변수를 사용하여 각 배열 항목의 인덱스 번호를 참조할 수 있습니다.

정수, float, 부울, 문자열, 배열, 개체 등의 데이터 형식에 대한 변수를 만들 수 있습니다. 변수를 만든 후 다른 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다.

* 변수 값을 가져오거나 참조합니다.
* 증가 및 감소라고도 하는 상수 값으로 변수를 늘리거나 줄입니다.
* 변수에 다른 값을 할당합니다.
* 변수의 값을 문자열 또는 배열의 마지막 항목으로 삽입하거나 *추가* 합니다.

변수는 해당 변수를 만드는 논리 앱 인스턴스 내에서만 존재하고 전역입니다. 또한 논리 앱 인스턴스 내의 모든 루프 반복에서 지속됩니다. 변수를 참조하는 경우, 작업의 출력을 참조하는 일반적인 방법인 작업 이름이 아니라 변수 이름을 토큰으로 사용합니다.

> [!IMPORTANT]
> 기본적으로 “Foreach” 루프의 주기는 병렬로 실행됩니다. 루프에서 변수를 사용하는 경우 루프를 [순차적으로](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) 실행하므로 변수가 예측 가능한 결과를 반환합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 변수를 만들려는 논리 앱

  논리 앱을 처음 사용하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* 논리 앱의 첫 번째 단계인 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  변수를 만들고 사용하기 위한 작업을 추가하려면 먼저 논리 앱이 트리거로 시작되어야 합니다.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>변수 초기화

변수를 만들고 해당 데이터 형식 및 초기 값을 선언할 수 있습니다. 논리 앱에서 하나의 작업으로 모두 가능합니다. 전역 수준에서만 변수를 선언할 수 있고 범위, 조건 및 루프 내에서는 선언할 수 없습니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 기존 트리거가 있는 논리 앱과 Azure Portal을 사용합니다.

1. 논리 앱에서, 변수를 추가하려는 단계 아래에서 다음 단계 중 하나를 따릅니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** 를 선택합니다.

     !["논리 앱 디자이너" 페이지에서 선택한 "새 단계" 작업을 보여 주는 스크린샷.](./media/logic-apps-create-variables-store-values/add-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호( **+** )가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 더하기 기호를 선택한 다음, **작업 추가** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 `variables`을 필터로 입력합니다. 작업 목록에서 **변수 초기화** 를 선택합니다.

   ![작업 선택](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. 아래에서 설명된 대로 변수에 대한 정보를 제공합니다.

   | 속성 | 필수 | 값 |  Description |
   |----------|----------|-------|--------------|
   | **이름** | 예 | <*variable-name*> | 증가할 변수의 이름입니다. |
   | **형식** | 예 | <*variable-type*> | 변수의 데이터 형식입니다. |
   | **값** | 예 | <*start-value*> | 변수의 초기 값 <p><p>**팁**: 선택 사항이지만, 변수의 시작 값을 항상 알 수 있도록 이 값을 설정하는 것이 좋습니다. |
   |||||

   예를 들면 다음과 같습니다.

   ![변수 초기화](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. 이제 원하는 작업을 계속해서 추가합니다. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

디자이너에서 코드 보기 편집기로 전환하는 경우, **변수 초기화** 작업은 논리 앱 정의 내에서 다음과 같이 JSON(JavaScript Object Notation) 형식으로 표시됩니다.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> **변수 초기화** 작업에는 배열로 구조화된 `variables` 섹션이 있지만 작업에서 한 번에 하나의 변수만 만들 수 있습니다. 각 새 변수에는 개별 **변수 초기화** 작업이 필요합니다.

다른 몇 가지 변수 형식에 대한 예는 다음과 같습니다.

‘문자열 변수’

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

‘부울 변수’

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

‘정수가 포함된 배열’

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

‘문자열이 포함된 배열’

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>변수 값 가져오기

변수의 내용을 검색하거나 참조하기 위해 논리 앱 디자이너 및 코드 보기 편집기에서 [variables() 함수](../logic-apps/workflow-definition-language-functions-reference.md#variables)를 사용할 수도 있습니다. 변수를 참조하는 경우, 작업의 출력을 참조하는 일반적인 방법인 작업 이름이 아니라 변수 이름을 토큰으로 사용합니다.

예를 들어, 이 식은 [이 문서의 앞부분에서 만든](#append-value) 배열 변수에서 `variables()` 함수를 사용해 항목을 가져옵니다. `string()` 함수는 변수의 콘텐츠를 문자열 형식으로 반환합니다: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>변수 증가 

상수 값으로 변수를 늘리거나 *증분* 하려면 **변수 증분** 작업을 논리 앱에 추가합니다. 이 작업은 정수 및 float 변수에서만 작동합니다.

1. 논리 앱 디자이너에서, 기존 변수를 늘리려는 단계 아래에서 **다음 단계** 를 선택합니다. 

   예를 들어, 이 논리 앱에는 변수를 만든 작업과 트리거가 이미 있습니다. 따라서 다음 단계 아래에 새 작업을 추가합니다.

   ![작업 추가](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   기존 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 더하기 기호를 선택한 다음, **작업 추가** 를 선택합니다.

1. 검색 상자에 “변수 증가”를 필터로 입력합니다. 작업 목록에서 **변수 증분** 을 선택합니다.

   ![“변수 증가” 작업 선택](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. 변수 증가에 대한 다음 정보를 입력합니다.

   | 속성 | 필수 | 값 |  Description |
   |----------|----------|-------|--------------|
   | **이름** | 예 | <*variable-name*> | 증가할 변수의 이름입니다. |
   | **값** | 예 | <*increment-value*> | 변수를 증가하는 데 사용되는 값입니다. 기본값은 1입니다. <p><p>**팁**: 선택 사항이지만, 변수를 증가하기 위한 특정 값을 항상 알 수 있도록 이 값을 설정하는 것이 좋습니다. |
   ||||

   예를 들면 다음과 같습니다.

   ![증가 값 예제](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

디자이너에서 코드 보기 편집기로 전환하는 경우, **변수 증분** 작업은 논리 앱 정의 내에서 다음과 같이 JSON 형식으로 표시됩니다.

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>예제: 루프 카운터 만들기

변수는 일반적으로 루프가 실행되는 횟수를 계산하는 데 사용됩니다. 이 예제에서는 메일의 첨부 파일 수를 계산하는 루프를 만들어 이 작업의 변수를 만들고 사용하는 방법을 보여 줍니다.

1. Azure Portal에서 빈 논리 앱을 만듭니다. 새 메일 및 첨부 파일을 확인하는 트리거를 추가합니다.

   이 예제에서는 **새 메일이 도착하는 경우** 에 대한 Office 365 Outlook 트리거를 사용합니다. 메일에 첨부 파일이 있는 경우에만 실행되도록 이 트리거를 설정할 수 있습니다. 그러나 Outlook.com 커넥터와 같이 첨부 파일이 있는 새 메일을 확인하는 모든 커넥터를 사용할 수 있습니다.

1. 트리거가 첨부 파일을 확인하고 논리 앱의 워크플로에 해당 첨부 파일을 전달하게 하려면 다음 속성에 대해 **예** 를 선택합니다.

   * **첨부 파일 있음**
   * **첨부 파일 포함**

   ![첨부 파일 확인 및 포함](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. [**변수 초기화** 작업](#create-variable)을 추가합니다. 시작 값을 0으로 지정한 `Count` 라는 정수 변수를 만듭니다.

   ![“변수 초기화” 작업 추가](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. 각 첨부 파일을 순환하려면 *for each* 루프를 추가합니다.

   1. **변수 초기화** 작업에서 **새 단계** 를 선택합니다.

   1. **작업 선택** 아래에서 **기본 제공** 을 선택합니다. 검색 상자에서 검색 필터로 `for each`을 입력한 다음 **For each** 를 선택합니다.

      ![“For each” 루프 추가](./media/logic-apps-create-variables-store-values/add-loop.png)

1. 루프에서 **이전 단계에서 출력 선택** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 나타나면 **첨부 파일** 을 선택합니다.

   !["첨부 파일" 선택](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Attachments** 속성은 이메일 첨부 파일을 포함한 배열을 트리거 출력에서 사용자의 루프로 전달합니다.

1. **For each** 루프에서 **작업 추가** 를 선택합니다.

   ![“작업 추가” 선택](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. 검색 상자에 “변수 증가”를 필터로 입력합니다. 작업 목록에서 **변수 증분** 을 선택합니다.

   > [!NOTE]
   > **변수 증분** 작업이 루프 내부에 표시되는지 확인합니다. 작업이 루프 외부에 표시되는 경우 작업을 루프로 끌어옵니다.

1. **변수 증가** 작업의 **이름** 목록에서 **Count** 변수를 선택합니다.

   ![“Count” 변수 선택](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. 루프 아래에 첨부 파일 수를 전송하는 작업을 추가합니다. 작업에서 **Count** 변수의 값을 다음과 같이 포함합니다.

   ![결과를 전송하는 작업 추가](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 사용할 수 없는 경우 논리 앱 메뉴에서 **개요** 를 선택합니다. 도구 모음에서 **사용** 을 선택합니다.

1. 논리 앱 디자이너 도구 모음에서 **실행** 을 선택합니다. 이 단계에서는 논리 앱을 수동으로 시작합니다.

1. 이 예제에서 사용한 메일 계정에 하나 이상의 첨부 파일이 있는 메일을 보냅니다.

   이 단계에서는 논리 앱의 트리거가 실행되어 논리 앱의 워크플로에 대한 인스턴스가 생성 및 실행됩니다. 그 결과, 논리 앱이 전송한 메일에 포함된 첨부 파일 수를 보여 주는 메시지나 메일을 보냅니다.

디자이너에서 코드 보기 편집기로 전환하는 경우, **For each** 루프가 논리 앱 정의 내에 포함된 **변수 증분** 작업과 함께 다음과 같이 JSON 형식으로 표시됩니다.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>변수 감소

상수 값으로 변수를 줄이거나 *감소* 시키려면 [변수 증가](#increment-value) 단계에서 **변수 감소** 작업을 대신 찾아 선택하고 나머지를 따릅니다. 이 작업은 정수 및 float 변수에서만 작동합니다.

다음은 **변수 감소** 작업의 속성입니다.

| 속성 | 필수 | 값 |  Description |
|----------|----------|-------|--------------|
| **이름** | 예 | <*variable-name*> | 감소할 변수의 이름입니다. | 
| **값** | 예 | <*increment-value*> | 변수를 감소하기 위한 값입니다. 기본값은 1입니다. <p><p>**팁**: 선택 사항이지만, 변수를 감소하기 위한 특정 값을 항상 알 수 있도록 이 값을 설정하는 것이 좋습니다. |
||||| 

디자이너에서 코드 보기 편집기로 전환하는 경우, **변수 감소** 작업은 논리 앱 정의 내에서 다음과 같이 JSON 형식으로 표시됩니다.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>변수 설정

기존 변수에 다른 값을 할당하려면 다음 경우를 제외하고 [변수를 늘리기](#increment-value) 위한 단계를 따릅니다.

1. 대신 **변수 설정** 작업을 찾아 선택합니다.

1. 할당할 변수 이름과 값을 입력하는 경우 새 값과 변수의 데이터 형식이 같아야 합니다. 이 작업에는 기본값이 없으므로 값이 필요합니다.

다음은 **변수 설정** 작업의 속성입니다.

| 속성 | 필수 | 값 |  Description |
|----------|----------|-------|--------------|
| **이름** | 예 | <*variable-name*> | 변경할 변수의 이름입니다. |
| **값** | 예 | <*new-value*> | 변수에 할당할 값입니다. 둘 다 데이터 형식이 같아야 합니다. |
||||| 

> [!NOTE]
> 변수를 증가 또는 감소하지 않는 경우 루프 내부에서 변수를 변경하면 루프가 기본적으로 병렬로 실행되거나 동시에 실행되므로 예기치 않은 결과가 생성될 수 있습니다. 이러한 경우, 순차적으로 실행되도록 루프를 설정합니다. 예를 들어, 루프 내에서 변수 값을 참조하고 해당 루프 인스턴스의 시작 및 끝에서 동일한 값이 필요한 경우, 다음 단계에 따라 루프 실행 방법을 변경합니다. 
>
> 1. 루프의 오른쪽 위 모서리에서 줄임표( **...** ) 단추를 선택한 다음 **설정** 을 선택합니다.
> 
> 2. **동시성 제어** 아래에서 **기본값 재정의** 설정을 **켬** 으로 변경합니다.
>
> 3. **병렬도** 슬라이더를 **1** 로 끕니다.

디자이너에서 코드 보기 편집기로 전환하는 경우, **변수 설정** 작업은 논리 앱 정의 내에서 다음과 같이 JSON 형식으로 표시됩니다. 이 예에서는 `Count` 변수의 현재 값을 다른 값으로 변경합니다.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>변수에 추가

문자열 또는 배열을 저장하는 변수의 경우, 변수 값을 해당 문자열 또는 배열의 마지막 항목으로 삽입하거나 추가할 수 있습니다. 대신 다음 단계를 따르는 경우를 제외하고, [변수를 늘리기](#increment-value) 위한 단계를 따를 수 있습니다. 

1. 변수가 문자열인지 또는 배열인지에 따라 다음 작업 중 하나를 찾아서 선택합니다. 

   * **문자열 변수에 추가**
   * **배열 변수에 추가** 

1. 문자열 또는 배열의 마지막 항목으로 추가할 값을 입력합니다. 이 값은 필수입니다.

다음은 **추가...** 작업의 속성입니다.

| 속성 | 필수 | 값 |  Description |
|----------|----------|-------|--------------|
| **이름** | 예 | <*variable-name*> | 변경할 변수의 이름입니다. |
| **값** | 예 | <*append-value*> | 추가하려는 값으로, 임의 형식일 수 있습니다. |
|||||

디자이너에서 코드 보기 편집기로 전환하는 경우, **배열 변수에 추가** 작업은 논리 앱 정의 내에서 다음과 같이 JSON 형식으로 표시됩니다. 이 예제에서는 배열 변수를 만들고 배열의 마지막 항목으로 다른 값을 추가합니다. 결과는 `[1,2,3,"red"]` 배열을 포함하는 업데이트된 변수입니다.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>다음 단계

* [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

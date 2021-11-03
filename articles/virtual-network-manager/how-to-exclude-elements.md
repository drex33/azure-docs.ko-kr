---
title: Azure Virtual Network Manager에서 동적 멤버 자격의 요소 제외 (미리 보기)
description: Azure Virtual Network Manager에서 동적 멤버 자격의 요소를 제외 하는 방법에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 318a2eba29ee7641219e8c970c3dd3a51407ddf3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102790"
---
# <a name="excluding-elements-from-dynamic-membership-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager에서 동적 멤버 자격의 요소 제외 (미리 보기)

이 문서에서는 조건부 문을 사용 하 여 동적 멤버 자격에서 가상 네트워크를 제외 하는 방법에 대해 알아봅니다. 드롭다운 메뉴에서 매개 변수 및 연산자를 선택 하 여 기본 편집기를 사용 하 여 이러한 조건문을 만듭니다. 또한 고급 편집기를 사용 하 여 기존 네트워크 그룹의 조건부 문을 업데이트 하는 방법도 알아봅니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="parameters-and-operators"></a><a name="parameters"></a> 매개 변수 및 연산자

동적 멤버 자격이 있는 가상 네트워크는 조건문을 사용 하 여 선택 됩니다. **And** 및 OR와 같은 *논리 연산자* 를 사용 하 여 둘 이상의 조건문을 정의 **하거나** 선택한 가상 네트워크의 범위를 좁히는 시나리오에 대해 정의할 수 있습니다. 

지원 되는 매개 변수 목록:

| 매개 변수 | 고급 편집기 필드 |
| ---------- | ------------------------- |
| 속성 | `Name` |
| ID | `Id` |
| 태그| `tag['tagName']` |
| Subscription Name | `[subscription().Name]` |
| 구독 ID | `[subscription().Id]` |
| 구독 태그 | `[subscription().tags['tagName']]` |
| 리소스 그룹 이름 | `[resourceGroup().Name]` |
| 리소스 그룹 ID | `[resourceGroup().Id]` |
| 리소스 그룹 태그 | `[resourceGroup().tags['tagName']]` |

지원 되는 연산자 목록:

| 연산자 | 고급 편집기 |
| --------- | --------------- |
| 포함 | `"contains": <>` |
| 포함하지 않음 | `"notcontains": <>` |
| In(다음 안에) | `"in": <>` |
| 속하지 않음 | `"notin": <>` |
| 같음 | `"equals": <>` |
| 같지 않음 | `"notequals": <>` |
| 다음 중 하나가 포함 됩니다. | `"contains": <>` |
| 모두 포함 | `"contains": <>` |
| 다음 중 하나가 포함 되지 않습니다. | `"notcontains": <>` |
| Exists | `"exists": true` |
| 존재 하지 않음 | `"exists": false` |

> [!NOTE]
> *Exists* 및 Exists *가 없는* 연산자는 **Tags** 매개 변수와 함께 사용 됩니다.

## <a name="basic-editor"></a>기본 편집기

구독에 다음과 같은 가상 네트워크가 있다고 가정 합니다. 각 가상 네트워크에는 *프로덕션* 또는 *테스트* 태그가 연결 되어 있습니다. 프로덕션 태그를 사용 하 여 가상 네트워크를 선택 하 고 이름에 **VNet-A** 를 포함 하려고 합니다.

* VNet-a-미국- *프로덕션*
* VNet-WestUS- *프로덕션*
* VNet-B-WestUS- *테스트*
* VNet-C-WestUS- *테스트*
* VNetA- *프로덕션*
* VNetB- *테스트*

기본 편집기를 사용 하 여 조건문을 만들기 시작 하려면 새 네트워크 그룹을 만들어야 합니다.

1. Azure Virtual Network Manager 인스턴스로 이동 하 여 *설정* 에서 **네트워크 그룹** 을 선택 합니다. 그런 다음 **+ 추가** 를 선택 하 여 새 네트워크 그룹을 만듭니다.

1. 네트워크 그룹의 **이름을** 입력 합니다. 그런 다음 **조건부 문** 탭을 선택 합니다.

1. *매개 변수* 아래의 드롭다운에서 **태그** 를 선택 하 고 *연산자* 아래의 드롭다운에서 **존재** 를 선택 합니다.

1. *조건* 아래에서 **Prod** 를 입력 한 다음 **평가** 를 선택 합니다. VNet-A-EastUS, VNet-B-WestUS 및 VNetA가 목록에 표시 됩니다.

1. 논리 연산자 및를 선택 하 여 다른 조건문 **을** 추가 합니다. *매개 변수의* **이름** 을 선택 하 고 *연산자* 에 대해를 **포함** 합니다. *조건* 필드에 **VNet-A를** 입력 합니다. **평가** 를 선택 하 여 목록에 표시 되는 가상 네트워크를 확인 합니다. VNet-A-E미국 및 VNet-WestUS만 표시 됩니다.

1. **검토 + 만들기** 를 선택 하 고 유효성 검사를 통과 한 후 **만들기** 를 선택 합니다.

> [!NOTE] 
> **기본 편집기** 는 네트워크 그룹을 만드는 동안에만 사용할 수 있습니다. 

## <a name="advanced-editor"></a>고급 편집기

고급 편집기는 네트워크 그룹을 만드는 동안 또는 기존 네트워크 그룹을 업데이트할 때 가상 네트워크를 선택 하는 데 사용할 수 있습니다. 

1. 이전 섹션에서 만든 네트워크 그룹을 선택 합니다. 그런 다음 **조건부 문** 탭을 선택 합니다.

1. 네트워크 그룹에 대 한 조건문은 다음과 같이 고급 편집기 보기에서 볼 수 있습니다.

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          }
       ]
    }
    ```

    `"allOf"`매개 변수는 **AND** 논리 연산자로 구분 된 조건문을 모두 포함 합니다.

1. **WestUS** 를 *포함 하지 않는* *이름* 필드에 대해 다른 조건문을 추가 하려면 고급 편집기에 다음을 입력 합니다.

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "notcontains": "WestUS"
          }
       ]
    }
    ```

1. 그런 다음 **평가** 를 선택 합니다. 목록에서 VNet-A-A-A-미국 가상 네트워크만 표시 되어야 합니다.

1. **검토 + 저장** 을 선택한 다음 유효성 검사가 통과 되 면 **저장** 을 선택 합니다.

고급 편집기에서 사용할 수 있는 매개 변수 및 연산자의 전체 목록은 [매개 변수 및 연산자](#parameters) 를 참조 하세요. 더 많은 예제는 아래를 참조 하세요.

## <a name="more-examples"></a>추가 예제

### <a name="example-1-or-operator-only"></a>예제 1: OR 연산자만

이 예에서는 **OR** 논리 연산자를 사용 하 여 두 개의 조건문을 구분 합니다.

* 기본 편집기:

    :::image type="content" source="./media/how-to-exclude-elements/or-operator.png" alt-text="OR 논리 연산자를 사용 하는 네트워크 그룹 조건문의 스크린샷":::

* 고급 운영자:

    ```json
    {
       "anyOf": [
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "contains": "VNetA"
          }
       ]
    }
    ```

`"anyOf"`매개 변수는 **OR** 논리 연산자로 구분 된 조건문을 모두 포함 합니다.

### <a name="example-2-and-and-or-operator-at-the-same-time"></a>예 2: AND and OR 연산자 (동시)

* 기본 편집기:

    :::image type="content" source="./media/how-to-exclude-elements/both-operator.png" alt-text="OR 및 AND 논리 연산자를 사용 하는 네트워크 그룹 조건문의 스크린샷":::

* 고급 편집기:

```json
{
   "allOf": [
      {
         "anyOf": [
            {
               "field": "Name",
               "contains": "VNet-A"
            },
            {
               "field": "Name",
               "contains": "VNetA"
            }
         ]
      },
      {
         "field": "Name",
         "notcontains": "West"
      }
   ]
}
```

및는 모두 `"allOf"` `"anyOf"` 코드에서 사용 됩니다. **및** 연산자는 목록에서 마지막 이므로 **OR** 연산자를 사용 하는 두 개의 조건문을 포함 하는 코드의 외부 부분에 있습니다.

## <a name="next-steps"></a>다음 단계

- [네트워크 그룹](concept-network-groups.md)에 대해 알아봅니다.
- [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.

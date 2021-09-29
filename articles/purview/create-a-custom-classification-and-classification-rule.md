---
title: 사용자 지정 분류 및 분류 규칙 만들기
description: Azure Purview에서 조직에 고유한 데이터 자산의 데이터 형식을 정의하는 사용자 지정 분류를 만드는 방법을 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 12af9165b8393291b67fd0cbe4cb2ced6ae77a17
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212191"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure Purview의 사용자 지정 분류

이 문서에서는 사용자 지정 분류를 만들어 조직에 고유한 데이터 자산의 데이터 형식을 정의하는 방법을 설명합니다. 또한 데이터 자산 전체에서 지정된 데이터를 찾을 수 있는 사용자 지정 분류 규칙 만들기에 대해서도 설명합니다.

## <a name="default-system-classifications"></a>기본 시스템 분류

Azure Purview Data Catalog는 데이터 자산에 있을 수 있는 일반적인 개인 데이터 형식을 나타내는 대규모 기본 시스템 분류 세트를 제공합니다. 사용 가능한 시스템 분류의 전체 목록은 [Azure Purview에서 지원되는 분류](supported-classifications.md)를 참조하세요.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="분류 선택" border="true":::

또한 기본 분류가 요구 사항을 충족하지 않는 경우 사용자 지정 분류를 만들 수 있습니다.

> [!Note]
> 당사 [데이터 샘플링 규칙](sources-and-scans.md#sampling-within-a-file)은 시스템 분류와 사용자 지정 분류 모두에 적용됩니다.  

## <a name="steps-to-create-a-custom-classification"></a>사용자 지정 분류를 만드는 단계

사용자 지정 분류를 만들려면 다음 단계를 수행합니다.

1. 카탈로그의 왼쪽 메뉴에서 **데이터 맵** 을 선택 합니다.

2. **주석 관리** 에서 **분류** 를 선택 합니다.

3. **+ 새로 만들기** 를 선택합니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="새 분류" border="true":::

분류에 이름과 설명을 지정할 수 있는 **새 분류 추가** 창이 열립니다. `your company name.classification name`처럼 이름 간격 규칙을 사용하는 것이 좋습니다.

Microsoft 시스템 분류는 예약된 `MICROSOFT.` 네임스페이스 아래에 그룹화됩니다. 예를 들어 **MICROSOFT.GOVERNMENT.US.SOCIAL\_SECURITY\_NUMBER** 입니다.

분류 이름은 문자로 시작해야 하며 이는 문자, 숫자, 마침표(.) 또는 밑줄 문자 순서이어야 합니다. 공백은 허용되지 않습니다. 입력하면 UX에서 자동으로 식별 이름을 생성합니다. 이 식별 이름은 카탈로그의 자산에 적용할 때 사용자에게 표시됩입니다.

이름을 짧게 유지하기 위해 시스템은 다음 논리에 따라 식별 이름을 만듭니다.

- 네임스페이스의 마지막 두 세그먼트를 제외한 모든 세그먼트가 잘립니다.

- 각 단어의 첫 글자가 대문자로 시작하도록 대/소문자를 조정합니다.

- 모든 밑줄(\_)은 공백으로 바뀝니다.

예를 들어, 분류 이름을 **CONTOSO.HR.EMPLOYEE\_ID** 로 지정한 경우 식별 이름은 **Hr.Employee ID** 로 시스템에 저장됩니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

**확인** 을 선택하면 새 분류가 분류 목록에 추가됩니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="사용자 지정 분류" border="true":::

목록에서 분류를 선택하면 분류 세부 정보 페이지가 열립니다. 여기서 분류에 대한 모든 세부 정보를 찾을 수 있습니다.

이러한 세부 정보에는 인스턴스 수, 정식 이름, 관련 분류 규칙(있는 경우), 소유자 이름이 포함됩니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="분류 선택" border="true":::

## <a name="custom-classification-rules"></a>사용자 지정 분류 규칙

카탈로그 서비스는 스캐너가 특정 데이터 형식을 자동으로 검색하는 데 사용하는 기본 분류 규칙 세트를 제공합니다. 또한 고유한 사용자 지정 분류 규칙을 추가하여 데이터 자산에서 찾고자 하는 다른 형식의 데이터를 검색할 수도 있습니다. 이 기능은 데이터 자산 내에서 데이터를 찾으려고 할 때 매우 유용할 수 있습니다.

예를 들어, Contoso라는 회사가 회사 전체에서 표준화된 직원 ID(\"Employee\" 단어와 그 뒤에 EMPLOYEE{GUID}를 만드는 GUID로 구성)를 가지고 있다고 가정해 보겠습니다. 예를 들어, 직원 ID의 한 인스턴스는 `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55`와 같습니다.

Contoso는 사용자 지정 분류 규칙을 만들어 이러한 ID의 인스턴스를 찾도록 검색 시스템을 구성할 수 있습니다. 이 경우 데이터 패턴 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$`와 일치하는 정규식을 제공할 수 있습니다. 경우에 따라 데이터가 일반적으로 Employee\_ID 또는 EmployeeID와 같이 이름을 알고 있는 열에 있는 경우 열 패턴 정규식을 추가하여 검사의 정확도를 높일 수 있습니다. 예제 정규식은 Employee\_ID\|EmployeeID입니다.

그런 다음, 검색 시스템에서 이 규칙을 사용하여 열의 실제 데이터와 열 이름을 검사하여 직원 ID 패턴이 있는 모든 인스턴스를 식별할 수 있습니다.

## <a name="steps-to-create-a-custom-classification-rule"></a>사용자 지정 분류 규칙을 만드는 단계

사용자 지정 분류 규칙을 만들려면 다음을 수행합니다.

1. 이전 섹션의 지침에 따라 사용자 지정 분류를 만듭니다. 이 사용자 지정 분류를 분류 규칙 구성에 추가하여 시스템이 열에서 일치하는 항목을 찾을 때 적용하도록 합니다.

2. **데이터 맵** 아이콘을 선택 합니다.

3. **분류 규칙** 섹션을 선택합니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification-rules.png" alt-text="분류 규칙 타일" border="true":::

4. **새로 만들기** 를 선택합니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification-rule.png" alt-text="새 분류 규칙 추가" border="true":::

5. **새 분류 규칙** 대화 상자가 열립니다. 필드를 입력하고 **정규식 규칙** 또는 **사전 규칙** 을 만들지 여부를 결정합니다.

   |필드     |Description  |
   |---------|---------|
   |속성   |    필수 사항입니다. 최대 100자입니다.    |
   |Description      |선택 사항입니다. 최대 256자입니다.    |
   |분류 이름    | 필수 요소. 드롭다운 목록에서 분류의 이름을 선택하여 일치하는 항목이 있는 경우 적용하도록 스캐너에 지시합니다.        |
   |시스템 상태   |  필수 요소. 옵션은 사용 또는 사용 안 함입니다. 기본값은 사용입니다.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="새 분류 규칙 만들기" border="true":::

### <a name="creating-a-regular-expression-rule"></a>정규식 규칙 만들기

1. 정규식 규칙을 만드는 경우 다음 화면이 표시됩니다. 규칙에 대해 **제안된 정규식 패턴을 생성** 하는 데 사용할 파일을 선택적으로 업로드할 수 있습니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="새 정규식 규칙 만들기" border="true":::

1. 제안된 정규식 패턴을 생성하기로 결정한 경우 파일을 업로드한 후 제안된 패턴 중 하나를 선택하고 **패턴에 추가** 를 선택하여 제안된 데이터 및 열 패턴을 사용합니다. 제안된 패턴을 수정하거나 파일을 업로드하지 않고 사용자 고유의 패턴을 입력할 수도 있습니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="제안된 정규식 생성" border="true":::

   |필드     |Description  |
   |---------|---------|
   |데이터 패턴    |선택 사항입니다. 데이터 필드에 저장된 데이터를 나타내는 정규식입니다. 제한이 매우 큽니다. 이전 예제에서 데이터 패턴은 문자 그대로 `Employee{GUID}`라는 단어인 직원 ID를 테스트합니다.  |
   |열 패턴    |선택 사항입니다. 일치시킬 열 이름을 나타내는 정규식입니다. 제한이 매우 큽니다. |

1. **데이터 패턴** 에서 **최소 일치 임계값** 을 사용하여 분류를 적용하기 위해 스캐너가 찾아야 하는 열에서 고유 데이터 값 일치의 최소 백분율을 설정할 수 있습니다. 제안 값은 60%입니다. 여러 데이터 패턴을 지정하는 경우 이 설정은 사용하지 않도록 설정되고 값은 60%로 고정됩니다.

   > [!Note]
   > 최소 일치 임계값은 1% 이상이어야 합니다.

1. 이제 규칙을 확인하고 **만들** 수 있습니다.
1. 만들기 프로세스를 완료하기 전에 분류 규칙을 테스트하여 자산에 태그가 적용되는지 유효성을 검사합니다. 규칙의 분류는 스캔에서와 마찬가지로 업로드된 샘플 데이터에 적용됩니다. 즉, 모든 시스템 분류 및 사용자 지정 분류가 파일의 데이터와 일치합니다.

   입력 파일에는 구분된 파일(CSV, PSV, SSV, TSV), JSON 또는 XML 콘텐츠가 포함될 수 있습니다. 콘텐츠는 입력 파일의 파일 확장명에 따라 구문 분석됩니다. 구분된 데이터는 언급된 형식과 일치하는 파일 확장명을 가질 수 있습니다. 예를 들어, TSV 데이터는 MySampleData.csv라는 파일에 존재할 수 있습니다. 또한 구분된 콘텐츠에는 최소 3개의 열이 있어야 합니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-screen.png" alt-text="만들기 전에 규칙 테스트" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-uploaded-file-result-screen.png" alt-text="테스트 파일 업로드 후 적용된 분류 보기" border="true":::

### <a name="creating-a-dictionary-rule"></a>사전 규칙 만들기

1. 사전 규칙을 만드는 경우 다음 화면이 표시됩니다. 단일 열에서 만드는 분류에 사용할 수 있는 모든 값이 포함된 파일을 업로드합니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="사전 규칙 만들기" border="true":::

1. 사전이 생성된 후 최소 일치 임계값을 조정하고 규칙을 제출할 수 있습니다.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="사전 생성 확인 표시가 있는 사전 규칙을 만듭니다." border="true":::

## <a name="next-steps"></a>다음 단계

분류 규칙을 만들었으므로 이제 검사 시 규칙을 사용할 수 있도록 검사 규칙 집합에 추가할 준비가 되었습니다. 자세한 내용은 [검사 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조하세요.

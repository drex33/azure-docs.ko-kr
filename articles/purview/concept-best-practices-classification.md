---
title: Azure Purview 분류 모범 사례
description: 이 문서에서는 Azure Purview의 분류에 대한 모범 사례를 제공합니다.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 5d9f5fd2efbfbe01e69db31866ae55f6c26fd2f9
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133426306"
---
# <a name="azure-purview-classification-best-practices"></a>Azure Purview 분류 모범 사례

Azure Purview의 컨텍스트에서 데이터 분류는 데이터의 비즈니스 컨텍스트(예: Passport 번호, 운전 라이선스 번호, 신용 카드 번호, SWIFT 코드, 사람의 이름 등)에 따라 데이터 자산에 고유한 논리적 레이블 또는 클래스를 할당하여 데이터 자산을 분류하는 방법입니다. 데이터 자산이 분류되면 더 잘 이해하고 검색하고 제어할 수 있습니다. 가장 중요한 것은 데이터와 관련된 위험을 이해하고 데이터 자산 전체에서 중요한 데이터 또는 중요한 데이터를 비제한 확산 및 무단 액세스로부터 보호하는 조치를 구현하는 데 도움이 된다는 것입니다. 
 
Azure Purview는 데이터 원본을 검사하는 동안 자동화된 분류 기능을 제공하여 200개 이상의 시스템 기본 제공 분류와 데이터에 대한 사용자 지정 분류를 만드는 기능을 제공합니다. 분류는 자동으로(검사의 일부로 구성된 경우) 자산에 적용하거나 검색 및 검색 후 Purview Studio에서 수동으로 편집할 수 있습니다.  
 
## <a name="why-do-you-need-to-adopt-classification"></a>분류를 채택해야 하는 이유는 무엇인가요? 

분류는 데이터를 **논리적 범주로** 구성하여 나중에 사용하기 위해 쉽게 검색, 정렬 및 식별할 수 있도록 하는 프로세스입니다. 이는 데이터 거버넌스에 특히 중요할 수 있습니다. 분류가 중요한 이유는 여러 가지가 있으며, 가장 일반적인 이유는 다음과 같습니다. 
* 관심 있는 데이터 자산에 대한 검색 범위를 좁히는 데 도움이 됩니다.
* 조직에서 중요한 다양한 데이터 클래스와 저장되는 위치를 구성하고 이해하는 데 도움이 됩니다.
* 중요한 데이터 자산과 관련된 위험을 이해하고 위험을 완화하기 위한 적절한 조치를 취하는 데 도움이 됩니다.

아래 시나리오에서는 자산 수준과 Azure SQL Database *Customers* 테이블에 대한 스키마 수준에서 분류를 적용할 수 있습니다. 

:::image type="content" source="./media/concept-best-practices/classification-customers-example-1.png" alt-text="Azure SQL Database 분류를 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-customers-example-1.png":::

**시스템 분류:** Azure Purview는 기본적으로 대규모 시스템 분류 집합을 지원합니다. 사용 가능한 시스템 분류의 전체 목록은 [Azure Purview에서 지원되는 분류](./supported-classifications.md)를 참조하세요. 

위의 예제에서 Person's Name은 시스템 분류입니다. 

**사용자 지정 분류:** 기본 시스템 분류로 사용할 수 없는 패턴 또는 특정 열 이름을 기반으로 자산을 분류하려는 경우 사용자 지정 분류를 만들 수 있습니다. 사용자 지정 분류 규칙은 **정규식** 패턴 또는 사전을 기반으로 할 수 **있습니다.** 

예를 들어 직원 ID 열이 EMPLOYEE{GUID} 패턴(예: EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55)을 따르는 경우 과 같은 정규식을 사용하여 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` 사용자 지정 분류를 만들 수 있습니다.


> [!NOTE]
> 민감도 레이블은 분류와 다릅니다. 민감도 레이블은 극비, 제한된, 공용 등과 같은 데이터 보안 및 개인 정보 보호의 컨텍스트에서 분류됩니다. Azure Purview에서 민감도 레이블을 사용하려면 Azure Purview 계정과 동일한 azure AD(Azure Active Directory) 테넌트 내에서 하나 이상의 Microsoft 365 라이선스/계정이 필요합니다. 자세한 내용은 둘 사이의 [차이점을](sensitivity-labels-frequently-asked-questions.yml#what-is-the-difference-between-classifications-and-sensitivity-labels-in-azure-purview) 읽어 읽어 들이십시오.

## <a name="classification-best-practice-and-considerations"></a>분류 모범 사례 및 고려 사항

### <a name="considerations"></a>고려 사항

* Purview에서 분류는 자산 수준 및/또는 열 수준에서 할당할 수 있으며, 검사 규칙에 관련 분류를 자동으로 포함하거나 Purview에 메타데이터를 받은 후 수동으로 할당할 수 있습니다.
* 자동 할당의 경우 [지원되는 데이터 원본 유형 을 검토합니다.](./purview-connector-overview.md)
* Azure Purview에서 데이터 원본을 검사하기 전에 데이터를 이해하고 적절한 검사 규칙 집합(관련 시스템 분류, 사용자 지정 분류 또는 둘의 조합 선택)을 구성하여 데이터 자산을 분류하는 것이 중요합니다. 이는 검색 성능에 영향을 줄 수 있습니다. 자세한 내용은 [지원되는 분류를](./supported-classifications.md) 참조하세요.
* 검사 전에 자산에 적용해야 하는 분류를 결정하려면 분류를 사용하는 방법의 사용 사례를 고려합니다. 불필요한 분류 레이블은 노이즈나 데이터 소비자에 대해 오해의 불가로 보일 수 있습니다. 아래 측면을 고려하여 결정합니다. 예를 들면 다음과 같습니다.
    * 분류는 검사 중인 데이터 자산 또는 스키마에 존재하는 데이터의 특성을 설명하는 데 사용할 수 있습니다. 즉, 분류를 사용하면 고객이 카탈로그를 검색하는 동안 분류 레이블에서 데이터 자산 또는 스키마의 콘텐츠를 식별할 수 있습니다.
    * 분류는 우선 순위를 설정하고 조직의 보안 및 규정 준수 요구 사항을 달성하기 위한 계획을 개발하는 데 사용할 수 있습니다. 
    * 분류를 사용하여 데이터 준비 프로세스의 단계(원시 영역, 랜딩 존 등)를 설명하고 특정 자산에 분류를 할당하여 프로세스의 단계를 표시할 수 있습니다.

* Purview 스캐너는 시스템 및 사용자 지정 분류 모두에 대해 심도 있는 검사(분류에 따라 다행임)에 데이터 샘플링 규칙을 적용합니다. 샘플링 규칙은 데이터 원본의 유형을 기반으로 합니다. 자세한 내용은 [이](./sources-and-scans.md#sampling-within-a-file) 문서를 참조하세요. 
* 샘플링 규칙은 리소스 집합에도 적용됩니다. 자세한 내용은 [여기](./sources-and-scans.md#resource-set-file-sampling)를 참조하세요.
* 사용자 지정 분류는 사용자 지정 분류 규칙을 사용하여 문서 유형 자산에 적용할 수 없습니다. 이러한 형식에 대한 분류는 수동으로만 적용할 수 있습니다.
* 사용자 지정 분류는 기본 검사 규칙에 포함되지 않으므로 사용자 지정 분류의 자동 할당이 필요한 경우 사용자 지정 분류를 포함한 사용자 지정 검사 규칙을 배포하고 사용하여 검사를 실행해야 합니다.
* Purview Studio에서 분류를 수동으로 적용하는 경우 이러한 분류는 후속 검사에서 유지됩니다. 
* 분류 규칙을 적용할 수 없는 경우에도 이전에 검색된 경우 후속 검사는 자산에서 분류를 제거하지 않습니다.
* **암호화된 원본** 데이터 자산의 경우 Purview는 구조적 파일 형식 및 데이터베이스 테이블에 대한 파일 이름, 정규화된 이름 및 스키마 세부 정보만 선택합니다. 분류가 작동하려면 검사를 실행하기 전에 암호화된 데이터의 암호를 해독합니다. 

### <a name="best-practices"></a>모범 사례

#### <a name="scan-rule-set"></a>검사 규칙 집합:

* 검사 규칙 집합은 검사할 파일 형식과 검사하는 데이터 자산에 적용할 분류 규칙을 결정합니다. 검사 중인 데이터 원본에 대한 적절한 검사 규칙 집합을 선택/만들고 구성하는 것이 좋습니다. 
* 분류와 관련된 관련 시스템 분류 및/또는 사용자 지정 분류(검사 중인 데이터에 대해 분류를 만든 경우)를 선택합니다. 

예를 들어 아래 시나리오에서는 검사 중인 데이터 원본(예: 재무 데이터)에 대해 선택한 특정 시스템 및 사용자 지정 분류만 적용됩니다.
    
:::image type="content" source="./media/concept-best-practices/classification-select-classification-rules-example-3.png" alt-text="선택한 분류 규칙을 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-select-classification-rules-example-3.png":::

#### <a name="annotation-management"></a>주석 관리:

* 적용할 분류를 결정하는 동안 다음을 권장합니다.
    * 데이터 맵>**주석 관리>분류 블레이드로** 시작하고 
    * 검사 중인 데이터 자산에 적용할 사용 가능한 시스템 분류를 검토합니다. 시스템 분류의 정식 이름에는 MICROSOFT* 접두사
 
    :::image type="content" source="./media/concept-best-practices/classification-classification-example-4.png" alt-text="분류를 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-classification-example-4.png":::

    * 필요한 경우 이 블레이드에서 먼저 사용자 지정 분류 이름을 만든 **다음, 데이터 맵>주석 관리>분류 규칙으로** 이동하고 이전 단계에서 만든 사용자 지정 분류 이름에 대한 분류 규칙을 만듭니다.

    :::image type="content" source="./media/concept-best-practices/classification-classification-rules-example-2.png" alt-text="분류 규칙을 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-classification-rules-example-2.png":::

#### <a name="custom-classification"></a>사용자 지정 분류:

* 사용 가능한 시스템 분류가 요구 사항을 충족하지 않는 경우에만 사용자 지정 분류를 만듭니다.
* 사용자 지정 분류의 **이름에는** 네임스페이스 규칙(예: . . )을 사용하는 것이 **\<company name> 좋습니다. \<business unit> \<custom classification name>** 

예를 들어 Contoso라는 회사의 사용자 지정 EMPLOYEE_ID 분류의 경우 사용자 지정 분류의 이름은 CONTOSO.HR. EMPLOYEE_ID 및 친숙한 이름은 시스템에 HR로 저장됩니다. 직원 ID입니다.
    
:::image type="content" source="./media/concept-best-practices/classification-custom-classification-example-5.png" alt-text="EMPLOYEE_ID 사용자 지정 분류를 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-custom-classification-example-5.png":::

* 사용자 지정 분류에 대한 분류 규칙을 만들고 구성하는 동안:

    * 분류 **규칙을** 만들 적절한 분류 **이름을** 선택합니다.

    * Purview는 사용자 지정 분류 규칙에 대해 정규식 및 사전의 두 가지 방법을 지원합니다. 

    * 분류 규칙에 적합한 방법을 선택합니다.
        
        * 제네릭 패턴을 통해 데이터 채우기를 표현할 수 있는 경우 정규식(regex) 메서드를 사용합니다. 
        * 사전 파일의 값 목록이 분류할 데이터의 가능한 모든 값을 나타내는 경우에만 **Dictionary** 메서드를 사용합니다(향후 값도 고려).
 
            :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-example-6.png" alt-text="정규식 및 사전의 설정을 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-custom-classification-rule-example-6.png":::

* **regex 메서드의 경우:**

    * 분류할 데이터에 대한 regex 패턴을 구성합니다. regex 패턴이 분류되는 데이터를 처리할 만큼 충분히 일반적인지 확인합니다.

    * Purview는 제안된 regex 패턴을 생성하는 기능도 제공합니다. 샘플 데이터 파일을 업로드한 후 제안된 패턴 중 하나를 선택하고 **패턴에 추가를** 선택하여 제안된 데이터 및 열 패턴을 사용합니다. 제안된 패턴을 수정하거나 파일을 업로드하지 않고 사용자 고유의 패턴을 입력할 수도 있습니다.

    * 가양성을 최소화하기 위해 열을 분류하도록 열 이름 패턴을 추가로 구성할 수도 있습니다.

    * 데이터 패턴과 일치하는 데이터에 허용되는 "최소 일치 임계값" 매개 변수를 구성하여 분류를 적용합니다. 임계값은 1%에서 100% 사이일 수 있습니다. 일반적으로 가양성을 방지하기 위해 임계값으로 60% 이상을 사용하는 것이 좋습니다. 그러나 특정 분류 시나리오에 대해 필요에 따라 구성할 수 있습니다(예: 패턴과 일치하는 경우 데이터의 모든 값에 대한 분류를 검색하고 적용하려는 경우 1%만큼 낮을 수 있음). 
  
        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-regular-expressions-example-7.png" alt-text="사용자 지정 분류 규칙의 regex 메서드를 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-custom-classification-rule-regular-expressions-example-7.png":::

    * 분류 규칙에 데이터 패턴이 두 개 이상 추가되면 최소 일치 규칙을 설정하는 옵션이 자동으로 비활성화됩니다.

    * "테스트 **분류** 규칙"을 사용하고 샘플 데이터로 테스트하여 분류 규칙이 예상대로 작동하는지 확인합니다. 샘플 데이터(예: .csv 파일)에 분류를 적용할 열을 포함하여 3개 이상의 열이 있는지 확인합니다. 테스트에 성공하면 열에 분류 레이블이 표시됩니다(아래 예제).
   
        :::image type="content" source="./media/concept-best-practices/classification-test-classification-rule-example-8.png" alt-text="테스트 분류가 성공하는 경우 분류를 보여주는 스크린샷." lightbox="./media/concept-best-practices/classification-test-classification-rule-example-8.png":::

* **Dictionary 메서드의 경우:**

    * 사전 메서드는 열거형 데이터를 맞추거나 사전(가능한 값 목록)을 사용할 수 있는 경우 사용할 수 있습니다.

    * 파일 크기 제한이 30MB인 .csv 또는 .tsv 파일을 지원합니다.

#### <a name="custom-classification-archetypes"></a>사용자 지정 분류 아키타입

* **정규식에서 "threshold" 매개 변수가 작동하는 방식**

    * 데이터 패턴 "N{Digit}{Digit}{Digit}AN"에 대해 'Sample_col1, Sample_col2, Sample_col3' 열에 5개의 열과 사용자 지정 분류 규칙을 적용해야 하는 예제 원본 데이터를 예로 들어 보겠습니다.
    
        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-example-source-data-9.png" alt-text="예제 소스 데이터를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-example-source-data-9.png":::

    * 사용자 지정 분류의 이름은 ' NDDDAN '로 지정 됩니다.

    * 분류 규칙 (데이터 패턴의 regex)은 ^ N [0-9] $입니다. {3}
     
        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-ndddan-10.png" alt-text="사용자 지정 분류 규칙을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-ndddan-10.png":::

    * 임계값은 "^ N [0-9] {3} $" 패턴:
     
        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-threshold-11.png" alt-text="사용자 지정 분류 규칙의 임계값을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-threshold-11.png":::

    * 임계값이 55% 이면 Sample_col1와 Sample_col2만 분류 됩니다. 위와 같이 55% 임계값 조건을 충족 하지 않으므로 Sample_col3 분류 되지 않습니다.
      
        :::image type="content" source="./media/concept-best-practices/classification-test-custom-classification-rule-12.png" alt-text="높은 임계값 조건의 결과를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-test-custom-classification-rule-12.png":::

* **데이터 및 열 패턴을 모두 사용 하는 방법**

    * 지정 된 샘플 데이터의 경우 열 B와 열 C는 모두 유사한 데이터 패턴을 가지 며, 데이터 패턴 "^ P [0-9] {3} [a-z] $"를 기반으로 b 열을 분류 하려는 경우에 사용 {2} 합니다. 
       
        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-sample-data-13.png" alt-text="예제 데이터를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-sample-data-13.png":::

    * 열 패턴을 데이터 패턴과 함께 사용 하 여 "제품 ID" 열만 분류 되도록 합니다.

        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-14.png" alt-text="분류 규칙을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-14.png":::

        > [!NOTE]
        > 열 패턴은 데이터 패턴이 있는 "AND" 조건으로 확인 됩니다.
    
    * "테스트 분류 규칙"을 사용 하 고 샘플 데이터를 사용 하 여 테스트 하 여 분류 규칙이 예상 대로 작동 하는지 확인 합니다.

        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-column-pattern-15.png" alt-text="열 패턴을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-column-pattern-15.png":::

*   **여러 열 패턴을 사용 하는 방법**

    동일한 분류 규칙에 대해 분류할 열 패턴이 여러 개 있는 경우에는 파이프 (|)의 구분 된 열 이름을 사용 합니다. 예를 들어 Product ID, Product_ID, ProductID 등의 열이 있습니다. 아래에 표시 된 대로를 사용 합니다.
        
    :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-multiple-column-patterns-16.png" alt-text="여러 열 패턴을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-multiple-column-patterns-16.png":::

    자세한 내용은 [regex 교체 구문](/dotnet/standard/base-types/regular-expression-language-quick-reference#alternation-constructs) 을 참조 하세요.

#### <a name="manually-applyingediting-classification-from-purview-studio"></a>부서의 범위 Studio에서 수동으로 분류를 적용 하거나 편집 합니다.

* 부서의 범위 Studio의 자산 및 스키마 수준에서 분류 레이블을 수동으로 편집 하 고 업데이트할 수 있습니다. 그러나 스키마 수준에서 분류를 수동으로 적용 하면 이후 검색에서 업데이트 되지 않습니다.
         
    :::image type="content" source="./media/concept-best-practices/classification-update-classification-17.png" alt-text="업데이트 분류를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-update-classification-17.png":::
          
    :::image type="content" source="./media/concept-best-practices/classification-update-classification-18.png" alt-text="열 분류 업데이트를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-update-classification-18.png":::

* 부서의 범위를 사용 하면 데이터 자산에 적용 되는 분류를 제거 하는 옵션과 함께 사용자 지정 분류 규칙을 삭제할 수 있습니다 (아래 예제 참조).
           
    :::image type="content" source="./media/concept-best-practices/classification-delete-classification-rule-19.png" alt-text="삭제 분류 규칙을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-delete-classification-rule-19.png":::

* 부서의 범위를 사용 하면 부서의 범위 Studio를 통해 분류를 대량으로 편집할 수도 있습니다. 자세한 내용은 [여기](how-to-bulk-edit-assets.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [시스템 분류 적용](./apply-classifications.md)
- [사용자 지정 분류 만들기](./create-a-custom-classification-and-classification-rule.md)


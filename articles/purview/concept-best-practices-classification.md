---
title: Azure 부서의 범위 분류 모범 사례
description: 이 문서에서는 Azure 부서의 범위에서 분류를 위한 모범 사례를 제공 합니다.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 0875e09c311324ba05d17e51995dd0289d21e74b
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289587"
---
# <a name="azure-purview-classification-best-practices"></a>Azure 부서의 범위 분류 모범 사례

Azure 부서의 범위의 컨텍스트에서 데이터 분류는 데이터의 비즈니스 컨텍스트 (예: Passport 번호, 운전 면허 번호, 신용 카드 번호, SWIFT 코드, 사람 이름 등)를 기반으로 데이터 자산에 고유한 논리적 레이블 또는 클래스를 할당 하 여 데이터 자산을 분류 하는 방법입니다. 데이터 자산이 분류 되 면 잘 이해 하 고 검색 하 고 관리할 수 있습니다. 가장 중요 한 점은 데이터와 관련 된 위험을 이해 하 고 데이터 공간에 대 한 비관리 확산 및 무단 액세스 로부터 중요 한 데이터 나 중요 한 데이터를 보호 하기 위한 조치를 구현 하는 것입니다. 
 
Azure 부서의 범위는 데이터 원본을 검색 하는 동안 자동화 된 분류 기능을 제공 하 여 데이터에 대 한 사용자 지정 분류를 만드는 기능 뿐만 아니라 200 개 이상의 시스템 기본 제공 분류를 제공 합니다. 분류는 자동으로 (검색의 일부로 구성 된 경우) 자산에 적용 하거나 부서의 범위 Studio에서 수동으로 편집 하 여 검색 및 수집을 게시할 수 있습니다.  
 
## <a name="intended-audience"></a>대상 그룹

* 데이터 엔지니어 
* 도메인 소유자 
* 비즈니스 소유자
* 데이터 소비자
* 데이터 소유자 및 관리자

## <a name="why-do-you-need-to-adopt-classification"></a>분류를 채택 해야 하는 이유는 무엇 인가요? 

분류는 나중에 사용 하기 위해 데이터를 검색, 정렬 및 식별 하기 쉽도록 **논리적 범주로** 구성 하는 프로세스입니다. 이는 데이터 거 버 넌 스의 특정 중요할 수 있습니다. 분류가 중요 한 이유는 여러 가지가 있으며, 다음은 가장 일반적인 이유입니다. 
* 관심이 있는 데이터 자산에 대 한 검색 범위를 좁혀 지원 합니다.
* 조직에서 중요 한 여러 데이터 클래스를 구성 하 고 이해 하는 데 도움이 됩니다.
* 중요 한 데이터 자산과 관련 된 위험을 이해 하 고 위험을 완화 하기 위한 적절 한 조치를 사용 하는 데 도움이 됩니다.

아래 시나리오에서는 Azure SQL Database의 *Customers* 테이블에 대 한 자산 수준과 스키마 수준 모두에서 분류를 적용할 수 있습니다. 

:::image type="content" source="./media/concept-best-practices/classification-customers-example-1.png" alt-text="Azure SQL Database 분류를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-customers-example-1.png":::

**시스템 분류**: Azure 부서의 범위는 기본적으로 많은 시스템 분류 집합을 지원 합니다. 사용 가능한 시스템 분류의 전체 목록은 [Azure Purview에서 지원되는 분류](./supported-classifications.md)를 참조하세요. 

위의 예에서는 사용자의 이름이 시스템 분류입니다. 

**사용자 지정 분류**: 기본 시스템 분류로 사용할 수 없는 패턴 또는 특정 열 이름에 따라 자산을 분류 하려는 경우 사용자 지정 분류를 만들 수 있습니다. 사용자 지정 분류 규칙은 **정규식** 패턴 또는 **사전을** 기반으로 할 수 있습니다. 

예를 들어 employee ID 열이 EMPLOYEE {GUID} 패턴 (예: EMPLOYEE9c55c474-9996-420c-a285)을 따르는 경우이 경우와 같은 정규식을 사용 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` 하 여 사용자 지정 분류를 만들 수 있습니다.


> [!NOTE]
> 민감도 레이블은 분류와 다릅니다. 민감도 레이블은 데이터 보안 및 개인 정보 (예: 매우 기밀, 제한, 공용 등)의 컨텍스트에서 분류 됩니다. azure 부서의 범위에서 민감도 레이블을 사용 하려면 azure 부서의 범위 계정과 동일한 Azure Active Directory (azure AD) 테 넌 트 내에 하나 이상의 Microsoft 365 라이선스/계정이 필요 합니다. 자세한 내용은 둘 간의 [차이점](sensitivity-labels-frequently-asked-questions.yml#what-is-the-difference-between-classifications-and-sensitivity-labels-in-azure-purview) 을 참조 하세요.

## <a name="classification-best-practice-and-considerations"></a>분류 모범 사례 및 고려 사항

### <a name="considerations"></a>고려 사항

* 부서의 범위에서 분류는 자동으로 자산 수준 및/또는 열 수준에서 할당 될 수 있습니다. 즉, 검색 규칙에 관련 분류를 포함 하거나 메타 데이터를 부서의 범위로 수집 후 수동으로 분류 합니다.
* 자동 할당의 경우 [지원 되는 데이터 원본 유형을](./purview-connector-overview.md)검토 합니다.
* Azure 부서의 범위에서 데이터 원본을 검색 하기 전에 데이터를 이해 하 고, 검색 성능에 영향을 줄 수 있는 적절 한 검색 규칙 집합 (관련 시스템 분류, 사용자 지정 분류 또는 둘의 조합 선택)을 구성 하 여 데이터 자산을 분류 해야 합니다. 자세한 내용은 [지원 되는 분류](./supported-classifications.md) 를 참조 하세요.
* 검색 하기 전에 자산에 적용 해야 하는 분류를 결정 하려면 분류 사용 사례를 고려 합니다. 불필요 한 분류 레이블은 데이터 소비자의 경우에도 잡음이 나 잘못 될 수 있습니다. 예를 들어 아래 사항을 결정 하는 것이 좋습니다.
    * 분류를 사용 하 여 데이터 자산이 나 검색 중인 스키마에 존재 하는 데이터의 특성을 설명할 수 있습니다. 즉, 분류를 통해 고객은 카탈로그를 검색 하는 동안 분류 레이블에서 데이터 자산 또는 스키마의 콘텐츠를 식별할 수 있습니다.
    * 등급을 사용 하면 조직의 보안 및 규정 준수 요구를 충족 하는 계획을 수립 하 고 우선 순위를 설정할 수 있습니다. 
    * 분류를 사용 하 여 데이터 준비 프로세스 (raw 영역, 방문 영역 등)의 단계를 설명 하 고 분류를 특정 자산에 할당 하 여 프로세스의 단계를 표시할 수 있습니다.

* 부서의 범위 스캐너는 시스템 및 사용자 지정 분류에 대 한 심층 검색 (분류에 적용 됨)에 대 한 데이터 샘플링 규칙을 적용 합니다. 샘플링 규칙은 데이터 원본의 유형을 기반으로 합니다. 자세한 내용은 [이](./sources-and-scans.md#sampling-within-a-file) 문서를 참조하세요. 
* 샘플링 규칙도 리소스 집합에도 적용 됩니다. 자세한 내용은 [여기](./sources-and-scans.md#resource-set-file-sampling)를 참조하세요.
* 사용자 지정 분류 규칙을 사용 하는 문서 유형 자산에는 사용자 지정 분류를 적용할 수 없습니다. 이러한 형식의 분류는 수동으로 적용할 수 있습니다.
* 사용자 지정 분류는 기본 검색 규칙에 포함 되지 않으므로 사용자 지정 분류의 자동 할당이 필요한 경우 검색을 실행 하려면 사용자 지정 분류를 포함 하 여 사용자 지정 검색 규칙을 배포 하 고 사용 해야 합니다.
* 부서의 범위 Studio에서 분류를 수동으로 적용 하는 경우 이러한 분류는 후속 검색에서 유지 됩니다. 
* 이후 검색은 분류 규칙이 적용 되지 않는 경우에도 이전에 검색 된 경우 자산에서 분류를 제거 하지 않습니다.
* **암호화 된 원본** 데이터 자산의 경우 부서의 범위는 구조화 된 파일 형식 및 데이터베이스 테이블에 대 한 파일 이름, 정규화 된 이름 및 스키마 정보만 선택 합니다. 분류가 작동 하려면 검색을 실행 하기 전에 암호화 된 데이터의 암호를 해독 합니다. 

### <a name="best-practices"></a>모범 사례

#### <a name="scan-rule-set"></a>검색 규칙 집합:

* 검색 규칙 집합은 검색 하는 파일 형식을 결정 하 고 검색 하는 데이터 자산에 적용할 분류 규칙을 결정 합니다. 검색 되는 데이터 원본에 적합 한 검색 규칙 집합을 선택/만들고 구성 하는 것이 좋습니다. 
* 분류와 관련 하 여 관련 시스템 분류 및/또는 사용자 지정 분류를 선택 합니다 (검색 중인 데이터에 대해 하나를 만든 경우). 

예를 들어 아래 시나리오에서는 검색 중인 데이터 원본 (예: 재무 데이터)에 대해 선택한 특정 시스템 및 사용자 지정 분류만 적용 됩니다.
    
:::image type="content" source="./media/concept-best-practices/classification-select-classification-rules-example-3.png" alt-text="선택한 분류 규칙을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-select-classification-rules-example-3.png":::

#### <a name="annotation-management"></a>주석 관리:

* 적용할 분류를 결정 하는 동안 다음을 수행 하는 것이 좋습니다.
    * **주석 관리>분류 블레이드>데이터 맵으로** 시작 
    * 검색 되는 데이터 자산에 적용할 사용 가능한 시스템 분류를 검토 합니다. 시스템 분류의 정식 이름에 MICROSOFT * 접두사가 있습니다.
 
    :::image type="content" source="./media/concept-best-practices/classification-classification-example-4.png" alt-text="분류를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-classification-example-4.png":::

    * 필요한 경우 먼저이 블레이드에서 사용자 지정 분류 이름을 만든 후 데이터 맵으로 이동 하 여 **분류 규칙>>** 하 고 이전 단계에서 만든 사용자 지정 분류 이름에 대 한 분류 규칙을 만듭니다.

    :::image type="content" source="./media/concept-best-practices/classification-classification-rules-example-2.png" alt-text="분류 규칙을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-classification-rules-example-2.png":::

#### <a name="custom-classification"></a>사용자 지정 분류:

* 사용 가능한 시스템 분류가 사용자의 요구에 맞지 않는 경우에만 사용자 지정 분류를 만듭니다.
* 사용자 지정 분류의 **이름** 에는 네임 스페이스 규칙을 사용 하는 것이 **좋습니다. 즉, ... \<company name> \<business unit> \<custom classification name>** 

예를 들어 Contoso 라는 회사에 대 한 사용자 지정 EMPLOYEE_ID 분류의 경우 사용자 지정 분류의 이름이 CONTOSO.HR 됩니다. EMPLOYEE_ID 및 이름은 시스템에 HR로 저장 됩니다. 직원 ID입니다.
    
:::image type="content" source="./media/concept-best-practices/classification-custom-classification-example-5.png" alt-text="EMPLOYEE_ID 사용자 지정 분류를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-example-5.png":::

* 사용자 지정 분류에 대 한 분류 규칙을 만들고 구성 하는 동안:

    * 분류 **규칙** 을 만들 적절 한 분류 **이름을** 선택 합니다.

    * 부서의 범위는 사용자 지정 분류 규칙에 대 한 두 가지 방법, 즉 정규식 및 사전을 지원 합니다. 

    * 분류 규칙에 적절 한 방법 선택:
        
        * 제네릭 패턴을 통해 데이터 채우기를 표현할 수 있는 경우 **정규식** (regex) 메서드를 사용 합니다.
        * 사전 파일의 값 목록이 분류할 데이터의 가능한 모든 값을 나타내는 경우에만 **사전** 메서드를 사용 합니다 (미래 값도 고려).
 
            :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-example-6.png" alt-text="정규식 및 사전의 설정을 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-example-6.png":::

* **Regex 메서드의 경우**:

    * 분류할 데이터의 regex 패턴을 구성 합니다. Regex 패턴이 분류 되는 데이터를 처리할 수 있을 정도로 제네릭 인지 확인 합니다.

    * 또한 부서의 범위는 제안 된 regex 패턴을 생성 하는 기능을 제공 합니다. 샘플 데이터 파일을 업로드 한 후 제안 된 패턴 중 하나를 선택 하 고 **패턴에 추가** 를 선택 하 여 제안 된 데이터 및 열 패턴을 사용 합니다. 제안 된 패턴을 수정 하거나 파일을 업로드 하지 않고 사용자 고유의 패턴을 입력할 수도 있습니다.

    * 또한 열 이름 패턴을 구성 하 여 가양성을 최소화 하도록 열을 분류할 수 있습니다.

    * 분류를 적용 하기 위해 데이터 패턴과 일치 하는 데이터에 사용할 수 있는 "최소 일치 임계값" 매개 변수를 구성 합니다. 임계값은 1%에서 100% 사이가 될 수 있습니다. 일반적으로 거짓 긍정을 방지 하기 위해 임계값으로 최소 60%를 사용 하는 것이 좋습니다. 그러나 특정 분류 시나리오에 필요에 따라 구성할 수 있습니다. 예를 들어 패턴과 일치 하는 경우 데이터의 모든 값에 대 한 분류를 검색 하 고 적용 하려는 경우에는 1%가 될 수 있습니다. 
  
        :::image type="content" source="./media/concept-best-practices/classification-custom-classification-rule-regular-expressions-example-7.png" alt-text="사용자 지정 분류 규칙의 regex 메서드를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-custom-classification-rule-regular-expressions-example-7.png":::

    * 두 개 이상의 데이터 패턴이 분류 규칙에 추가 되 면 최소 일치 규칙을 설정 하는 옵션이 자동으로 사용 하지 않도록 설정 됩니다.

    * "**테스트 분류 규칙**"을 사용 하 고 샘플 데이터를 사용 하 여 테스트 하 여 분류 규칙이 예상 대로 작동 하는지 확인 합니다. 예제 데이터 (예: .csv 파일)에서 분류가 적용 될 열을 포함 하 여 3 개 이상의 열이 있는지 확인 합니다. 테스트에 성공 하면 열에 분류 레이블이 표시 됩니다 (아래 예제 참조).
   
        :::image type="content" source="./media/concept-best-practices/classification-test-classification-rule-example-8.png" alt-text="테스트 분류가 성공 했을 때 분류를 보여 주는 스크린샷" lightbox="./media/concept-best-practices/classification-test-classification-rule-example-8.png":::

* **사전 방법**:

    * 사전 메서드를 사용 하 여 열거형 데이터를 맞추거나, 사전 (가능한 값 목록)을 사용할 수 있는 경우 사용할 수 있습니다.

    * 파일 크기가 30MB로 제한 된 .csv 또는. tsv 파일을 지원 합니다.

#### <a name="custom-classification-archetypes"></a>사용자 지정 분류 Archetype

* **정규식에서 "threshold" 매개 변수는 어떻게 작동 하나요?**

    * 예를 들어 아래 샘플 원본 데이터를 고려 합니다. 예를 들어 5 개의 열이 있고 사용자 지정 분류 규칙을 ' N {Digit} {Digit} {Digit} AN 데이터 패턴의 ' Sample_col1, Sample_col2 Sample_col3 ' 열에 적용 해야 합니다.
    
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


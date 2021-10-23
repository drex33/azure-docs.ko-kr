---
title: 복사 마법사를 사용하여 데이터를 쉽게 복사 - Azure
description: Data Factory 복사 마법사를 사용하여 지원되는 데이터 소스의 데이터를 싱크로 복사하는 방법에 대해 알아보세요.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8bf7648dbb1de372302315780e6df45604327afa
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243812"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Azure Data Factory 복사 마법사를 사용하여 데이터를 쉽게 복사 또는 이동
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 


Azure Data Factory Copy Wizard는 일반적으로 엔드투엔드 데이터 통합 시나리오의 첫 번째 단계인 데이터 수집 프로세스를 용이하게 합니다. Azure Data Factory Copy Wizard를 진행할 때는 연결된 서비스, 데이터 세트 및 파이프라인에 대한 JSON 정의를 이해할 필요가 없습니다. 그러나 마법사의 모든 단계를 완료한 후 선택한 데이터 원본에서 선택한 대상으로 데이터를 복사하는 파이프라인이 자동으로 생성됩니다. 또한 Copy Wizard를 사용하면 작성 당시에 수집 중인 데이터의 유효성을 검사할 수 있으므로 특히 데이터 원본에서 데이터를 처음 수집하는 경우 많은 시간이 절약됩니다. 복사 마법사를 시작하려면 Data Factory 홈 페이지에서 **데이터 복사** 타일을 클릭합니다.

:::image type="content" source="./media/data-factory-copy-wizard/copy-data-wizard.png" alt-text="복사 마법사":::

## <a name="an-intuitive-wizard-for-copying-data"></a>데이터를 복사할 수 있는 직관적인 마법사
이 마법사를 사용하면 다양한 원본에서 대상으로 데이터를 몇 분만에 쉽게 이동할 수 있습니다. 마법사를 거친 후 종속 Data Factory 엔터티(연결된 서비스 및 데이터 세트)와 함께 복사 작업이 있는 파이프라인이 자동으로 생성됩니다. 파이프라인을 만드는 데 필요한 추가 단계는 없습니다.   

:::image type="content" source="./media/data-factory-copy-wizard/select-data-source-page.png" alt-text="데이터 원본 선택":::

> [!NOTE]
> Azure Blob에서 Azure SQL Database 테이블로 데이터를 복사하기 위한 샘플 파이프라인을 만드는 단계별 지침은 [Copy Wizard 자습서](data-factory-copy-data-wizard-tutorial.md) 문서를 참조하세요. 
> 
> 

이 마법사는 처음부터 빅 데이터를 염두에 두고 설계되었습니다. Copy Data Wizard를 사용하여 수백 개의 폴더, 파일 또는 테이블을 이동하는 Data Factory 파이프라인을 간단하고 효율적으로 만들 수 있습니다. 이 마법사는 자동 데이터 미리 보기, 스키마 캡처 및 매핑, 데이터 필터링이라는 세 가지 기능을 지원합니다. 

## <a name="automatic-data-preview"></a>자동 데이터 미리 보기
복사 마법사를 사용하면 선택한 데이터 원본에서 데이터의 일부를 검토하여 데이터가 복사하려는 올바른 데이터인지 여부를 확인할 수 있습니다. 또한 원본 데이터가 텍스트 파일에 있는 경우 복사 마법사는 텍스트 파일을 구문 분석하여 행 및 열 구분 기호와 스키마를 자동으로 파악합니다. 

:::image type="content" source="./media/data-factory-copy-wizard/file-format-settings.png" alt-text="파일 형식 설정":::

## <a name="schema-capture-and-mapping"></a>스키마 캡처 및 매핑
입력 데이터의 스키마는 경우에 따라 출력 데이터의 스키마와 일치하지 않을 수 있습니다. 이 시나리오에서는 원본 스키마의 열을 대상 스키마의 열에 매핑해야 합니다. 

복사 마법사는 원본 스키마의 열을 대상 스키마의 열에 자동으로 매핑합니다. 드롭다운 목록을 사용하여 매핑을 재정의하거나 데이터를 복사하는 동안 열을 건너뛸지 여부를 지정할 수 있습니다.   

:::image type="content" source="./media/data-factory-copy-wizard/schema-mapping.png" alt-text="스키마 매핑":::

## <a name="filtering-data"></a>데이터 필터링
마법사를 통해 대상/싱크 데이터 저장소에 복사해야 하는 데이터만 선택하도록 원본 데이터를 필터링할 수 있습니다. 필터링하면 싱크 데이터 저장소에 복사할 데이터 양이 줄고 이에 따라 복사 작업의 처리량이 향상됩니다. 마법사에서는 SQL 쿼리 언어를 사용하여 관계형 데이터베이스의 데이터를 필터링하거나 [Data Factory 함수 및 변수](data-factory-functions-variables.md)를 사용하여 Azure blob 폴더의 파일을 필터링할 수 있는 유연한 방법을 제공합니다.   

### <a name="filtering-of-data-in-a-database"></a>데이터베이스 데이터 필터링
이 예제에서 SQL 쿼리는 `Text.Format` 함수 및 `WindowStart` 변수를 사용합니다. 

:::image type="content" source="./media/data-factory-copy-wizard/validate-expressions.png" alt-text="식 유효성 검사":::

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob 폴더의 데이터 필터링
폴더 경로의 변수를 사용하여 [시스템 변수](data-factory-functions-variables.md#data-factory-system-variables)를 기반으로 런타임 시 결정되는 폴더의 데이터를 복사할 수 있습니다. 지원되는 변수는 **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** 및 **{custom}** 입니다. 예: inputfolder/{year}/{month}/{day}.

다음과 같은 형식의 입력 폴더가 있다고 가정하겠습니다.

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

**파일 또는 폴더** 의 **찾아보기** 단추를 클릭하여 이러한 폴더(예: 2016->03->01->02)중 하나를 찾아서 **선택** 을 클릭합니다. 텍스트 상자에 `2016/03/01/02`가 표시됩니다. 이제 **2016** 을 **{year}** 로, **03** 을 **{month}** 로, **01** 을 **{Day}** 로, **02** 를 **{hour}** 로 바꾸고 Tab을 누릅니다. 다음 네 가지 변수의 형식을 선택하는 드롭다운 목록이 표시되어야 합니다.

:::image type="content" source="./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png" alt-text="시스템 변수 사용":::   

다음 스크린샷에 표시된 것처럼 **사용자 지정** 변수 및 모든 [지원되는 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)도 사용할 수 있습니다. 해당 구조의 폴더를 선택하려면 먼저 **찾아보기** 단추를 사용합니다. 그런 다음 값을 **{custom}** 으로 바꾸고, 탭을 누르면 형식 문자열을 입력할 수 있는 텍스트 상자가 표시됩니다.     

:::image type="content" source="./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png" alt-text="사용자 지정 변수 사용":::

## <a name="support-for-diverse-data-and-object-types"></a>다양한 데이터 및 개체 유형 지원
복사 마법사를 사용하여 효율적으로 수백 개의 폴더, 파일 또는 테이블을 이동할 수 있습니다.

:::image type="content" source="./media/data-factory-copy-wizard/select-tables-to-copy-data.png" alt-text="데이터를 복사할 테이블 선택":::

## <a name="scheduling-options"></a>일정 옵션
복사 작업을 한 번만 또는 일정에 따라(시간별, 일별, 등) 실행할 수 있습니다. 온-프레미스, 클라우드 및 로컬 데스크톱 복사에 걸쳐 광범위한 커넥터에 이 두 옵션을 모두 사용할 수 있습니다.

일 회 복사 작업을 통해 원본에서 대상으로 한 번만 데이터를 이동할 수 있습니다. 이는 모든 크기 및 지원되는 형식의 데이터에 적용됩니다. 예약 복사를 통해 미리 정해진 반복 주기에 따라 데이터를 복사할 수 있습니다. 다양한 설정(예: 재시도, 시간 제한, 경고 등)을 사용하여 예약 복사를 구성할 수 있습니다.

:::image type="content" source="./media/data-factory-copy-wizard/scheduling-properties.png" alt-text="일정 속성":::

## <a name="next-steps"></a>다음 단계
Data Factory 복사 마법사를 사용하여 복사 작업이 있는 파이프라인을 만드는 방법은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.
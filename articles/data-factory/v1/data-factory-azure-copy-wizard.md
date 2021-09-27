---
title: Data Factory Azure 복사 마법사
description: Data Factory Azure 복사 마법사를 사용하여 지원되는 데이터 소스의 데이터를 싱크로 복사하는 방법에 대해 알아보세요.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82ad8dbec999b1e65eb5b9d866eacaf95c8647d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649590"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory 복사 마법사

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 

Azure Data Factory 복사 마법사는 일반적으로 엔드투엔드 데이터 통합 시나리오의 첫 번째 단계인 데이터 수집 프로세스를 용이하게 합니다. Azure Data Factory 복사 마법사를 진행할 때는 연결된 서비스, 데이터 집합 및 파이프라인에 대한 JSON 정의를 이해할 필요가 없습니다. 선택한 데이터 원본에서 선택한 대상으로 데이터를 복사하는 파이프라인이 자동으로 만들어집니다. 또한 복사 마법사는 만들 때 수집된 데이터의 유효성을 검사하는 데 도움을 줍니다. 이렇게 하면 특히 데이터 원본에서 처음으로 데이터를 처리할 때 시간을 절약할 수 있습니다. 복사 마법사를 시작하려면 Data Factory 홈 페이지에서 **데이터 복사** 타일을 클릭합니다.

:::image type="content" source="./media/data-factory-copy-wizard/copy-data-wizard.png" alt-text="복사 마법사":::

## <a name="designed-for-big-data"></a>빅 데이터를 위한 설계
이 마법사를 사용하면 다양한 원본에서 대상으로 데이터를 몇 분만에 쉽게 이동할 수 있습니다. 마법사를 완료하면 종속 Data Factory 엔터티(연결된 서비스 및 데이터 집합)와 함께 복사 작업을 포함한 파이프라인이 자동으로 만들어집니다. 파이프라인을 만드는 데 필요한 추가 단계는 없습니다.   

:::image type="content" source="./media/data-factory-copy-wizard/select-data-source-page.png" alt-text="데이터 원본 선택":::

> [!NOTE]
> Azure Blob에서 Azure SQL Database 테이블로 데이터를 복사하기 위한 샘플 파이프라인을 만드는 단계별 지침은 [복사 마법사 자습서](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

마법사는 처음부터 빅 데이터를 염두에 두고 설계되었으며 다양한 데이터 및 개체 유형을 지원합니다. 수백 개의 폴더, 파일 또는 테이블을 이동하는 Data Factory 파이프라인을 만들 수 있습니다. 이 마법사는 자동 데이터 미리 보기, 스키마 캡처 및 매핑, 데이터 필터링을 지원합니다.

## <a name="automatic-data-preview"></a>자동 데이터 미리 보기
복사할 데이터인지 확인하기 위해 선택한 데이터 원본에서 데이터의 일부를 미리 볼 수 있습니다. 또한 원본 데이터가 텍스트 파일에 있는 경우 복사 마법사는 텍스트 파일을 구문 분석하여 행 및 열 구분 기호와 스키마를 자동으로 파악합니다.

:::image type="content" source="./media/data-factory-copy-wizard/file-format-settings.png" alt-text="파일 형식 설정":::

## <a name="schema-capture-and-mapping"></a>스키마 캡처 및 매핑
입력 데이터의 스키마는 경우에 따라 출력 데이터의 스키마와 일치하지 않을 수 있습니다. 이 시나리오에서는 원본 스키마의 열을 대상 스키마의 열에 매핑해야 합니다.

> [!TIP]
> SQL Server 또는 Azure SQL Database에서 Azure Synapse Analytics으로 데이터를 복사할 때, 테이블이 대상 저장소에 없을 경우 Data Factory는 원본의 스키마를 사용하여 자동 테이블 만들기를 지원합니다. [Azure Data Factory를 사용하여 Azure Synapse Analytics 간 데이터 이동](./data-factory-azure-sql-data-warehouse-connector.md)에서 자세한 정보를 알아봅니다.

드롭다운 목록을 사용하여 원본 스키마에서 대상 스키마의 열에 매핑할 열을 선택합니다. 복사 마법사는 열 매핑에 대한 패턴을 파악하려고 시도합니다. 나머지 열에도 동일한 패턴을 적용하므로 스키마 매핑을 완료하기 위해 각 열을 개별적으로 선택할 필요가 없습니다. 원하는 경우 드롭다운 목록을 사용하여 열을 하나씩 매핑하면 이러한 매핑을 무시할 수 있습니다. 열을 더 많이 매핑할수록 패턴이 더 정확하게 됩니다. 복사 마법사는 패턴을 지속적으로 업데이트하고, 궁극적으로는 도달하려는 열 매핑의 올바른 패턴에 도달합니다.     

:::image type="content" source="./media/data-factory-copy-wizard/schema-mapping.png" alt-text="스키마 매핑":::

## <a name="filtering-data"></a>데이터 필터링
싱크 데이터 저장소에 복사해야 하는 데이터만 선택하도록 원본 데이터를 필터링할 수 있습니다. 필터링하면 싱크 데이터 저장소에 복사할 데이터 양이 줄고 이에 따라 복사 작업의 처리량이 향상됩니다. 마법사에서는 SQL 쿼리 언어를 사용하여 관계형 데이터베이스의 데이터를 필터링하거나 [Data Factory 함수 및 변수](data-factory-functions-variables.md)를 사용하여 Azure Blob 폴더의 파일을 필터링할 수 있는 유연한 방법을 제공합니다.   

### <a name="filtering-of-data-in-a-database"></a>데이터베이스 데이터 필터링
다음 스크린샷은 `Text.Format` 함수와 `WindowStart` 변수를 사용하는 SQL 쿼리를 보여 줍니다.

:::image type="content" source="./media/data-factory-copy-wizard/validate-expressions.png" alt-text="식 유효성 검사":::

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob 폴더의 데이터 필터링
폴더 경로의 변수를 사용하여 [시스템 변수](data-factory-functions-variables.md#data-factory-system-variables)를 기반으로 런타임 시 결정되는 폴더의 데이터를 복사할 수 있습니다. 지원되는 변수는 **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** 및 **{custom}** 입니다. 예를 들어 inputfolder/{year}/{month}/{day}와 같습니다.

다음과 같은 형식의 입력 폴더가 있다고 가정하겠습니다.

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

**파일 또는 폴더** 의 **찾아보기** 단추를 클릭하여 이러한 폴더(예: 2016->03->01->02)중 하나를 찾아서 **선택** 을 클릭합니다. 텍스트 상자에 `2016/03/01/02`가 표시됩니다. 이제 **2016** 을 **{year}** 로, **03** 을 **{month}** 로, **01** 을 **{day}** 로, **02** 를 **{hour}** 로 바꾼 다음 **탭** 키를 누릅니다. 이러한 네 가지 변수의 형식을 선택하는 드롭다운 목록이 표시됩니다.

:::image type="content" source="./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png" alt-text="시스템 변수 사용":::   

다음 스크린샷에 표시된 것처럼 **사용자 지정** 변수 및 모든 [지원되는 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)도 사용할 수 있습니다. 해당 구조의 폴더를 선택하려면 먼저 **찾아보기** 단추를 사용합니다. 그런 다음 값을 **{custom}** 으로 바꾸고, **탭** 키를 누르면 형식 문자열을 입력할 수 있는 텍스트 상자가 표시됩니다.     

:::image type="content" source="./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png" alt-text="사용자 지정 변수 사용":::

## <a name="scheduling-options"></a>일정 옵션
복사 작업을 한 번만 또는 일정에 따라(시간별, 일별, 등) 실행할 수 있습니다. 이 두 가지 옵션은 온-프레미스, 클라우드 및 로컬 데스크톱 복사본을 포함한 환경의 다양한 커넥터에 모두 사용할 수 있습니다.

일 회 복사 작업을 통해 원본에서 대상으로 한 번만 데이터를 이동할 수 있습니다. 이는 모든 크기 및 지원되는 형식의 데이터에 적용됩니다. 예약 복사를 통해 미리 정해진 반복 주기에 따라 데이터를 복사할 수 있습니다. 다양한 설정(예: 재시도, 시간 제한, 경고 등)을 사용하여 예약 복사를 구성할 수 있습니다.

:::image type="content" source="./media/data-factory-copy-wizard/scheduling-properties.png" alt-text="일정 속성":::

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 Azure Data Factory의 복사 마법사에 대한 일반적인 문제 해결 방법을 살펴봅니다.

> [!NOTE] 
> 해당 문제 해결 팁은 Data Factory의 버전 1 복사 마법사에 적용됩니다. Data Factory v2의 경우 Azure Data Factory Studio 문제 해결 가이드를 [참조하세요.](../data-factory-ux-troubleshoot-guide.md)

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>오류 코드: 복사 마법사에서 유효성을 검사할 수 없음

- **증상**: 복사 마법사의 첫 번째 단계에서 “유효성을 검사할 수 없습니다”라는 경고 메시지가 표시됩니다.
- **원인**: 모든 타사 쿠키를 사용하지 않도록 설정하는 경우 발생할 수 있습니다.
- **해결 방법**: 
    - Internet Explorer 또는 Microsoft Edge 브라우저를 사용합니다.
    - Chrome 브라우저를 사용하는 경우 아래 지침에 따라 *microsoftonline.com* 및 *windows.net* 를 쿠키 예외에 추가합니다.
        1.  Chrome 브라우저를 엽니다.
        2.  오른쪽의 렌치 또는 세 줄을 클릭합니다(Google Chrome 사용자 지정 및 제어).
        3.  **설정** 을 클릭합니다.
        4.  **쿠키** 를 검색하거나 고급 설정에서 **개인 정보** 로 이동합니다.
        5.  **콘텐츠 설정** 을 선택합니다.    
        6.  쿠키를 **로컬 데이터 설정 허용(권장)** 으로 설정해야 합니다.
        7.  **예외 관리** 를 클릭합니다. **호스트 이름 패턴** 아래에 다음을 입력하고 설정된 동작이 **허용** 인지 확인합니다.
            - login.microsoftonline.com
            - login.windows.net
        8.  브라우저를 닫고 재설치합니다.
    - Firefox 브라우저를 사용하는 경우 아래 지침에 따라 쿠키 예외를 추가합니다.
        1. Firefox 메뉴에서 **도구** > **옵션** 으로 이동합니다.
        2. **개인 정보** > **기록** 에서, 현재 **사용자 지정 기록 설정 사용** 으로 설정된 것을 볼 수 있습니다.
        3. **타사 쿠키 수락** 에서 현재 설정이 **안 함** 인 경우 오른쪽의 **예외** 를 클릭하여 다음 사이트를 추가해야 합니다.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  브라우저를 닫고 재설치합니다. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>오류 코드: 로그인 페이지를 열고 암호를 입력할 수 없음

- **증상**: 복사 마법사는 로그인 페이지로 리디렉션되지만 로그인 페이지가 성공적으로 표시되지 않습니다.
- **원인**: 네트워크 환경을 사무실 네트워크에서 홈 네트워크로 변경한 경우 해당 문제가 발생할 수 있습니다. 브라우저에는 몇 가지 캐시가 있습니다. 
- **해결 방법**: 
    1.  브라우저를 닫고 다시 시도하세요. 여전히 문제가 있는 경우 다음 단계로 이동합니다.   
    2.  Internet Explorer 브라우저를 사용하는 경우 “Ctrl” + “Shift” + “P”를 눌러 프라이빗 모드로 엽니다. Chrome 브라우저를 사용하는 경우 “Ctrl” + “shift” + “N”을 눌러 시크릿 모드로 엽니다. 여전히 문제가 있는 경우 다음 단계로 이동합니다. 
    3.  다른 브라우저를 사용하세요. 


## <a name="next-steps"></a>다음 단계
Data Factory 복사 마법사를 사용하여 복사 작업이 있는 파이프라인을 만드는 방법은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.
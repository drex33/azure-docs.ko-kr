---
title: SAP R3의 메타데이터 추출 ABAP 함수 모듈 - Azure Purview
description: 이 문서에서는 SAP 서버에 ABAP 함수 모듈을 배포하는 단계를 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/07/2021
ms.openlocfilehash: 7894e187efedf91e3c586dd0d8caaf35e753be3c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028587"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>SAP R3 브리지 패밀리에 대한 메타데이터 추출 ABAP 함수 모듈을 배포

이 문서에서는 SAP 서버에 ABAP 함수 모듈을 배포하는 단계를 설명합니다.

## <a name="overview"></a>개요

Sap Business Suite 4 HANA(S/4HANA), ECC, R/3 ERP 브리지를 사용하여 SAP 서버에서 메타데이터를 추출할 수 있습니다. ABAP 함수 모듈을 SAP 서버에 배치하여 메타데이터를 추출할 수 있습니다. 해당 함수 모듈은 원격으로 연결하여 SAP 서버 내에 포함된 메타데이터를 쿼리하고 텍스트 파일로 다운로드할 수 있습니다.

실행되면 브리지는 다음 중 하나를 수행합니다.

1. 이전 브리지 실행에서 로컬로 이미 다운로드한 기존 파일에서 메타데이터를 가져옵니다.

2. ABAP 모듈 API를 호출하고 다운로드를 기다린 다음 해당 파일에서 메타데이터를 가져옵니다.

이 문서에서는 해당 모듈을 배포하는 데 필요한 단계에 대해 자세히 설명합니다.

> [!Note]
> 다음 지침은 SAP GUI v.7.2를 기반으로 컴파일되었습니다.

## <a name="prerequisites"></a>사전 요구 사항

부서의 범위 Studio에서 ABAP 함수 모듈 소스 코드를 다운로드 합니다. 소스를 등록 하 고 [SAP ECC](register-scan-sapecc-source.md) 또는 [SAP S/4hana](register-scan-saps4hana-source.md)에 대 한 새 검색을 수행 하는 경우 위에서 다음과 같이 다운로드 링크를 찾을 수 있습니다. 

:::image type="content" source="media/abap-functions-deployment-guide/download-abap-code.png" alt-text="부서의 범위 Studio에서 ABAP 함수 모듈 소스 코드 다운로드" border="true":::

## <a name="deployment-of-the-module"></a>모듈 배포

### <a name="create-a-package"></a>패키지 만들기

이 단계는 선택사항이며 기존 패키지를 사용할 수 있습니다.

1. SAP S/4HANA 또는 SAP ECC 서버에 로그인하고 **개체 탐색기**(SE80 트랜잭션)를 엽니다.

2. 목록에서 옵션 **패키지** 를 선택하고 새 패키지의 이름(예: Z\_MITI)을 입력한 다음 **표시** 단추를 누릅니다.

3. **패키지 만들기** 창에서 **예** 를 선택합니다. **패키지 작성기: 패키지 만들기** 창이 열립니다. **간단한 설명** 필드에 값을 입력하고 **계속** 아이콘을 선택합니다.

4. **로컬 Workbench 요청 프롬프트** 창에서 **Own Requests(사용자 요청)** 를 선택합니다. **개발** 요청을 선택합니다.

### <a name="create-a-function-group"></a>함수 그룹 만들기

개체 탐색기에서 목록의 **함수 그룹** 을 선택하고 아래 입력 필드에 해당 이름(예: Z\_MITI\_FGROUP)을 입력합니다. **보기** 아이콘을 선택합니다.

1. **개체 만들기** 창에서 **예** 를 선택하여 새 함수 그룹을 만듭니다.

2. **짧은 텍스트** 필드에 적절한 설명을 지정하고 **저장** 단추를 누릅니다.

3. **패키지 만들기** 의 이전 단계에서 준비한 패키지를 선택하고 **저장** 을 선택합니다.

4. **계속** 아이콘을 눌러 요청을 확인합니다.

5. 함수 그룹을 활성화합니다.

### <a name="create-the-abap-function-module"></a>ABAP 함수 모듈 만들기

1. 함수 그룹이 생성되면 해당 그룹을 선택합니다.

2. 리포지토리 브라우저에서 함수 그룹 이름을 선택 하 고 길게 클릭 하거나 마우스 오른쪽 단추를 클릭 하 고 **만들기**, **함수 모듈** 을 차례로 선택 합니다.

3. **함수 모듈** 필드에서 `Z_MITI_DOWNLOAD`를 입력합니다. **짧은 텍스트** 입력을 적절한 설명으로 채웁니다.

모듈을 만든 후에는 다음 정보를 지정합니다.

1. **특성** 탭으로 이동합니다.

2. **프로세스 유형** 을 **원격 사용 함수 모듈** 로 선택합니다.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="원본 등록 옵션 - 원격 사용 함수 모듈" border="true":::

3. **소스 코드** 탭으로 이동합니다. 다음 두 가지 방법으로 함수에 대한 코드를 배포할 수 있습니다.

   a. 주 메뉴에서 [필수 구성 요소](#prerequisites)에 설명 된 대로 부서의 범위 Studio에서 다운로드 한 텍스트 파일을 업로드 합니다. 파일을 업로드하려면 **유틸리티**, **기타 유틸리티**, **업로드/다운로드**, **업로드** 를 차례로 선택합니다.

   b. 또는 파일을 열고 콘텐츠를 복사하여 **소스 코드** 영역에 붙여넣습니다.

4. **가져오기** 탭으로 이동하고 다음 매개 변수를 만듭니다.

   a.  P\_AREA TYPE DD02L-TABNAME (선택 사항 = True)

   b.  *P\_LOCAL\_PATH TYPE STRING* (선택 사항 = True)

   다.  *P\_LANGUAGE TYPE L001TAB-DATA DEFAULT \'E\'*

   d.  *ROWSKIPS TYPE SO\_INT DEFAULT 0*

   e.  *ROWCOUNT TYPE SO\_INT DEFAULT 0*

   > [!Note]
   > 모든 매개 변수에 대해 **전달 값** 을 선택합니다.

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="원본 등록 옵션 - 가져오기 매개 변수" border="true":::

5. “테이블” 탭으로 이동하여 다음을 정의합니다.

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="원본 등록 옵션 - 테이블 탭" border="true":::

6. **예외** 탭으로 이동하여 다음 예외를 정의합니다.`E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="원본 등록 옵션 - 예외 탭" border="true":::

7. 함수를 저장합니다. ctrl+S를 누르거나, 주 메뉴에서 **함수 모듈**, **저장** 을 차례로 선택합니다.

8. 도구 모음에서 **활성화** 아이콘 (Ctrl + F3)을 선택 하 고 대화 상자 창에서  **계속** 단추를 선택 합니다. 메시지가 표시되면 생성된 항목들을 선택하여 주 함수 모듈과 함께 활성화해야 합니다.

### <a name="testing-the-function"></a>함수 테스트

이전 단계를 모두 완료하면 아래 단계에 따라 함수를 테스트합니다.

1. Z\_MITI\_DOWNLOAD 함수 모듈을 엽니다.

2. 주 메뉴에서 **함수 모듈**, **테스트**, **함수 모듈 테스트** 를 차례로 선택하거나 F8 키를 누릅니다.

3. 로컬 파일 시스템의 폴더 경로를 매개 변수 P\_LOCAL\_PATH에 입력하고 도구 모음에서 **실행** 아이콘을 누르거나 F8 키를 누릅니다.

4. 메타데이터가 포함된 파일을 다운로드하거나 업데이트해야 하는 경우 원하는 영역의 이름을 P\_AREA 필드에 입력합니다. 함수가 작업을 마치면 P\_LOCAL\_PATH 매개 변수에 표시된 폴더에는 메타데이터가 포함된 여러 개의 파일이 있어야 합니다. 파일 이름은 P\_AREA 필드에 지정할 수 있는 영역을 모방합니다.

함수 실행이 완료되며, 해당 함수를 SAP S/4HANA 또는 ECC 서버와 고속 네트워크 연결을 사용하는 머신에서 실행하는 경우 메타데이터가 훨씬 빠르게 다운로드됩니다.

## <a name="next-steps"></a>다음 단계

- [SAP ECC 원본 등록 및 검사](register-scan-sapecc-source.md)
- [SAP S/4HANA 원본 등록 및 검사](register-scan-saps4hana-source.md)

---
title: Teradata 원본 등록 및 검사 설정
description: 이 문서에서는 Azure Purview에서 Teradata 원본을 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 5ba69e4b20edc74dfd9de43f19b2ba582b196353
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211315"
---
# <a name="register-and-scan-teradata-source"></a>Teradata 원본 등록 및 검사

이 문서에서는 Purview에서 Teradata 원본을 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Teradata 원본은 Teradata 데이터베이스에서 메타데이터를 추출할 수 있도록 **전체 검사** 를 지원하고, 데이터 자산 간의 **계보** 를 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

2.  자체 호스팅 통합 런타임이 설치된 가상 머신에 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 설치되어 있는지 확인합니다.

3.  자체 호스팅 통합 런타임 컴퓨터에 \"Visual C++ 재배포 가능 패키지 2012 업데이트 4\"가 설치되어 있는지 확인합니다. 아직 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)에서 다운로드하세요.

4.  자체 호스팅 통합 런타임이 실행되는 가상 머신에 Teradata의 JDBC 드라이버를 수동으로 다운로드해야 합니다.
    JAR 실행 파일은 Teradata [웹 사이트](https://downloads.teradata.com/)에서 다운로드할 수 있습니다.

    > [!Note]
    > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

5.  지원되는 Teradata 데이터베이스 버전은 12.x ~ 16.x입니다. 검사되는 Teradata 원본에 대한 읽기 액세스 권한이 있는지 확인하세요.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Teradata 원본에 대해 유일하게 지원되는 인증은 **기본 인증** 입니다.

## <a name="register-a-teradata-source"></a>Teradata 원본 등록

새 Teradata 원본을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1.  Purview 계정으로 이동합니다.
2.  왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
3.  **등록** 을 선택합니다.
4.  원본 등록에서 **Teradata** 를 선택합니다. **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata 등록 옵션" border="true":::

**원본 등록(Teradata)** 화면에서 다음을 수행합니다.

1.  카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

2.  Teradata 원본에 연결할 **호스트** 의 이름을 입력합니다. 이는 서버에 대한 IP 주소 또는 정규화된 연결 문자열일 수도 있습니다.

3.  컬렉션을 선택하거나 새로 만듭니다(선택 사항).

4.  마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Teradata 등록" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1.  관리 센터에서 **통합 런타임** 을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 설정할 수 있습니다.

2.  [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

3.  등록된 Teradata 원본을 선택합니다.

4.  **새 검사** 를 선택합니다.

5.  아래 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **자격 증명**: 데이터 원본에 연결할 자격 증명을 선택합니다. 다음을 수행해야 합니다.

    -   자격 증명을 만드는 동안 기본 인증을 선택합니다.
    -   데이터베이스 서버에 연결할 사용자 이름을 사용자 이름 입력 필드에 입력합니다.
    -   비밀 키에 데이터베이스 서버 암호를 저장합니다.

        자격 증명에 대한 자세한 내용은 [여기](./manage-credentials.md)에 있는 링크를 참조하세요.

6.  **스키마**: 가져올 스키마의 하위 집합을 나열하며, 세미콜론으로 구분된 목록으로 표현됩니다. 예: schema1; schema2. 목록이 비어 있으면 모든 사용자 스키마를 가져옵니다. 모든 시스템 스키마(예: SysAdmin) 및 개체는 기본적으로 무시됩니다. 목록이 비어 있는 경우 모든 사용 가능한 스키마를 가져옵니다.

    SQL LIKE 식 구문을 사용하는 허용되는 스키마 이름 패턴에는 %가 포함됩니다. 예를 들어 A%, %B, %C%, D입니다.
    - A로 시작합니다. 또는    
    - B로 끝납니다. 또는    
    - C를 포함합니다. 또는    
    - D와 같습니다.

    NOT 및 특수 문자는 허용되지 않습니다.

7.  **드라이버 위치**: VM에서 자체 호스팅 통합 런타임이 실행 중인 JDBC 드라이버 위치의 경로를 지정합니다. 이 경로는 유효한 JAR 폴더 위치에 대한 경로여야 합니다.

8.  **사용 가능한 최대 메모리:** 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 Teradata 원본의 크기에 따라 달라집니다.

    > [!Note] 
    > 일반적으로 1000개 테이블당 2GB 메모리를 제공하세요.

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="검사 설정" border="true":::

6.  **계속** 을 선택합니다.

7.  **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

8.  검사를 검토하고 **저장 및 실행** 을 선택합니다.

## <a name="viewing-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택합니다.

2. 원하는 데이터 원본을 선택합니다. 해당 데이터 원본에 기존 검사 목록이 표시됩니다.

3. 결과를 보려는 검사를 선택합니다.

4. 이 페이지는 각 스캔 실행에 대한 메트릭 및 상태와 함께 모든 이전 검사 실행을 표시합니다. 또한 검사가 예약되었는지 수동인지 여부, 분류가 적용된 자산 수, 발견된 총 자산 수, 검사 시작 및 종료 시간, 총 검사 기간이 표시됩니다.

## <a name="manage-your-scans"></a>검사 관리

검사를 관리하거나 삭제하려면 다음을 수행합니다.

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택한 다음, 원하는 데이터 원본을 선택합니다.

2. 관리하려는 검사를 선택합니다. **편집** 을 선택하여 검사를 편집할 수 있습니다.

3. **삭제** 를 선택하여 검사를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
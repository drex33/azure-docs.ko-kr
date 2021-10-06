---
title: Azure Purview에서 Google BigQuery 프로젝트 등록 및 검사 설정
description: 이 문서에서는 Azure Purview에서 Google BigQuery 프로젝트를 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: caaf78e14669d67f525e5756efd8e2fd301f9b38
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218543"
---
# <a name="register-and-scan-google-bigquery-source-preview"></a>Google BigQuery 원본 등록 및 검사(미리 보기)

이 문서에서는 Purview에서 Google BigQuery 프로젝트를 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

BigQuery 원본은 BigQuery 프로젝트에서 메타데이터를 추출하고 데이터 자산 간의 계보를 가져오는 전체 검사를 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

2.  자체 호스팅 통합 런타임이 설치된 가상 머신에 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 설치되어 있는지 확인합니다.

3.  자체 호스팅 통합 런타임 컴퓨터에 \"Visual C++ 재배포 가능 패키지 2012 업데이트 4\"가 설치되어 있는지 확인합니다. 아직 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)에서 다운로드하세요.

4.  자체 호스팅 통합 런타임이 실행되는 가상 머신에 BigQuery의 JDBC 드라이버는 [여기](https://cloud.google.com/bigquery/providers/simba-drivers)서 수동으로 다운로드해야 합니다.

    > [!Note]
    > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

5.  지원되는 Google BigQuery 버전은 11.0.0입니다.

## <a name="register-a-google-bigquery-project"></a>Google BigQuery 프로젝트 등록

새 Google BigQuery 프로젝트를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1.  Purview 계정으로 이동합니다.
2.  왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
3.  **등록** 을 선택합니다.
4.  [원본 등록]에서 **Google BigQuery** 를 선택합니다. **계속** 을 선택합니다.
    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="BigQuery 원본 등록" border="true":::
   
[원본 등록(Google BigQuery)] 화면에서 다음을 수행합니다.

1.  카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

2.  **ProjectID** 를 입력합니다. 정규화된 프로젝트 ID여야 합니다. 예를 들어 mydomain.com:myProject입니다.

3.  컬렉션을 선택하거나 새로 만듭니다(선택 사항).

4.  **등록** 을 선택합니다.
    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="BigQuery 원본 구성" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1.  관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 설정할 수 있습니다.

2.  **원본** 으로 이동합니다.

3.  등록된 **BigQuery** 프로젝트를 선택합니다.

4.  **+ 새 검사** 를 선택합니다.

5.  아래 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **자격 증명**: BigQuery 자격 증명을 구성하는 동안 다음을 수행해야 합니다.

    - 인증 방법으로 **기본 인증** 을 선택합니다.
    - 사용자 이름 필드에서 서비스 계정의 이메일 ID를 제공합니다. 예를 들어 xyz\@developer.gserviceaccount.com입니다.
    - 서비스 계정의 프라이빗 키 파일을 키 자격 증명 모음의 비밀에 JSON 형식으로 저장합니다.

    Google 클라우드 플랫폼에서 새 프라이빗 키를 만들려면 탐색 메뉴에서 IAM 및 관리자, \> 서비스 계정을 차례대로 선택하고 \> 프로젝트를 선택하고 \> 키를 만들려는 서비스 계정의 이메일 주소를 선택하고 \> **키** 탭을 선택하고 \> **키 추가** 드롭다운 메뉴를 선택한 다음, 새 키 만들기를 선택합니다. 이제 JSON 형식을 선택합니다.

      > [!Note]
      > 검사 프로세스가 실행되면 프라이빗 키의 콘텐츠가 VM의 임시 파일에 저장됩니다. 검사가 성공적으로 완료되면 이 임시 파일이 삭제됩니다. 검사 오류가 발생하는 경우 시스템에서 성공할 때까지 계속 다시 시도합니다. SHIR이 실행되는 VM에서 액세스가 적절하게 제한되는지 확인하세요.**

    자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

    d.  **드라이버 위치**: VM에서 자체 호스팅 통합 런타임이 실행 중인 JDBC 드라이버 위치의 경로를 지정합니다. 이는 유효한 JAR 폴더 위치의 경로여야 합니다.    
    > [!Note]
    > 드라이버는 VM의 모든 계정에서 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

    e.  **데이터 세트**: 가져올 BigQuery 데이터 세트 목록을 지정합니다. 예를 들어 dataset1; dataset2입니다. 목록이 비어 있으면 모든 사용 가능한 데이터 세트를 가져옵니다.
        SQL LIKE 식 구문을 사용하는 데 허용되는 데이터 세트 이름 패턴에는 %의 사용이 포함됩니다. 

    예를 들어 A%; %B; %C%; D가 있습니다.
    - A로 시작합니다. 또는
    - B로 끝납니다. 또는
    - C를 포함합니다. 또는
    - D와 같습니다.    
NOT 및 특수 문자는 허용되지 않습니다.
    
    f.  **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 Google BigQuery 프로젝트의 크기에 따라 달라집니다.
        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="scan BigQuery 원본" border="true":::

6.  **연결 테스트** 를 선택합니다.

7.  **계속** 을 선택합니다.

8.  **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

9.  검사를 검토하고 **저장 및 실행** 을 선택합니다.

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
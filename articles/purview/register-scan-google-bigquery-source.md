---
title: Google BigQuery 프로젝트에 연결 및 관리
description: 이 가이드에서는 Azure Purview에서 Google BigQuery 쿼리 프로젝트에 연결하고 Purview의 기능을 사용하여 Google BigQuery 원본을 검사하고 관리하는 방법을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f28de0520e4b69970549f7258a5a41dd109d2b48
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852582"
---
# <a name="connect-to-and-manage-google-bigquery-projects-in-azure-purview-preview"></a>Azure Purview에서 Google BigQuery 프로젝트에 연결 및 관리(미리 보기)

이 문서에서는 Google BigQuery 프로젝트를 등록하는 방법과 Azure Purview에서 Google BigQuery를 인증하고 상호 작용하는 방법을 간략하게 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

> [!IMPORTANT]
> 원본인 Google BigQuery는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register)| [예](#scan)| 예 | 예 | 예 | 예| [예](how-to-lineage-google-bigquery.md)|

> [!Important]
> 지원되는 Google BigQuery 버전은 11.0.0입니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요.

* [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

* 자체 호스팅 통합 런타임이 실행되고 있는 머신에 BigQuery의 JDBC 드라이버를 다운로드하여 설치합니다. [여기](https://cloud.google.com/bigquery/providers/simba-drivers)에서 드라이버를 찾을 수 있습니다.

    > [!Note]
    > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에 Google BigQuery 프로젝트를 등록하는 방법을 설명합니다.

### <a name="steps-to-register"></a>등록 단계

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. [원본 등록]에서 **Google BigQuery** 를 선택합니다. **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="BigQuery 원본 등록" border="true":::

[원본 등록(Google BigQuery)] 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

1. **ProjectID** 를 입력합니다. 이는 정규화된 프로젝트 ID여야 합니다. 예: mydomain.com:myProject

1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

1. **등록** 을 선택합니다.

    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="BigQuery 원본 구성" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 Google BigQuery 프로젝트를 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

1. 관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 설명된 단계를 사용합니다.

1. **원본** 으로 이동합니다.

1. 등록된 **BigQuery** 프로젝트를 선택합니다.

1. **+ 새 검사** 를 선택합니다.

1. 아래 세부 정보를 제공합니다.

    1. **이름**: 검사 이름

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **자격 증명**: BigQuery 자격 증명을 구성하는 동안 다음을 수행해야 합니다.

        * 인증 방법으로 **기본 인증** 을 선택합니다.
        * 사용자 이름 필드에서 서비스 계정의 이메일 ID를 제공합니다. 예를 들어 `xyz\@developer.gserviceaccount.com`
        * 아래 단계를 수행하여 프라이빗 키를 생성하고, JSON을 복사하여 Key Vault 암호 값으로 저장합니다.

        Google의 클라우드 플랫폼에서 새 프라이빗 키를 만들려면 다음을 수행합니다.
        1. 탐색 메뉴에서 IAM 및 관리 -\> 서비스 계정 -\> 프로젝트 선택 -\>을 선택합니다. 
        1. 키를 만들려는 서비스 계정의 이메일 주소를 선택합니다.
        1. **키** 탭을 선택합니다.
        1. **키 추가** 드롭다운 메뉴를 선택한 다음, 새 키 만들기를 선택합니다. 
        1. JSON 형식을 선택합니다.

          > [!Note]
          > 검사 프로세스가 실행되면 프라이빗 키의 콘텐츠가 VM의 임시 파일에 저장됩니다. 검사가 성공적으로 완료되면 이 임시 파일이 삭제됩니다. 검사 오류가 발생하는 경우 시스템에서 성공할 때까지 계속 다시 시도합니다. SHIR이 실행되는 VM에서 액세스가 적절하게 제한되는지 확인하세요.

        자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

    1. **드라이버 위치**: VM에서 자체 호스팅 통합 런타임이 실행 중인 JDBC 드라이버 위치의 경로를 지정합니다. 이 경로는 유효한 JAR 폴더 위치에 대한 경로여야 합니다. 

        > [!Note]
        > 드라이버는 VM의 모든 계정에서 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

    1. **데이터 세트**: 가져올 BigQuery 데이터 세트 목록을 지정합니다.
        예를 들어 dataset1; dataset2입니다. 목록이 비어 있으면 모든 사용 가능한 데이터 세트를 가져옵니다.
        SQL LIKE 식 구문을 사용하는 데 허용되는 데이터 세트 이름 패턴에는 %의 사용이 포함됩니다.

        예: A%; %B; %C%; D
        * A로 시작합니다. 또는
        * B로 끝납니다. 또는
        * C를 포함합니다. 또는
        * D와 같습니다.

        NOT 및 특수 문자는 허용되지 않습니다.

    1. **사용 가능한 최대 메모리**: VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 Google BigQuery 프로젝트의 크기에 따라 달라집니다.

        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="scan BigQuery 원본" border="true":::

1. **연결 테스트** 를 선택합니다.

1. **계속** 을 선택합니다.

1. **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)

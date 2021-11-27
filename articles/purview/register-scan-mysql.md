---
title: MySQL로 커넥트 및 관리
description: 이 가이드에서는 Azure Purview에서 MySQL에 연결하고 Purview의 기능을 사용하여 MySQL 원본을 검사하고 관리하는 방법을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 3b747f4b4b3d25c50e58fd6e8048c984912647eb
ms.sourcegitcommit: 625ffc4a23b7b71680aaf572fede507053d4ef03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2021
ms.locfileid: "133177331"
---
# <a name="connect-to-and-manage-mysql-in-azure-purview-preview"></a>Azure Purview에서 MySQL 커넥트 및 관리(미리 보기)

이 문서에서는 MySQL을 등록하는 방법과 Azure Purview에서 MySQL을 인증하고 상호 작용하는 방법을 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

> [!IMPORTANT]
> 원본인 MySQL은 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register)| [예](#scan)| 아니요 | 아니요 | 아니요 | 아니요| 예|

지원되는 MySQL 서버 버전은 5.7~8.x입니다.

MySQL 원본을 검색할 때 Purview는 다음을 지원합니다.

- MySQL 서버, 데이터베이스, 테이블, 뷰 및 테이블/뷰 열을 포함한 메타데이터 추출
- 테이블과 뷰 간의 자산 관계에 대한 계보 가져오기

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 사용 권한 페이지를](catalog-permissions.md) 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요. 지원되는 최소 자체 호스팅 Integration Runtime 버전은 5.11.7953.1입니다.

* [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

* MySQL 사용자는 메타데이터를 포함하는 각 대상 MySQL 스키마에 대한 SELECT, SHOW VIEW 및 EXECUTE 권한이 있어야 합니다.

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에서 MySQL을 등록하는 방법을 설명합니다.

### <a name="steps-to-register"></a>등록 단계

데이터 카탈로그에 새 MySQL 원본을 등록하려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)에서 Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. 원본 등록에서 **MySQL** 을 선택합니다. **계속** 을 선택합니다.

원본 **등록(MySQL)** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

1. MySQL 원본에 연결할 **서버** 이름을 입력합니다. 이는 다음과 같을 수 있습니다.
    * 데이터베이스 서버에 연결하는 데 사용되는 호스트 이름입니다. 예: `MyDatabaseServer.com`
    * IP 주소. 예: `192.169.1.2`
    * 정규화된 JDBC 연결 문자열. 예:

        ```
        jdbc:mysql://COMPUTER_NAME_OR_IP/DATABASE_NAME
        ```

1. 데이터베이스 서버에 연결하는 데 사용되는 **포트를** 입력합니다(MySQL의 경우 기본적으로 3306).

1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

1. 마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-mysql/register-sources.png" alt-text="원본 등록 옵션" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 MySQL을 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="authentication-for-a-scan"></a>검사 인증

MySQL 원본에 대해 지원되는 인증 유형은 **기본 인증** 입니다.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. 관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 만들 수 있습니다.

1. **원본** 으로 이동합니다.

1. 등록된 MySQL 원본을 선택합니다.

1. **+ 새 검사** 를 선택합니다.

1. 아래 세부 정보를 제공합니다.

    1. **이름**: 검사 이름

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **자격 증명**: 데이터 원본에 연결할 자격 증명을 선택합니다. 다음을 수행해야 합니다.
        * 자격 증명을 만드는 동안 **기본 인증을** 선택합니다.
        * 사용자 이름 입력 필드에서 데이터베이스 서버에 연결하는 데 사용되는 사용자 이름을 제공합니다.
        * 데이터베이스 서버에 연결하는 데 사용되는 사용자 암호를 비밀 키에 저장합니다.

    1. **데이터베이스:** 가져올 스키마의 하위 집합을 세미콜론으로 구분된 목록으로 표시합니다. 예들 들어 `schema1; schema2`입니다. 목록이 비어 있으면 모든 사용자 스키마를 가져옵니다. 모든 시스템 스키마(예: SysAdmin) 및 개체는 기본적으로 무시됩니다. 목록이 비어 있는 경우 모든 사용 가능한 스키마를 가져옵니다.

        SQL LIKE 식 구문을 사용하는 허용 가능한 스키마 이름 패턴에는 % 사용이 포함됩니다. 예: `A%; %B; %C%; D`
        * A로 시작합니다. 또는
        * B로 끝납니다. 또는
        * C를 포함합니다. 또는
        * D와 같습니다.

        NOT 및 특수 문자는 허용되지 않습니다.

    1. **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검색할 MySQL 원본의 크기에 따라 달라집니다.

        > [!Note]
        > 일반적으로 1,000개 테이블당 1GB 메모리를 제공하세요.

        :::image type="content" source="media/register-scan-mysql/scan.png" alt-text="MySQL 검사" border="true":::

1. **계속** 을 선택합니다.

1. **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)

---
title: Azure Purview에서 Looker 등록 및 검사 설정
description: 이 문서에서는 Azure Purview에서 Looker 원본을 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 1f39a1d261b4c8ed4223d66ebb34c94be7310b2e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214784"
---
# <a name="register-and-scan-looker-preview"></a>Looker 등록 및 검사(미리 보기)

이 문서에서는 Purview에서 Looker 서버를 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Looker 원본은 Looker 서버에서 메타데이터를 추출하기 위한 전체 검색을 지원합니다. 데이터베이스 연결, LookML 모델 및 관련 보고서(모양 및 대시보드)를 포함하여 Looker 서버에서 메타데이터를 가져왔습니다. 또한 이 데이터 원본은 데이터 자산 간의 계보를 가져옵니다.

> [!Note]
> 원본으로서의 Looker는 현재 프라이빗 미리 보기에서 지원됩니다. 이 원본을 등록하고 비 프로덕션 Purview 계정에서 검사를 설정하고 피드백을 제공해 주세요.

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

2.  자체 호스팅 통합 런타임이 설치된 가상 머신에 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 설치되어 있는지 확인합니다.

3.  \"Visual C++ 재배포 가능 패키지 2012 업데이트 4\"가 자체 호스팅 통합 런타임이 실행되는 VM에 설치되어 있어야 합니다. 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)서 다운로드하세요.

4.  지원되는 Looker 서버 버전은 7.2입니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Looker 서버에 연결하려면 API3 키가 필요합니다. API3 키는 공용 client_id와 프라이빗 client_secret으로 구성되며 OAuth2 인증 패턴을 따릅니다.

## <a name="register-a-looker-server"></a>Looker 서버 등록

새 Looker 서버를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
2. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
3. **등록** 을 선택합니다.
4. 원본 등록에서 **Looker** 를 선택합니다. **계속** 을 선택합니다.
    :::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="looker 원본 등록" border="true":::


원본 등록(Looker) 화면에서 다음을 수행합니다.

1. 카탈로그 내에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

2. **서버 API URL** 필드에 Looker API URL을 입력합니다. API 요청에 대한 기본 포트는 포트 19999입니다. 또한 모든 Looker API 엔드포인트에는 HTTPS 연결이 필요합니다. 예: 'https://azurepurview.cloud.looker.com '

3. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

4. 마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="looker 원본 검색" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. 관리 센터에서 통합 런타임을 선택합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 설정할 수 있습니다.

2. **원본** 으로 이동합니다.

3. 등록된 **Looker** 서버를 선택합니다.

4. **+ 새 검사** 를 선택합니다.

5. 아래 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **서버 API URL** 은 등록 중에 입력된 값에 따라 자동으로 채워집니다.

    d.  **자격 증명:** Looker 자격 증명을 구성하는 동안 다음을 수행해야 합니다.

    - 인증 방법으로 **기본 인증** 을 선택합니다.
    - 사용자 이름 필드에 Looker API3 키의 클라이언트 ID를 제공합니다.
    - Looker API3 키의 클라이언트 암호를 키 자격 증명 모음의 비밀에 저장합니다.

    **참고:** 클라이언트 ID 및 클라이언트 암호에 액세스하려면 Looker -\>Admin -\> Users -\>로 이동하여 user -\>에서 **편집**, **EditKeys** -\>를 차례로 선택합니다. 클라이언트 ID 및 클라이언트 암호를 사용하거나 새로 만듭니다.
    :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="looker 세부 정보 가져오기" border="true":::
    

    자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

    e.  **프로젝트 필터** -- 세미콜론으로 구분된 Looker 프로젝트 목록을 제공하여 검색 범위를 지정합니다. 이 옵션은 부모 프로젝트에서 모양과 대시보드를 선택하는 데 사용됩니다.

    f.  **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 erwin Mart의 크기에 따라 달라집니다.

    :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="검사 트리거" border="true":::

6. **연결 테스트** 를 선택합니다.

7. **계속** 을 선택합니다.

8. **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

9. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

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

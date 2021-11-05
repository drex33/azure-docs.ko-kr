---
title: erwin Mart 서버에 연결 및 관리
description: 이 가이드에서는 Azure Purview에서 erwin Mart 서버에 연결하고 Purview의 기능을 사용하여 erwin Mart 서버 원본을 검사하고 관리하는 방법을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0a6aef0cdbf55772ada02bef7090ccc3165b5658
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472450"
---
# <a name="connect-to-and-manage-erwin-mart-servers-in-azure-purview-preview"></a>Azure Purview에서 erwin Mart 서버에 연결 및 관리(미리 보기)

이 문서에서는 erwin Mart 서버를 등록하는 방법과 Azure Purview에서 erwin Mart 서버를 인증하고 상호 작용하는 방법을 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

> [!IMPORTANT]
> 원본인 erwin Mart 서버는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register)| [예](#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-erwin.md)|

erwin 원본은 erwin Mart 서버에서 메타데이터를 추출하는 전체 검사를 지원합니다. 메타데이터에는 다음이 포함됩니다.

1. 엔터티, 특성 및 도메인이 있는 논리적 전용 모델 또는
1. 테이블, 열, 데이터 형식이 있는 물리적 전용 모델 또는
1. 논리적/물리적 모델

> [!Important]
> 지원되는 erwin Mart 버전은 9.x~2021입니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요.

    > [!Note]
    > erwin Mart 인스턴스가 실행되는 VM에서 자체 호스팅 통합 런타임을 실행해야 합니다.

* [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에 erwin Mart 서버를 등록하는 방법을 설명합니다.

erwin Mart 원본에 지원되는 유일한 인증은 사용자 이름 및 암호 형식의 **서버 인증** 입니다.

### <a name="steps-to-register"></a>등록 단계

1. [Purview Studio](https://web.purview.azure.com/)에서 Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. [원본 등록]에서 **erwin** 을 선택합니다. **계속** 을 선택합니다.
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="erwin 원본 등록" border="true":::

[원본 등록(erwin)] 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. erwin Mart **서버 이름** 을 입력합니다. erwin Mart 서버에 연결하는 데 사용되는 네트워크 호스트 이름입니다. 예를 들어 localhost입니다.
1. erwin Mart에 연결할 때 사용되는 **포트** 번호를 입력합니다. 기본적으로 이 값은 18170입니다.
1. **애플리케이션 이름** 을 입력합니다.

    >[!Note]
    > 위의 세부 정보는 erwin 데이터 모델러로 이동하여 확인할 수 있습니다. 마트 -\> 연결을 차례로 선택하여 서버 이름, 포트 및 애플리케이션 이름과 관련된 세부 정보를 확인합니다.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="erwin 세부 정보 확인" border="true":::

1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

1. 마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="원본 등록" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 erwin Mart 서버를 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. 관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 erwin Mart 인스턴스가 실행되는 VM에 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 설정합니다.
1. **원본** 으로 이동합니다.

1. 등록된 **erwin** Mart를 선택합니다.

1. **+ 새 검사** 를 선택합니다.

1. 아래 세부 정보를 제공합니다.

    1. **이름**: 검사 이름

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **서버 이름, 포트** 및 **애플리케이션 이름** 은 등록 중에 입력한 값에 따라 자동으로 채워집니다.

    1. **자격 증명:** erwin Mart 서버에 연결하도록 구성된 자격 증명을 선택합니다. 자격 증명을 만드는 동안 다음을 수행해야 합니다.
        * 인증 방법으로 **기본 인증** 을 선택합니다.
        * 사용자 이름 필드에서 erwin Mart 서버의 사용자 이름을 제공합니다.
        * 서버 인증용 사용자 암호를 키 자격 증명 모음의 비밀에 저장합니다.

        자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

    1. **IIS(인터넷 정보 서비스) 사용** - True 또는 False를 선택하여 erwin Mart 서버에 연결할 때 Microsoft IIS(인터넷 정보 서비스)를 사용해야 하는지 여부를 알립니다. 기본적으로 이 값은 False로 설정되어 있습니다.

    1. **SSL(Secure Sockets Layer) 사용** - True 또는 False를 선택하여 erwin Mart 서버에 연결할 때 SSL(Secure Sockets Layer)을 사용해야 하는지 여부를 알립니다. 기본적으로 이 값은 False로 설정되어 있습니다.

        > [!Note]
        > 이 매개 변수는 erwin Mart 버전 9.1 이상에만 적용됩니다.

    1. **찾아보기 모드** - erwin Mart 찾아보기 모드를 선택합니다. 가능한 옵션은 "라이브러리 및 모델" 또는 "라이브러리 전용"입니다.

    1. **모델** - 세미콜론으로 구분된 erwin 모델 로케이터 문자열 목록을 제공하여 검사 범위를 지정합니다. 예를 들어 mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical입니다.

    1. **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 erwin Mart의 크기에 따라 달라집니다.

    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="검사 트리거" border="true":::

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

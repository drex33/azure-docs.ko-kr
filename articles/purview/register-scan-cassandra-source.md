---
title: Cassandra에 연결 및 관리
description: 이 가이드에서는 Azure Purview에서 Cassandra에 연결하고 Purview의 기능을 사용하여 Cassandra 원본을 검사하고 관리하는 방법을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 80f396e9b0bf53e25b188c42da6be6b8ad7f6061
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851898"
---
# <a name="connect-to-and-manage-cassandra-in-azure-purview-preview"></a>Azure Purview에서 Cassandra에 연결 및 관리(미리 보기)

이 문서에서는 Cassandra를 등록하는 방법과 Azure Purview에서 Cassandra를 인증하고 상호 작용하는 방법을 간략하게 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)| 예 | 예 | 예 | 예| [예](how-to-lineage-cassandra.md)|

> [!Important]
> 지원되는 Cassandra 서버 버전은 3.*x* 또는 4.*x* 입니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
  자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요.

* [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에 Cassandra를 등록하는 방법을 설명합니다.

### <a name="steps-to-register"></a>등록 단계

새 Cassandra 서버를 데이터 카탈로그에 등록하려면,

1. Purview 계정으로 이동합니다.
1. 왼쪽 창에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 화면에서 **Cassandra** 를 선택한 다음, **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="원본 등록 화면을 보여 주는 스크린샷" border="true":::

1. **원본 등록(Cassandra)** 화면에서 다음을 수행합니다.

   1. **이름** 을 입력합니다. 데이터 원본은 카탈로그에서 이 이름을 사용합니다.
   1. **호스트** 상자에서 Cassandra 서버가 실행되는 서버 주소를 입력합니다. 예를 들어 20.190.193.10입니다.
   1. **포트** 상자에서 Cassandra 서버에서 사용하는 포트를 입력합니다.
   1. 컬렉션을 선택하거나 새 컬렉션을 만듭니다(선택 사항).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="원본 등록(Cassandra) 화면을 보여 주는 스크린샷" border="true":::
   1. **등록** 을 선택합니다.

## <a name="scan"></a>검사

아래 단계에 따라 Cassandra를 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검사를 만들고 실행하려면,

1. 관리 센터에서 **통합 런타임** 을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 아직 설정되지 않은 경우 [이러한 단계를 수행하여 자체 호스팅 통합 런타임을 설정하세요](./manage-integration-runtimes.md).

1. **원본** 으로 이동합니다.

1. 등록된 Cassandra 서버를 선택합니다.

1. **새 검사** 를 선택합니다.

1. 다음 세부 정보를 제공합니다.

    1. **이름**: 검사 이름을 지정합니다.

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **자격 증명**: Cassandra 자격 증명을 구성하는 경우 다음을 수행해야 합니다.

        * 인증 방법으로 **기본 인증** 을 선택합니다.
        * **사용자 이름** 상자에서 연결하려는 사용자의 이름을 입력합니다. 
        * 키 자격 증명 모음의 비밀에서 연결하려는 Cassandra 사용자의 암호를 저장합니다.

        자세한 내용은 [Purview에서 원본 인증을 위한 자격 증명](manage-credentials.md)을 참조하세요.

    1. **키스페이스**: 가져올 Cassandra 키스페이스 목록을 지정합니다. 여러 키스페이스는 세미콜론으로 구분해야 합니다. 예를 들어, keyspace1; keyspace2. 목록이 비어 있으면 모든 사용 가능한 키스페이스를 가져옵니다.

        %를 포함하여 SQL LIKE 식 구문을 사용하는 키스페이스 이름 패턴을 사용할 수 있습니다.

        예를 들어 A%; %B; %C%; D입니다.

        이 식의 의미는 다음과 같습니다.
        * A로 시작합니다. 또는
        * B로 끝납니다. 또는
        * C를 포함합니다. 또는
        * D와 같습니다.

        NOT 또는 특수 문자는 사용할 수 없습니다.

    1. **SSL(Secure Sockets Layer) 사용**: **True** 또는 **False** 를 선택하여 Cassandra 서버에 연결할 때 SSL(Secure Sockets Layer)을 사용하는지 여부를 지정합니다. 기본적으로 이 옵션은 **False** 로 설정됩니다.

    1. **사용 가능한 최대 메모리**: 검사 프로세스에 사용할 VM에서 사용 가능한 최대 메모리(GB)를 지정합니다. 이 값은 검사할 Cassandra 서버의 크기에 따라 다릅니다.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Cassandra 원본 검사" border="true":::

1. **연결 테스트** 를 선택합니다.

1. **계속** 을 선택합니다.

1. **검사 트리거** 를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

1. 검사를 검토한 다음, **저장 및 실행** 을 선택합니다.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)

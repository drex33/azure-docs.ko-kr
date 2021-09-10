---
title: Azure Purview에서 Cassandra를 원본으로 등록하고 검사를 설정합니다.
description: 이 문서에서는 Azure Purview에서 Cassandra 서버를 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 8/06/2021
ms.openlocfilehash: 8e2136cdb4cca53d81412335f7f5878fd8d70b86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788062"
---
# <a name="register-and-scan-a-cassandra-source"></a>Cassandra 원본 등록 및 검사 

이 문서에서는 Purview에서 Cassandra 서버를 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Cassandra 원본은 전체 검사를 지원하여 Cassandra 서버에서 메타데이터를 추출하고 데이터 자산 간에 Lineage를 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

2.  자체 호스팅 통합 런타임이 설치된 가상 머신에 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 설치되어 있는지 확인합니다.

3.  자체 호스팅 통합 런타임 컴퓨터에 \"Visual C++ 재배포 가능 패키지 2012 업데이트 4\"가 설치되어 있는지 확인합니다. 아직 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)에서 다운로드하세요.

4.  지원되는 Cassandra 서버 버전은 3.x ~ 4.x입니다.

## <a name="register-a-cassandra-server"></a>Cassandra 서버 등록

새 Cassandra 서버를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1.  Purview 계정으로 이동합니다.
2.  왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
3.  **등록** 을 선택합니다.
4.  원본 등록에서 **Cassandra** 를 선택합니다. **계속** 을 선택합니다.
    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Cassandra 원본 등록" border="true":::
   
원본 등록(Cassandra) 화면에서 다음을 수행합니다.

1. 카탈로그 내에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

2. **호스트** 필드에 Cassandra 서버가 실행 중인 서버 주소를 입력합니다. 예: 20.190.193.10

3. **포트** 필드에 Cassandra 서버에서 사용하는 포트를 입력합니다.
4. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

5.  **등록** 을 클릭합니다.
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Cassandra 원본 구성" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1.  관리 센터에서 통합 런타임을 클릭합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 설정할 수 있습니다.

2.  **원본** 으로 이동합니다.

3.  등록된 **Cassandra** 서버를 선택합니다.

4.  **+ 새 검사** 를 선택합니다.

5.  아래 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **자격 증명:** Cassandra 자격 증명을 구성하는 동안 다음을 수행해야 합니다.

    - 인증 방법으로 **기본 인증** 을 선택합니다.
    - 사용자 이름 필드에 연결 중인 사용자를 대신하여 사용자 이름을 입력합니다. 
    - 대신 키 자격 증명 모음의 비밀에 연결되는 Cassandra 사용자의 암호를 저장합니다.

    자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

    d.  **키스페이스**: 가져올 Cassandra 키스페이스 목록을 지정합니다. 여러 키스페이스는 세미콜론으로 구분해야 합니다. 예를 들어, keyspace1; keyspace2. 목록이 비어 있으면 모든 사용 가능한 키스페이스를 가져옵니다.
    SQL LIKE 식 구문을 사용하는 데 허용되는 키스페이스 이름 패턴에는 %의 사용이 포함됩니다. 

    예를 들어 A%; %B; %C%; D가 있습니다.
    - A로 시작합니다. 또는
    - B로 끝납니다. 또는
    - C를 포함합니다. 또는
    - D와 같습니다.    
NOT 및 특수 문자는 허용되지 않습니다.
    
    f. **SSL(Secure Sockets Layer) 사용** - True 또는 False를 선택하여 Cassandra 서버에 연결할 때 SSL(Secure Sockets Layer)을 사용해야 하는지 여부를 알립니다. 기본적으로 이 값은 False로 설정되어 있습니다.

    g. **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 Cassandra 서버의 크기에 따라 다릅니다.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Cassandra 원본 검사" border="true":::

6.  **연결 테스트** 를 클릭합니다.

7.  **계속** 을 클릭합니다.

8.  **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

9.  검사를 검토하고, **저장 및 실행** 을 클릭합니다.

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
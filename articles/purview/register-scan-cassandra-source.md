---
title: Cassandra 원본 등록 및 검사
description: 이 문서에서는 Azure Purview에서 Cassandra 서버를 등록하고 메타데이터를 추출하도록 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 5f8feffebff71c25f2a0d62d775894e7c4431615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005319"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Cassandra 원본 등록 및 검사(미리 보기)

이 문서에서는 Azure Purview에서 Cassandra 서버를 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Purview를 사용하여 Cassandra에서 전체 검사를 수행하여 데이터 자산 간의 메타데이터 및 계보를 추출할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

2.  [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

3.  Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [다운로드](https://www.microsoft.com/download/details.aspx?id=30679)하세요.

4.  Cassandra 서버가 버전 3.*x* 또는 4.*x* 인지 확인합니다.

## <a name="register-a-cassandra-server"></a>Cassandra 서버 등록

새 Cassandra 서버를 데이터 카탈로그에 등록하려면,

1.  Purview 계정으로 이동합니다.
2.  왼쪽 창에서 **데이터 맵** 을 선택합니다.
3.  **등록** 을 선택합니다.
4.  **원본 등록** 화면에서 **Cassandra** 를 선택한 다음, **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="원본 등록 화면을 보여 주는 스크린샷" border="true":::
   
1. **원본 등록(Cassandra)** 화면에서 다음을 수행합니다.

   1. **이름** 을 입력합니다. 데이터 원본은 카탈로그에서 이 이름을 사용합니다.

   2. **호스트** 상자에서 Cassandra 서버가 실행되는 서버 주소를 입력합니다. 예를 들어 20.190.193.10입니다.

   3. **포트** 상자에서 Cassandra 서버에서 사용하는 포트를 입력합니다.
   4. 컬렉션을 선택하거나 새 컬렉션을 만듭니다(선택 사항).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="원본 등록(Cassandra) 화면을 보여 주는 스크린샷" border="true":::
   5.  **등록** 을 선택합니다.


## <a name="create-and-run-a-scan"></a>검사 만들기 및 실행

새 검사를 만들고 실행하려면,

1.  관리 센터에서 **통합 런타임** 을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 아직 설정되지 않은 경우 [이러한 단계를 수행하여 자체 호스팅 통합 런타임을 설정하세요](./manage-integration-runtimes.md).
    

2.  **원본** 으로 이동합니다.

3.  등록된 Cassandra 서버를 선택합니다.

4.  **새 검사** 를 선택합니다.

5.  다음 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름을 지정합니다.

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **자격 증명**: Cassandra 자격 증명을 구성하는 경우 다음을 수행해야 합니다.

    - 인증 방법으로 **기본 인증** 을 선택합니다.
    - **사용자 이름** 상자에서 연결하려는 사용자의 이름을 입력합니다. 
    - 키 자격 증명 모음의 비밀에서 연결하려는 Cassandra 사용자의 암호를 저장합니다.

    자세한 내용은 [Purview에서 원본 인증을 위한 자격 증명](manage-credentials.md)을 참조하세요.

    d.  **키스페이스**: 가져올 Cassandra 키스페이스 목록을 지정합니다. 여러 키스페이스는 세미콜론으로 구분해야 합니다. 예를 들어, keyspace1; keyspace2. 목록이 비어 있으면 모든 사용 가능한 키스페이스를 가져옵니다.
    
    %를 포함하여 SQL LIKE 식 구문을 사용하는 키스페이스 이름 패턴을 사용할 수 있습니다. 

    예를 들어 A%; %B; %C%; D입니다.

    이 식의 의미는 다음과 같습니다.
    - A로 시작합니다. 또는
    - B로 끝납니다. 또는
    - C를 포함합니다. 또는
    - D와 같습니다.    

    NOT 또는 특수 문자는 사용할 수 없습니다.
    
    e. **SSL(Secure Sockets Layer) 사용**: **True** 또는 **False** 를 선택하여 Cassandra 서버에 연결할 때 SSL(Secure Sockets Layer)을 사용하는지 여부를 지정합니다. 기본적으로 이 옵션은 **False** 로 설정됩니다.

    f. **사용 가능한 최대 메모리**: 검사 프로세스에 사용할 VM에서 사용 가능한 최대 메모리(GB)를 지정합니다. 이 값은 검사할 Cassandra 서버의 크기에 따라 다릅니다.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Cassandra 원본 검사" border="true":::

6.  **연결 테스트** 를 선택합니다.

7.  **계속** 을 선택합니다.

8.  **검사 트리거** 를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

9.  검사를 검토한 다음, **저장 및 실행** 을 선택합니다.

## <a name="view-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택합니다.

2. 검사를 보려는 데이터 원본을 선택합니다. 해당 데이터 원본에 대한 기존 검사 목록이 표시됩니다.

3. 결과를 보려는 검사를 선택합니다.

   결과 페이지에는 각 검사 실행에 대한 메트릭 및 상태와 함께 모든 이전 검사 실행이 표시됩니다. 
   또한 다음 사항도 표시됩니다. 
   - 검사가 예약되었는지, 아니면 수동인지의 여부 
   - 분류가 적용된 자산의 수 
   - 검색된 총 자산 수 
   - 검사의 시작 및 종료 시간
   - 검사 기간

## <a name="manage-your-scans"></a>검사 관리

검사를 관리하거나 삭제하려면,

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택합니다. 그런 다음, 검사를 관리하려는 데이터 원본을 선택합니다.

2. 관리하려는 검사를 선택합니다. 
   - **편집** 을 선택하여 검사를 편집할 수 있습니다.

   - **삭제** 를 선택하여 검사를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)

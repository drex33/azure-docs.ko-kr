---
title: Erwin Mart 등록 및 검사 설정
description: 이 문서에서는 Azure Purview에서 Erwin Mart를 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 8077658b644853f9ce0789a1d0863b3bde902818
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211535"
---
# <a name="register-and-scan-erwin-mart-server-preview"></a>Erwin Mart 서버 등록 및 검사(미리 보기)

이 문서에서는 Purview에서 Erwin Mart 서버를 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

erwin 원본은 Erwin Mart 서버에서 메타데이터를 추출하는 전체 검사를 지원합니다. 메타데이터는 다음과 같습니다.

1.  엔터티, 특성 및 도메인이 있는 논리적 전용 모델 또는
2.  테이블, 열, 데이터 형식이 있는 물리적 전용 모델 또는
3.  논리적/물리적 모델

또한 이 데이터 원본은 데이터 자산 간의 계보를 가져옵니다.

> [!Note]
> Erwin은 현재 미리 보기에서 원본으로 지원되고 있습니다. 이 원본을 등록하고 비 프로덕션 Purview 계정에서 검사를 설정하고 피드백을 제공해 주세요.

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은  
    [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

    > [!Note]
    > erwin Mart 인스턴스가 실행되는 VM에서 자체 호스팅 통합 런타임을 실행해야 합니다.

2.  자체 호스팅 통합 런타임이 설치된 가상 머신에 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 설치되어 있는지 확인합니다.

3.  \"Visual C++ 재배포 가능 패키지 2012 업데이트 4\"가 자체 호스팅 통합 런타임이 실행되는 VM에 설치되어 있어야 합니다. 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)서 다운로드하세요.

4.  지원되는 erwin Mart 버전은 9.x~2021입니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

erwin Mart 원본에 지원되는 유일한 인증은 사용자 이름 및 암호 형식의 **서버 인증** 입니다.

## <a name="register-an-erwin-mart"></a>erwin Mart 등록

새 erwin Mart를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. [원본 등록]에서 **erwin** 을 선택합니다. **계속** 을 선택합니다.
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="erwin 원본 등록" border="true":::

[원본 등록(erwin)] 화면에서 다음을 수행합니다.

1.  카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

2.  erwin Mart **서버 이름** 을 입력합니다. erwin Mart 서버에 연결하는 데 사용되는 네트워크 호스트 이름입니다. 예를 들어 localhost입니다.

3.  erwin Mart에 연결할 때 사용되는 **포트** 번호를 입력합니다. 기본적으로 이 값은 18170입니다.

4.  **애플리케이션 이름** 을 입력합니다.

    >[!Note]
    > 위의 세부 정보는 erwin 데이터 모델러로 이동하여 확인할 수 있습니다. 마트 -\> 연결을 차례로 선택하여 서버 이름, 포트 및 애플리케이션 이름과 관련된 세부 정보를 확인합니다.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="erwin 세부 정보 확인" border="true":::
    
5.  컬렉션을 선택하거나 새로 만듭니다(선택 사항).

6.  마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="원본 등록" border="true":::
    

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1.  관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 erwin Mart 인스턴스가 실행되는 VM에 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 설정할 수 있습니다.

2.  **원본** 으로 이동합니다.

3.  등록된 **erwin** Mart를 선택합니다.

4.  **+ 새 검사** 를 선택합니다.

5.  아래 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **서버 이름**, 포트 및 **애플리케이션 이름** 은 등록 중에 입력한 값에 따라 자동으로 채워집니다.

    d.  **자격 증명:** erwin Mart 서버에 연결하도록 구성된 자격 증명을 선택합니다. 자격 증명을 만드는 동안 다음을 수행해야 합니다.
    - 인증 방법으로 **기본 인증** 을 선택합니다.
    - 사용자 이름 필드에서 erwin Mart 서버의 사용자 이름을 제공합니다.
    - 서버 인증용 사용자 암호를 키 자격 증명 모음의 비밀에 저장합니다.

    자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

    e.  **IIS(인터넷 정보 서비스) 사용** - True 또는 False를 선택하여 erwin Mart 서버에 연결할 때 Microsoft IIS(인터넷 정보 서비스)를 사용해야 하는지 여부를 알립니다. 기본적으로 이 값은 False로 설정되어 있습니다.

    f.  **SSL(Secure Sockets Layer) 사용** - True 또는 False를 선택하여 erwin Mart 서버에 연결할 때 SSL(Secure Sockets Layer)을 사용해야 하는지 여부를 알립니다. 기본적으로 이 값은 False로 설정되어 있습니다.

    > [!Note]
    > 이 매개 변수는 erwin Mart 버전 9.1 이상에만 적용됩니다.

    g.  **찾아보기 모드** - erwin Mart 찾아보기 모드를 선택합니다. 가능한 옵션은 "라이브러리 및 모델" 또는 "라이브러리 전용"입니다.

    h.  **모델** - 세미콜론으로 구분된 erwin 모델 로케이터 문자열 목록을 제공하여 검사 범위를 지정합니다. 예를 들어 mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical입니다.

    i.  **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 erwin Mart의 크기에 따라 달라집니다.
    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="검사 트리거" border="true":::
   

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

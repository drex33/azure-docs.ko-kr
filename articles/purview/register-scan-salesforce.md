---
title: Salesforce에 커넥트 및 관리
description: 이 가이드에서는 Azure 부서의 범위에서 Salesforce에 연결 하 고, 부서의 범위의 기능을 사용 하 여 Salesforce 원본을 검색 하 고 관리 하는 방법을 설명 합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 81f4775771c162dce061bc1ad8901bd9b9542d40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554943"
---
# <a name="connect-to-and-manage-salesforce-in-azure-purview-preview"></a>Azure 부서의 범위에서 Salesforce 커넥트 및 관리 (미리 보기)

이 문서에서는 Salesforce를 등록 하는 방법 및 Azure 부서의 범위에서 Salesforce를 인증 하 고 상호 작용 하는 방법을 간략하게 설명 합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

> [!IMPORTANT]
> 원본으로 Salesforce는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register)| [예](#scan)| 예 | 예 | 예 | 예| 예|

Salesforce를 스캔할 때 부서의 범위는 Salesforce 조직, 개체, 필드, 외래 키, unique_constraints 등의 메타 데이터 추출을 지원 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요.

* [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

* 자체 호스팅 integration runtime 컴퓨터의 IP가 Salesforce에 설정 된 [조직의 신뢰할 수 있는 ip 범위](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) 내에 있는지 확인 합니다. 그렇지 않으면 신뢰할 수 없는 네트워크에서 Salesforce에 인증 하기 위해 보안 토큰을 추가적으로 제공 해야 합니다. 자세한 내용은 [Scan](#scan) 의 자격 증명 구성 섹션을 확인 하세요.

* 사용자가 Salesforce 문서를 제출할 경우 표준 개체 및 사용자 지정 개체에 대해이 액세스를 허용 하도록 특정 보안 설정을 구성 해야 합니다. 권한을 구성하려면:
    - Salesforce 내에서 설치를 클릭 한 다음 사용자 관리를 클릭 합니다.
    - 사용자 관리 트리에서 프로필을 클릭 합니다.
    - 프로필이 오른쪽에 표시 되 면 편집 하려는 프로필을 선택 하 고 해당 프로필 옆의 편집 링크를 클릭 합니다.
    
    표준 개체의 경우 "Documents" 섹션에 읽기 권한이 선택 되어 있는지 확인 합니다. 사용자 지정 개체의 경우 각 사용자 지정 개체에 대해 읽기 권한이 선택 되어 있는지 확인 합니다.

## <a name="register"></a>등록

이 섹션에서는 [부서의 범위 Studio](https://web.purview.azure.com/)를 사용 하 여 Azure 부서의 범위에서 Salesforce를 등록 하는 방법을 설명 합니다.

### <a name="steps-to-register"></a>등록 단계

데이터 카탈로그에 새 Salesforce 원본을 등록 하려면 다음을 수행 합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)에서 Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. 소스 등록에서 **Salesforce** 를 선택 합니다. **계속** 을 선택합니다.

**소스 등록 (Salesforce)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

1. Salesforce 로그인 끝점 URL을 **도메인 URL** 로 입력 합니다. 예들 들어 `https://login.salesforce.com`입니다. 회사의 인스턴스 URL (예: `https://na30.salesforce.com` ) 또는 내 도메인 url (예:)을 사용할 수 있습니다 `https://myCompanyName.my.salesforce.com/` .

1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

1. 마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-salesforce/register-sources.png" alt-text="원본 등록 옵션" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 Salesforce를 검색 하 여 자산을 자동으로 식별 하 고 데이터를 분류 합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

Azure 부서의 범위는 Salesforce REST API 버전 41.0을 사용 하 여 '/v41.0/sobjects/(Global ' URI), ' sObject Basic Information ' URI (/v41.0/sobjects/sObject/) 및 ' SOQL Query ' URI (/v41.0/query?)와 같은 REST 요청을 비롯 한 메타 데이터를 추출 합니다.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. 관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 만들 수 있습니다.

1. **원본** 으로 이동합니다.

1. 등록 된 Salesforce 원본을 선택 합니다.

1. **+ 새 검사** 를 선택합니다.

1. 아래 세부 정보를 제공합니다.

    1. **이름**: 검사 이름

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **자격 증명**: 데이터 원본에 연결할 자격 증명을 선택합니다. 다음을 수행해야 합니다.
        * 자격 증명을 만드는 동안 **소비자 키** 를 선택 합니다.
        * 사용자 이름 입력 필드에 연결 된 앱이 모방 사용자의 사용자 이름을 입력 합니다.
        * 연결 된 앱이 모방 되는 사용자의 암호를 Azure Key Vault 비밀에 저장 합니다. 
            * 자체 호스팅 integration runtime 컴퓨터의 IP가 Salesforce에 설정 된 [조직의 신뢰할 수 있는 ip 범위](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) 내에 있는 경우 사용자의 암호만 제공 합니다.
            * 그렇지 않으면 암호 및 보안 토큰을 암호 값으로 연결 합니다. 보안 토큰은 신뢰할 수 없는 네트워크에서 Salesforce에 로그인 할 때 암호의 끝에 추가 해야 하는 자동으로 생성 된 키입니다. [보안 토큰을 가져오거나 다시 설정](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)하는 방법에 대해 자세히 알아보세요.
        * 연결 된 앱 정의에서 소비자 키를 제공 합니다. 연결 된 앱의 연결 된 앱 관리 페이지 또는 연결 된 앱 정의에서 찾을 수 있습니다.
        * Azure Key Vault 암호에 연결 된 앱 정의의 소비자 암호를 저장 합니다. 소비자 키와 함께 찾을 수 있습니다.

    1. **개체**: 검색 범위를 지정할 개체 이름 목록을 제공 합니다. 예들 들어 `object1; object2`입니다. 빈 목록은 사용 가능한 모든 개체를 검색 하는 것을 의미 합니다. 개체 이름을 와일드 카드 패턴으로 지정할 수 있습니다. `topic?`, `*topic*` 또는 `topic_?,*topic*`).

    1. **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검색할 Salesforce 원본의 크기에 따라 달라 집니다.

        > [!Note]
        > 일반적으로 1,000개 테이블당 1GB 메모리를 제공하세요.

        :::image type="content" source="media/register-scan-salesforce/scan.png" alt-text="Salesforce 스캔" border="true":::

1. **계속** 을 선택합니다.

1. **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)

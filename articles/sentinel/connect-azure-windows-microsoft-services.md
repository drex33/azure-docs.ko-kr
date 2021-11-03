---
title: Azure Sentinel을 Azure, Windows 및 Microsoft 서비스에 연결
description: Azure Sentinel을 Azure 및 Microsoft 365 클라우드 서비스와 Windows Server 이벤트 로그에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/18/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6f8a4d8c7d049241354a5cdeaf1613edfbe71b19
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131047576"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Azure Sentinel을 Azure, Windows, Microsoft 및 Amazon 서비스에 연결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel은 Azure 기반을 사용하여 많은 Azure 및 Microsoft 365 서비스, Amazon Web Services 및 다양한 Windows Server 서비스의 데이터 수집에 대한 기본 제공 서비스 간 지원을 제공합니다. 이러한 연결에는 몇 가지 다른 방법이 있으며 이 문서에서는 이러한 연결을 만드는 방법에 대해 설명합니다.

이 문서에서는 다음 유형의 커넥터에 대해 설명합니다.

- **API 기반** 연결
- **진단 설정** 연결(일부는 Azure Policy에서 관리)
- **Log Analytics 에이전트** 기반 연결

이 문서에서는 커넥터 그룹에 공통적인 정보를 제공합니다. 라이선스 필수 조건 및 데이터 저장을 위한 Log Analytics 표와 같이 각 커넥터에 고유한 정보는 함께 제공되는 [데이터 커넥터 참조](data-connectors-reference.md) 페이지를 참조하세요.

다음 통합은 더 독특하고 더 대중적이며 자체 문서와 함께 개별적으로 처리됩니다.

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Azure Defender](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows 보안 이벤트](connect-windows-security-events.md)
- [AWS(Amazon Web Services) CloudTrail](connect-aws.md)


## <a name="api-based-connections"></a>API 기반 연결

### <a name="prerequisites"></a>필수 조건

- Log Analytics 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- Azure Sentinel 작업 영역의 테넌트에 대한 전역 관리자 또는 보안 관리자 역할이 있어야 합니다.

### <a name="instructions"></a>Instructions

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 서비스를 선택한 다음, 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. **연결** 을 선택하여 서비스에서 Azure Sentinel로 이벤트 및/또는 알림 스트리밍을 시작합니다.

1. 커넥터 페이지에 **인시던트 만들기 - 권장됨!** 이라는 섹션이 있는 경우 경고에서 인시던트를 자동으로 만들려면 **사용** 을 선택합니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스 커넥터 섹션에 나타나는 테이블 이름을 사용하여 각 서비스의 데이터를 찾고 쿼리할 수 있습니다.

## <a name="diagnostic-settings-based-connections"></a>진단 설정 기반 연결

이 유형의 일부 커넥터 구성은 Azure Policy에서 관리합니다. 지침을 보려면 아래에서 **Azure Policy** 탭을 선택합니다. 이 유형의 다른 커넥터의 경우 **독립 실행형** 탭을 선택합니다.

# <a name="standalone"></a>[독립 실행형](#tab/SA)

### <a name="prerequisites"></a>필수 조건

Azure Sentinel로 데이터를 수집하려면:

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 사용 권한이 있어야 합니다.

### <a name="instructions"></a>Instructions

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 리소스 종류를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 링크를 선택하여 리소스 구성 페이지를 엽니다.

    원하는 유형의 리소스 목록이 표시되면 로그를 수집하려는 리소스에 대한 링크를 선택합니다.

1. 리소스의 탐색 메뉴에서 **진단 설정** 을 선택합니다.

1. 목록의 맨 아래에 있는 **+ 진단 설정 추가** 를 선택합니다.

1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력합니다.

    **Log Analytics에 보내기** 확인란을 선택합니다. 두 개의 새 필드가 아래에 표시됩니다. 관련 **구독** 및 **Log Analytics 작업 영역**(Azure Sentinel이 있는 위치)을 선택합니다.

1. 수집하려는 로그 및 메트릭 유형의 확인란을 선택합니다. [데이터 커넥터 참조](data-connectors-reference.md) 페이지의 리소스 커넥터 섹션에서 각 리소스 유형에 대한 권장 선택 사항을 참조하세요.

1. 화면 위쪽에서 **저장** 을 선택합니다.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>필수 조건

Azure Sentinel로 데이터를 수집하려면:

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 사용 권한이 있어야 합니다.

- Azure Policy를 사용하여 리소스에 로그 스트리밍 정책을 적용하려면 정책 할당 범위에 대한 Owner 역할이 있어야 합니다.

### <a name="instructions"></a>Instructions

이 유형의 커넥터는 Azure Policy를 사용하여 범위로 정의된 단일 유형의 리소스 컬렉션에 단일 진단 설정 구성을 적용합니다. 해당 리소스에 대한 커넥터 페이지 왼쪽의 **데이터 유형** 에서 지정된 리소스 유형에서 수집된 로그 유형을 볼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 리소스 종류를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 표시되는 확장기를 확장하고 **Azure Policy Assignment 마법사 시작** 단추를 선택합니다.

    정책 할당 마법사가 열리고 정책 이름이 미리 채워진 상태로 새 정책을 만들 준비가 됩니다.

    1. **기본** 탭에서 **범위** 아래의 세 점이 있는 단추를 선택하여 구독을 선택하고, 필요에 따라 리소스 그룹을 선택합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭에서 다음을 수행합니다.
       - **입력이 필요한 매개 변수만 표시** 확인란의 선택을 취소합니다.
       - **효과** 및 **설정 이름** 필드가 표시되면 그대로 유지합니다.
       - **Log Analytics 작업 영역** 드롭다운 목록에서 Azure Sentinel 작업 영역을 선택합니다.
       - 나머지 드롭다운 필드는 사용 가능한 진단 로그 형식을 나타냅니다. 수집하려는 모든 로그 형식을 “True”로 표시된 채로 둡니다.

    1. 정책은 나중에 추가되는 리소스에 적용됩니다. 기존 리소스에도 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 생성** 확인란에 표시합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

---

> [!NOTE]
>
> 이 데이터 커넥터 유형을 사용하면 지난 14일 동안 특정 시점에 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆에 연결 아이콘)가 *연결됨*(녹색)으로 표시됩니다. 데이터 수집 없이 14일이 경과되면 커넥터는 연결 끊김 상태로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지의 리소스 커넥터 섹션에 나타나는 테이블 이름을 사용하여 각 리소스 유형에 대한 데이터를 찾고 쿼리할 수 있습니다.

## <a name="log-analytics-agent-based-connections"></a>Log Analytics 에이전트 기반 연결

### <a name="prerequisites"></a>필수 조건

- Log Analytics 작업 영역과 로그를 수집할 컴퓨터가 포함된 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- Azure Sentinel 역할 외에 해당 작업 영역의 SecurityInsights(Azure Sentinel) 솔루션에 대한 **Log Analytics 기여자** 역할이 있어야 합니다.

### <a name="instructions"></a>Instructions

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 서비스(**DNS** 또는 **Windows 방화벽**)를 선택한 다음 **커넥터 페이지 열기** 를 선택합니다.

1. 로그를 생성하는 디바이스에 에이전트를 설치하고 온보딩합니다.

    | 머신 형식  | Instructions  |
    | --------- | --------- |
    | **Azure Windows VM의 경우** | 1. **에이전트를 설치할 위치 선택** 에서 **Azure Windows 가상 머신에 에이전트 설치** 를 확장합니다. <br><br>2. **Azure Windows Virtual Machine용 에이전트 다운로드 및 설치 >** 링크를 선택합니다. <br><br>3. **가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 선택한 다음 **연결** 을 선택합니다. 연결하려는 각 VM에 이 단계를 반복합니다. |
    | **기타 Windows 컴퓨터의 경우** | 1. **에이전트를 설치할 위치 선택** 에서 **Azure가 아닌 Windows 컴퓨터에 에이전트 설치** 를 확장합니다. <br><br>2. **비 Azure Windows 컴퓨터용 에이전트 다운로드 및 설치** 링크를 선택합니다.  <br><br>3. **에이전트 관리** 블레이드의 **Windows 서버** 탭에서 다음과 같이 32비트 또는 64비트 시스템용 **Windows 에이전트 다운로드** 링크를 적절하게 선택합니다.      |

1. DNS 또는 Windows 방화벽에 대해 **솔루션 설치** 단추를 선택합니다.

**DnsEvents**, **DnsInventory** 및 **WindowsFirewall** 테이블 이름을 각각 사용하여 DNS 및 Windows 방화벽에 대한 데이터를 찾고 쿼리할 수 있습니다. [데이터 커넥터 참조](data-connectors-reference.md) 페이지의 섹션에서 이 두 서비스 커넥터에 대한 정보와 기타 정보를 볼 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure, Microsoft 및 Windows 서비스와 Amazon Web Services를 Azure Sentinel에 연결하는 방법을 배웠습니다. 
- 일반적으로 [Azure Sentinel 데이터 커넥터](connect-data-sources.md)에 대해 알아봅니다.
- [Azure Sentinel 데이터 커넥터 찾기](data-connectors-reference.md).
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.

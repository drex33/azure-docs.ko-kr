---
title: 사용자 지정 로그 형식의 데이터를 Azure Sentinel로 수집 | Microsoft Docs
description: 사용자 지정 데이터 원본에서 데이터를 수집하고 Log Analytics 에이전트를 사용하여 Azure Sentinel로 수집합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d99785da00f277480505b417947781821df1caa2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123261202"
---
# <a name="collect-data-in-custom-log-formats-to-azure-sentinel-with-the-log-analytics-agent"></a>Log Analytics 에이전트를 사용하여 Azure Sentinel에 사용자 지정 로그 형식의 데이터 수집

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

많은 애플리케이션이 Windows 이벤트 로그 또는 Syslog 같은 표준 로깅 서비스 대신 텍스트 파일에 정보를 기록합니다. Log Analytics 에이전트를 사용하여 Windows 및 Linux 컴퓨터에서 비표준 형식의 텍스트 파일로 데이터를 수집할 수 있습니다. 수집된 데이터를 쿼리의 개별 필드로 구문 분석하거나 수집 중에 데이터를 개별 필드로 추출할 수 있습니다.

이 문서에서는 사용자 지정 로그 형식을 사용하여 Azure Sentinel에 데이터 원본을 연결하는 방법을 설명합니다. 이 방법을 사용하는 지원되는 데이터 커넥터에 대한 자세한 정보는 [데이터 커넥터 참조](data-connectors-reference.md)를 확인하세요.

[Azure Monitor의 모든 사용자 지정 로그 설명서](../azure-monitor/agents/data-sources-custom-logs.md)에 대해 알아봅니다.

Syslog와 마찬가지로 사용자 지정 로그 수집을 구성하는 단계는 다음의 두 단계로 구성됩니다.

- 로그를 생성하는 Linux 또는 Windows 컴퓨터에 Log Analytics 에이전트를 설치합니다.

- 애플리케이션의 로깅 설정을 구성합니다.

- Azure Sentinel내에서 Log Analytics 에이전트를 구성합니다.

## <a name="install-the-log-analytics-agent"></a>Log Analytics 에이전트 설치

로그를 생성하는 Linux 또는 Windows 컴퓨터에 Log Analytics 에이전트를 설치합니다.

> [!NOTE]
> 일부 공급 업체는 장치에 직접 설치하는 대신 별도의 로그 서버에 Log Analytics 에이전트를 설치하는 것이 좋습니다. [데이터 커넥터 참조](data-connectors-reference.md) 페이지 또는 제품의 자체 설명서에서 제품 섹션을 참조하세요.

커넥터에 Azure Sentinel의 데이터 커넥터 페이지가 있는지 여부에 따라 아래에서 적절한 탭을 선택합니다.

# <a name="from-a-specific-data-connector-page"></a>[특정 데이터 커넥터 페이지에서](#tab/DCG)

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 디바이스 유형을 선택한 다음 **커넥터 페이지 열기** 를 선택합니다.

1. 로그를 생성하는 장치에 에이전트를 설치하고 온보딩합니다. Linux 또는 Windows을 적절하게 선택합니다.

    | 머신 형식 | Instructions |
    | --------- | --------- |
    | **Azure Linux VM의 경우** | <ol><li>**Linux 에이전트 설치 위치 선택** 에서 **Azure Linux Virtual Machines에 에이전트 설치** 를 참조하세요.<br><br><li>**Azure Linux Virtual Machines용 에이전트 다운로드 및 설치 >** 링크를 선택합니다.<br><br><li>**가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 선택한 다음 **연결** 을 선택합니다. 연결하려는 각 VM에 이 단계를 반복합니다. |
    | **기타 Linux 컴퓨터의 경우** | <ol><li>**Linux 에이전트 설치 위치 선택** 에서 **비 Azure linux 컴퓨터에 에이전트 설치** 를 확장합니다.<br><br><li>**비 Azure Linux 컴퓨터용 에이전트 다운로드 및 설치>** 링크를 클릭합니다.<br><br><li>**에이전트 관리** 블레이드에서 **Linux 서버** 탭을 클릭한 다음 **Linux용 에이전트 다운로드 및 온보딩** 명령을 복사하여 Linux 컴퓨터에서 실행합니다.<br><br> Linux 에이전트 설치 파일의 로컬 복사본을 유지하려면 "에이전트 다운로드 및 등록" 명령 위의 **Linux 에이전트 다운로드** 링크를 선택합니다.|
    | **Azure Windows VM의 경우** | <ol><li>**Windows 에이전트 설치 위치 선택** 에서 **Azure Windows 가상 머신에 에이전트 설치** 를 확장합니다.<br><br><li>**Azure Windows Virtual Machines용 에이전트 다운로드 및 설치 >** 링크를 클릭합니다.<br><br><li>**가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 선택한 다음 **연결** 을 선택합니다. 연결하려는 각 VM에 이 단계를 반복합니다. |
    | **기타 Windows 컴퓨터의 경우** | <ol><li>**Windows 에이전트 설치 위치 선택** 에서 **비 Azure Windows 컴퓨터에 에이전트 설치** 를 확장합니다.<br><br><li>**비 Azure Windows 컴퓨터용 에이전트 다운로드 및 설치 >** 링크를 선택합니다.<br><br><li>**에이전트 관리** 블레이드의 **Windows 서버** 탭에서 32비트 또는 64비트 시스템에 대한 **Windows 에이전트 다운로드** 링크를 적절하게 선택합니다. |


# <a name="other-data-sources"></a>[기타 데이터 원본](#tab/CUS)

1. Azure Sentinel 탐색 메뉴에서 **설정** 을 선택한 다음, **작업 영역 설정** 탭을 선택합니다.

1. 로그를 생성하는 장치에 에이전트를 설치하고 온보딩합니다. Linux 또는 Windows을 적절하게 선택합니다.

    | 머신 형식 | Instructions |
    | --------- | --------- |
    | **Azure VM (Windows 또는 Linux)** | <ol><li>Log Analytics 작업 영역 탐색 메뉴에서 **가상 머신** 을 선택 합니다.<br><br><li>**가상 머신** 블레이드에서 에이전트를 설치할 가상 머신을 선택한 다음 **연결** 을 선택합니다.<br>연결하려는 각 VM에 이 단계를 반복합니다. |
    | **기타 Windows 또는 Linux 컴퓨터** | <ol><li>Log Analytics 작업 영역 탐색 메뉴에서 **에이전트 관리** 를 선택합니다.<br><br><li>**Windows 서버**, **Linux 서버** 탭을 적절히 선택합니다.<br><br><li>Windows의 경우 32비트 또는 64비트 시스템에 대한 **Download Windows 에이전트 다운로드** 링크를 적절히 선택 합니다. Linux의 경우 **Linux용 에이전트 다운로드 및 온보드** 명령을 복사하고 명령줄에서 실행하거나 **Linux 에이전트 다운로드** 링크를 선택하여 설치 파일의 로컬 복사본을 다운로드합니다. |

---

## <a name="configure-the-logs-to-be-collected"></a>수집할 로그 구성

많은 디바이스 유형에서 Azure Sentinel의 **데이터 커넥터** 페이지에 고유한 데이터 커넥터가 표시됩니다. 이러한 커넥터 중 일부에는 Azure Sentinel에서 로그 수집을 제대로 설정하기 위해 특별한 추가 지침이 필요합니다. 이 지침에는 Kusto 함수를 기반으로 하는 파서 구현이 포함될 수 있습니다. 

Azure Sentinel에 나열된 모든 커넥터는 포털의 해당 커넥터 페이지와 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 해당 섹션에 특정 지침을 표시합니다.

제품이 **데이터 커넥터** 페이지에 나열되지 않는 경우 장치에 대한 로깅을 구성하는 방법에 대한 지침은 공급업체의 설명서를 참조하세요.

## <a name="configure-the-log-analytics-agent"></a>Log Analytics 에이전트 구성

1. 커넥터 페이지에서 **작업 영역 사용자 지정 로그 구성 열기** 링크를 선택합니다.
    또는 Log Analytics 작업 영역 탐색 메뉴에서 **사용자 지정 로그** 를 선택합니다.

1. **사용자 지정** 탭에서 **사용자 지정 로그 추가** 를 선택합니다.

1. **샘플** 탭에서 디바이스에서 로그 파일의 샘플을 업로드합니다(예: access.log 또는 error.log). 그다음에 **다음** 을 선택합니다.

1. **레코드 구분 기호** 탭에서 레코드 구분 기호인 **새 줄** 또는 **타임스탬프** 를 선택하고(해당 탭의 지침 참조) **다음** 을 선택합니다.

1. **컬렉션 경로** 탭에서 Windows 또는 Linux의 경로 유형을 선택하고 구성에 따라 디바이스 로그의 경로를 입력합니다. 그다음에 **다음** 을 선택합니다.

1. 사용자 지정 로그에 이름을 로그하고 선택적으로 설명을 입력한 후 **다음** 을 선택합니다.  
    이름에 "_CL"로 끝내면 자동으로 추가되므로 이렇게 설정하지 마세요.


## <a name="find-your-data"></a>데이터 찾기

**로그** 에서 사용자 지정 로그 데이터를 쿼리하려면 쿼리 창에서 사용자 지정 로그에 지정한 이름을("_CL"로 끝남) 입력합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 로그 형식에서 데이터를 수집하여 Azure Sentinel로 수집하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.

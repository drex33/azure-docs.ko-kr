---
title: Azure Sentinel에 Windows Defender 방화벽 데이터 연결 | Microsoft Docs
description: Log Analytics 에이전트가 설치된 Windows 컴퓨터에서 방화벽 이벤트를 쉽게 스트리밍하려면 Azure Sentinel의 Windows 방화벽 커넥터를 사용하도록 설정합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 3af0977880adf900171dad6c518f0267a1d9d2a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535929"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>고급 보안이 포함된 Windows Defender 방화벽을 Azure Sentinel에 연결

[고급 보안이 포함된 Windows Defender 방화벽](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 커넥터를 사용하면 Azure Sentinel이 작업 영역에 있는 모든 Windows 컴퓨터의 고급 보안 로그를 사용하여 Windows Defender 방화벽을 쉽게 수집할 수 있습니다. 이 연결을 사용하면 통합 문서에서 Windows 방화벽 이벤트를 보고 분석하여 사용자 지정 경고를 만들고, 사용자 지정 경고를 통합하고, 조직의 네트워크에 대한 자세한 정보를 제공하고, 보안 작업 기능을 향상시킬 수 있습니다. 

이 솔루션은 Log Analytics 에이전트가 설치된 Windows 컴퓨터에서 Windows 방화벽 이벤트를 수집합니다. 

> [!NOTE]
> - 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.
>
> - Azure Security Center의 Azure Defender 경고가 이미 Azure Sentinel 작업 영역에 수집된 경우 이 커넥터를 통해 Windows 방화벽 솔루션을 사용하도록 설정할 필요가 없습니다. 그러나 이 기능을 사용하도록 설정 하면 중복된 데이터가 발생하지 않습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>사전 요구 사항

- 모니터링할 컴퓨터가 연결된 작업 영역에 대한 읽기 및 쓰기 사용 권한이 있어야 합니다.

- **Azure Sentinel** 역할 외에도 해당 작업 영역의 SecurityInsights 솔루션에 대해 **Log Analytics 참가자** 역할을 할당받아야 합니다. [자세히 알아보기](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>커넥터 사용 

1. Azure Sentinel 포털의 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 커넥터 갤러리에서 **Windows 방화벽** 을 선택하고 **커넥터 페이지 열기** 를 클릭합니다.

### <a name="instructions-tab"></a>지침 탭

- **Windows 컴퓨터가 Azure에 있는 경우:**

    1. **Azure Windows 가상 머신에 에이전트 설치** 를 클릭합니다.

    1. 나타나는 **Azure Windows Virtual Machine용 에이전트 다운로드 및 설치 >** 링크를 클릭합니다.

    1. **가상 머신** 목록에서 Azure Sentinel로 스트리밍할 Windows 컴퓨터를 선택합니다. (Windows VM만 표시되도록 OS 열 필터에서 **Windows** 를 선택할 수 있습니다).

    1. 해당 VM에 대해 열리는 창에서 **연결** 을 클릭합니다.

    1. **가상 머신** 창으로 돌아가서 연결하려는 다른 VM에 대해 이전 두 단계를 반복합니다. 완료되면 **Windows 방화벽** 창으로 돌아갑니다.

- **Windows 컴퓨터가 Azure VM이 아닌 경우:**

    1. **비 Azure Windows 컴퓨터에 에이전트 설치** 를 선택합니다.

    1. 나타나는 **비 Azure Windows 컴퓨터용 에이전트 다운로드 및 설치** 링크를 클릭합니다.

    1. **에이전트 관리** 창에서 필요에 따라 **Windows 에이전트 다운로드(64비트)** 또는 **Windows 에이전트 다운로드(32비트)** 를 선택합니다.

    1. **작업 영역 ID**, **기본 키**, 그리고 **보조 키** 문자열을 텍스트 파일에 복사합니다. 해당 파일과 다운로드한 설치 파일을 Windows 컴퓨터에 복사합니다. 설치 파일을 실행하고 메시지가 표시되면 설치하는 동안 텍스트 파일에 ID 및 키 문자열을 입력합니다.

    1. **Windows 방화벽** 창으로 돌아갑니다.

1. **솔루션 설치** 를 클릭합니다.

### <a name="next-steps-tab"></a>다음 단계 탭

- Windows 방화벽 로그 데이터에 대한 인사이트를 얻으려면 **Windows 방화벽** 데이터 커넥터와 함께 제공되는 사용 가능한 권장 통합 문서 및 쿼리 샘플을 참조하세요.

- **로그** 에서 Windows 방화벽 데이터를 쿼리하려면 쿼리 창에 **WindowsFirewall** 을 입력합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
 
Windows 방화벽 로그는 로컬 로그 파일이 용량에 도달할 때만 Azure Sentinel로 전송되므로 로그를 기본 크기인 4096KB로 두면 수집 대기 시간이 높아질 가능성이 높습니다. 로그 파일 크기를 줄여 대기 시간을 낮출 수 있습니다. 자세한 내용은 [Windows 방화벽 로그 구성](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)을 참조하세요.

> [!NOTE]
>
> - 로그 크기가 작을수록 수집 대기 시간이 줄어들지만 로컬 시스템의 성능에 부정적인 영향을 줄 수도 있습니다.
> 
> - 데이터 수집 구성에서는 데이터를 수집하기 전에 로그 파일에 최소 1000개의 새 행이 필요합니다. 따라서 로그 파일 크기는 100(100)KB 이상으로 설정해야 합니다. 이렇게 하면 1000줄이 누적됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Windows 방화벽을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.

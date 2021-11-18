---
title: Microsoft 센티널에 가상 데스크톱 커넥트 Windows Microsoft Docs
description: Windows 가상 데스크톱 데이터를 Microsoft 센티널에 연결 하는 방법을 알아봅니다.
author: batamig
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1cf9e4f7109e7ff84b99364ba77b4bfcb07a1a64
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751671"
---
# <a name="connect-windows-virtual-desktop-data-to-microsoft-sentinel"></a>Microsoft 센티널에 가상 데스크톱 데이터 Windows 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널을 사용 하 여 wvd (Windows 가상 데스크톱) 환경을 모니터링할 수 있는 방법을 설명 합니다.

예를 들어 Windows Virtual Desktop 환경을 모니터링하면 조직의 보안 상태를 유지 관리하면서 가상화된 데스크톱을 사용하여 더 많은 원격 작업을 제공할 수 있습니다.

## <a name="windows-virtual-desktop-data-in-microsoft-sentinel"></a>Microsoft 센티널의 가상 데스크톱 데이터 Windows

Microsoft 센티널의 가상 데스크톱 데이터 Windows에는 다음과 같은 형식이 포함 됩니다.


|데이터  |Description  |
|---------|---------|
|**Windows 이벤트 로그**     |  wvd 환경의 Windows 이벤트 로그는 wvd 환경 외부에 있는 다른 Windows 컴퓨터의 이벤트 로그를 Windows 하는 것과 동일한 방식으로 Microsoft 센티널 사용 Log Analytics 작업 영역으로 스트리밍됩니다. <br><br>Windows 머신에 Log Analytics 에이전트를 설치하고 Log Analytics 작업 영역으로 전송할 Windows 이벤트 로그를 구성합니다.<br><br>자세한 내용은 다음을 참조하세요.<br>- [Windows 컴퓨터에 Log Analytics 에이전트 설치](../azure-monitor/agents/agent-windows.md)<br>- [Log Analytics 에이전트를 사용하여 Windows 이벤트 로그 데이터 원본 수집](../azure-monitor/agents/data-sources-windows-events.md)<br>- [Windows 보안 이벤트 연결](connect-windows-security-events.md)       |
|**엔드포인트용 Microsoft Defender 경고**     |  Windows Virtual Desktop에 대해 엔드포인트용 Defender를 구성하려면 다른 Windows 엔드포인트와 동일한 절차를 사용합니다. <br><br>자세한 내용은 다음을 참조하세요. <br>- [엔드포인트용 Microsoft Defender 배포 설정](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Microsoft 365 Defender에서 Microsoft 센티널로 데이터 커넥트](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop 진단**     | Windows Virtual Desktop 진단은 Windows Virtual Desktop PaaS 서비스의 기능으로, Windows Virtual Desktop 역할이 할당된 사용자가 서비스를 사용할 때마다 정보를 로깅합니다. <br><br>각 로그에는 작업과 관련된 Windows Virtual Desktop 역할에 대한 정보, 세션 중에 표시되는 오류 메시지, 테넌트 정보, 사용자 정보 등이 포함됩니다. <br><br>진단 기능은 사용자 및 관리 작업 모두에 대해 활동 로그를 만듭니다. <br><br>자세한 내용은 [Windows Virtual Desktop의 진단 기능에 Log Analytics 사용](../virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)을 참조하세요.        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Windows Virtual Desktop 데이터 연결

Microsoft 센티널로 수집 Windows 가상 데스크톱 데이터를 시작 하려면 Windows 가상 데스크톱 설명서의 지침을 사용 합니다.

자세한 내용은 [Log Analytics 작업 영역으로 Windows Virtual Desktop 데이터 푸시](../virtual-desktop/diagnostics-log-analytics.md)를 참조하세요.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 Log Analytics 데이터에 대해 Microsoft 센티널에서 쿼리를 실행 합니다.

예를 들어 [Windows Virtual Desktop 설명서](../virtual-desktop/diagnostics-log-analytics.md)의 샘플 쿼리를 참조하세요.


Microsoft 센티널은 또한 **일반**  >  **로그**  >  **WINDOWS 가상 데스크톱** 영역에서 기본 제공 쿼리를 제공 합니다.

[![Microsoft 센티널의 가상 데스크톱 기본 제공 쿼리를 Windows 합니다. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png) ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>다음 단계


자세한 내용은 [Azure Monitor for Windows Virtual Desktop 용어집](../virtual-desktop/azure-monitor-glossary.md)을 참조하세요.

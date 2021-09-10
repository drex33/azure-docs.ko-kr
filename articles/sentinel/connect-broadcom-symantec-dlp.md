---
title: Azure Sentinel에 Broadcom Symantec DLP(데이터 손실 방지) 데이터 연결 | Microsoft Docs
description: Broadcom Symantec DLP 데이터 커넥터를 사용하여 Azure Sentinel에 Symantec DLP 로그를 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Symantec DLP 데이터를 보고, 경고를 생성하고, 조사를 개선합니다.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: be7decff244bffa329f46109a5ab2fca7772bee1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695211"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Azure Sentinel에 Broadcom Symantec DLP(데이터 손실 방지) 연결

> [!IMPORTANT]
> Broadcom Symantec DLP 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서는 Broadcom Symantec DLP 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Broadcom Symantec DLP 데이터 커넥터를 사용하면 Symantec DLP 로그를 Azure Sentinel에 쉽게 연결하여, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 기능을 개선할 수 있습니다. 이 기능을 통해 이동할 조직의 정보에 대한 인사이트를 얻고 보안 작업 기능이 향상됩니다. Broadcom Symantec DLP와 Azure Sentinel을 통합하면 CEF 형식의 Syslog, Linux 기반 로그 전달자 및 Log Analytics 에이전트를 사용하게 됩니다. 또한 Kusto 함수를 기반으로 하는 사용자 지정 파서를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Azure Sentinel에 Broadcom Symantec DLP 로그 보내기

Azure Sentinel로 로그를 가져오려면 Broadcom Symantec DLP 어플라이언스가 CEF 형식의 Syslog 메시지를 Linux 기반 로그 전달 서버(rsyslog 또는 syslog-ng 실행)로 전송하도록 구성합니다. 이 서버에는 Log Analytics 에이전트가 설치되어 있으며, 에이전트는 Azure Sentinel 작업 영역에 로그를 전달합니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Broadcom Symantec DLP(미리 보기)** 커넥터를 선택한 다음, **커넥터 페이지를 엽니다**.

1. **지침** 탭의 **구성** 아래에 있는 지침을 따르세요.

    1. **1. Linux Syslog 에이전트 구성** - 실행 중인 로그 전달자가 아직 없는 경우 또는 다른 로그 전달자가 필요한 경우 이 단계를 수행합니다. 자세한 지침 및 설명은 Azure Sentinel 설명서에서 [1단계: 로그 전달자 배포](connect-cef-agent.md)를 참조하세요.

    1. **2. Syslog 에이전트에 Symantec DLP 로그 전달** - Broadcom의 지침에 따라 [를 구성](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)합니다. 이 구성은 다음 요소를 포함해야 합니다.
        - 로그 대상 – 로그 전달 서버의 호스트 이름 및/또는 IP 주소
        - 프로토콜 및 포트 – TCP 514(다른 프로토콜 및 포트가 권장되는 경우 로그 전달 서버의 syslog 데몬에서 병렬 변경을 수행해야 함)
        - 로그 형식 – CEF
        - 로그 유형 – 모두 사용 가능 또는 모두 해당

    1. **3. 연결 유효성 검사** - 커넥터 페이지에서 명령을 복사하고 로그 전달자에서 실행하여 데이터 수집을 확인합니다. 자세한 지침 및 설명은 Azure Sentinel 설명서에서 [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)를 참조하세요.

        로그가 Log Analytics에 표시될 때까지 최대 20분 정도 소요될 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정된 후 *CommonSecurityLog* 테이블에서 **Azure Sentinel** 섹션의 **로그** 에 데이터가 표시됩니다.

이 데이터 커넥터는 정상적으로 작동하기 위해 Kusto 함수를 기반으로 하는 파서를 사용합니다. **SymantecDLP** [Kusto 함수 별칭을 만들려면](https://aka.ms/sentinel-symantecdlp-parser) 다음 지침을 따릅니다.

그런 다음 Log Analytics에서 Broadcom Symantec DLP 데이터를 쿼리하려면 쿼리 창의 맨 위에 `SymantecDLP`를 입력합니다.

몇 가지 유용한 쿼리 샘플은 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Symantec DLP를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
---
title: Azure Arc 지원 서버 VM 확장 문제 해결
description: 이 문서에서는 Azure Arc 지원 서버에서 발생하는 Azure VM 확장 문제를 해결하는 방법을 설명합니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 690b92389d86ca497801af79c6930677c19178fe
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390205"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Arc 지원 서버 VM 확장 문제 해결

이 문서에서는 Arc 지원 서버에서 Azure VM 확장을 배포 또는 제거를 시도하는 동안 발생할 수 있는 문제를 해결하는 방법에 대한 정보를 제공합니다. 일반 정보는 [Azure VM 확장 관리 및 사용](./manage-vm-extensions.md)을 참조하세요.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

확장 배포 상태에 대한 데이터는 Azure Portal에서 검색할 수 있습니다.

다음 문제 해결 단계는 모든 VM 확장에 적용됩니다.

1. 게스트 에이전트 로그를 확인하려면 Windows의 경우 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs`에서, Linux의 경우 `/var/lib/GuestConfig/ext_mgr_logs`에서 확장이 프로비전되었을 때의 작업을 살펴봅니다.

2. Windows의 경우 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>`에서 특정 확장에 대한 확장 로그를 확인하여 자세한 내용을 알아봅니다. 확장 출력은 `/var/lib/GuestConfig/extension_logs`에서 Linux에 설치된 각 확장에 대한 파일에 기록됩니다.

3. 오류 코드, 알려진 문제 등에 대한 확장 관련 설명서 문제 해결 섹션을 확인합니다. 각 확장에 대한 추가 문제 해결 정보는 확장에 대한 개요의 **문제 해결 및 지원** 섹션에서 찾을 수 있습니다. 여기에는 로그에 기록된 오류 코드에 대한 설명이 포함됩니다. 확장 문서는 [확장 테이블](manage-vm-extensions.md#extensions)에 연결되어 있습니다.

4. 시스템 로그를 확인합니다. 단독 패키지 관리자 액세스가 필요한 다른 애플리케이션의 장기 실행 설치 등 확장을 방해할 수 있는 다른 작업을 확인합니다.

## <a name="troubleshooting-specific-extension-scenarios"></a>특정 확장 시나리오 문제 해결

### <a name="vm-insights"></a>VM 인사이트

- Azure Arc 지원 서버에 대해 VM Insights를 사용하도록 설정하면 종속성 및 Log Analytics 에이전트가 설치됩니다. 저속 머신이거나 네트워크 연결 속도가 느린 머신에서는 설치 프로세스 중 시간 초과가 표시될 수 있습니다. Microsoft는 이 조건을 해결하는 데 도움이 되도록 연결된 머신 에이전트에서 이 문제를 해결하는 단계를 수행합니다. 중간에 설치를 다시 시도하면 성공할 수 있습니다.

### <a name="log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트

- Log Analytics 에이전트 버전 1.13.9(해당 확장 버전은 1.13.15)가 Azure Arc 지원 서버의 리소스 ID로 업로드된 데이터를 올바르게 표시하지 않습니다. 로그가 서비스로 전송되기는 하지만, **로그** 또는 **정보** 를 선택한 후 선택한 사용 가능한 서버에서 데이터를 보려고 하면 데이터가 반환되지 않습니다. Azure Monitor Logs 또는 작업 영역으로 범위가 지정된 VM용 Azure Monitor에서 쿼리를 실행하여 해당 데이터를 볼 수 있습니다.

- 일부 배포판은 Linux용 Log Analytics 에이전트에서 현재 지원되지 않습니다. 에이전트를 설치하려면 Python 2를 포함한 추가 종속성이 필요합니다. [여기](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)에서 지원 매트릭스 및 필수 구성 요소를 검토합니다.

- 상태 메시지의 오류 코드 52는 종속성이 누락되었음을 나타냅니다. 출력 및 로그에서 누락된 종속성에 대한 자세한 내용을 확인합니다.

- 설치에 실패하는 경우 확장에 대한 개요에서 **문제 해결 및 지원** 섹션을 검토합니다. 대부분의 경우 상태 메시지에는 오류 코드가 포함되어 있습니다. Linux용 Log Analytics 에이전트의 경우 이 VM 확장에 대한 일반적인 문제 해결 정보와 함께 상태 메시지가 [여기](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)에 설명되어 있습니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

- [Microsoft Q&A](/answers/topics/azure-arc.html)를 통해 Azure 전문가의 답변을 받습니다.

- 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.

- Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.

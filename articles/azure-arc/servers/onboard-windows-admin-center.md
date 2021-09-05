---
title: Windows Admin Center에서 하이브리드 컴퓨터를 Azure에 연결
description: 이 문서에서는 Windows Admin Center에서 Azure Arc 지원 서버를 사용하여 에이전트를 설치하고 머신을 Azure에 연결하는 방법을 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1abfe2e69a8f3e7b2ab1abcc2d34c79877b750d3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538682"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Windows Admin Center에서 하이브리드 컴퓨터를 Azure에 연결

일단의 단계를 수동으로 수행하여 환경에서 Azure Arc 지원 서버를 하나 이상의 Windows 컴퓨터에 사용하도록 설정할 수 있습니다. 또는 [Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is)를 사용하여 연결된 컴퓨터 에이전트를 배포하고, 이 도구 외부에서 아무 단계도 수행하지 않고 온-프레미스 서버를 등록할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Arc 지원 서버 - [필수 구성 요소](agent-overview.md#prerequisites)를 검토하고 구독, Azure 계정 및 리소스에서 요구 사항을 충족하는지 확인합니다.

* Windows Admin Center - 요구 사항을 검토하여 [Azure 통합을 배포하고 구성](/windows-server/manage/windows-admin-center/azure/azure-integration)할 [환경을 준비](/windows-server/manage/windows-admin-center/deploy/prepare-environment)합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

* 관리하려는 대상 Windows 서버는 Azure에 액세스할 수 있도록 인터넷에 연결되어 있어야 합니다.

### <a name="security"></a>보안

이 배포 방법을 사용하려면 대상 Windows 컴퓨터 또는 서버에 대해 에이전트를 설치하고 구성할 수 있는 관리자 권한이 있어야 습니다. 또한 [**게이트웨이 사용자**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) 역할의 구성원이어야 합니다.

## <a name="deploy"></a>배포:

Arc 지원 서버를 사용하여 Windows 서버를 구성하려면 다음 단계를 수행합니다.

1. Windows Admin Center에 로그인합니다.

1. **개요** 페이지의 연결 목록에 있는 연결된 Windows 서버 목록에서 연결할 서버를 선택합니다.

1. 왼쪽 창에서 **Azure 하이브리드 서비스** 를 선택합니다.

1. **Azure 하이브리드 서비스** 에서 **Azure 서비스 검색** 을 선택합니다.

1. **Azure 서비스 검색** 페이지의 **Azure 정책 및 솔루션을 활용하여 Azure Arc에서 서버 관리** 아래에서 **설정** 을 선택합니다.

1. **Settings\서버용 Azure Arc** 페이지에서 Azure에 인증하라는 메시지가 표시되면 **시작** 을 선택합니다.

1. **Azure에 서버 연결** 페이지에서 다음을 제공합니다.

    1. **Azure 구독** 드롭다운 목록에서 Azure 구독을 선택합니다.
    1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하여 새 리소스 그룹을 만들거나 **리소스 그룹** 드롭다운 목록 아래에서 컴퓨터를 등록하고 관리할 기존 리소스 그룹을 선택합니다.
    1. **지역** 드롭다운 목록에서 서버 메타데이터를 저장할 Azure 지역을 선택합니다.
    1. 컴퓨터 또는 서버에서 프록시 서버를 통해 통신하여 인터넷에 연결하는 경우 **프록시 서버 사용** 옵션을 선택합니다. 이 구성을 사용하여 에이전트는 HTTP 프로토콜을 사용하는 프록시 서버를 통해 통신합니다. 컴퓨터에서 프록시 서버와 통신하는 데 사용할 프록시 서버 IP 주소 또는 이름 및 포트 번호를 지정합니다.

1. **설정** 을 선택하여 Azure Arc 지원 서버를 사용하여 Windows 서버 구성을 계속 진행합니다.

Windows 서버에서 Azure에 연결하고, 연결된 컴퓨터 에이전트를 다운로드하고, Azure Arc 지원 서버에 등록합니다. 진행률을 추적하려면 메뉴에서 **알림** 을 선택합니다.

연결된 컴퓨터 에이전트 설치를 확인하려면 Windows Admin Center의 왼쪽 창에서 [**이벤트**](/windows-server/manage/windows-admin-center/use/manage-servers#events)를 선택하여 애플리케이션 이벤트 로그에서 *MsiInstaller* 이벤트를 검토합니다.

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 지원 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://portal.azure.com)에서 머신을 확인합니다.

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="머신 연결 성공" border="false":::

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM 인사이트로](../../azure-monitor/vm/vminsights-enable-policy.md)로 모니터링 사용 등).
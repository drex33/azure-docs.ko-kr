---
title: Microsoft 센티널에 Azure Stack 허브 가상 머신 등록 | Microsoft Docs
description: 이 문서에서는 Azure Stack 허브 가상 머신에서 Azure Monitor, 업데이트 및 구성 관리 가상 머신 확장을 프로 비전 하 고 Microsoft 센티널을 사용 하 여 모니터링을 시작 하는 방법을 보여 줍니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 570f297cfdd16aaac18f36d11d989c1dfa732e6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518910"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-microsoft-sentinel"></a>Microsoft 센티널로 허브 가상 컴퓨터 Azure Stack 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft 센티널을 사용 하면 Azure에서 실행 되는 Vm과 Azure Stack 허브를 한 곳에서 모니터링할 수 있습니다. Microsoft 센티널에 Azure Stack 컴퓨터를 온보드 하려면 먼저 기존 Azure Stack 허브 가상 컴퓨터에 가상 컴퓨터 확장을 추가 해야 합니다. 

Azure Stack Hub 머신을 연결한 후 데이터 기반 인사이트를 표시하는 대시보드 갤러리에서 대시보드를 선택합니다. 해당 대시보드는 필요에 따라 쉽게 사용자 지정할 수 있습니다.

## <a name="add-the-virtual-machine-extension"></a>가상 머신 확장 추가 

Azure Stack Hub에서 실행 중인 가상 머신에 **Azure Monitor, 업데이트 및 구성 관리** 가상 머신 확장을 추가합니다. 

1. 새 브라우저 탭에서 [Azure Stack Hub 포털](/azure-stack/user/azure-stack-use-portal#access-the-portal)에 로그인합니다.

1. **Virtual machines** 페이지로 이동 하 여 Microsoft 센티널로 보호할 가상 머신을 선택 합니다. Azure Stack Hub에서 가상 머신을 만드는 방법에 대한 자세한 내용은 [Azure Stack Hub 포털을 사용하여 Windows 서버 VM 만들기](/azure-stack/user/azure-stack-quick-windows-portal) 또는 [Azure Stack Hub 포털을 사용하여 Linux 서버 VM 만들기](/azure-stack/user/azure-stack-quick-linux-portal)를 참조하세요.

1. **확장** 을 섡택합니다. 이 가상 머신에 설치된 가상 머신 확장 목록이 표시됩니다.

1. **추가** 탭을 선택합니다. **새 리소스** 메뉴 블레이드가 열리고 사용 가능한 가상 머신 확장 목록이 표시됩니다. 

1. **Azure Monitor, Update 및 Configuration Management** 확장을 선택하고 **만들기** 를 선택합니다. **확장 설치** 구성 페이지가 열립니다.

   ![Azure Monitor, 업데이트 및 구성 관리 설정](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > **Azure Monitor, 업데이트 및 구성 관리** 확장 목록이 마켓플레이스에 표시되지 않으면 해당 확장을 사용할 수 있도록 Azure Stack Hub 운영자에게 연락하세요.

1. Microsoft 센티널 메뉴에서 **작업 영역 설정** , **고급** 을 차례로 선택 하 고 **작업 영역 ID** 및 **작업 영역 키 (기본 키)** 를 복사 합니다. 

1. Azure Stack Hub **확장 설치** 창에서 표시된 필드에 붙여 넣고 **확인** 을 선택합니다.

1. 확장 설치가 완료되면 상태가 **프로비저닝 성공** 으로 표시됩니다. 가상 머신이 Microsoft 센티널 포털에 표시 되는 데 최대 1 시간이 걸릴 수 있습니다.

Windows용 에이전트 설치 및 구성에 대한 자세한 내용은 [Windows 컴퓨터 연결](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)을 참조하세요.

Linux 에이전트 문제 해결 방법은 [Azure Log Analytics Linux 에이전트 문제 해결](../azure-monitor/agents/agent-linux-troubleshoot.md)을 참조하세요.

Azure의 Microsoft 센티널 포털의 **Virtual Machines** 에는 모든 vm 및 컴퓨터의 상태와 함께 개요가 표시 됩니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure Stack Hub 포털을 통해 가상 머신에서 확장을 제거할 수 있습니다.

확장을 제거하려면:

1. **Azure Stack Hub 포털** 을 엽니다.

1. **가상 머신** 페이지로 이동하고 확장을 제거할 가상 머신을 선택합니다.

1. **확장**, **Microsoft.EnterpriseCloud.Monitoring** 확장을 차례로 선택합니다.

1. **제거** 를 선택하고 선택을 확인합니다.

## <a name="next-steps"></a>다음 단계

Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
- [일반적인 이벤트 형식 어플라이언스](connect-common-event-format.md) 에서 Microsoft 센티널로 데이터를 스트리밍합니다.

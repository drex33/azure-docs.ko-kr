---
title: Azure Bastion의 세션 모니터링 및 관리
description: 진행 중인 세션을 선택하고 강제로 연결을 끊거나 삭제하는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: 6b0df7ae98c3718ddfadf9e13354b4f961585c98
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540439"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure Bastion의 세션 모니터링 및 관리

Bastion 서비스가 가상 네트워크에 프로비저닝되고 배포되면 이를 사용하여 이 가상 네트워크의 모든 VM에 원활하게 연결할 수 있습니다. 사용자가 작업에 연결하면 Azure Bastion을 사용하여 원격 세션을 모니터링하고 신속한 관리 작업을 수행할 수 있습니다. Azure Bastion 세션 모니터링을 통해 어떤 사용자가 어떤 VM에 연결되어 있는지 확인할 수 있습니다. 사용자가 연결한 IP, 연결한 시간 및 연결한 시기를 보여줍니다. 세션 관리 환경을 통해 진행 중인 세션을 선택하고 세션을 강제로 연결 해제하거나 삭제하여 진행 중인 세션에서 사용자의 연결을 끊을 수 있습니다.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>원격 세션 모니터링

1. [Azure Portal](https://portal.azure.com)에서 Azure Bastion 리소스로 이동하고 Azure Bastion 페이지에서 **세션** 을 선택합니다.

   ![스크린샷은 세션이 선택된 Azure Portal 메뉴 설정을 보여줍니다.](./media/session-monitoring/sessions.png)
2. **세션** 페이지의 오른쪽에서 진행 중인 원격 세션을 볼 수 있습니다.

   ![세션 보기](./media/session-monitoring/view-session.png)
3. **새로 고침** 을 선택하여 업데이트된 원격 세션 목록을 확인합니다. 새로 고침을 선택하면 Azure Bastion이 최신 모니터링 정보를 가져와서 포털에서 새로 고칩니다.

   ![스크린샷은 새로 고침 옵션이 강조 표시된 Azure Bastion를 사용하는 세션을 보여 줍니다.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>진행 중인 원격 세션 삭제 또는 강제 연결 끊기

세션 세트를 선택하고 강제로 연결을 끊을 수 있습니다. 다음 단계에서는 원격 세션을 삭제하는 방법을 보여 줍니다.

1. Azure Bastion 리소스로 이동하고 Azure Bastion 페이지에서 **세션** 을 선택합니다.

   ![스크린샷은 설정 아래에서 세션이 선택된 Azure Portal을 보여줍니다.](./media/session-monitoring/navigate.png)
2. 세션을 선택하면 원격 세션의 목록이 표시됩니다.

   ![세션 목록](./media/session-monitoring/list.png)
3. 특정 원격 세션을 선택한 후, 세션 행의 오른쪽 끝에 있는 줄임표(...)를 선택한 다음, **삭제** 를 선택합니다.

   ![스크린샷에는 삭제 아이콘이 선택된 세션을 표시하는 Azure Portal이 표시됩니다.](./media/session-monitoring/delete.png)
4. 삭제를 선택하면 원격 세션의 연결이 끊기고 원격 세션에 "연결이 끊어짐"이라는 메시지가 사용자에게 표시됩니다.

   ![스크린샷은 연결 끊김 상태를 확인하는 메시지를 보여 줍니다.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.

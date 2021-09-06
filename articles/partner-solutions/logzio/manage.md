---
title: Logz.io를 사용하여 Azure 통합 관리 - Azure 파트너 솔루션
description: Logz.io를 사용하여 Azure 통합을 관리하는 방법을 알아봅니다.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.openlocfilehash: e86b42694276c23f4d38809c9b40a7f92e39fa64
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531006"
---
# <a name="manage-the-logzio-integration-in-azure"></a>Azure에서 Logz.io 통합 관리

이 문서에서는 Logz.io를 사용하여 Azure 통합 설정을 관리하는 방법을 설명합니다.

## <a name="resource-overview"></a>리소스 개요

Logz.io **개요** 화면에는 다음 항목을 포함한 리소스 세부 정보가 표시됩니다.

- **리소스 그룹**
- **위치**
- **구독**
- **태그**
- **Logz.io SSO**
- **청구 기간**

SSO(Single Sign-On) 옵트인 또는 옵트아웃 여부에 따라 **Logz.io SSO** 연결이 다릅니다.

- SSO에 옵트인한 경우:

    :::image type="content" source="./media/manage/sso-opt-in.png" alt-text="Single Sign-On 옵트인":::

- SSO를 옵트아웃한 경우:

    :::image type="content" source="./media/manage/sso-opt-out.png" alt-text="Single Sign-On 옵트아웃":::

## <a name="reconfigure-rules-for-logs-and-metrics"></a>로그 및 메트릭에 대한 규칙 다시 구성

구성 규칙을 변경하려면 **Logz 구성** 에서 **로그 및 메트릭** 을 선택합니다. 자세한 내용은 [로그 구성](./create.md#configure-logs)을 참조하세요.

:::image type="content" source="./media/manage/logs-metrics.png" alt-text="로그 및 메트릭 구성":::

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

진단 설정을 구성하려면 리소스를 선택합니다. 왼쪽 창에서 **진단 설정** 을 선택합니다.

**대상 세부 정보** 열에서 **파트너 솔루션에 보내기** 옵션을 선택하여 Logz.io를 대상으로 선택합니다. Logz 리소스를 만든 후에만 이 옵션을 사용할 수 있습니다.

:::image type="content" source="./media/manage/configure-diagnostics.png" alt-text="진단 설정을 구성합니다.":::

## <a name="view-monitored-resources"></a>모니터링되는 리소스 보기

구성된 비즈니스 규칙을 통해 로그를 Logz.io로 보내는 리소스의 목록을 확인하려면 **Logz 구성** 으로 이동하고 **모니터링되는 리소스** 를 선택합니다.

:::image type="content" source="./media/manage/monitored-resources.png" alt-text="모니터링되는 리소스 구성":::

리소스 종류, 리소스 그룹 이름, 위치, 리소스에서 로그를 전송하고 있는지를 기준으로 목록을 필터링할 수 있습니다.

다음은 **모니터링되는 리소스** 화면에 표시되는 열 설명입니다.

- **리소스 이름**: Azure 리소스 이름입니다.
- **리소스 종류**: Azure 리소스 종류입니다.
- **리소스 그룹**: Azure 리소스의 리소스 그룹 이름을 표시합니다.
- **지역**: Azure 리소스의 지역이나 위치를 표시합니다.
- **Logz로 로그 전송**: 리소스가 로그를 Logz.io로 보내고 있는지 여부를 나타냅니다. 리소스가 로그를 보내지 않는 경우 이 필드는 로그가 Logz.io에 전송되지 않는 적절한 이유를 보여줍니다. 다음과 같은 이유가 있을 수 있습니다.
  - 리소스에서 로그를 보낼 수 없음: 모든 리소스 종류 및 [여기에서 정의한 로그 범주](../../azure-monitor/essentials/resource-logs-categories.md)에 대한 Azure 리소스만 로그를 Logz.io에 보내도록 구성할 수 있습니다.
  - 진단 설정 5개 제한에 도달함: 각 Azure 리소스에는 진단 설정이 최대 5개까지 있을 수 있습니다. 자세한 내용은 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.
  - 오류: 오류로 인해 로그를 Logz.io에 보낼 수 없습니다.
  - 로그가 구성되지 않음: 적절한 리소스 태그가 있는 리소스만 로그를 Logz.io에 보낼 수 있습니다. [로그 구성](./create.md#configure-logs)을 참조하세요.
  - 지역이 지원되지 않음: 현재 Azure 리소스가 로그를 Logz.io로 보내지 않는 지역에 있습니다.

Azure 리소스의 태그 목록을 편집하려면 **태그 편집** 을 선택합니다. 새 태그를 추가하거나 기존 태그를 삭제할 수 있습니다.

## <a name="monitor-vm-using-logzio-agent"></a>Logz.io 에이전트를 사용하여 VM 모니터링

가상 머신(VM)에 Logz.io 에이전트를 확장으로 설치할 수 있습니다. **Logz.io 구성** 으로 이동하고 **가상 머신 에이전트** 를 선택합니다. 이 화면에는 구독에 있는 모든 VM의 목록이 표시됩니다.

VM마다 다음 데이터가 표시됩니다.

- **리소스 이름**: 가상 머신 이름입니다.
- **리소스 종류**: Azure 리소스 종류입니다.
- **리소스 그룹**: Azure 리소스의 리소스 그룹 이름을 표시합니다.
- **지역**: Azure 리소스의 지역이나 위치를 표시합니다.
- **에이전트 버전**: Logz.io 에이전트 버전 번호입니다.
- **확장 상태**: Logz.io 에이전트가 VM에 **설치됐는지** 또는 **설치되지 않았는지** 여부를 나타냅니다.
- **Logz로 로그 전송**: Logz.io 에이전트가 로그를 Logz.io로 보내는지 여부를 지정합니다.

:::image type="content" source="./media/manage/vm-agent.png" alt-text="가상 머신 에이전트":::

### <a name="install-vm-agent"></a>VM 에이전트 설치

VM 에이전트를 설치하려면 다음 단계를 수행합니다.

1. Logz.io 에이전트를 설치할 VM을 선택한 다음, **에이전트 설치** 를 선택합니다.

   포털에 기본 인증으로 에이전트를 설치할지 확인하는 메시지가 표시됩니다.

1. **확인** 을 선택하여 설치를 시작합니다.

   Azure는 에이전트가 설치 및 프로비저닝될 때까지 상태를 **설치 중** 으로 표시합니다.

   Logz.io 에이전트가 설치되면 상태가 **설치됨** 으로 변경됩니다.

1. Logz.io 에이전트가 설치되었는지 확인하려면 VM을 선택하고 **확장** 창으로 이동합니다.

> [!NOTE]
> VM 에이전트가 설치된 후 VM이 중지되면 **Logz로 로그 전송** 열에 해당 VM의 **보내지 않음** 이 표시됩니다.

### <a name="uninstall-vm-agent"></a>VM 에이전트 제거

**가상 머신 에이전트** 에서 VM의 Logz.io 에이전트를 제거할 수 있습니다. VM과 **에이전트 제거** 를 선택합니다.

## <a name="create-logzio-sub-account"></a>Logz.io 하위 계정을 만듭니다.

Logz.io 계정 관리자만 하위 계정을 만들 수 있습니다. 기본적으로 Logz.io 계정 작성자가 관리자로 설정됩니다. Logz.io 계정에 로그인하여 다른 사용자에게 관리자 권한을 부여합니다.

자세한 내용은 Logz.io 웹 사이트의 [Logz.io 하위 계정](https://logz.io/blog/diving-deeper-logz-io-sub-accounts/)을 참조하세요.

**개요** > **하위 계정 추가** 단추를 사용하여 Logz.io 하위 계정을 만들 수 있습니다.

:::image type="content" source="./media/manage/create-sub-account-overview.png" alt-text="개요에서 Logz.io 하위 계정을 만듭니다.":::

다른 방법은 **Logz 구성** 으로 이동하고 **하위 계정 추가** 를 선택하는 것입니다. 프롬프트에 따라 하위 계정을 설정하고 하위 계정을 통해 로그 전달을 사용합니다.

:::image type="content" source="./media/manage/create-sub-account-logz.png" alt-text="Logz 구성에서 Logz.io 하위 계정을 만듭니다.":::

## <a name="delete-logzio-sub-account"></a>Logz.io 하위 계정 삭제

1. Logz.io 리소스의 경우 **Logz 구성** 과 **Logz 하위 계정** 을 선택합니다.
1. 삭제하려는 하위 계정을 선택합니다.
1. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage/delete-sub-account-1.png" alt-text="Logz 구성에서 Logz.io 하위 계정을 삭제합니다.":::

1. _예_ 를 입력하여 Logz.io 리소스 삭제 여부를 확인합니다.
1. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage/delete-sub-account-2.png" alt-text="Logz.io 하위 계정 삭제를 확인합니다.":::

로그는 더 이상 Logz.io로 전송되지 않으며 Logz.io에 대한 모든 청구가 중지됩니다.

## <a name="delete-logzio-account"></a>Logz.io 계정 삭제

1. Logz.io 리소스에서 **개요** 와 **삭제** 를 선택합니다.
1. Logz.io 리소스 삭제 여부를 확인합니다.
1. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage/delete-resource-1.png" alt-text="개요에서 Logz.io 리소스를 삭제합니다.":::

1. _예_ 를 입력하여 Logz.io 리소스 삭제 여부를 확인합니다.
1. **삭제** 를 선택합니다.

    :::image type="content" source="./media/manage/delete-resource-2.png" alt-text="Logz.io 리소스 삭제를 확인합니다.":::

> [!NOTE]
> 주 계정의 **삭제** 옵션은 주 계정에 매핑된 모든 하위 계정이 이미 삭제된 경우에만 활성화됩니다. 하위 계정을 삭제하는 방법에 대한 자세한 내용은 [Logz.io 하위 계정 삭제](#delete-logzio-sub-account)를 참조하세요.

리소스를 삭제하면 로그가 더 이상 Logz.io로 전송되지 않으며 Logz에 대한 모든 청구는 Azure Marketplace을 통해 중지됩니다.

## <a name="get-support"></a>지원 받기

**지원 + 문제 해결** 과 **새 지원 요청** 에서 Azure Logz.io 통합에 대한 지원을 문의할 수 있습니다. 이 옵션을 선택하면 [Logz.io 포털](https://app.logz.io/)로 이동합니다. 채팅 내 지원을 사용하거나 [help@logz.io](mailto:help@logz.io)로 이메일을 보냅니다.

## <a name="next-steps"></a>다음 단계

- 통합 관련 문제를 해결하려면 [문제 해결](troubleshoot.md)을 참조하세요.

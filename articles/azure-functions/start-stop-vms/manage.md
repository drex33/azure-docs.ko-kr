---
title: VM v2 시작/중지(미리 보기) 관리
description: 이 문서에서는 VM v2 시작/중지(미리 보기) 기능을 통해 관리되는 Azure VM의 상태를 모니터링하고 기타 관리 작업을 수행하는 방법을 설명합니다.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 40c3d2dba3d41c7651846d09d01dd7afdce15af9
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967064"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>VM v2 시작/중지(미리 보기)를 관리하는 방법

## <a name="azure-dashboard"></a>Azure 대시보드

VM v2 시작/중지(미리 보기)에는 VM에 대한 관리 범위 및 최근 작업을 이해하는 데 도움이 되는 [대시보드](../../azure-monitor/visualizations.md#azure-dashboards)가 포함됩니다. Azure VM에서 수행되는 각 작업의 상태를 빠르고 쉽게 확인하는 방법입니다. 각 타일의 시각화는 로그 쿼리를 기반으로 하며 쿼리를 보려면 타일의 오른쪽 모서리에 있는 **로그에서 열기 블레이드** 옵션을 선택합니다. 그러면 Azure Portal에서 [로그 분석](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) 도구가 열리며 여기에서 쿼리를 평가하고 사용자 지정 [로그 경고](../../azure-monitor/alerts/alerts-log.md), 사용자 지정 [통합 문서](../../azure-monitor/visualize/workbooks-overview.md) 등의 요구 사항을 지원하도록 수정할 수 있습니다.

지정된 시각화에서 **새로 고침** 아이콘을 클릭하거나 전체 대시보드를 새로 고쳐 요청 시 수동 새로 고침 옵션을 사용하면 대시보드의 각 타일이 표시하는 로그 데이터가 매시간 새로 고침됩니다.

로그 기반 대시보드를 사용하는 방법에 대한 자세한 내용은 다음 [자습서](../../azure-monitor/visualize/tutorial-logs-dashboards.md)를 참조하세요.

> [!NOTE]
> 배포 중에 문제가 발생하면 VM v2 시작/중지(미리 보기)를 사용할 때 문제가 발생하거나 관련 질문이 있는 경우 [GitHub](https://github.com/microsoft/startstopv2-deployments/issues)에서 문제를 제출할 수 있습니다. 이 미리 보기 버전에서는 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)에서 Azure 지원 인시던트를 제출할 수 없습니다. 

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

VM v2 시작/중지(미리 보기)가 배포된 후 메일 알림을 변경하려면 배포 중에 생성된 작업 그룹을 수정하면 됩니다.

1. Azure Portal에서 **모니터**, **경고** 로 차례로 이동합니다. **작업 관리** 를 선택합니다.

1. **작업 관리** 페이지에서 **StartStopV2_VM_Notication** 이라는 작업 그룹을 선택합니다.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="작업 그룹 페이지의 스크린샷":::

1. **StartStopV2_VM_Notification** 페이지에서 메일/SMS/푸시/음성 알림 옵션을 수정할 수 있습니다. 다른 작업을 추가하거나 기존 구성을 이 작업 그룹에 업데이트한 다음 **확인** 을 클릭하여 변경 내용을 저장합니다.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="업데이트된 예제 메일 주소를 보여 주는 메일/SMS/푸시/음성 페이지의 스크린샷":::

    작업 그룹에 대한 자세한 정보는 [작업 그룹](../../azure-monitor/alerts/action-groups.md)을 참조하세요.

다음 스크린샷은 기능이 가상 머신을 종료할 때 전송되는 예제 메일입니다.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="기능이 가상 머신을 종료할 때 전송되는 예제 메일의 스크린샷":::

## <a name="next-steps"></a>다음 단계

VM을 관리하는 동안 문제를 처리하려면 [VM v2 시작/중지 문제 해결](troubleshoot.md)(미리 보기)를 참조하세요.
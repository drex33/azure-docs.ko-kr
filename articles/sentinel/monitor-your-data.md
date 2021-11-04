---
title: Azure Sentinel에서 Azure Monitor 통합 문서를 사용하여 데이터 시각화 | Microsoft Docs
description: Azure Sentinel에서 통합 문서를 사용하여 데이터를 시각화하는 방법을 알아봅니다.
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
ms.date: 04/04/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ef77535810671c0f442aa90eade688778c381750
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064490"
---
# <a name="use-azure-monitor-workbooks-to-visualize-and-monitor-your-data"></a>Azure Monitor 통합 문서를 사용하여 데이터 시각화 및 모니터링

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel에 [데이터 원본을 연결](quickstart-onboard.md)한 후에는 사용자 지정 대시보드를 만들 때 다양한 기능을 제공하는 Azure Monitor 통합 문서의 Azure Sentinel 채택을 사용하여 데이터를 시각화하고 모니터링할 수 있습니다. 통합 문서가 Azure Sentinel에 다르게 표시되는 반면, [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법을 확인하는 것이 유용할 수 있습니다. Azure Sentinel을 사용하면 데이터에 대한 사용자 지정 통합 문서를 만들 수 있으며 데이터 원본을 연결하는 즉시 데이터를 신속하게 파악할 수 있는 기본 제공 통합 문서 템플릿도 제공됩니다.

이 문서에서는 Azure Sentinel에서 데이터를 시각화하는 방법을 설명합니다.

> [!div class="checklist"]
> * 기본 제공 통합 문서 사용
> * 새 통합 문서 만들기

## <a name="prerequisites"></a>사전 요구 사항

Azure Sentinel 작업 영역의 리소스 그룹에 대한 **통합 문서 읽기 권한자** 또는 **통합 문서 기여자** 권한 이상이 있어야 합니다.

> [!NOTE]
> Azure Sentinel에서 볼 수 있는 통합 문서는 Azure Sentinel 작업 영역의 리소스 그룹 내에 저장되고 생성된 작업 영역에 의해 태그가 지정됩니다.

## <a name="use-built-in-workbooks"></a>기본 제공 통합 문서 사용

1. **통합 문서** 로 이동한 다음, **템플릿** 을 선택하여 Azure Sentinel 기본 제공 통합 문서의 전체 목록을 확인합니다. 

    연결한 데이터 형식과 관련된 항목을 확인하기 위해 관련 데이터를 Azure Sentinel로 이미 스트리밍한 경우 각 통합 문서의 **필수 데이터 형식** 필드에 녹색 확인 표시 옆에 있는 데이터 형식이 나열됩니다.

    [ ![통합 문서로 이동](media/tutorial-monitor-data/access-workbooks.png) ](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. **템플릿 보기** 를 선택하여 데이터로 채워진 템플릿을 확인합니다.

1. 통합 문서를 편집하려면 **저장** 을 선택한 다음, 템플릿의 JSON 파일을 저장할 위치를 선택합니다.

   > [!NOTE]
   > 그러면 관련 템플릿을 기반으로 Azure 리소스가 생성되고 데이터가 아닌 통합 문서의 JSON 파일이 저장됩니다.


1. **저장된 통합 문서 보기** 를 선택합니다. 

    [ ![통합 문서 보기](media/tutorial-monitor-data/workbook-graph.png) ](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    통합 문서 도구 모음에서 **편집** 단추를 선택하여 필요에 따라 통합 문서를 사용자 지정합니다. 선택을 마치면 **저장** 을 선택하여 변경 내용을 저장합니다.

    자세한 내용은 [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법을 참조하세요.

> [!TIP]
> 통합 문서를 복제하려면 **편집** 을 선택한 다음, **다른 이름으로 저장** 을 선택하여 동일한 구독과 리소스 그룹 아래에 다른 이름으로 저장합니다.
> 복제된 통합 문서는 **내 통합 문서** 탭 아래에 표시됩니다.
>
## <a name="create-new-workbook"></a>새 통합 문서 만들기

1. **통합 문서** 로 이동한 다음, **통합 문서 추가** 를 선택하여 새 통합 문서를 처음부터 만듭니다.

    [ ![새 통합 문서](media/tutorial-monitor-data/create-workbook.png) ](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. 통합 문서를 편집하려면 **편집** 을 선택한 다음, 필요에 따라 텍스트, 쿼리 및 매개 변수를 추가합니다. 통합 문서를 사용자 지정하는 방법에 대한 자세한 내용은 [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/visualize/workbooks-overview.md) 방법을 참조하세요. 

1. 쿼리를 빌드하는 경우 **데이터 원본** 이 **로그** 로 설정되고 **리소스 종류** 가 **Log Analytics** 로 설정되었는지 확인한 다음, 관련 작업 영역을 선택합니다. 

1. 통합 문서를 만든 후 통합 문서를 저장하여 Azure Sentinel 작업 영역의 구독과 리소스 그룹 아래에 저장해야 합니다.

1. 조직의 다른 사용자가 통합 문서를 사용하도록 하려면 **저장 위치** 에서 **공유 보고서** 를 선택합니다. 이 통합 문서를 사용자만 사용할 수 있도록 하려면 **내 보고서** 를 선택합니다.

1. 작업 영역에서 통합 문서 간에 전환하려면 통합 문서의 도구 모음에서 **열기** ![통합 문서를 열기 위한 아이콘](./media/tutorial-monitor-data/switch.png)을 선택합니다. 화면이 전환할 수 있는 다른 통합 문서의 목록으로 전환됩니다.

    열려는 통합 문서를 선택합니다.

    [ ![통합 문서 전환](media/tutorial-monitor-data/switch-workbooks.png) ](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>통합 문서 데이터 새로 고침

통합 문서를 새로 고쳐서 업데이트된 데이터를 표시합니다. 도구 모음에서 다음 옵션 중 하나를 선택합니다.

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **새로 고침** - 통합 문서 데이터를 수동으로 새로 고칩니다.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **자동 새로 고침** - 통합 문서를 구성된 간격으로 자동으로 새로 고치도록 설정합니다.

    - 지원되는 자동 새로 고침 간격의 범위는 **5분** 에서 **1일** 사이입니다.

    - 통합 문서를 편집하는 동안 자동 새로 고침이 일시 중지되며, 편집 모드에서 보기 모드로 다시 전환할 때마다 간격이 다시 시작됩니다.

    - 데이터를 수동으로 새로 고치는 경우에도 자동 새로 고침 간격이 다시 시작됩니다.

    > [!TIP]
    > 기본적으로 자동 새로 고침은 꺼져 있습니다. 성능을 최적화하기 위해 자동 새로 고침은 통합 문서를 닫을 때마다 꺼지며 백그라운드에서 실행되지 않습니다. 다음에 통합 문서를 열 때 필요에 따라 자동 새로 고침을 다시 켭니다.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>통합 문서 인쇄 또는 PDF로 저장

통합 문서를 인쇄하거나 PDF로 저장하려면 통합 문서 제목 오른쪽에 있는 옵션 메뉴를 사용합니다.

1. 옵션 > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **콘텐츠 인쇄** 를 선택합니다. 
2. 인쇄 화면에서 필요에 따라 인쇄 설정을 조정하거나 **PDF로 저장** 을 선택하여 로컬로 저장합니다.

예:

[ ![통합 문서 인쇄 또는 PDF로 저장](media/whats-new/print-workbook.png) ](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>통합 문서를 삭제하는 방법

저장된 통합 문서(저장된 템플릿 또는 사용자 지정된 통합 문서)를 삭제하려면 통합 문서 페이지에서 삭제하려는 저장된 통합 문서를 선택하고 **삭제** 를 선택합니다. 그러면 저장된 통합 문서가 제거됩니다.

> [!NOTE]
> 이렇게 하면 통합 문서 리소스뿐만 아니라 템플릿의 모든 변경 내용도 제거됩니다. 원래 템플릿은 사용 가능한 상태로 유지됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 통합 문서를 사용하여 Azure Sentinel에서 데이터를 시각화하는 방법을 알아보았습니다.

위협에 대한 응답을 자동화하는 방법에 대해 알아보려면 [Azure Sentinel에서 자동화된 위협 응답 설정](tutorial-respond-threats-playbook.md)을 참조하세요.

인기 있는 기본 제공 통합 문서에 대한 자세한 내용은 [일반적으로 사용되는 Azure Sentinel 통합 문서](top-workbooks.md)를 참조하세요. 

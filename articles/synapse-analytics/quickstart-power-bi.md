---
title: '빠른 시작: Synapse 작업 영역에 Power BI 작업 영역 연결'
description: 이 가이드의 단계에 따라 Azure Synapse Analytics 작업 영역에 Power BI 작업 영역을 연결합니다.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.custom: mode-other
ms.openlocfilehash: 9e2bbe6529b0bd3ce5551ff9d41b4b474495ab04
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063395"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>빠른 시작: Synapse 작업 영역에 Power BI 작업 영역 연결

이 빠른 시작에서는 Azure Synapse Analytics 작업 영역에 Power BI 작업 영역을 연결하여 Synapse Studio에서 새 Power BI 보고서 및 데이터 세트를 만드는 방법에 대해 설명합니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Synapse 작업 영역 및 연결된 스토리지 계정 만들기](quickstart-create-workspace.md)
- [Power BI Professional 또는 Premium 작업 영역](/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Synapse 작업 영역에 Power BI 작업 영역 연결

1. Synapse Studio에서 시작하여 **관리** 를 클릭합니다.

    ![Synapse Studio에서 관리를 클릭합니다.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. **외부 연결** 에서 **연결된 서비스** 를 클릭합니다.

    ![강조 표시된 연결된 서비스입니다.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. **+새로 만들기** 를 클릭합니다.

    ![+ 새 연결된 서비스가 강조 표시됩니다.](media/quickstart-link-powerbi/new-highlighted.png)

4. **Power BI** 를 클릭하고 **계속** 을 클릭합니다.

    ![Power BI 연결된 서비스를 표시합니다.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. 연결된 서비스의 이름을 입력하고 드롭다운 목록에서 작업 영역을 선택합니다.

    ![Power BI 연결된 서비스 설정을 표시합니다.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. **만들기** 를 클릭합니다.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Synapse Studio에서 Power BI 작업 영역 보기

작업 영역이 연결되면 Synapse Studio에서 Power BI 데이터 세트를 찾아보고, 새 Power BI 보고서를 편집/만들 수 있습니다.

1. **개발** 을 클릭합니다.

    ![Synapse Studio에서 개발을 클릭합니다.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Power BI 및 사용하려는 작업 영역을 확장합니다.

    ![Power BI 및 작업 영역을 확장합니다.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

**개발** 탭의 맨 위에 있는 **+** 를 클릭하여 새 보고서를 만들 수 있습니다. 보고서 이름을 클릭하여 기존 보고서를 편집할 수 있습니다. 저장된 변경 내용은 Power BI 작업 영역에 다시 기록됩니다.

![Power BI 보고서를 보고 편집합니다.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>다음 단계

[Azure Storage에 저장된 파일에 대한 Power BI 보고서 만들기](sql/tutorial-connect-power-bi-desktop.md)에 대해 자세히 알아보세요.

---
title: Azure Backup 보고서 메일 보내기
description: 메일을 통해 정기적으로 보고서를 받을 수 있는 자동화된 작업 만들기
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: dd5e31663262463449df4377329edc6604ffb691
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235984"
---
# <a name="email-azure-backup-reports"></a>Azure Backup 보고서 메일 보내기

Backup 보고서에서 사용할 수 있는 **보고서 메일 보내기** 기능을 사용하면 자동화된 작업을 만들어 메일을 통해 정기적으로 보고서를 받을 수 있습니다. 이 기능은 사용자가 입력한 내용을 기반으로 선택한 LA(Log Analytics) 작업 영역에서 데이터를 쿼리하는 논리 앱을 Azure 환경에 배포하는 방식으로 작동합니다. [논리 앱과 앱의 가격 책정에 대한 자세한 정보를 알아봅니다](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>시작하기

Backup 보고서를 통해 메일 작업을 구성하려면 다음 단계를 수행합니다.

1.  **Backup 센터**  > **Backup 보고서** 로 이동하여 **보고서 메일 보내기** 탭을 클릭합니다.
2.  다음 정보를 지정해서 작업을 만듭니다.
    * **작업 세부 정보** - 만들 논리 앱의 이름, 구독, 리소스 그룹, 그리고 앱을 만들어야 할 위치입니다. 논리 앱은 여러 구독, 리소스 그룹 및 위치(보고서 필터 섹션에서 선택됨)에서 데이터를 쿼리할 수 있지만 단일 구독, 리소스 그룹 및 위치의 컨텍스트에서 만들어집니다.
    * **내보낼 데이터** - 내보내고자 하는 탭입니다. 탭마다 단일 작업 앱 만들기 및 단일 작업을 이용해 모든 탭을 메일로 보내기 중 하나의 작업을 **모든 탭** 옵션을 선택해서 수행할 수 있습니다.
    * **메일 옵션**: 메일 빈도, 수신자 메일 ID, 그리고 메일 제목입니다.

    ![메일 탭](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  **제출** 및 **확인** 을 클릭하면 논리 앱이 만들어집니다. 쉽게 찾을 수 있도록 **UsedByBackupReports: true** 태그를 사용하여 논리 앱, 그리고 관련된 API 연결을 만듭니다. 아래 섹션에 설명된 대로 논리 앱을 성공적으로 실행하려면 일회성 권한 부여 단계를 수행해야 합니다.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Azure Monitor 로그 및 Office 365에 대한 연결 권한 부여

논리 앱은 LA 작업 영역을 쿼리하는 데 [azuremonitorlogs](/connectors/azuremonitorlogs/) 커넥터를 사용하고 메일을 보내는 데 [Office365 Outlook](/connectors/office365connector/) 커넥터를 사용합니다. 이러한 두 커넥터에 일회성 권한 부여를 수행해야 합니다. 
 
권한 부여를 수행하려면 다음 단계를 따라합니다.

1.  Azure Portal의 **Logic Apps** 로 이동합니다.
2.  만든 논리 앱의 이름을 검색한 후 리소스로 이동합니다.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  **API 연결** 메뉴 항목을 클릭합니다.

    ![API 연결](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  두 개의 연결이 `<location>-azuremonitorlogs` 및 `<location>-office365` 형식으로 표시됩니다. 즉, _eastus-azuremonitorlogs_ 와 _eastus-office365_ 가 표시됩니다.
5.  이러한 각 연결로 이동하고 **API 연결 편집** 메뉴 항목을 선택합니다. 표시되는 화면에서 **권한 부여** 를 선택하고 권한 부여가 완료되면 연결을 저장합니다.

    ![연결 권한 부여](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  권한 부여 후에 논리 앱이 작동하는지 테스트하려면 논리 앱으로 다시 이동하고, **개요** 를 열어 위쪽 창에서 **트리거 실행** 을 선택하여 메일이 성공적으로 생성되는지 여부를 테스트할 수 있습니다.

## <a name="contents-of-the-email"></a>메일의 콘텐츠

* 포털에 표시된 모든 차트와 그래프는 메일에서 인라인 콘텐츠로 사용할 수 있습니다. Backup 보고서 표시된 정보에 대해 [자세히 알아보세요.](configure-reports.md)
* 포털에 표시된 그리드는 메일에서 *.csv 첨부 파일로 사용할 수 있습니다.
* 메일에 표시되는 데이터는 메일 작업을 만들 때 보고서에서 사용자가 선택한 모든 보고서 수준 필터를 사용합니다.
* **백업 인스턴스 이름**, **정책 이름** 등의 탭 수준 필터는 적용되지 않습니다. 이에 대한 유일한 예외는 **보존 최적화** 그리드로, **최적화** 탭에 있습니다. 최적화 탭에는 **일간**, **주간**, **월간** 그리고 **연간** RP 보존 필터가 적용됩니다.
* 시간 범위와 집계 형식(차트의 경우)은 보고서의 사용자 시간 범위 선택 영역에 따라 결정됩니다. 예를 들어 시간 범위 선택 영역이 최근 60일(주간 집계 형식으로 변환)이고 메일 빈도가 매일이면, 수신자는 지난 60일 동안 발생한 데이터를 포괄하는 차트가 포함된 메일을 매일 받게 되며 주간 수준에서 데이터를 집계합니다.

## <a name="troubleshooting-issues"></a>문제 해결

논리 앱을 성공적으로 배포한 후에도 예상대로 메일을 받지 못하는 경우 아래 단계에 따라 구성 문제를 해결할 수 있습니다.

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>시나리오 1: 성공 메일과 오류 메일 모두 수신하지 않음

* 이 문제는 Outlook API 커넥터에 권한이 부여되지 않은 경우에 발생할 수 있습니다. 연결에 권한을 부여하려면 위에 제공된 권한 부여 단계를 따르세요.

* 논리 앱을 만드는 동안 잘못된 메일 수신자를 지정한 경우에도 이 문제가 발생할 수 있습니다. 메일 수신자를 올바르게 지정했는지 확인하려면 Azure Portal에서 논리 앱으로 이동해 논리 앱 디자이너를 열고 메일 단계를 선택하여 올바른 메일 ID를 사용하고 있는지 확인합니다.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>시나리오 2: 논리 앱이 완료될 때까지 실행되지 않았음을 알리는 오류 메일을 수신함

이 문제를 해결하려면 다음을 수행합니다.
1.  Azure Portal에서 논리 앱으로 이동합니다.
2.  **개요** 화면 아래쪽에 **실행 기록** 섹션이 보입니다. 최근 실행에서 열고 워크플로의 실패한 단계를 볼 수 있습니다. 가능한 원인은 다음과 같습니다.
    * **Azure Monitor 로그 커넥터에 권한이 부여되지 않았습니다**: 이 문제를 해결하려면 위에 제공된 권한 부여 단계를 따르세요.
    * **LA 쿼리의 오류**: 사용자 고유의 쿼리로 논리 앱을 사용자 지정한 경우에는 LA 쿼리에서 오류가 발생하여 논리 앱이 실패할 수 있습니다. 관련된 단계를 선택하고 쿼리의 비정상 실행의 원인이 된 오류를 볼 수 있습니다.

문제가 지속되면 Microsoft 지원에 문의하세요.

## <a name="next-steps"></a>다음 단계
[Backup 보고서에 대한 자세한 정보를 알아봅니다](./configure-reports.md)
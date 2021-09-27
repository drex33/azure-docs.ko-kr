---
title: Azure Automation 감시자 태스크로 업데이트된 파일 추적
description: 이 문서에서는 폴더에 생성된 새 파일을 감시하도록 Azure Automation 계정에 감시자 태스크를 만드는 방법을 알아봅니다.
services: automation
ms.subservice: process-automation
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 0a4b62d60c255d79eaf0042aa075b216d362dbce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758506"
---
# <a name="track-updated-files-with-a-watcher-task"></a>감시자 태스크를 사용하여 업데이트된 파일 추적

Azure Automation은 감시자 태스크를 사용하여 이벤트를 찾고 PowerShell Runbook을 사용하여 작업을 트리거합니다. 감시자 태스크에는 감시자 및 작업의 두 부분이 포함됩니다. 감시자 Runbook은 감시자 태스크에 정의된 간격으로 실행되며 작업 Runbook에 데이터를 출력합니다.

> [!NOTE]
> 감시자 태스크는 Azure 중국 Vianet 21에서 지원되지 않습니다.

> [!IMPORTANT]
> 2020년 5월부터는 Azure Logic Apps를 사용하여 이벤트를 모니터링하고 반복 태스크를 예약하고 작업을 트리거할 수 있습니다. [Azure Logic Apps를 사용하여 반복적인 자동화 태스크, 프로세스 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조하세요.

이 문서에서는 새 파일이 디렉터리에 추가되는 시기를 모니터링하는 감시자 작업을 생성하는 과정을 안내합니다. 다음 방법을 알아봅니다.

* 감시자 Runbook 가져오기
* Automation 변수 만들기
* 작업 Runbook 만들기
* 감시자 태스크 만들기
* 감시자 트리거
* 출력 검사

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음 항목이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자 및 작업 Runbook과 감시자 태스크를 포함하는 [Automation 계정](./index.yml)
* 감시자 작업이 실행되는 [하이브리드 Runbook 작업자](automation-hybrid-runbook-worker.md)
* PowerShell runbooks PowerShell 워크플로 Runbook은 감시자 태스크에서 지원되지 않습니다.

## <a name="import-a-watcher-runbook"></a>감시자 Runbook 가져오기

이 문서에서는 디렉터리에서 새 파일을 찾기 위해 **디렉터리에서 새 파일을 찾는 Watcher Runbook** 이라는 Watcher Runbook을 사용합니다. 감시자 Runbook은 폴더의 파일에 마지막으로 쓴 것으로 알려진 시간을 검색하고 해당 워터마크보다 최근 버전의 파일을 찾습니다.

다음 단계를 수행하여 포털에서 이 Runbook을 Automation 계정으로 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Automation 계정** 을 검색하여 선택합니다. 
1. **Automation 계정** 페이지의 목록에서 Automation 계정의 이름을 선택합니다.
1. 왼쪽 창의 **프로세스 자동화** 에서 **Runbook 갤러리** 를 선택합니다.
1. **원본** 드롭다운 목록에서 **GitHub** 가 선택되어 있는지 확인합니다.
1. **Watcher Runbook** 을 검색합니다.
1. **디렉터리에서 새 파일을 찾는 Watcher Runbook** 을 선택하고 세부 정보 페이지에서 **가져오기** 를 선택합니다.
1. Runbook 이름과 설명(선택 사항)을 입력하고 **확인** 을 클릭하여 Runbook을 Automation 계정으로 가져옵니다. 창의 오른쪽 위에 있는 창에 **가져오기 성공** 메시지가 표시됩니다.
1. 왼쪽 창에서 Runbook을 선택하면 가져온 Runbook이 지정한 이름 아래 목록에 나타납니다.
1. Runbook을 클릭하고 Runbook 세부 정보 페이지에서 **편집** 을 선택하고 **게시** 를 선택합니다. 메시지가 표시되면 **예** 를 클릭하여 Runbook을 게시합니다.

[Azure Automation GitHub 조직](https://github.com/azureautomation)에서 Runbook을 다운로드할 수도 있습니다.

1. [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-runbook-that-looks-for-new-files-in-a-directory#watcher-runbook-that-looks-for-new-files-in-a-directory)에 대한 Azure Automation GitHub 조직 페이지로 이동합니다.
1. GitHub에서 Runbook을 다운로드하려면 페이지 오른쪽에서 **코드** 를 선택하고 **ZIP 다운로드** 를 선택하여 전체 코드를 zip 파일로 다운로드합니다.
1. 콘텐츠를 추출하고 [Runbook을 가져옵니다](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

## <a name="create-an-automation-variable"></a>Automation 변수 만들기

[Automation 변수](./shared-resources/variables.md)는 위의 Runbook이 각 파일에서 읽고 저장하는 타임스탬프를 저장하는 데 사용됩니다.

1. **공유 리소스** 아래에서 **변수** 를 선택하고 **+ 변수 추가** 를 클릭합니다.
1. 이름에 **Watch-NewFileTimestamp** 를 입력합니다.
1. 형식에서 **DateTime** 을 선택합니다. 기본값은 현재 날짜 및 시간입니다.

   :::image type="content" source="./media/automation-watchers-tutorial/create-new-variable.png" alt-text="새 변수 블레이드를 만드는 스크린샷.":::

1. **만들기** 를 클릭하여 Automation 변수를 만듭니다.

## <a name="create-an-action-runbook"></a>작업 Runbook 만들기

작업 Runbook은 감시자 Runbook에서 전달된 데이터에 대한 작업을 수행하기 위해 감시자 태스크에서 사용됩니다. [Azure Automation GitHub 조직](https://github.com/azureautomation)의 Azure Portal에서 미리 정의된 작업 Runbook을 가져와야 합니다.

Azure Portal에서 이 Runbook을 Automation 계정으로 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Automation 계정** 을 검색하여 선택합니다. 
1. **Automation 계정** 페이지의 목록에서 Automation 계정의 이름을 선택합니다.
1. 왼쪽 창의 **프로세스 자동화** 에서 **Runbook 갤러리** 를 선택합니다.
1. **원본** 드롭다운 목록에서 **GitHub** 가 선택되어 있는지 확인합니다.
1. **감시자 작업** 을 검색한 후 **감시자 Runbook에 의해 트리거되는 이벤트를 처리하는 감시자 작업** 을 선택하고 **가져오기** 를 클릭합니다.
1. 가져오기 페이지에서 Runbook 이름을 변경하고 **확인** 을 클릭하여 Runbook을 가져올 수도 있습니다. 브라우저의 오른쪽 위에 있는 알림 창에 **가져오기 성공** 메시지가 표시됩니다.
1. Automation 계정 페이지로 이동하고 왼쪽에 있는 **Runbook** 을 클릭합니다. 이전 단계에서 지정한 이름 아래에 새 Runbook이 나열됩니다. Runbook을 클릭하고 Runbook 세부 정보 페이지에서 **편집** 을 선택하고 **게시** 를 선택합니다. 메시지가 표시되면 **예** 를 클릭하여 Runbook을 게시합니다.

[Azure Automation GitHub 조직](https://github.com/azureautomation)에서 다운로드하여 작업 Runbook을 만들려면 다음을 수행합니다.

1. Azure Automation GitHub 조직 페이지의 [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook)로 이동합니다.
1. GitHub에서 Runbook을 다운로드하려면 페이지 오른쪽에서 **코드** 를 선택하고 **ZIP 다운로드** 를 선택하여 전체 코드를 zip 파일로 다운로드합니다.
1. 콘텐츠를 추출하고 [Runbook을 가져옵니다](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

## <a name="create-a-watcher-task"></a>감시자 태스크 만들기

이 단계에서는 이전 섹션에서 정의한 감시자 및 작업 Runbook을 참조하는 감시자 태스크를 구성합니다.

1. Automation 계정으로 이동하여 **프로세스 자동화** 아래에서 **감시자 태스크** 를 선택합니다.
1. 감시자 태스크 페이지를 선택하고 **+ 감시자 태스크 추가** 를 클릭합니다.
1. 이름으로 **WatchMyFolder** 를 입력합니다.

1. **감시자 구성**, **Watch-NewFile** Runbook을 차례로 선택합니다.

1. 매개 변수에 대해 다음 값을 입력합니다.

   * **FOLDERPATH** - 새 파일을 만들 Hybrid Runbook Worker의 폴더입니다(예: **d:\examplefiles**).
   * **EXTENSION** - 구성의 확장명입니다. 모든 파일 확장명을 처리하려면 비워 둡니다.
   * **RECURSE** - 재귀 작업입니다. 이 값은 기본값으로 유지합니다.
   * **RUN SETTINGS** - Runbook을 실행하기 위한 설정입니다. 하이브리드 작업자를 선택합니다.

1. **확인**, **선택** 을 차례로 클릭하여 감시자 페이지로 돌아갑니다.
1. **작업 구성** 을 선택하고 **Process-NewFile** Runbook을 선택합니다.
1. 매개 변수에 대해 다음 값을 입력합니다.

   * **EVENTDATA** - 이벤트 데이터입니다. 비워 둡니다. 데이터는 감시자 Runbook에서 제공됩니다.
   * **Run Settings** - Runbook을 실행하기 위한 설정입니다. 이 Runbook이 Azure Automation에서 실행되므로 Azure로 유지합니다.

1. **확인**, **선택** 을 차례로 클릭하여 감시자 페이지로 돌아갑니다.
1. **확인** 을 클릭하여 감시자 태스크를 만듭니다.

   :::image type="content" source="./media/automation-watchers-tutorial/watchertaskcreation.png" alt-text="Azure Portal에서 Watcher 작업을 구성하는 스크린샷.":::


## <a name="trigger-a-watcher"></a>감시자 트리거

감시자 태스크가 제대로 작동하도록 아래에 설명된 대로 테스트를 실행해야 합니다. 

1. Hybrid Runbook Worker에 원격으로 연결합니다.
1. **PowerShell** 을 열고 폴더에 테스트 파일을 만듭니다.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

다음 예제에는 테스트가 정상적으로 완료된 경우의 결과가 나와 있습니다.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>출력 검사

1. Automation 계정으로 이동하여 **프로세스 자동화** 아래에서 **감시자 태스크** 를 선택합니다.
1. 감시자 태스크 **WatchMyFolder** 를 선택합니다.
1. **스트림** 아래에서 **감시자 스트림 보기** 를 클릭하여 감시자가 새 파일을 찾았으며 작업 Runbook을 시작했음을 확인합니다.
1. 작업 Runbook 작업을 확인하려면 **감시자 작업 보기** 를 클릭합니다. 각 작업을 선택하면 작업 세부 정보를 확인할 수 있습니다.

   :::image type="content" source="./media/automation-watchers-tutorial/WatcherActionJobs.png" alt-text="Azure Portal의 Watcher 작업 스크린샷.":::


다음 예에는 새 파일을 찾은 경우 표시되는 출력이 나와 있습니다.

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>다음 단계

사용자 고유의 Runbook을 작성하는 방법에 대한 자세한 내용은 [PowerShell Runbook 생성하기](./learn/powershell-runbook-managed-identity.md)를 참조하세요.
---
title: Azure Automation에서 변경 내용 추적 및 인벤토리 관리
description: 이 문서에서는 변경 내용 추적 및 인벤토리를 사용하여 사용자 환경에서 소프트웨어 및 Microsoft 서비스 변경 내용을 추적하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: d67acd025e453cee41a2461276d2f5df3066a141
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112518459"
---
# <a name="manage-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 관리

추적할 새 파일 또는 레지스트리 키를 추가하면 Azure Automation에서 [변경 내용 추적 및 인벤토리](overview.md)에 사용하도록 설정합니다. 이 문서에서는 추적을 구성하고, 추적 결과를 검토하고, 변경 내용이 검색될 때 경고를 처리하는 방법을 설명합니다.

이 문서의 절차를 수행하기 전에 다음 기법 중 하나를 사용하여 VM에 변경 내용 추적 및 인벤토리를 사용하도록 설정했는지 확인하세요.

* [Automation 계정에서 변경 내용 추적 및 인벤토리 사용](enable-from-automation-account.md)
* [Azure Portal을 탐색하여 변경 내용 추적 및 인벤토리 사용](enable-from-portal.md)
* [Runbook에서 변경 내용 추적 및 인벤토리 사용](enable-from-runbook.md)
* [Azure VM에서 변경 내용 추적 및 인벤토리 사용](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>배포 범위 제한

변경 내용 추적 및 인벤토리는 작업 영역 내의 범위 구성을 사용하고 변경 내용을 수신할 컴퓨터를 대상으로 합니다. 자세한 내용은 [변경 내용 추적 및 인벤토리 배포 범위 제한](manage-scope-configurations.md)을 참조하세요.

## <a name="track-files"></a>파일 추적

변경 내용 추적 및 인벤토리를 사용하여 파일 및 폴더/디렉터리의 변경 내용을 추적할 수 있습니다. 이 섹션에서는 Windows와 Linux에서 파일 추적을 구성하는 방법을 설명합니다.

### <a name="configure-file-tracking-on-windows"></a>Windows에서 파일 추적 구성

다음 단계를 사용하여 Windows 컴퓨터에서 파일 추적을 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모든 서비스** 를 선택합니다. 리소스 목록에서 **Automation** 을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록에서 제안 항목이 필터링됩니다. **Automation 계정** 을 선택합니다.

3. Automation 계정 목록에서 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 선택한 계정을 선택합니다.

4. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적** 을 선택합니다.

5. **설정 편집**(톱니 바퀴 기호)을 선택합니다.

6. 작업 영역 구성 페이지에서 **Windows 파일** 을 선택한 다음 **+ 추가** 를 클릭하여 추적할 새 파일을 추가합니다.

7. 변경 내용 추적을 위해 Windows 파일 추가 창에서 추적할 파일 또는 폴더에 대한 정보를 입력하고 **저장** 을 클릭합니다. 다음 표에는 정보에 사용할 수 있는 속성이 정의되어 있습니다.

    |속성  |설명  |
    |---------|---------|
    |사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 파일의 이름입니다.        |
    |그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
    |경로 입력     | 파일을 확인할 경로입니다(예: **c:\temp\\\*.txt**). `%winDir%\System32\\\*.*`와 같은 환경 변수를 사용할 수도 있습니다.       |
    |경로 유형     | 경로의 유형입니다. 가능한 값은 파일 및 폴더입니다.        |    
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용되면 True이 고, 그렇지 않으면 False입니다.        |    
    |파일 콘텐츠 업로드 | 추적된 변경 내용에 대한 파일 콘텐츠를 업로드하려면 True이고, 그렇지 않으면 False입니다.|

    와일드카드를 사용하여 파일 및 폴더의 모니터링을 구성하려는 경우 다음 사항을 고려하세요.

    - 와일드 카드는 여러 파일을 추적하는 데 필요합니다.
    - *C:\folder\file* 또는 */etc/* .conf*와 같은 경로의 마지막 세그먼트에만 와일드카드를 사용할 수 있습니다.
    - 환경 변수에 유효하지 않은 경로가 포함된 경우 인벤토리를 실행하면 유효성 검사는 성공하지만 경로는 실패합니다.
    - 경로를 설정할 때 c:*c:* .*와 같은 일반 경로를 사용하면 너무 많은 폴더가 트래버스되므로 사용하지 않는 것이 좋습니다.

8. **파일 콘텐츠 업로드** 에 대해 True를 지정해야 합니다. 이 설정은 표시된 파일 경로에 대해 파일 콘텐츠 추적을 사용합니다.

### <a name="configure-file-tracking-on-linux"></a>Linux에서 파일 추적 구성

다음 단계를 사용하여 Linux 컴퓨터에서 추적할 파일을 구성합니다.

1. **설정 편집**(톱니 바퀴 기호)을 선택합니다.

2. 작업 영역 구성 페이지에서 **Linux 파일** 을 선택한 다음 **+ 추가** 를 선택하여 추적할 새 파일을 추가합니다.

3. **변경 내용 추적을 위해 Linux 파일 추가** 페이지에서 추적할 파일이나 디렉터리의 정보를 입력한 다음 **저장** 을 클릭합니다. 다음 표에는 정보에 사용할 수 있는 속성이 정의되어 있습니다.

    |속성  |설명  |
    |---------|---------|
    |사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 파일의 이름입니다.        |
    |그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
    |경로 입력     | 파일을 확인할 경로입니다(예: **/etc/*.conf**).       |
    |경로 유형     | 경로의 유형입니다. 가능한 값은 파일 및 디렉터리입니다.        |
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용되면 True이고, 그렇지 않으면 False입니다.        |
    |sudo 사용     | 항목을 확인할 때 sudo를 사용하려면 True이고, 그렇지 않으면 False입니다.         |
    |링크     | 디렉터리를 트래버스할 때 바로 가기 링크를 처리하는 방법을 결정하는 설정입니다. 가능한 값은 다음과 같습니다.<br> 무시 - 바로 가기 링크가 무시되고 참조된 파일/디렉터리가 포함되지 않습니다.<br>실행 - 재귀 중에 바로 가기 링크를 따르고 참조된 파일/디렉터리도 포함합니다.<br>관리 - 바로 가기 링크를 따르고 반환된 콘텐츠 변경을 허용합니다.<br>**참고:** 관리 옵션은 파일 콘텐츠 검색을 지원하지 않으므로 권장하지 않습니다.    |
    |파일 콘텐츠 업로드 | 추적된 변경 내용에 대한 파일 콘텐츠를 업로드하려면 True이고, 그렇지 않으면 False입니다. |

4. **파일 콘텐츠 업로드** 에 **True** 를 지정해야 합니다. 이 설정은 표시된 파일 경로에 대해 파일 콘텐츠 추적을 사용합니다.

   ![Linux 파일 추가](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>파일 콘텐츠 추적

파일 콘텐츠 추적을 사용하면 추적된 변경 전후의 파일 콘텐츠를 볼 수 있습니다. 이 기능은 각 변경 작업 수행 후 [스토리지 계정](../../storage/common/storage-account-overview.md)에 파일 콘텐츠를 저장합니다. 다음은 파일 콘텐츠를 추적하기 위해 따라야 하는 몇 가지 규칙입니다.

* 파일 콘텐츠를 저장하려면 Resource Manager 배포 모델을 사용하는 표준 스토리지 계정이 필요합니다.
* 기본적으로 스토리지 계정은 네트워크에 있는 모든 클라이언트의 연결을 허용합니다. 특정 트래픽만 허용하도록 스토리지 계정 보안을 구성한 경우 Automation 계정이 스토리지 계정에 연결할 수 있도록 구성 규칙을 수정해야 합니다. [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md)을 참조하세요.
* 프리미엄 및 클래식 배포 모델 스토리지 계정을 사용하지 마세요. [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.
* 스토리지 계정은 하나의 Automation 계정에만 연결할 수 있습니다.
* 변경 내용 추적 및 인벤토리는 Automation 계정에서 사용하도록 설정해야 합니다.

### <a name="enable-tracking-for-file-content-changes"></a>파일 콘텐츠 변경 내용 추적 사용 설정

파일 콘텐츠 변경 내용 추적을 사용하도록 설정하려면 다음 단계를 사용합니다.

1. **설정 편집**(톱니 바퀴 기호)을 선택합니다.

2. **파일 콘텐츠** 를 선택한 다음 **연결** 을 선택합니다. 그러면 **변경 내용 추적을 위한 콘텐츠 위치 추가** 페이지가 열립니다.

   ![콘텐츠 위치 추가](./media/manage-change-tracking/enable.png)

3. 파일 콘텐츠를 저장하는 데 사용할 구독 및 스토리지 계정을 선택합니다.

5. 기존의 모든 추적된 파일에 대해 파일 콘텐츠 추적을 사용하려면 **모든 설정에 대한 파일 콘텐츠 업로드** 에 대해 **켜기** 를 선택합니다. 나중에 각 파일 경로에 대해 이 설정을 변경할 수 있습니다.

   ![스토리지 계정 설정](./media/manage-change-tracking/storage-account.png)

6. 변경 내용 추적 및 인벤토리는 파일 콘텐츠 변경 내용 추적을 사용하는 경우 스토리지 계정 및 SAS(공유 액세스 서명) URI를 표시합니다. 서명은 365일 후 만료되고 **다시 생성** 을 선택하여 다시 만들 수 있습니다.

   ![계정 키 나열](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>추적된 파일의 콘텐츠 보기

변경 내용 추적 및 인벤토리가 추적된 파일에 대한 변경 내용을 감지하면 변경 내용 정보 창에서 파일 콘텐츠를 볼 수 있습니다.  

![변경 내용 나열](./media/manage-change-tracking/change-list.png)

1. Automation 계정의 **변경 내용 추적** 페이지에서 변경 내용 목록의 파일을 선택한 다음 **파일 콘텐츠 변경 내용 보기** 를 선택하여 파일 내용을 확인합니다. 변경 내용 세부 정보 창에는 각 속성의 표준 전후 파일 정보가 표시됩니다.

   ![변경 세부 정보](./media/manage-change-tracking/change-details.png)

2. 파일 콘텐츠를 나란히 보기에서 볼 수 있습니다. **인라인** 을 선택하여 변경 내용을 인라인 보기에 표시할 수도 있습니다.

## <a name="track-registry-keys"></a>레지스트리 키 추적

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 레지스트리 키를 구성합니다.

1. Automation 계정의 **변경 내용 추적** 페이지에서 **설정 편집**(톱니 바퀴 기호)을 선택합니다.

2. 작업 영역 구성 페이지에서 **Windows 레지스트리** 를 선택합니다.

3. **+ 추가** 를 선택하여 추적할 새 레지스트리 키를 추가합니다.

4. **변경 내용 추적을 위해 Windows 레지스트리 추가** 페이지에서 추적할 키의 정보를 입력한 다음 **저장** 을 선택합니다. 다음 표에는 정보에 사용할 수 있는 속성이 정의되어 있습니다. 레지스트리 경로를 지정할 때 경로가 값이 아니라 키여야 합니다.

    |속성  |설명  |
    |---------|---------|
    |사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
    |Item Name     | 추적할 레지스트리 키의 식별 이름입니다.        |
    |그룹     | 논리적으로 레지스트리 키를 그룹화하는 그룹 이름입니다.        |
    |Windows 레지스트리 키   | 경로가 포함된 키 이름입니다(예: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup`).      |

## <a name="search-logs-for-change-records"></a>변경 레코드에 대한 로그 검색

변경 레코드에 대한 Azure Monitor 로그에서 다양한 검색을 수행할 수 있습니다. 변경 내용 추적 페이지가 열려 있는 상태에서 **Log Analytics** 를 클릭하여 로그 페이지를 엽니다. 다음 표에서는 변경 레코드에 대한 로그 검색 샘플을 제공합니다.

|쿼리  |Description  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Auto로 설정되었지만 Stopped로 보고된 Windows 서비스에 대한 최근의 인벤토리 레코드를 표시합니다. 결과는 지정된 소프트웨어 이름 및 컴퓨터에 대한 최신 레코드로 제한됩니다.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|제거된 소프트웨어에 대한 변경 레코드를 표시합니다.|

## <a name="next-steps"></a>다음 단계

* 범위 구성에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리 배포 범위 제한](manage-scope-configurations.md)을 참조하세요.
* Azure Monitor 로그에 저장된 로그를 검색해야 하는 경우 [Azure Monitor 로그에서 로그 검색](../../azure-monitor/logs/log-query-overview.md)을 참조하세요.
* 배포가 완료되면 [변경 내용 추적 및 인벤토리 제거](remove-feature.md)를 참조하세요.
* 변경 내용 추적 및 인벤토리에서 VM을 삭제하려면 [변경 내용 추적 및 인벤토리에서 VM 제거](remove-vms-from-change-tracking.md)를 참조하세요.
* 기능 오류를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](../troubleshoot/change-tracking.md)을 참조하세요.

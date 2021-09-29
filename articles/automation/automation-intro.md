---
title: Azure Automation에 대한 소개
description: 이 문서에서는 Azure Automation이 무엇인지와 이를 사용하여 인프라 및 애플리케이션의 수명 주기를 자동화하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, 업데이트 관리, 변경 내용 추적, DSC, 인벤토리, Runbook, Python, 그래픽
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: fcf32e654b3d5a60b542747fd731c329979412e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812585"
---
# <a name="an-introduction-to-azure-automation"></a>Azure Automation에 대한 소개

Azure Automation은 Azure 및 Azure 이외의 환경에서 일관된 관리를 지원하는 클라우드 기반 자동화 및 구성 서비스를 제공하며, 프로세스 자동화, 구성 관리, 업데이트 관리, 공유 기능 및 이기종 기능으로 구성됩니다. Automation을 통해 워크로드와 리소스를 배포하고, 운영하고, 서비스를 해제하는 동안 완벽한 제어가 가능합니다.

![Automation 기능](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>프로세스 Automation

Azure Automation의 프로세스 Automation을 사용하면 빈번하고, 시간이 오래 걸리고, 오류가 발생하기 쉬운 클라우드 관리 작업을 자동화할 수 있습니다. 이러한 서비스를 통해 비즈니스 가치를 높이는 작업에 집중할 수 있습니다. 또한 오류를 줄이고 효율성을 높여 운영 비용을 낮출 수 있습니다. 프로세스 자동화 운영 환경은 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)에 자세히 설명되어 있습니다.

프로세스 자동화는 Azure 서비스와 엔드투엔드 프로세스를 배포, 구성 및 관리하는 데 필요한 다른 공용 시스템을 통합하도록 지원합니다. 이 서비스를 사용하면 PowerShell에서 또는 Python을 사용하여 그래픽으로 [Runbook](automation-runbook-types.md)을 만들 수 있습니다. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 사용하면 온-프레미스 환경 전체에서 오케스트레이션하여 관리를 통합할 수 있습니다. [웹후크](automation-webhooks.md)를 사용하면 ITSM, DevOps 및 모니터링 시스템에서 자동화를 트리거하여 요청을 수행하고 지속적인 업데이트 및 작업을 보장할 수 있습니다. 

## <a name="configuration-management"></a>구성 관리

Azure Automation의 구성 관리를 통해 다음과 같은 두 가지 기능에 액세스할 수 있습니다.

* 변경 내용 추적 및 인벤토리
* Azure Automation 상태 구성

### <a name="change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리

변경 내용 추적 및 인벤토리는 변경 추적 및 인벤토리 기능을 결합하여 가상 머신 및 서버 인프라 변경 내용을 추적할 수 있도록 합니다. 이 서비스는 사용자 환경에서 서비스, 디먼, 소프트웨어, 레지스트리 및 파일에 변경한 내용을 추적하는 기능을 지원하기 때문에 원하지 않는 변경 내용을 진단하고 경고를 생성할 수 있습니다. 인벤토리 지원을 사용하면 게스트 내 리소스를 쿼리하여 설치된 애플리케이션 및 기타 구성 항목을 볼 수 있습니다. 이 기능에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리](change-tracking/overview.md)를 참조하세요.

### <a name="azure-automation-state-configuration"></a>Azure Automation 상태 구성

[Azure Automation State Configuration](automation-dsc-overview.md)은 엔터프라이즈 환경에 필요한 서비스를 제공하는 PowerShell DSC(Desired State Configuration)에 대한 클라우드 기반 기능입니다. 이 기능을 사용하면 Azure Automation에서 DSC 리소스를 관리하고 Azure 클라우드의 DSC 끌어오기 서버에서 가상 머신이나 실제 머신에 구성을 적용할 수 있습니다. 

## <a name="update-management"></a>업데이트 관리

Azure Automation에는 하이브리드 환경의 Windows 및 Linux 시스템용 [업데이트 관리](./update-management/overview.md) 기능이 포함되어 있습니다. 업데이트 관리는 Azure와 기타 클라우드 및 온-프레미스에서 업데이트 규정 준수에 대한 가시성을 제공합니다. 이 기능을 사용하면 정의한 유지 관리 기간 내에 업데이트 설치를 오케스트레이션하는 예약된 배포를 만들 수 있습니다. 특정 머신에 업데이트를 설치하지 않아야 하는 경우에는 업데이트 관리 기능을 사용하여 배포에서 제외할 수 있습니다.

## <a name="shared-capabilities"></a>공유 기능

Azure Automation은 다양한 공유 기능을 제공하며, 여기에는 공유 리소스, 역할 기반 액세스 제어, 유연한 일정 예약, 소스 제어 통합, 감사 및 태그 지정 기능이 포함됩니다.

### <a name="shared-resources"></a><a name="shared-resources"></a>공유 리소스

Azure Automation은 대규모 환경을 더 쉽게 자동화하고 구성할 수 있게 하는 일단의 공유 리소스로 구성됩니다.

* **[일정](./shared-resources/schedules.md)** - 미리 정의한 시간에 자동화 작업을 트리거합니다.
* **[모듈](./shared-resources/modules.md)** - Azure 및 기타 시스템을 관리합니다. Microsoft, 타사, 커뮤니티, 사용자 정의 cmdlet 및 DSC 리소스에 대한 Automation 계정으로 모듈을 가져올 수 있습니다.
* **[모듈 갤러리](automation-runbook-gallery.md)** - PowerShell 갤러리와 네이티브 통합을 지원하기 때문에 Runbook을 볼 수 있고 Automation 계정으로 가져올 수 있습니다. 갤러리를 사용하면 PowerShell 갤러리 및 Microsoft 스크립트 센터에서 프로세스를 빠르게 통합하고 제작할 수 있습니다.
* **[Python 2 패키지](python-packages.md)** - Automation 계정에 대해 Python 2 Runbook을 지원합니다.
* **[자격 증명](./shared-resources/credentials.md)** - Runbook 및 구성이 런타임 시 사용할 수 있는 중요한 정보를 안전하게 저장합니다.
* **[연결](automation-connections.md)** - 시스템 연결을 위한 공통 정보의 이름 값 쌍을 저장합니다. 런타임 시 사용할 Runbook 및 구성의 연결은 모듈 작성자가 정의합니다.
* **[인증서](./shared-resources/certificates.md)** - 런타임 시 Runbook 또는 DSC 구성이 액세스하는 경우 배포된 리소스을 보호하고 인증하는 데 사용되는 정보를 정의합니다. 
* **[변수](./shared-resources/variables.md)** - Runbook 및 구성 전체에서 사용할 수 있는 콘텐츠를 보관합니다. Runbook과 이를 참조하는 구성을 수정하지 않고도 변수 값을 변경할 수 있습니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Automation은 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원하여 Automation 계정 및 해당 리소스에 대한 액세스를 제어합니다. Automation 계정, Runbooks 및 작업에서 Azure RBAC를 구성하는 방법을 자세히 알아보려면 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.

### <a name="source-control-integration"></a>소스 제어 통합

Azure Automation은 [소스 제어 통합](source-control-integration.md)을 지원합니다. 이 기능은 구성을 코드로 승격하며, 이렇게 하면 Runbook 또는 구성을 소스 제어 시스템에 체크인할 수 있습니다.

## <a name="heterogeneous-support-windows-and-linux"></a>이기종 지원(Windows 및 Linux)

Automation은 하이브리드 클라우드 환경과 Windows 및 Linux 시스템에서도 작동하도록 설계되었습니다. 배포된 워크로드와 워크로드를 실행하는 운영 체제를 자동화하고 구성하는 일관된 방법을 제공합니다.

## <a name="common-scenarios-for-automation"></a>일반적인 자동화 시나리오

Azure Automation은 인프라와 애플리케이션의 수명 주기 전반에 걸친 관리를 지원합니다. 일반적인 시나리오는 다음과 같습니다.

* **Runbook 작성** - PowerShell, PowerShell Workflow, 그래픽, Python 2 및 DSC Runbook을 공용 언어로 작성합니다. 
* **리소스 빌드 및 배포** - Runbook 및 Azure Resource Manager 템플릿을 사용하여 하이브리드 환경 전반에 가상 머신을 배포합니다. Jenkins 및 Azure DevOps와 같은 개발 도구에 통합합니다.
* **VM 구성** - 인프라 및 애플리케이션에 대해 구성을 사용하여 Windows 및 Linux 머신을 평가하고 구성합니다.
* **지식 공유** - 조직에서 워크로드를 제공하고 유지 관리하는 방법에 대한 지식을 시스템에 전달합니다. 
* **인벤토리 검색** - 배포된 리소스의 대상 지정, 보고 및 규정 준수에 대한 전체 인벤토리를 가져옵니다. 
* **변경 내용 찾기** - 잘못된 구성을 유발할 수 있는 변경 내용을 식별하여 운영 규정 준수를 향상시킵니다.
* **모니터링** - 문제를 유발하는 머신 변경 내용을 격리하여 수정하거나 관리 시스템에 에스컬레이션합니다.
* **보호** - 보안 경고가 발생하면 머신을 격리합니다. 게스트 내 요구 사항을 설정합니다.
* **거버넌스** - 팀을 위한 Azure RBAC를 설정합니다. 사용되지 않는 리소스를 복구합니다.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Azure Automation에 대한 가격 책정

Azure Automation과 관련된 가격은 [가격 책정](https://azure.microsoft.com/pricing/details/automation/) 페이지에서 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Automation 계정 만들기](./quickstarts/create-account-portal.md)
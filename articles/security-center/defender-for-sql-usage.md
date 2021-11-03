---
title: microsoft Defender for SQL 설정하는 방법
description: Microsoft Defender for Cloud의 선택적 Microsoft Defender for SQL 플랜을 사용하도록 설정하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/11/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9a7e817df5e938756ca9fe331f82136505515451
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009741"
---
# <a name="enable-microsoft-defender-for-sql-servers-on-machines"></a>컴퓨터에서 SQL 서버에 대해 Microsoft Defender 사용 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 Microsoft Defender 계획은 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 검색합니다.

의심스러운 데이터베이스 활동, 잠재적 취약성 또는 SQL 삽입 공격, 비정상 데이터베이스 액세스 및 쿼리 패턴이 있으면 경고가 표시됩니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**머신의 SQL 서버용 Microsoft Defender는** [가격 책정 페이지에](https://azure.microsoft.com/pricing/details/security-center/) 표시된 대로 요금이 청구됩니다.|
|보호되는 SQL 버전:|Azure SQL Server(Microsoft 지원에 포함된 모든 버전)|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure 중국 21Vianet|
|||

## <a name="set-up-microsoft-defender-for-sql-servers-on-machines"></a>컴퓨터에서 SQL 서버용 Microsoft Defender 설정

이 플랜을 사용하려면 다음의 단계를 따릅니다.

[1단계. 에이전트 확장 설치](#step-1-install-the-agent-extension).

[2단계. SQL Server의 호스트에 Log Analytics 에이전트를 프로비전합니다.](#step-2-provision-the-log-analytics-agent-on-your-sql-servers-host)

[3단계. Defender for Cloud의 가격 책정 및 설정 페이지에서 선택적 계획을 사용하도록 설정합니다.](#step-3-enable-the-optional-plan-in-defender-for-clouds-pricing-and-settings-page)


### <a name="step-1-install-the-agent-extension"></a>1단계. 에이전트 확장 설치

- **Azure VM의 SQL Server** - [SQL IaaS Agent 확장에 SQL Server VM 등록](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)에 설명된 대로 SQL IaaS 에이전트 확장에 SQL Server VM를 등록합니다.

- **Azure Arc의 SQL Server** - [Azure Arc 설명서](../azure-arc/servers/manage-vm-extensions.md)에 설명된 설치 방법에 따라 Azure Arc 에이전트를 설치합니다.

### <a name="step-2-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>2단계. SQL server의 호스트에 Log Analytics 에이전트를 프로비전합니다.

- **Azure VM의 SQL Server** - SQL 컴퓨터가 Azure VM에서 호스트되는 경우 [Log Analytics 에이전트 <a name="auto-provision-mma"></a>](enable-data-collection.md#auto-provision-mma)의 자동 프로비전을 사용하도록 설정할 수 있습니다. 또는 [Azure Stack Hub VM](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms)을 등록하는 수동 프로시저를 따를 수 있습니다.
- **Azure Arc SQL Server** - Azure Arc 사용하도록 설정된 서버에서 [SQL Server](../azure-arc/index.yml) 관리하는 경우 Defender for Cloud 권장 사항 "Log Analytics 에이전트를 Windows 기반 Azure Arc 머신(미리 보기)에 설치해야 합니다."를 사용하여 Log Analytics 에이전트를 배포할 수 있습니다.

- **SQL Server 온-프레미스** - SQL Server Azure Arc 없이 온-프레미스 Windows 컴퓨터에서 호스트되는 경우 Azure에 연결하는 두 가지의 옵션이 있습니다.
    
    - **Azure Arc 배포** - 모든 Windows 머신을 Defender for Cloud에 연결할 수 있습니다. 그러나 Azure Arc는 *모든* Azure 환경에서 심층 통합을 제공 합니다. Azure Arc를 설정하는 경우 포털에서 **SQL Server – Azure arc** 페이지가 표시되고 해당 페이지의 전용 **보안** 탭에 보안 경고가 표시됩니다. 따라서 첫 번째 권장 옵션은 [호스트에서 Azure Arc를 설정하고](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) 위의 **Azure Arc의 SQL Server** 지침을 따르는 것입니다.
        
    - **Azure Arc 없이 Windows 컴퓨터 연결** - Azure Arc를 사용하지 않고 Windows 컴퓨터에서 실행되는 SQL Server를 연결하도록 선택하는 경우 [Windows 컴퓨터를 Azure Monitor에 연결](../azure-monitor/agents/agent-windows.md)을 참조하세요.


### <a name="step-3-enable-the-optional-plan-in-defender-for-clouds-pricing-and-settings-page"></a>3단계: Defender for Cloud의 가격 책정 및 설정 페이지에서 선택적 계획을 사용하도록 설정합니다.

1. Defender for Cloud 메뉴에서 환경 **설정** 페이지를 엽니다.

    - **클라우드의 기본 작업** 영역("defaultworkspace-[구독 ID]-[region]")을 사용하는 경우 관련 **구독을** 선택합니다.

    - **기본값이 아닌 작업 영역** 을 사용하는 경우 관련 **작업 영역** 을 선택합니다(필요한 경우 필터에 작업 영역 이름을 입력).

        ![제목으로 기본값이 아닌 작업 영역 찾기](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. **컴퓨터의 SQL 서버용 Microsoft Defender에 대한** 옵션을 **의** 로 설정합니다. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="선택적 계획이 있는 Defender for Cloud 가격 책정 페이지.":::

    이 플랜은 선택한 작업 영역에 연결된 모든 SQL Server에서 사용하도록 설정됩니다. SQL Server 인스턴스를 처음 다시 시작하면 보호가 완전히 활성화됩니다.

    >[!TIP] 
    > 새 작업 영역 만들기에 대한 내용은 [Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)의 지침을 참조하세요.


1. 필요에 따라 보안 경고에 대한 전자 메일 알림을 구성합니다. 

    Defender for Cloud 경고가 생성될 때 이메일 알림을 받도록 받는 사람 목록을 설정할 수 있습니다. 메일에는 모든 관련 세부 정보가 포함된 Microsoft Defender for Cloud의 경고에 대한 직접 링크가 포함되어 있습니다. 자세한 정보는 [보안 경고에 대한 전자 메일 알림 설정](configure-email-notifications.md)을 참조하세요.


## <a name="microsoft-defender-for-sql-alerts"></a>microsoft Defender for SQL 경고
경고는 SQL 컴퓨터에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도에 의해 생성됩니다. 이러한 이벤트는 [경고 참조 페이지](alerts-reference.md#alerts-sql-db-and-warehouse)에 표시된 경고를 트리거 할 수 있습니다.

## <a name="explore-and-investigate-security-alerts"></a>보안 경고 탐색 및 조사

Microsoft Defender for SQL 경고는 Defender for Cloud의 경고 페이지, 머신의 보안 페이지, [워크로드 보호 대시보드](workload-protections-dashboard.md)또는 경고 이메일의 직접 링크를 통해 사용할 수 있습니다.

1. 경고를 보려면 Defender for Cloud 메뉴에서 **보안 경고를** 선택하고 경고를 선택합니다.

1. 경고는 각각 자세한 수정 단계 및 조사 정보와 함께 자체적으로 포함되도록 설계되었습니다. 더 넓은 보기를 위해 다른 Microsoft Defender for Cloud 및 Microsoft Sentinel 기능을 사용하여 더 자세히 조사할 수 있습니다.

    * 추가 조사를 위해 SQL Server의 감사 기능을 사용하도록 설정합니다. Microsoft Sentinel 사용자인 경우 Windows 보안 Log 이벤트에서 Sentinel로 SQL 감사 로그를 업로드하고 풍부한 조사 환경을 이용할 수 있습니다. [SQL Server 감사에 대해 자세히 알아보세요](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * 보안 태세를 개선하려면 각 경고에 표시된 호스트 머신에 대한 Defender for Cloud 권장 사항을 사용합니다. 이를 통해 향후 공격의 위험을 축소할 수 있습니다. 

    [경고 관리 및 대응에 대해 자세히 알아보세요](managing-and-responding-alerts.md).


## <a name="faq---microsoft-defender-for-sql-servers-on-machines"></a>FAQ - 머신의 SQL 서버용 Microsoft Defender

### <a name="if-i-enable-this-microsoft-defender-plan-on-my-subscription-are-all-sql-servers-on-the-subscription-protected"></a>내 구독에서 이 Microsoft Defender 계획을 사용하도록 설정하면 구독의 모든 SQL 서버가 보호됩니까? 

아니요. Azure 가상 머신 또는 Azure Arc 지원 컴퓨터에서 실행되는 SQL Server SQL Server 배포를 방어하려면 Defender for Cloud에 다음이 필요합니다.

- 컴퓨터의 Log Analytics 에이전트
- 관련 Log Analytics 작업 영역에서 microsoft Defender for SQL 솔루션을 사용하도록 설정

Azure Portal의 SQL Server 페이지에 표시되는 구독 *상태* 는 기본 작업 영역 상태를 반영하여 연결된 모든 컴퓨터에 적용됩니다. Log Analytics 에이전트가 해당 작업 영역에 보고하는 호스트의 SQL 서버만 Defender for Cloud로 보호됩니다.




## <a name="next-steps"></a>다음 단계

관련 자료는 다음 문서를 참조하세요.

- [SQL Database 및 Azure Synapse Analytics에 대한 경고](alerts-reference.md#alerts-sql-db-and-warehouse)
- [보안 경고에 대한 이메일 알림 설정](configure-email-notifications.md)
- [Microsoft Sentinel에 대한 자세한 정보](../sentinel/index.yml)

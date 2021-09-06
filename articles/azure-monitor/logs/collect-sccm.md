---
title: Azure Monitor에 Configuration Manager 연결 | Microsoft Docs
description: 이 문서에서는 Configuration Manager를 Azure Monitor의 작업 영역에 연결하고 데이터 분석을 시작하는 단계를 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/02/2021
ms.openlocfilehash: 2bb6bcc45c6ebb325dbb8ec56eb37b6fdce293ab
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536100"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Azure Monitor에 Configuration Manager 연결
Microsoft Endpoint Configuration Manager 환경을 Azure Monitor에 연결하면 디바이스 컬렉션 데이터를 동기화하고 Azure Monitor 및 Azure Automation에서 이러한 컬렉션을 참조할 수 있습니다.  

> [!IMPORTANT]
> Configuration Manager 버전 2010부터 이 기능은 사용되지 않습니다.<!-- 8269855 --> 버전 2107부터 Configuration Manager에서 제거됩니다.<!-- 9649296 --> 자세한 내용은 [Configuration Manager에서 제거되는 기능과 이후 지원되지 않는 기능](/mem/configmgr/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure Monitor는 Configuration Manager 현재 분기인 버전 1606 이상을 지원합니다.

>[!NOTE]
>Log Analytics 작업 영역을 사용하여 Configuration Manager를 연결하는 기능은 선택 사항이며 기본적으로 사용되지 않습니다. 이 기능은 사용하기 전에 활성화해야 합니다. 자세한 내용은 [업데이트에서 선택적 기능 사용](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)을 참조하세요.

## <a name="configuration-overview"></a>구성 개요

다음 단계에서는 Azure Monitor와 Configuration Manager 통합을 구성하는 단계를 간략하게 설명합니다.  

1. Azure Active Directory에서 Configuration Manager를 웹 애플리케이션 및/또는 Web API 앱으로 등록하고 Azure Active Directory에서 등록할 때 사용한 클라이언트 ID와 클라이언트 암호 키가 있는지 확인합니다. 이 단계를 수행하는 방법에 대한 자세한 내용은 [포털 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

2. Azure Active Directory에서 [Azure Monitor에 액세스할 수 있는 권한을 Configuration Manager(등록된 웹앱)에 부여](#grant-configuration-manager-with-permissions-to-log-analytics)합니다.

3. Configuration Manager에서 **Azure 서비스** 마법사를 사용하여 연결을 추가합니다.

4. Configuration Manager 서비스 연결점 사이트 시스템 역할이 실행되는 컴퓨터에서 [Windows용 Log Analytics 에이전트를 다운로드하여 설치](#download-and-install-the-agent)합니다. 에이전트에서 Configuration Manager 데이터를 Azure Monitor의 Log Analytics 작업 영역으로 전송합니다.

5. Azure Monitor에서 컴퓨터 그룹으로 [Configuration Manager의 컬렉션을 가져옵니다](#import-collections).

6. Azure Monitor에서 [컴퓨터 그룹](computer-groups.md)으로 Configuration Manager의 데이터를 봅니다.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager에 Log Analytics에 대한 사용 권한 제공

다음 절차에서는 Log Analytics 작업 영역의 *Contributor* 역할을 Configuration Manager에 대해 이전에 만든 AD 애플리케이션 및 서비스 주체에 부여합니다. 작업 영역이 아직 없으면 진행하기 전에 [Azure Monitor에 작업 영역 만들기](./quick-create-workspace.md)를 참조하세요. 이를 통해 Configuration Manager는 Log Analytics 작업 영역을 인증하고 연결할 수 있습니다.  

> [!NOTE]
> Configuration Manager의 Log Analytics 작업 영역에 권한을 지정해야 합니다. 그렇지 않고 Configuration Manager에서 구성 마법사를 사용하면 오류 메시지가 표시됩니다.
>

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 클릭합니다. 리소스 목록에서 **Log Analytics** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics** 를 선택합니다.

2. Log Analytics 작업 영역 목록에서 수정할 작업 영역을 선택합니다.

3. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

4. [액세스 제어(IAM)] 페이지에서 **역할 할당 추가** 를 클릭합니다. 그러면 **역할 할당 추가** 창이 나타납니다.

5. **역할 할당 추가** 창의 **역할** 드롭다운 목록 아래에서 **기여자** 역할을 선택합니다.  

6. **다음에 대한 액세스 할당** 드롭다운 목록 아래에서 이전에 AD에서 만든 Configuration Manager 애플리케이션을 선택한 다음, **확인** 을 클릭합니다.  

## <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치

Configuration Manager 서비스 연결점 사이트 시스템 역할을 호스트하는 컴퓨터에 Windows용 Log Analytics 에이전트를 설치하는 데 사용할 수 있는 방법을 이해하려면 [Azure에서 Azure Monitor에 Windows 컴퓨터 연결](../agents/agent-windows.md) 문서를 검토합니다.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Log Analytics 작업 영역에 Configuration Manager 연결

>[!NOTE]
> Log Analytics 연결을 추가하려면 Configuration Manager 환경에 온라인 모드를 위해 구성된 [서비스 연결점](/configmgr/core/servers/deploy/configure/about-the-service-connection-point)이 있어야 합니다.

> [!NOTE]
> 계층 구조에서 최고 계층 사이트를 Azure Monitor에 연결해야 합니다. 독립 실행형 기본 사이트를 Azure Monitor에 연결한 다음, 환경에 중앙 관리 사이트를 추가할 경우 새 계층 구조 안에서 연결을 삭제하고 다시 만들어야 합니다.

1. Configuration Manager의 **관리** 작업 영역에서 **Cloud Services** 를 선택한 다음, **Azure 서비스** 를 선택합니다. 

2. **Azure 서비스** 를 마우스 오른쪽 단추로 클릭한 다음, **Azure 서비스 구성** 을 선택합니다. **Azure 서비스 구성** 페이지가 나타납니다. 
   
3. **일반** 화면에서 아래의 작업을 완료했는지와 각 항목에 대한 상세 정보가 있는지 확인하고 **다음** 을 선택합니다.

4. Azure 서비스 마법사의 Azure 서비스 페이지에서 다음 작업을 수행합니다.

    1. Configuration Manager의 개체 **이름** 을 지정합니다.
    2. 서비스를 식별하는 데 도움이 되도록 선택적 **설명** 을 지정합니다.
    3. Azure 서비스 **OMS 커넥터** 를 선택합니다.

    >[!NOTE]
    >이제 OMS를 Azure Monitor 기능인 Log Analytics라고 합니다.

5. **다음** 을 선택하여 Azure 서비스 마법사의 Azure 앱 속성 페이지로 넘어갑니다.

6. Azure 서비스 마법사 **앱** 페이지의 목록에서 먼저 Azure 환경을 선택한 다음, **가져오기** 를 클릭합니다.

7. **앱 가져오기** 페이지에서 다음 정보를 지정합니다.

    1. 앱의 **Azure AD 테넌트 이름** 을 지정합니다.

    2. **Azure AD 테넌트 ID** 에 Azure AD 테넌트를 지정합니다. Azure Active Directory **속성** 페이지에서 이 정보를 확인할 수 있습니다. 

    3. **애플리케이션 이름** 에 애플리케이션 이름을 지정합니다.

    4. **클라이언트 ID** 에 이전에 만든 Azure AD 앱의 애플리케이션 ID를 지정합니다.

    5. **비밀 키** 에 만든 Azure AD 앱의 클라이언트 암호 키를 지정합니다.

    6. **비밀 키 만료** 에 키 만료 날짜를 지정합니다.

    7. **앱 ID URI** 에 이전에 만든 Azure AD 앱의 앱 ID URI를 지정합니다.

    8. **확인** 을 선택합니다. 그러면 오른쪽 결과에 **확인했습니다** 가 표시됩니다.

8. **구성** 페이지에서 정보를 검토하여 **Azure 구독**, **Azure 리소스 그룹** 및 **Operations Management Suite 작업 영역** 필드가 미리 채워져 있는지 확인합니다. 이는 Azure AD 애플리케이션에 리소스 그룹의 충분한 권한이 있음을 나타냅니다. 필드가 비어 있으면 애플리케이션에 필요한 권한이 없음을 나타냅니다. 디바이스 컬렉션을 선택하여 수집하고 작업 영역에 전달한 다음, **추가** 를 선택합니다.

9. **설정 확인** 페이지에서 옵션을 검토하고 **다음** 을 선택하여 연결 만들기와 구성을 시작합니다.

10. 구성이 완료되면 **완료** 페이지가 나타납니다. **닫기** 를 선택합니다. 

Configuration Manager를 Azure Monitor에 연결하면 컬렉션을 추가하거나 제거하고 연결 속성을 볼 수 있습니다.

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics 작업 영역 연결 속성 업데이트

암호나 클라이언트 암호 키가 만료되거나 분실된 경우 Log Analytics 연결 속성을 수동으로 업데이트해야 합니다.

1. Configuration Manager의 **관리** 작업 영역에서 **Cloud Services** 를 선택한 다음, **OMS 커넥터** 를 선택하여 **OMS 연결 속성** 페이지를 엽니다.
2. 이 페이지에서 **Azure Active Directory** 탭을 클릭하여 **테넌트**, **클라이언트 ID**, **클라이언트 비밀 키 만료** 를 클릭합니다. **클라이언트 비밀 키** 가 만료되었는지 **확인** 합니다.

## <a name="import-collections"></a>컬렉션 가져오기

Log Analytics 연결을 Configuration Manager에 추가하고 Configuration Manager 서비스 연결점 사이트 시스템 역할이 실행되는 컴퓨터에 에이전트를 설치했습니다. 다음 단계에서는 Azure Monitor에서 Configuration Manager의 컬렉션을 컴퓨터 그룹으로 가져옵니다.

계층 구조에서 디바이스 컬렉션을 가져오도록 초기 구성을 완료하면 멤버 자격을 최신으로 유지하기 위해 컬렉션 정보가 3시간마다 검색됩니다. 언제든지 이를 비활성화하도록 선택할 수 있습니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 클릭합니다. 리소스 목록에서 **Log Analytics** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역** 을 선택합니다.
2. Log Analytics 작업 영역 목록에서 Configuration Manager를 등록할 작업 영역을 선택합니다.  
3. **고급 설정** 을 선택합니다.
4. **컴퓨터 그룹** 을 선택한 다음, **SCCM** 을 선택합니다.  
5. **구성 관리자 컬렉션 멤버 자격 가져오기** 를 선택한 다음 **저장** 을 클릭합니다.  
   
    ![Configuration Manager 컬렉션 멤버 자격 가져오기 옵션이 포함된 SCCM의 컴퓨터 그룹 고급 설정 스크린샷](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>구성 관리자의 데이터 보기

Log Analytics 연결을 Configuration Manager에 추가하고 Configuration Manager 서비스 연결점 사이트 시스템 역할이 실행되는 컴퓨터에 에이전트를 설치하면 에이전트의 데이터가 Azure Monitor의 Log Analytics 작업 영역으로 전송됩니다. Azure Monitor에서 Configuration Manager 컬렉션이 [컴퓨터 그룹](./computer-groups.md)으로 나타납니다. **Settings\Computer Groups** 아래 **Configuration Manager** 페이지에서 그룹을 볼 수 있습니다.

컬렉션을 가져오면 컬렉션 멤버 자격이 있는 컴퓨터 중 삭제된 컴퓨터 수를 확인할 수 있습니다. 또한 가져온 컬렉션 수도 확인할 수 있습니다.

![Configuration Manager 컬렉션 멤버 자격 가져오기 옵션을 보여주는 SCCM의 컴퓨터 그룹 고급 설정 스크린샷](./media/collect-sccm/sccm-computer-groups02.png)

각 항목을 클릭하면 가져온 모든 그룹이나 각 그룹에 속하는 모든 컴퓨터가 표시된 로그 쿼리 편집기가 열립니다. [로그 검색](./log-query-overview.md)을 사용하면 컬렉션 멤버 자격 데이터를 자세히 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[로그 검색](./log-query-overview.md)을 사용하여 구성 관리자 데이터에 대한 자세한 정보를 볼 수 있습니다.

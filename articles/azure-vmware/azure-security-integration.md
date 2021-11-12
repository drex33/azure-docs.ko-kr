---
title: Azure VMware 솔루션과 함께 클라우드 용 Microsoft Defender 통합
description: 워크 로드 보호 대시보드에서 Azure의 기본 보안 도구를 사용 하 여 Azure VMware 솔루션 Vm을 보호 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: f1ec6d6282a773cca3164f377f7efd8bdaa42c77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312691"
---
# <a name="integrate-microsoft-defender-for-cloud-with-azure-vmware-solution"></a>Azure VMware 솔루션과 함께 클라우드 용 Microsoft Defender 통합 

Microsoft Defender for Cloud는 Azure VMware 솔루션 및 온-프레미스 Vm (가상 머신)에서 고급 위협 방지 기능을 제공 합니다. Azure VMware Solution VM의 취약성을 평가하고 필요에 따라 경고를 발생시킵니다. 해당 보안 경고를 Azure Monitor에 전달하여 해결할 수 있습니다. Microsoft Defender for Cloud에서 보안 정책을 정의할 수 있습니다. 자세한 내용은 [보안 정책 작업](../security-center/tutorial-security-policy.md)을 참조하세요. 

Microsoft Defender for Cloud는 다음과 같은 다양 한 기능을 제공 합니다.
- 파일 무결성 모니터링
- 파일리스 공격 탐지
- 운영 체제 패치 평가 
- 잘못된 보안 구성 평가
- Endpoint Protection 평가

다이어그램에서는 Azure VMware Solution VM에 대한 통합 보안의 통합 모니터링 아키텍처를 보여 줍니다.
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="Azure 통합 보안의 아키텍처를 보여 주는 다이어그램" border="false":::

**Log Analytics 에이전트** 는 Azure, Azure VMware Solution 및 온-프레미스 VM에서 로그 데이터를 수집합니다. 로그 데이터는 Azure Monitor 로그에 전송되고 **Log Analytics 작업 영역** 에 저장됩니다. 각 작업 영역에는 데이터를 저장할 고유한 데이터 리포지토리 및 구성이 있습니다.  로그가 수집 되 면 **클라우드 용 Microsoft Defender** 는 Azure VMware 솔루션 vm의 취약성 상태를 평가 하 고 위험 취약성에 대 한 경고를 발생 시킵니다. 평가 되 면 Microsoft Defender for Cloud는 취약성 상태를 Microsoft 센티널로 전달 하 여 인시던트를 만들고 다른 위협에 매핑합니다.  Microsoft Defender for Cloud는 클라우드 커넥터용 Microsoft Defender를 사용 하 여 Microsoft 센티널에 연결 됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [클라우드에 대 한 Defender의 최적화 사용을 계획](../security-center/security-center-planning-and-operations-guide.md)합니다.

- [Defender For Cloud에서 지원 되는 플랫폼을 검토](../security-center/security-center-os-coverage.md)합니다.

- 다양한 원본에서 데이터를 수집하려면 [Log Analytics 작업 영역을 만듭니다](../azure-monitor/logs/quick-create-workspace.md).

- [구독에서 클라우드에 대해 Microsoft Defender를 사용 하도록 설정](../security-center/security-center-get-started.md)합니다. 

   >[!NOTE]
   >Microsoft Defender for Cloud는 배포가 필요 하지 않은 미리 구성 된 도구 이지만이를 사용 하도록 설정 해야 합니다.

- [클라우드 용 Microsoft Defender를 사용 하도록 설정](../security-center/enable-azure-defender.md)합니다. 


## <a name="add-azure-vmware-solution-vms-to-defender-for-cloud"></a>클라우드에 대 한 Defender에 Azure VMware 솔루션 Vm 추가

1. Azure Portal에서 **Azure Arc** 를 검색하고 선택합니다.

2. 리소스에서 **서버** 를 선택하고 **+추가** 를 선택합니다.

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure에 Azure VMware Solution VM을 추가하기 위한 Azure Arc 서버 페이지를 보여 주는 스크린샷":::

3. **스크립트 생성** 을 선택합니다.
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="대화형 스크립트를 사용하여 서버를 추가하기 위한 옵션을 보여 주는 Azure Arc 페이지의 스크린샷"::: 
 
4. **필수 구성 요소** 탭에서 **다음** 을 선택합니다.

5. **리소스 세부 정보** 탭에서 다음 세부 정보를 입력한 후 **다음: 태그** 를 선택합니다. 

    - Subscription

    - Resource group

    - 지역 

    - 운영 체제

    - 프록시 서버 세부 정보
    
6. **태그** 탭에서 **다음** 을 선택합니다.

7. **스크립트 다운로드 및 실행** 탭에서 **다운로드** 를 선택합니다.

8. 운영 체제를 지정하고 Azure VMware Solution VM에서 스크립트를 실행합니다.

## <a name="view-recommendations-and-passed-assessments"></a>권장 사항 및 통과된 평가 보기

권장 사항 및 평가는 리소스의 보안 상태 세부 정보를 제공합니다. 

1. 클라우드 용 Microsoft Defender의 왼쪽 창에서 **인벤토리** 를 선택 합니다.

2. 리소스 종류의 경우, **서버 - Azure Arc** 를 선택합니다.
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="리소스 유형에서 선택한 서버-Azure Arc가 있는 클라우드 재고 용 Microsoft Defender 페이지를 보여 주는 스크린샷":::

3. 리소스 이름을 선택합니다. 리소스의 보안 상태 세부 정보를 보여 주는 페이지가 열립니다.

4. **권장 사항 목록** 에서 **권장 사항**, **통과된 평가** 및 **사용할 수 없는 평가** 탭을 선택하여 해당 세부 정보를 확인합니다.

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="클라우드 보안 권장 사항 및 평가에 대 한 Microsoft Defender를 보여 주는 스크린샷":::

## <a name="deploy-a-microsoft-sentinel-workspace"></a>Microsoft 센티널 작업 영역 배포

Microsoft 센티널은 환경 전체에서 보안 분석, 경고 검색 및 자동화 된 위협 응답을 제공 합니다. Log Analytics 작업 영역을 기반으로 구축된 클라우드 기본 SIEM(보안 정보 이벤트 관리) 솔루션입니다.

Microsoft 센티널은 Log Analytics 작업 영역을 기반으로 하기 때문에 사용할 작업 영역을 선택 하기만 하면 됩니다.

1. Azure Portal에서 **Microsoft 센티널** 를 검색 하 고 선택 합니다.

2. Microsoft 센티널 작업 영역 페이지에서 **+ 추가** 를 선택 합니다.

3. Log Analytics 작업 영역을 선택하고 **추가** 를 선택합니다.

## <a name="enable-data-collector-for-security-events"></a>보안 이벤트에 데이터 수집기 사용

1. Microsoft 센티널 작업 영역 페이지에서 구성 된 작업 영역을 선택 합니다.

2. 구성 아래에서 **데이터 커넥터** 를 선택합니다.

3. 커넥터 이름 열 아래 목록에서 **보안 이벤트** 를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

4. 커넥터 페이지에서 스트림 할 이벤트를 선택한 다음 **변경 내용 적용** 을 선택합니다.

   :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="스트리밍할 이벤트를 선택할 수 있는 Microsoft 센티널의 보안 이벤트 페이지 스크린샷":::




## <a name="connect-microsoft-sentinel-with-microsoft-defender-for-cloud"></a>커넥트 Microsoft에서 클라우드 용 Microsoft Defender로의 센티널  

1. Microsoft 센티널 작업 영역 페이지에서 구성 된 작업 영역을 선택 합니다.

2. 구성 아래에서 **데이터 커넥터** 를 선택합니다.

3. 목록에서 **Microsoft Defender For Cloud** 를 선택 하 고 **커넥터 페이지 열기** 를 선택 합니다.

   :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Microsoft 센티널과 클라우드에 Microsoft Defender를 연결 하기 위한 선택 항목을 보여 주는 Microsoft 센티널의 데이터 커넥터 페이지 스크린샷":::

4. **커넥트** 를 선택 하 여 microsoft에 클라우드 용 microsoft Defender를 연결 합니다.

5. **인시던트 만들기** 를 사용 하 여 클라우드 용 Microsoft Defender에 대 한 인시던트를 생성 합니다.

## <a name="create-rules-to-identify-security-threats"></a>보안 위협을 식별하는 규칙 만들기

Microsoft 센티널에 데이터 원본을 연결한 후 검색 된 위협에 대 한 경고를 생성 하는 규칙을 만들 수 있습니다. 다음 예제에서 잘못된 암호로 Windows 서버에 로그인하려고 시도하는 규칙을 만들어보겠습니다.

1. Microsoft 센티널 개요 페이지의 구성에서 **분석** 을 선택 합니다.

2. 구성에서 **분석** 을 선택합니다.

3. **+만들기** 를 선택하고 드롭다운에서 **예약된 쿼리 규칙** 을 선택합니다.

4. **일반** 탭에서 필수 정보를 입력하고 **다음: 규칙 논리 설정** 을 선택합니다.

    - 속성

    - 설명

    - 전술

    - 심각도

    - 상태

5. **규칙 논리 설정** 탭에서 필수 정보를 입력한 다음, **다음** 을 선택합니다.

    - 규칙 쿼리(예제 쿼리 여기 표시)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - 맵 엔터티

    - 쿼리 예약

    - 경고 임계값

    - 이벤트 그룹화

    - 표시 안 함


6. **인시던트 설정** 탭에서 **이 분석 규칙에 의해 트리거되는 경고에서 인시던트 만들기** 를 사용하도록 설정하고 **다음: 자동화된 응답** 을 선택합니다.
 
    :::image type="content" source="../sentinel/media/tutorial-detect-threats-custom/general-tab.png" alt-text="Microsoft 센티널에서 새 규칙을 만들기 위한 분석 규칙 마법사를 보여 주는 스크린샷":::

7. 완료되면 **다음: 검토** 를 클릭합니다.

8. **검토 및 만들기** 탭에서 정보를 검토하고 **만들기** 를 선택합니다.

>[!TIP]
>세 번째로 Windows 서버에 로그인하지 못한 후에 생성된 규칙은 실패한 모든 시도에 대한 인시던트를 트리거합니다.

## <a name="view-alerts"></a>경고 보기

Microsoft 센티널을 사용 하 여 생성 된 인시던트를 볼 수 있습니다. 인시던트를 할당 하 고 해결 되 면 모두 Microsoft 센티널 내에서 종료할 수도 있습니다.

1. Microsoft 센티널 개요 페이지로 이동 합니다.

2. 위협 관리에서 **인시던트** 를 선택합니다.

3. 인시던트 선택 후 해결을 위해 팀에 할당합니다.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="인시던트가 선택 된 Microsoft 센티널 인시던트 페이지의 스크린샷 및 해결을 위한 인시던트를 할당 하는 옵션입니다.":::

>[!TIP]
>문제를 해결한 후에는 종료할 수 있습니다.

## <a name="hunt-security-threats-with-queries"></a>쿼리를 사용하여 보안 위협 헌팅

Microsoft 센티널에서 사용 가능한 미리 정의 된 쿼리를 사용 하 여 사용자 환경에서 위협을 식별할 수 있습니다. 다음 단계에서는 미리 정의된 쿼리를 실행합니다.

1. Microsoft 센티널 개요 페이지의 위협 관리에서 **구하기** 를 선택 합니다. 미리 정의된 쿼리 목록이 나타납니다.

   >[!TIP]
   >**새 쿼리** 를 선택하여 새 쿼리를 만들 수도 있습니다. 
   >
   >:::image type="content" source="../sentinel/media/hunting/save-query.png" alt-text="+ 새 쿼리가 강조 표시 된 Microsoft 센티널 구하기 페이지의 스크린샷":::

3. 쿼리를 선택한 다음 **쿼리 실행** 을 선택합니다.

4. **결과 보기** 를 선택하여 결과를 확인합니다.



## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution VM을 보호하는 방법을 살펴보았으므로 다음 사항에 대해 알아볼 수 있습니다.

- [작업 보호 대시보드 사용](../security-center/azure-defender-dashboard.md)
- [Microsoft 센티널의 Advanced 다단계 공격 감지](../azure-monitor/logs/quick-create-workspace.md)
- [Azure VMware Solution에서 Azure 네이티브 서비스 통합](integrate-azure-native-services.md)

---
title: 위협 인텔리전스 데이터 업데이트
description: 위협 인텔리전스 데이터 패키지는 각 새 Defender for IoT 버전과 함께 제공되거나 필요한 경우 릴리스 간에 제공됩니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: ac743c9e8c53e40743bda0c50017885f40381971
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293196"
---
# <a name="threat-intelligence-research-and-packages"></a>위협 인텔리전스 리서치 및 패키지 #
## <a name="overview"></a>개요 ##

Microsoft의 보안팀은 독점 ICS 위협 인텔리전스 및 취약성 연구를 수행합니다. 이 팀에는 MSTIC(Microsoft 위협 인텔리전스 센터), DART(Microsoft Detection and Response Team), DCU(Digital Crimes Unit) 및 섹션 52(ICS 관련 제로 데이, 리버스 엔지니어링 맬웨어, 캠페인, 악의적 사용자를 추적하는 IoT/OT/ICS 도메인 전문가)가 포함됩니다.

해당 팀은 다음 Microsoft의 서비스에 대한 보안 검색, 분석, 대응을 제공합니다.

- 클라우드 인프라 및 서비스.
- 기존 제품 및 디바이스.
- 내부 회사 리소스.

보안 팀은 다음과 같은 이점을 얻을 수 있습니다.

- 알려진 위협 및 관련 위협으로부터의 보호.
- 심사 및 우선 순위를 지정하는 데 도움이 되는 인사이트.
- 위협의 영향을 받기 전에 위협에 대한 전체 컨텍스트 이해.
- 더욱 적절하고 정확하며 실행 가능한 데이터.

이 인텔리전스는 상황에 맞는 정보를 제공하여 Microsoft 플랫폼 분석을 강화하고 인시던트 대응 및 위반 조사를 위해 회사의 관리 서비스를 지원합니다. 위협 인텔리전스 패키지에는 서명(맬웨어 서명 포함), CVE, 기타 보안 콘텐츠가 포함됩니다.

## <a name="when-are-packages-delivered"></a>패키지 제공 시기 ##

위협 인텔리전스 패키지는 대략적으로 한 달에 한 번 또는 필요한 경우에는 이보다 더 자주 제공됩니다. 새 패키지 공지는 https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT 에서 확인할 수 있습니다.

또한 Azure Portal Defender for IoT의 **업데이트** 페이지에 있는 **위협 인텔리전스 업데이트** 섹션에서 제공되는 최신 패키지를 확인할 수 있습니다.

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>센서에 위협 인텔리전스 패키지 업데이트 ##

센서에 위협 인텔리전스 패키지를 업데이트하기 위해서는 세 가지 옵션이 제공됩니다.

- Defender for IoT에 제공될 때 패키지를 센서에 자동으로 푸시합니다.
- 필요에 따라 위협 인텔리전스 패키지를 센서에 수동으로 푸시합니다.
- 패키지를 다운로드하고 이를 하나 또는 여러 개의 센서에 업로드합니다.

Defender for IoT 보안 읽기 권한자 권한이 있는 사용자는 자동 및 수동으로 패키지를 센서에 푸시할 수 있습니다.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>위협 인텔리전스 업데이트를 센서에 자동으로 푸시 ###

Defender for IoT에서 릴리스될 때 *클라우드에 연결된* 센서에 위협 인텔리전스 패키지를 자동으로 업데이트할 수 있습니다. 자동 패키지 업데이트가 수행될 수 있도록 **자동 위협 인텔리전스 업데이트** 옵션을 사용해서 클라우드에 연결된 센서를 온보딩합니다. 자세한 내용은 [센서 온보딩](getting-started.md#onboard-a-sensor)을 참조하세요.

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>위협 인텔리전스 업데이트를 센서에 수동으로 푸시 ###

위협 인텔리전스 패키지를 사용하여 *클라우드에 연결된* 센서를 자동으로 업데이트할 수 있습니다. 그러나 좀 더 논리적으로 접근하려는 경우 필요한 경우에만 Defender for IoT에서 센서로 패키지를 푸시할 수 있습니다. 그러면 패키지를 다운로드한 후 센서에 업로드할 필요 없이 패키지 설치 시기를 제어할 수 있습니다.

**패키지를 수동으로 푸시하려면 다음과 같이 하십시오.**

1. Microsoft Defender for IoT **사이트 및 센서 페이지로** 이동합니다.
1. 특정 센서의 줄임표(...)를 선택한 후 **위협 인텔리전스 업데이트 푸시** 를 선택합니다. **위협 인텔리전스 업데이트 상태** 필드에 업데이트 진행 상태가 표시됩니다.

#### <a name="change-the-threat-intelligence-update-mode"></a>위협 인텔리전스 업데이트 모드 변경 ####

초기 온보딩 후 센서 위협 인텔리전스 업데이트 모드를 변경할 수 있습니다.

**업데이트 모드를 변경하려면 다음과 같이 하십시오.**

1. 센서에 대해 줄임표(...)를 선택한 후 **편집** 을 선택합니다.
1. **자동 위협 인텔리전스 업데이트** 토글을 사용하거나 사용하지 않도록 설정합니다.

### <a name="download-packages-and-upload-to-sensors"></a>패키지 다운로드 및 센서에 업로드 ###

패키지는 Azure Portal 다운로드하고 개별 센서에 수동으로 업로드할 수 있습니다. 온-프레미스 관리 콘솔에서 센서를 관리하는 경우 위협 인텔리전스 패키지를 관리 콘솔로 다운로드하고 여러 센서로 동시에 푸시할 수 있습니다.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Azure Portal 업데이트를 다운로드합니다.":::

이 옵션은 *클라우드에 연결된* 센서 및 *로컬로 관리되는* 센서 모두에서 사용할 수 있습니다.

**단일 센서에 업로드하려면 다음과 같이 하십시오.**

1. Microsoft Defender for IoT **업데이트 페이지로** 이동합니다.

2. **위협 인텔리전스** 패키지를 다운로드하고 저장합니다.

3. 센서 콘솔에 로그인합니다.

4. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

5. **위협 인텔리전스 데이터** 를 선택하고 **업데이트** 를 선택합니다.

6. 새 패키지를 업로드합니다.

**여러 센서에 동시에 업로드하려면 다음과 같이 하십시오.**

1. Microsoft Defender for IoT **업데이트 페이지로** 이동합니다.

2. **위협 인텔리전스** 패키지를 다운로드하고 저장합니다.

3. 관리 콘솔에 로그인합니다.

4. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

5. **센서 엔진 구성** 섹션에서 업데이트된 패키지를 수신해야 하는 센서를 선택합니다.  

6. **위협 인텔리전스 데이터 선택** 섹션에서 더하기 기호( **+** )를 선택합니다.

7. 패키지를 업로드합니다.

## <a name="review-package-update-status-on-the-sensor"></a>센서의 패키지 업데이트 상태 검토 ##

패키지 업데이트 상태 및 버전 정보는 센서 **시스템 설정**, **위협 인텔리전스** 섹션에 표시됩니다.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>클라우드에 연결된 센서의 패키지 정보 검토 ##

클라우드에 연결된 센서에서 위협 인텔리전스 패키지에 대해 다음 정보를 검토하세요.

- 설치된 패키지 버전
- 위협 인텔리전트 업데이트 모드
- 위협 인텔리전스 업데이트 상태

위협 인텔리전스 정보를 검토하려면 다음과 같이 하십시오.

1. Microsoft Defender for IoT **사이트 및 센서 페이지로** 이동합니다.
1. 각 센서에 설치된 **위협 인텔리전스 버전** 을 검토합니다. 버전 이름 지정은 Defender for IoT에서 패키지를 빌드한 날짜를 기준으로 합니다.
1. **위협 인텔리전스 모드** 를 검토합니다. *자동* 이면 새로 사용 가능한 패키지가 Defender for IoT에서 릴리스될 때 센서에 자동으로 설치됩니다. *수동* 이면 새로 사용 가능한 패키지를 필요에 따라 센서에 직접 푸시할 수 있습니다.
1. **위협 인텔리전스 업데이트 상태** 를 검토합니다. 다음 상태가 표시될 수 있습니다.

- Failed
- 진행 중
- 업데이트 사용 가능
- 확인

클라우드에 연결된 위협 인텔리전스 업데이트가 실패하면 **사이트 및 센서** 페이지에서 **센서 상태** 및 **마지막 연결 시간(UTC)** 열에서 연결 정보를 검토합니다. 

## <a name="see-also"></a>참조

[센서 온보딩](getting-started.md#onboard-a-sensor)

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)

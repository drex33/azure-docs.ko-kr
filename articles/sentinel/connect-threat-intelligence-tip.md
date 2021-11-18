---
title: Microsoft 센티널에 위협 인텔리전스 플랫폼 커넥트 | Microsoft Docs
description: Microsoft 센티널에 위협 인텔리전스 플랫폼 (TIP) 또는 사용자 지정 피드를 연결 하 고 위협 지표를 보내는 방법에 대해 알아봅니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0e75e14af24aa25d1ab2e7dc4f77e1568a4be6a9
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751824"
---
# <a name="connect-your-threat-intelligence-platform-to-microsoft-sentinel"></a>Microsoft 센티널에 위협 인텔리전스 플랫폼 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**참고** 항목 [커넥트: Microsoft 센티널 to stix/TAXII 위협 인텔리전스 피드](connect-threat-intelligence-taxii.md)

많은 조직에서는 TIP (위협 인텔리전스 플랫폼) 솔루션을 사용 하 여 다양 한 원본에서 위협 지표 피드를 집계 하 고, 플랫폼 내에서 데이터를 만든 다음, 네트워크 장치, EDR/n e t 솔루션 또는 siems (예: Microsoft 센티널)와 같은 다양 한 보안 솔루션에 적용할 위협 지표를 선택 합니다. **위협 인텔리전스 플랫폼 데이터 커넥터** 를 사용 하면 이러한 솔루션을 사용 하 여 위협 지표를 Microsoft 센티널로 가져올 수 있습니다. 

TIP 데이터 커넥터는 [Microsoft Graph Security tiindicators API](/graph/api/resources/tiindicator) 와 함께 작동 하므로이를 위해 커넥터를 사용 하 여 해당 api와 통신할 수 있는 다른 사용자 지정 위협 인텔리전스 플랫폼의 microsoft 센티널 (및 Microsoft 365 Defender와 같은 기타 Microsoft 보안 솔루션)에 지표를 보낼 수 있습니다.

:::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-import-path.png" alt-text="위협 인텔리전스 가져오기 경로":::

Microsoft 센티널의 [위협 인텔리전스](understand-threat-intelligence.md) 에 대해 자세히 알아보고 microsoft 센티널과 통합할 수 있는 [위협 인텔리전스 플랫폼 제품](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products) 에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>필수 조건  

- Microsoft Graph Security tiIndicators API에 대한 직접 통합을 사용하는 TIP 제품이나 사용자 지정 애플리케이션에 대한 권한을 부여하려면 **전역 관리자** 또는 **보안 관리자** Azure AD 역할이 있어야 합니다.

- 위협 지표를 저장 하려면 Microsoft 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

## <a name="instructions"></a>Instructions

다음 단계에 따라 통합 팁 또는 사용자 지정 위협 인텔리전스 솔루션에서 Microsoft 센티널에 위협 지표를 가져옵니다.
1.  Azure Active Directory에서 애플리케이션 ID 및 클라이언트 비밀 가져오기
2.  TIP 솔루션 또는 사용자 지정 애플리케이션에 이 정보를 입력
3.  Microsoft 센티널에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용 하도록 설정

### <a name="sign-up-for-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 ID 및 클라이언트 암호 등록

TIP을 사용하든 사용자 지정 솔루션을 사용하든 관계없이 tiIndicators API를 사용하려면 피드를 연결하고 위협 지표를 보내기 위한 몇 가지 기본 정보가 필요합니다. 다음 세 가지 정보가 필요합니다.

- 애플리케이션(클라이언트) ID
- 디렉터리(테넌트) ID
- 클라이언트 암호

이 정보는 **앱 등록** 이라는 프로세스를 통해 Azure Active Directory에서 가져올 수 있습니다. 이 프로세스에는 다음 세 단계가 포함됩니다.

- Azure Active Directory에 앱 등록
- 앱이 Microsoft Graph tiIndicators API에 연결하고 위협 지표를 보내기 위해 필요한 권한을 지정합니다.
- 조직에서 동의를 받아 이 애플리케이션에 이러한 권한을 부여합니다.

#### <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory에 애플리케이션 등록

1. Azure Portal을 열고 **Azure Active Directory** 서비스로 이동합니다.
1. 메뉴에서 **앱 등록** 을 선택하고 **새 등록** 을 선택합니다.
1. 애플리케이션 등록의 이름을 선택하고, **단일 테넌트** 라디오 단추를 선택하고, **등록** 을 선택합니다. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-register-application.png" alt-text="애플리케이션 등록":::

1. 결과 화면에서 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 값을 복사합니다. Microsoft 센티널에 위협 지표를 보내도록 팁 또는 사용자 지정 솔루션을 구성 하는 데 필요한 첫 번째 두 가지 정보는 다음과 같습니다. 세 번째로 **클라이언트 암호** 가 필요합니다.

#### <a name="specify-the-permissions-required-by-the-application"></a>애플리케이션에 필요한 권한 지정

1. **Azure Active Directory** 서비스의 기본 페이지로 돌아갑니다.

1. 메뉴에서 **앱 등록** 을 선택하고 새로 등록한 앱을 선택합니다.

1. 메뉴에서 **API 권한** 을 선택하고 **권한 추가** 단추를 선택합니다.

1. **API 선택** 페이지에서 **Microsoft Graph** API를 선택한 후 Microsoft Graph 권한 목록 중에서 선택합니다.

1. "애플리케이션에 어떤 유형의 권한이 필요한가요?" 프롬프트에서 **애플리케이션 권한** 을 선택합니다. 이는 앱 ID 및 앱 비밀(API 키)을 사용하여 인증하는 애플리케이션에서 사용하는 유형의 권한입니다.

1. **ThreatIndicators.ReadWrite.OwnedBy** 를 선택하고 **권한 추가** 를 선택하여 앱의 권한 목록에 이 권한을 추가합니다.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-1.png" alt-text="권한 지정":::

#### <a name="get-consent-from-your-organization-to-grant-these-permissions"></a>조직에서 동의를 받아 이 애플리케이션에 이러한 권한을 부여

1. 동의를 받으려면 앱의 **API 권한** 페이지에서 **테넌트에 대한 관리자 동의 부여** 단추를 선택할 수 있는 Azure Active Directory 전역 관리자가 필요합니다. 계정에 대한 전역 관리자 역할이 없으면 이 단추를 사용할 수 없으며 조직의 전역 관리자에게 이 단계를 수행하도록 요청해야 합니다. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-2.png" alt-text="동의 권한 부여":::

1. 앱에 동의가 부여되면 **상태** 아래에 녹색 확인 표시가 표시됩니다.

이제 앱이 등록되고 권한이 부여되었으므로 목록의 마지막 항목인 앱의 클라이언트 비밀을 가져올 수 있습니다.

1. **Azure Active Directory** 서비스의 기본 페이지로 돌아갑니다.

1. 메뉴에서 **앱 등록** 을 선택하고 새로 등록한 앱을 선택합니다.

1. 메뉴에서 **인증서 및 비밀** 을 선택하고 **새 클라이언트 암호** 단추를 선택하여 앱의 암호(API 키)를 받습니다.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-client-secret.png" alt-text="클라이언트 비밀 가져오기":::

1. **추가** 단추 및 **클라이언트 암호 복사** 를 선택합니다.

    > [!IMPORTANT]
    > 이 화면을 나가기 전에 **클라이언트 암호** 를 복사해야 합니다. 이 페이지에서 다른 곳으로 이동하는 경우 이 암호를 다시 검색할 수 없습니다. TIP 또는 사용자 지정 솔루션을 구성할 때 이 값이 필요합니다.

### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>TIP 솔루션 또는 사용자 지정 애플리케이션에 이 정보를 입력

이제 Microsoft 센티널에 위협 지표를 보내도록 팁 또는 사용자 지정 솔루션을 구성 하는 데 필요한 세 가지 정보를 모두 사용할 수 있습니다.

- 애플리케이션(클라이언트) ID
- 디렉터리(테넌트) ID
- 클라이언트 암호

1. 필요한 경우 통합 TIP 또는 사용자 지정 솔루션의 구성에 이러한 값을 입력합니다.

1. 대상 제품에 대해 **Microsoft 센티널** 을 지정 합니다.

1. 작업의 경우 **경고** 를 지정합니다.

이 구성이 완료 되 면 Microsoft 센티널을 대상으로 하는 **Microsoft Graph tiindicators API** 를 통해 팁 또는 사용자 지정 솔루션에서 위협 표시기가 전송 됩니다.

### <a name="enable-the-threat-intelligence-platforms-data-connector-in-microsoft-sentinel"></a>Microsoft 센티널에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용 하도록 설정

통합 프로세스의 마지막 단계는 Microsoft 센티널에서 **위협 인텔리전스 플랫폼 데이터 커넥터** 를 사용 하도록 설정 하는 것입니다. 커넥터를 사용 하도록 설정 하면 Microsoft 센티널이 TIP 또는 사용자 지정 솔루션에서 전송 된 위협 지표를 받을 수 있습니다. 이러한 지표는 조직의 모든 Microsoft 센티널 작업 영역에서 사용할 수 있습니다. 각 작업 영역에서 위협 인텔리전스 플랫폼 데이터 커넥터를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Microsoft 센티널** 서비스로 이동 합니다.

1. TIP 또는 사용자 지정 솔루션에서 보낸 위협 지표를 가져올 **작업 영역** 을 선택합니다.

1. 메뉴에서 **데이터 커넥터** 를 선택하고 커넥터 갤러리에서 **위협 인텔리전스 플랫폼** 을 선택한 다음 **커넥터 페이지 열기** 단추를 선택합니다.

1. 앱 등록을 이미 완료했고 위협 지표를 보내도록 TIP 또는 사용자 지정 솔루션을 구성했으므로 이제 남은 단계는 **연결** 단추를 선택하는 것입니다.

몇 분 내에 위협 표시기가이 Microsoft 센티널 작업 영역으로 이동 하기 시작 합니다. Microsoft 센티널 탐색 메뉴에서 액세스할 수 있는 **위협 인텔리전스** 블레이드에서 새 지표를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 위협 인텔리전스 플랫폼을 Microsoft 센티널에 연결 하는 방법을 알아보았습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](./detect-threats-built-in.md)시작 하세요.

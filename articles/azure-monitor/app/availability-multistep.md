---
title: 다단계 웹 테스트를 사용하여 모니터링 - Azure Application Insights
description: Azure Application Insights를 사용하여 웹 애플리케이션을 모니터링하도록 다단계 웹 테스트 설정
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: d248340aa272a6a1fef386ca755ed46536668ad9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448001"
---
# <a name="multi-step-web-tests"></a>다중 단계 웹 테스트

다단계 웹 테스트를 통해 웹 사이트와의 상호 작용 및 URL의 기록된 시퀀스를 모니터링할 수 있습니다. 이 문서에서는 Visual Studio Enterprise를 사용하여 다단계 웹 테스트를 만드는 과정을 안내합니다.

> [!NOTE]
> 다단계 웹 테스트는 [Azure Government](../../azure-government/index.yml) 클라우드에서 **지원되지 않습니다**.

> [!NOTE]
> 다단계 웹 테스트는 클래식 테스트로 분류되며 가용성 창의 **클래식 테스트 추가** 에서 찾을 수 있습니다.

## <a name="multi-step-webtest-alternative"></a>다단계 웹 테스트 대체

다단계 웹 테스트는 Visual Studio webtest 파일에 따라 달라집니다. Visual Studio 2019가 웹 테스트 기능을 사용하는 마지막 버전이 될 것이라고 [발표](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)했습니다. 새 기능이 추가되는 것은 아니지만 Visual Studio 2019의 웹 테스트 기능은 현재 지원되며 제품의 지원 수명 주기 동안 계속 지원된다는 점을 이해해야 합니다. 

다단계 웹 테스트 대신 [TrackAvailability](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)를 사용하여 [사용자 지정 가용성 테스트](./availability-azure-functions.md)를 제출하는 것이 좋습니다. 이는 다중 요청 또는 인증 테스트 시나리오에 대해 장기적으로 지원되는 솔루션입니다. TrackAvailability() 및 사용자 지정 가용성 테스트를 사용하면 원하는 모든 컴퓨팅에서 테스트를 실행하고 C#을 사용하여 새 테스트를 쉽게 작성할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

* Visual Studio 2017 Enterprise 이상
* Visual Studio 웹 성능 및 부하 테스트 도구

테스트 도구 필수 구성 요소를 찾으려면 다음을 실행합니다. **Visual Studio 설치 관리자** > **개별 구성 요소** > **디버깅 및 테스트** > **웹 성능 및 부하 테스트 도구** 를 시작합니다.

![웹 성능 및 부하 테스트 도구 항목 옆에 있는 확인란을 선택하여 개별 구성 요소가 선택된 Visual Studio 설치 관리자 UI의 스크린샷](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 다단계 웹 테스트에는 이와 관련된 추가 비용이 있습니다. 자세한 내용은 [공식 가격 책정 가이드](https://azure.microsoft.com/pricing/details/application-insights/)를 참조하세요.

## <a name="record-a-multi-step-web-test"></a>다단계 웹 테스트 기록 

> [!WARNING]
> 다단계 레코더를 사용하는 것이 더 이상 권장되지 않습니다. 이 레코더는 기본 상호 작용이 포함된 정적 HTML 페이지에 대해 개발되었으며, 최신 웹 페이지에 대한 기능 환경을 제공하지 않습니다.

Visual Studio 웹 테스트를 만드는 방법에 대한 지침은 [공식 Visual Studio 2019 설명서](/visualstudio/test/how-to-create-a-web-service-test)를 참조하세요.

## <a name="upload-the-web-test"></a>웹 테스트 업로드

1. 가용성 창의 Application Insights 포털에서 **클래식 테스트 추가** 를 선택한 다음, *SKU* 로 **다단계** 를 선택합니다.
2. 다단계 웹 테스트를 업로드합니다.
3. 테스트 위치, 빈도 및 경고 매개 변수를 설정합니다.
4. **만들기** 를 선택합니다.

### <a name="frequency--location"></a>빈도 및 위치

|설정| 설명
|----|----|----|
|**테스트 빈도**| 각 테스트 위치에서 테스트를 실행하는 빈도를 설정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.|
|**테스트 위치**| 서버가 URL로 웹 요청을 보내는 곳입니다. 웹 사이트 문제를 네트워크 문제와 구분할 수 있도록 적어도 **5개 이상의 테스트 위치를 권장** 합니다. 최대 16 개의 위치를 선택할 수 있습니다.

### <a name="success-criteria"></a>성공 조건

|설정| 설명
|----|----|----|
| **테스트 시간 제한** |느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석** 을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.|
| **HTTP 응답** | 성공으로 계산되어 반환된 상태 코드입니다. 200은 일반적인 웹 페이지의 반환을 나타내는 코드입니다.|
| **콘텐츠 일치** | "Welcome!"과 같은 문자열 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다. **콘텐츠 일치에서는 영어 문자만 지원됩니다.** |

### <a name="alerts"></a>경고

|설정| 설명
|----|----|----|
|**거의 실시간(미리 보기)** | 거의 실시간으로 경고를 사용하는 것이 좋습니다. 이 유형의 경고 구성은 가용성 테스트를 만든 후에 수행됩니다.  |
|**경고 위치 임계값**|최소 3/5 위치를 사용하는 것이 좋습니다. 경고 위치 임계값과 테스트 위치 수 사이의 최적 관계는 **경고 위치 임계값** = **테스트 위치 수 - 2이고, 최소 테스트 위치 수는 5입니다.**|

## <a name="configuration"></a>구성

### <a name="plugging-time-and-random-numbers-into-your-test"></a>테스트에 시간과 난수 연결

외부 피드에서 주식과 같이 시간에 따라 변하는 데이터를 가져오는 도구를 테스트한다고 가정합니다. 웹 테스트를 기록할 때 특정 시간을 사용해야 하지만 테스트 매개 변수로 StartTime 및 EndTime을 설정할 수 있습니다.

![내 뛰어난 재고 앱 스크린샷](./media/availability-multistep/app-insights-72webtest-parameters.png)

테스트를 실행하면 EndTime이 항상 현재 시간이 되고 StartTime은 15분이 됩니다.

웹 테스트 날짜 시간 플러그 인은 매개 변수화된 시간을 처리하는 방법을 제공합니다.

1. 원하는 각 가변 매개 변수 값에 대한 웹 테스트 플러그 인을 추가합니다. 웹 테스트 도구 모음에서 **웹 테스트 플러그 인 추가** 를 선택합니다.
    
    ![웹 테스트 플러그 인 추가](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    이 예에서는 날짜 시간 플러그인의 두 인스턴스를 사용합니다. 한 인스턴스는 "15분 전"이고 다른 하나는 "지금"입니다.

2. 각 플러그인의 속성을 엽니다. 이름을 지정하고 현재 시간을 사용하도록 설정합니다. 둘 중 하나에 대해 Add Minutes = -15를 설정합니다.

    ![컨텍스트 매개 변수](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 웹 테스트 매개 변수에 {{플러그 인 이름}}을(를) 사용하여 플러그 인 이름을 참조합니다.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

이제 테스트를 포털에 업로드합니다. 테스트의 모든 실행에 동적 값을 사용합니다.

### <a name="dealing-with-sign-in"></a>로그인 처리

사용자가 앱에 로그인하면 로그인 뒤에 페이지를 테스트할 수 있도록 로그인을 시뮬레이션하기 위한 여러 옵션이 있습니다. 앱에서 제공하는 보안의 형식에 따라 사용하는 방법이 달라집니다.

모든 경우에 테스트하기 위해 애플리케이션에 계정을 만들어야 합니다. 웹 테스트가 실제 사용자에 영향을 줄 가능성이 발생하지 않도록 가능하면 이 테스트 계정의 사용 권한을 제한합니다.

**간단한 사용자 이름 및 암호** 일반적인 방법으로 웹 테스트를 기록합니다. 우선 쿠키를 삭제합니다.

**SAML 인증**

|속성 이름| Description|
|----|-----|
| 대상 URI | SAML 토큰에 대한 대상 URI입니다.  ACS 네임스페이스 및 호스트 이름을 포함하여 ACS(Access Control Service)에 대한 URI입니다. |
| 인증서 암호 | 포함된 프라이빗 키에 대한 액세스 권한을 부여하는 클라이언트 인증서의 암호입니다. |
| 클라이언트 인증서  | Base64 인코딩 형식의 프라이빗 키가 있는 클라이언트 인증서 값입니다. |
| 이름 식별자 | 토큰의 이름 식별자 |
| 다음 이후로는 아님 | 토큰이 유효한 시간 간격입니다.  기본값은 5분입니다. |
| 이전이 아님 | 이전에 만든 토큰이 유효한 시간 범위입니다(시간 왜곡 문제 해결).  기본값은 (음수) 5분입니다. |
| 대상 컨텍스트 매개 변수 이름 | 생성된 어설션을 수신하는 컨텍스트 매개 변수입니다. |


**클라이언트 암호** 앱에 클라이언트 암호를 포함하는 로그인 경로가 있는 경우 해당 경로를 사용합니다. AAD(Azure Active Directory)는 클라이언트 암호 로그인을 제공하는 서비스의 예입니다. AAD에서 클라이언트 암호는 앱 키입니다.

앱 키를 사용하는 Azure 웹앱의 샘플 웹 테스트는 다음과 같습니다.

![샘플 스크린샷](./media/availability-multistep/client-secret.png)

클라이언트 암호(AppKey)를 사용하여 AAD에서 토큰을 가져옵니다.
응답에서 전달자 토큰을 추출합니다.
인증 헤더에서 전달자 토큰을 사용하여 API를 호출합니다.
웹 테스트는 실제 클라이언트여야 합니다. 즉, AAD에 고유한 앱이 있고 clientId + 앱 키를 사용합니다. 테스트 대상 서비스는 AAD에 고유한 앱을 가집니다. 이 앱의 appID URI는 리소스 필드의 웹 테스트에 반영됩니다.

### <a name="open-authentication"></a>공개 인증
공개 인증의 예는 Microsoft 또는 Google 계정으로 로그인하는 것입니다. OAuth를 사용하는 많은 앱은 클라이언트 암호 대안을 제공하므로 첫 번째 방법으로 해당 가능성을 조사해야 합니다.

테스트가 OAuth를 사용하여 로그인해야 하는 경우 일반적인 방법은 다음과 같습니다.

Fiddler와 같은 도구를 사용하여 웹 브라우저, 인증 사이트 및 응용 프로그램 간의 트래픽을 검사합니다.
다른 컴퓨터 또는 브라우저를 사용하거나 긴 간격으로 두 개 이상의 로그인을 수행합니다(토큰이 만료되도록 허용).
다른 세션을 비교하여 인증 사이트에서 다시 전달된 토큰을 식별하고 이는 로그인한 후에 앱 서버에 전달됩니다.
Visual Studio Online을 사용하여 웹 테스트 기록
토큰을 매개 변수화하고 토큰이 인증자에서 반환되면 매개 변수를 설정하고 사이트에 대한 쿼리에서 사용합니다. (Visual Studio는 테스트를 매개 변수화하려고 하지만 올바르게 토큰을 매개 변수화하지 않습니다.)

## <a name="troubleshooting"></a>문제 해결

전용 [문제 해결 문서](troubleshoot-availability.md)

## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [Url ping 웹 테스트](monitor-web-app-availability.md)

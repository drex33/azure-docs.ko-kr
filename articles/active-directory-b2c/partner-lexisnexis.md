---
title: LexisNexis를 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 프로파일링 및 ID 유효성 검사 서비스로, 사용자의 디바이스에 따라 사용자 ID를 확인하고 포괄적인 위험 평가를 제공하는 LexisNexis에 Azure AD B2C 인증을 통합하는 방법을 알아봅니다.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 18dd0fef7cc4a2d344abaf392bcc2ca3331bd828
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021907"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 LexisNexis를 구성하기 위한 자습서

이 샘플 자습서에서는 Azure AD(Active Directory) B2C를 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)와 통합하는 방법에 대한 지침을 제공합니다. LexisNexis는 다양한 솔루션을 제공하며 [여기](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)에서 찾을 수 있습니다. 이 샘플 자습서에서는 LexisNexis의 **ThreatMetrix** 솔루션을 다룹니다. ThreatMetrix는 프로파일링 및 ID 유효성 검사 서비스입니다. 사용자 ID를 확인하고 사용자의 디바이스에 따라 포괄적인 위험 평가를 제공하는 데 사용됩니다.

이 통합은 등록 흐름 중에 사용자가 제공하는 몇 가지 사용자 정보를 기준으로 프로파일링을 수행합니다. ThreatMetrix는 사용자가 계속 로그인할 수 있도록 허용할지 여부를 결정합니다. 다음 특성은 ThreatMetrix의 위험 분석에서 고려됩니다.

- 메일
- 전화 번호
- 사용자의 머신에서 수집된 프로파일링 정보

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md)를 Azure 구독에 연결

## <a name="scenario-description"></a>시나리오 설명

ThreatMetrix 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C - 사용자의 자격 증명(ID 공급자라고도 함)을 확인하는 권한 부여 서버입니다.

- ThreatMetrix – ThreatMetrix 서비스는 사용자가 제공하는 입력을 가져와 사용자의 머신에서 수집된 프로파일링 정보와 결합한 후 사용자 상호 작용의 보안을 확인합니다.

- 사용자 지정 Rest API – 이 API는 Azure AD B2C와 ThreatMetrix 서비스 간의 통합을 구현합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![lexisnexis 아키텍처 다이어그램 스크린샷](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|단계 | 설명 |
|:--------------|:-------------|
|1. | 사용자가 로그인 페이지를 엽니다. 사용자가 등록을 선택하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C가 사용자 특성을 수집합니다.
| 2. | Azure AD B2C는 중간 계층 API를 호출하고 사용자 특성을 전달합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집하고 LexisNexis API에서 사용할 수 있는 형식으로 변환합니다. 그런 다음, LexisNexis로 보냅니다.  
| 4. | LexisNexis는 정보를 사용하고 이를 처리하여 위험 분석을 기반으로 사용자 ID의 유효성을 검사합니다. 그런 다음, 중간 계층 API에 결과를 반환합니다.
| 5. | 중간 계층 API는 정보를 처리하고 Azure AD B2C에 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. 성공 응답이 표시되면 사용자가 인증되고 액세스 권한이 부여됩니다.

## <a name="onboard-with-lexisnexis"></a>LexisNexis로 온보딩

1. LexisNexis 계정을 만들려면 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)에 문의하세요.

2. 요구 사항에 맞는 LexisNexis 정책을 만듭니다. [여기](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)에서 사용 가능한 설명서를 참조하세요.

>[!NOTE]
> 정책 이름은 나중에 사용됩니다.

계정이 만들어지면 API 구성에 필요한 정보를 받게 됩니다. 다음 섹션은 이 프로세스를 설명합니다.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>LexisNexis를 사용하여 Azure AD B2C 구성

### <a name="part-1---deploy-the-api"></a>1부 - API 배포

제공된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api)를 Azure 서비스에 배포합니다. 이러한 [지침](/visualstudio/deployment/quickstart-deploy-to-azure)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

>[!NOTE]
>필요한 설정을 사용하여 Azure AD를 구성하려면 배포된 서비스의 URL이 필요합니다.

### <a name="part-2---configure-the-api"></a>2부 - API 구성

애플리케이션 설정은 [Azure의 App Service에서 구성](../app-service/configure-common.md#configure-app-settings)할 수 있습니다.  이 방법을 사용하면 설정을 리포지토리로 체크 인하지 않고도 안전하게 구성할 수 있습니다. Rest API에 대한 다음 설정을 제공해야 합니다.

| 애플리케이션 설정 | 원본 | 참고 |
| :-------- | :------------| :-----------|
|ThreatMetrix:Url | ThreatMetrix 계정 구성 |     |
|ThreatMetrix:OrgId | ThreatMetrix 계정 구성 |     |
|ThreatMetrix:ApiKey |ThreatMetrix 계정 구성|  |
|ThreatMetrix:Policy | ThreatMetrix에서 만든 정책의 이름 | |
| BasicAuth:ApiUsername |API에 대한 사용자 이름 정의| 사용자 이름은 Azure AD B2C 구성에 사용됩니다.
| BasicAuth:ApiPassword | API에 대한 암호를 정의합니다. | 암호는 Azure AD B2C 구성에서 사용됩니다.

### <a name="part-3---deploy-the-ui"></a>3부 - UI 배포

이 솔루션은 Azure AD B2C에서 로드하는 사용자 지정 UI 템플릿을 사용합니다. 이러한 UI 템플릿은 ThreatMetrix 서비스로 직접 전송되는 프로파일링을 수행합니다.

이러한 [지침](./customize-ui-with-html.md#custom-page-content-walkthrough)을 참조하여 포함된 [UI 파일](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template)을 Blob Storage 계정에 배포합니다. 이러한 지침에는 Blob Storage 계정 설정, CORS 구성 및 퍼블릭 액세스 사용 설정이 포함됩니다.

UI는 [바다색 템플릿](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)을 기준으로 합니다. 배포된 위치를 참조하도록 UI 내의 모든 링크를 업데이트해야 합니다. UI 폴더에서 https://yourblobstorage/blobcontainer 를 찾아 배포된 위치로 바꿉니다.

### <a name="part-4---create-api-policy-keys"></a>4부 - API 정책 키 만들기

이 [문서](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys)를 참조하여 두 개의 정책 키를 만듭니다. 하나는 API 사용자 이름, 하나는 위에서 정의한 API 암호입니다.

샘플 정책은 다음 키 이름을 사용합니다.

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>5부 - API URL 업데이트

제공된 [TrustFrameworkExtensions 정책](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)에서 `Rest-LexisNexus-SessionQuery`라는 기술 프로필을 찾은 후 `ServiceUrl` 메타데이터 항목을 위에 배포된 API의 위치로 업데이트합니다.

### <a name="part-6---update-ui-url"></a>6부 - UI URL 업데이트

제공된 [TrustFrameworkExtensions 정책](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)에서 찾기 및 바꾸기를 수행하여 https://yourblobstorage/blobcontainer/ 를 검색한 후 UI 파일이 배포된 위치로 바꿉니다.

>[!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보가 보내짐을 사용자에게 알립니다.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>7부 - Azure AD B2C 정책 구성

이 [문서](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)를 참조하여 [로컬 계정 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드하고 Azure AD B2C 테넌트에 대한 [정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy)을 구성합니다.

>[!NOTE]
>특정 테넌트와 관련하여 제공된 정책을 업데이트합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책 아래에서 **사용자 흐름** 을 선택합니다.

2. 이전에 만든 **사용자 흐름** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. **애플리케이션**: 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL**: **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 따라 계정 만들기

5. 로그아웃

6. 로그인 흐름 진행  

7. **continue** 를 입력하면 ThreatMetrix 퍼즐이 팝업됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

---
title: Onfido를 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 문서 ID 및 얼굴 생체 인식 확인을 위해 Azure AD B2C 인증을 Onfido와 통합하는 방법에 대해 알아봅니다
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 25d24c6f9d1f8346ad088308badca181a5bf1944
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012798"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Onfido를 구성하기 위한 자습서

이 샘플 자습서에서는 Azure AD B2C를 [Onfido](https://onfido.com/)와 통합하는 방법에 대한 참고 자료를 제공합니다. Onfido은 문서 ID 및 얼굴 생체 인식 확인 앱입니다. 회사에서 *고객알기제도* 충족 및 ID 요구 사항을 실시간으로 파악하는 데 사용할 수 있습니다. Onfido는 먼저 사진 ID를 확인한 후 얼굴 생체 인식에 대해 일치시키는 정교한 AI 기반 ID 확인을 사용합니다. 이 솔루션은 디지털 ID를 실제 사용자에게 연결하고 사기 행위를 줄이는 동시에 안전한 온보딩 환경을 제공합니다.

이 샘플에서는 등록 또는 로그인 흐름에 Onfido의 서비스를 연결하여 ID 확인을 수행합니다. 사용자가 액세스할 수 있는 제품 및 서비스에 대한 의사 결정은 Onfido의 결과에 따라 결정됩니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결되어 있는 [An Azure AD B2C 테넌트](./tutorial-create-tenant.md).

- Onfido [평가판 계정](https://onfido.com/signup/).

## <a name="scenario-description"></a>시나리오 설명

Onfido 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C 테넌트 - 테넌트에 정의된 사용자 지정 정책을 기반으로 사용자의 자격 증명을 확인하는 권한 부여 서버입니다. ID 공급자라고도 합니다. Onfido 클라이언트 앱을 호스팅하고 사용자 문서를 수집하여 Onfido API 서비스로 전송합니다.

- Onfido client – 다른 웹 페이지 내에 배포되는 구성 가능한 JavaScript 클라이언트 문서 컬렉션 유틸리티입니다. 문서를 수집하고 문서 크기 및 품질과 같은 예비 검사를 수행합니다.

- 중간 Rest API – Onfido API 서비스와 통신하고, 데이터 처리를 다루며 두 작업 모두의 보안 요구 사항을 준수하는 Azure AD B2C 테넌트에 대한 엔드포인트를 제공합니다.

- Onfido API 서비스 – Onfido에서 제공하는 백 엔드 서비스로, 사용자가 제공하는 문서를 저장하고 확인합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여줍니다.

![onfido-아키텍처-다이어그램에 대한 스크린샷](media/partner-onfido/onfido-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지를 엽니다. 사용자가 등록하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C가 사용자 특성을 수집합니다. Azure AD B2C에서 호스트되는 Onfido 클라이언트 앱은 예비로 사용자 정보를 확인합니다.
| 2. | Azure AD B2C는 중간 계층 API를 호출하고 사용자 특성을 전달합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집하고 Onfido API에서 사용할 수 있는 형식으로 변환합니다. 그런 다음 Onfido로 보냅니다.
| 4. | Onfido는 정보를 사용하고 이를 처리하여 사용자 ID의 유효성을 검사합니다. 그런 다음, 중간 계층 API에 결과를 반환합니다.
| 5. | 중간 계층 API는 정보를 처리하고 Azure AD B2C에 올바른 JSON 형식으로 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. 성공 응답이 표시되면 사용자가 인증되고 디렉터리에 기록됩니다.

## <a name="onboard-with-onfido"></a>Onfido으로 등록

1. Onfido 계정을 만들려면 [Onfido](https://onfido.com/signup/)에 문의하세요.

2. 계정이 만들어지면 [API 키](https://documentation.onfido.com/)를 만듭니다. 라이브 키는 청구 가능 하지만, 솔루션을 [테스트하는 데 sandbox 키](https://documentation.onfido.com/?javascript#sandbox-and-live-differences)를 사용할 수 있습니다. sandbox 키는 라이브 키와 동일한 결과 구조를 생성하지만, 결과는 항상 미리 정해져 있습니다. 문서를 처리하거나 저장하지 않습니다.

>[!NOTE]
> 나중에 키가 필요할 것입니다.

Onfido에 대한 자세한 내용은 [Onfido API 설명서](https://documentation.onfido.com) 및 [Onfido 개발자 허브](https://developers.onfido.com)를 참조하세요.  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Onfido로 Azure AD B2C 구성

### <a name="part-1---deploy-the-api"></a>1부 - API 배포

- 제공된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api)를 Azure 서비스에 배포합니다. 이러한 [지침](/visualstudio/deployment/quickstart-deploy-to-azure)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.
- CORS를 설정하고 **허용된 원본을** https://{your_tenant_name}.b2clogin.com로 추가합니다.

>[!NOTE]
>필요한 설정을 사용하여 Azure AD를 구성하려면 배포된 서비스의 URL이 필요합니다.

#### <a name="adding-sensitive-configuration-settings"></a>중요한 구성 설정 추가

애플리케이션 설정은 [Azure의 App Service](../app-service/configure-common.md#configure-app-settings)에서 구성할 수 있습니다. 앱 서비스를 사용하면 설정을 리포지토리로 체크인하지 않고도 안전하게 구성할 수 있습니다. Rest API에는 다음 설정이 제공되어야 합니다.

| 애플리케이션 설정 이름 | 원본 | 참고 |
|:-------------------------|:-------|:-------|
|OnfidoSettings:AuthToken| Onfido 계정 |

### <a name="part-2---deploy-the-ui"></a>2부 - UI 배포

#### <a name="configure-your-storage-location"></a>스토리지 위치 구성

1. [스토리지 계정에 Blob Storage 컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)를 설정합니다.

2. [Ui 폴더](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI)의 UI 파일을 blob 컨테이너에 저장합니다.

3. 다음 지침에 따라 만든 스토리지 컨테이너에 대한 CORS 액세스를 허용합니다.

   a. **설정** >**허용된 원본** 으로 이동하고, `https://{your_tenant_name}.b2clogin.com`을 입력합니다. your-tenant-name을 Azure AD B2C 테넌트 이름으로 바꿉니다. 예를 들어, https://fabrikam.b2clogin.com. 테넌트 이름을 입력할 때는 모두 소문자를 사용합니다.

   b. **허용된 메서드** 에 대해 `GET`과 `PUT`를 선택합니다.

   c. **저장** 을 선택합니다.

#### <a name="update-ui-files"></a>UI 파일 업데이트

1. UI 파일에서 [**ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)폴더로 이동합니다.

2. 각 HTML 파일을 엽니다.

3. 검색하여 `{your-ui-blob-container-url}`을 UI **ocean_blue**, **dist** 및 **자산** 폴더가 위치한 곳의 URL로 바꿉니다

4. 검색하여 `{your-intermediate-api-url}`을 중간 API 앱 서비스의 URL로 바꿉니다.

#### <a name="upload-your-files"></a>파일 업로드

1. UI 폴더의 UI 파일을 blob 컨테이너에 저장합니다.

2. [Azure Storage 탐색기](../virtual-machines/disks-use-storage-explorer-managed-disks.md)를 사용하여 파일 및 액세스 권한을 관리합니다.

### <a name="part-3---configure-azure-ad-b2c"></a>3부 - Azure AD B2C 구성하기

#### <a name="replace-the-configuration-values"></a>구성 값 바꾸기

제공된 [사용자 지정 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)에서 다음 자리 표시자를 찾아 인스턴스의 해당 값으로 바꿉니다.

| 자리표시자 | 대체할 값 | 예제  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | 테넌트 약식 이름 |  yourtenant.onmicrosoft.com의 "yourtenant" |
| {your_tenantID} | Azure AD B2C 테넌트의 TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 테넌트에 구성된 IdentityExperienceFramework 앱의 앱 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 테넌트에 구성된 ProxyIdentityExperienceFramework 앱의 앱 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 테넌트 스토리지 애플리케이션의 앱 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 테넌트 스토리지 애플리케이션의 개체 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | App Insights 인스턴스의 계측 키*| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| UI **ocean_blue**, **dist** 및 **자산** 폴더가 위치한 곳의 URL | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 설정한 앱 서비스의 URL                                             | `https://yourapp.azurewebsites.net`          |

*App Insights는 다른 테넌트에 있을 수 있습니다. 이 단계는 선택 사항입니다. 필요하지 않은 경우 해당 TechnicalProfiles 및 OrchestrationSteps를 제거합니다.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>4부 - Azure AD B2C 정책 구성

Azure AD B2C 테넌트를 설정하고 정책을 구성하는 방법에 대한 명령은 이 [문서](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)를 참조하세요.

>[!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보가 보내질 것임을 사용자에게 알립니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **SignUpSignIn** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. **애플리케이션**: 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL**: **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 따라 계정 만들기

5. 사용자 특성을 만든 후 흐름을 따라 진행하는 동안 Onfido 서비스가 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

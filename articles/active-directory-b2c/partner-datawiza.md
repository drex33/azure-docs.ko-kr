---
title: Datawiza를 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 안전한 하이브리드 액세스를 위해 Azure AD B2C 인증을 Datawiza와 통합하는 방법을 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/07/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c33694c624deafa8cdc7ef37ac1dc39d98b5a4f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044744"
---
# <a name="tutorial-configure-azure-ad-b2c-with-datawiza-to-provide-secure-hybrid-access"></a>자습서: 안전한 하이브리드 액세스를 제공하기 위해 Datawiza를 사용하여 Azure AD B2C 구성

이 샘플 자습서에서는 Azure AD(Active Directory) B2C를 [Datawiza](https://www.datawiza.com/)와 통합하는 방법을 알아봅니다.
Datawiza의 [DAB(Datawiza Access Broker)](https://www.datawiza.com/access-broker)를 사용하면 SSO(Single Sign-on) 및 세분화된 액세스 제어를 통해 Azure AD B2C를 확장하여 온-프레미스 레거시 애플리케이션을 보호할 수 있습니다. 이 솔루션 엔터프라이즈를 사용하면 애플리케이션을 다시 작성하지 않고도 레거시에서 Azure AD B2C로 신속하게 전환할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)입니다.

- DAB를 실행하려면 [Docker](https://docs.docker.com/get-docker/)가 필요합니다. 애플리케이션은 가상 머신 및 운영 체제 미설치 컴퓨터 등 모든 플랫폼에서 실행할 수 있습니다.

- 레거시 ID 시스템에서 Azure AD B2C로 전환할 온-프레미스 애플리케이션입니다. 이 샘플에서 DAB는 애플리케이션이 있는 동일한 서버에 배포됩니다. 애플리케이션은 localhost: 3001에서 실행되고 DAB는 localhost: 9772를 통해 애플리케이션으로 트래픽을 프록시 처리합니다. 애플리케이션에 대한 트래픽은 먼저 DAB에 도달한 다음 애플리케이션에 프록시 처리됩니다.

## <a name="scenario-description"></a>시나리오 설명

Datawiza 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C**: 사용자의 자격 증명 확인을 담당하는 권한 부여 서버입니다. 인증된 사용자는 Azure AD B2C 디렉터리에 저장된 로컬 계정을 사용하여 온-프레미스 애플리케이션에 액세스할 수 있습니다.

- **DAB(Datawiza Access Broker)** : 서비스 사용자가 로그인하고 HTTP 헤더를 통해 ID를 애플리케이션에 투명하게 전달합니다.

- **DCMC(Datawiza Cloud Management Console)** - DAB를 관리하는 중앙 집중식 관리 콘솔입니다. DCMC는 관리자가 DAB 구성 및 액세스 제어 정책을 관리할 수 있도록 UI 및 RESTful API를 제공합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![이미지는 하이브리드 애플리케이션의 보안 액세스를 위한 Datawiza와 Azure AD B2C의 통합 아키텍처를 보여 줍니다.](./media/partner-datawiza/datawiza-architecture-diagram.png)

| 단계 | 설명 |
|:-------|:---------------|
| 1. | 사용자가 온-프레미스 호스트된 애플리케이션에 대한 액세스를 요청합니다. DAB는 애플리케이션에 대한 사용자의 요청을 프록시합니다.|
| 2. | DAB는 사용자의 인증 상태를 확인합니다. 세션 토큰이 수신되지 않거나 제공된 세션 토큰이 유효하지 않은 경우 인증을 위해 사용자를 Azure AD B2C로 보냅니다.|
| 3. | Azure AD B2C는 Azure AD B2C 테넌트에서 DAB 애플리케이션 등록 중에 지정된 엔드포인트로 사용자 요청을 보냅니다.|
| 4. | DAB는 액세스 정책을 평가하고 애플리케이션에 전달된 HTTP 헤더에 포함할 특성 값을 계산합니다. 이 단계를 수행하는 동안 DAB는 IdP를 호출하여 헤더 값을 설정하는 데 필요한 정보를 검색할 수 있습니다. DAB는 헤더 값을 설정하고 요청을 애플리케이션으로 보냅니다. |
|5.  | 이제 사용자가 인증되고 애플리케이션에 액세스할 수 있습니다.|

## <a name="onboard-with-datawiza"></a>Datawiza로 온보딩

레거시 온-프레미스 앱을 Azure AD B2C와 통합하려면 [Datawiza](https://login.datawiza.com/df3f213b-68db-4966-bee4-c826eea4a310/b2c_1a_linkage/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile&client_id=4f011d0f-44d4-4c42-ad4c-88c7bbcd1ac8&redirect_uri=https%3A%2F%2Fconsole.datawiza.com%2Fhome&state=eyJpZCI6Ijk3ZjI5Y2VhLWQ3YzUtNGM5YS1hOWU2LTg1MDNjMmUzYWVlZCIsInRzIjoxNjIxMjg5ODc4LCJtZXRob2QiOiJyZWRpcmVjdEludGVyYWN0aW9uIn0%3D&nonce=08e1b701-6e42-427b-894b-c5d655a9a6b0&client_info=1&x-client-SKU=MSAL.JS&x-client-Ver=1.3.3&client-request-id=3ac285ba-2d4d-4ae5-8dc2-9295ff6047c6&response_mode=fragment)에 문의하세요.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 구성

1. Azure AD B2C 테넌트에 웹 애플리케이션을 [등록](https://docs.datawiza.com/idp/azureb2c.html#microsoft-azure-ad-b2c-configuration)합니다.

2. Azure 관리 포털에서 [가입 및 로그인 사용자 흐름을 구성](https://docs.datawiza.com/idp/azureb2c.html#configure-a-user-flow)합니다.

  >[!NOTE]
  >나중에 DCMC에서 DAB를 설정할 때 테넌트 이름, 사용자 흐름 이름, 클라이언트 ID 및 클라이언트 암호가 필요합니다.

## <a name="create-an-application-on-dcmc"></a>DCMC에서 애플리케이션 만들기

1. [애플리케이션을 만들고](https://docs.datawiza.com/step-by-step/step2.html) DCMC에서 이 애플리케이션에 대한 `PROVISIONING_KEY` 및 `PROVISIONING_SECRET` 키 쌍을 생성합니다.

2. [Azure AD B2C를 IdP(ID 공급자)로 구성](https://docs.datawiza.com/tutorial/web-app-azure-b2c.html#part-i-azure-ad-b2c-configuration)

![이미지는 Idp를 구성하기 위한 값을 보여 줍니다.](./media/partner-datawiza/configure-idp.png)

## <a name="run-dab-with-a-header-based-application"></a>헤더 기반 애플리케이션으로 DAB 실행

1. Docker 또는 Kubernetes를 사용하여 DAB를 실행할 수 있습니다. 사용자가 샘플 헤더 기반 애플리케이션을 만들려면 Docker 이미지가 필요합니다. 자세한 내용은 [DAB 및 SSO 통합 구성](https://docs.datawiza.com/step-by-step/step3.html) 방법을 참조하고 Kubernetes 관련 지침은 [Kubernetes를 사용한 DAB 배포](https://docs.datawiza.com/tutorial/web-app-AKS.html) 방법을 참조하세요. 다운로드하여 사용할 수 있는 샘플 Docker 이미지 `docker-compose.yml file`가 제공됩니다. 컨테이너 레지스트리에 로그인하여 DAB 및 헤더 기반 애플리케이션의 이미지를 다운로드합니다. [다음 지침](https://docs.datawiza.com/step-by-step/step3.html#important-step)을 따릅니다.
 
   ```yaml
   version: '3'

    services:
    datawiza-access-broker:
    image: registry.gitlab.com/datawiza/access-broker
    container_name: datawiza-access-broker
    restart: always
    ports:
      - "9772:9772"
    environment:
      PROVISIONING_KEY: #############################
      PROVISIONING_SECRET: #############################

    header-based-app:
    image: registry.gitlab.com/datawiza/header-based-app
    container_name: ab-demo-header-app
    restart: always
    environment:
      CONNECTOR: B2C
    ports:
      - "3001:3001"
    ```

 2. `docker-compose -f docker-compose.yml up`을 실행한 후 헤더 기반 애플리케이션은 Azure AD B2C에서 SSO를 사용하도록 설정해야 합니다. 브라우저를 열고 `http://localhost:9772/`를 입력합니다.

3. Azure AD B2C 로그인 페이지가 표시됩니다.

## <a name="pass-user-attributes-to-the-header-based-application"></a>헤더 기반 애플리케이션에 사용자 특성 전달

1. DAB는 IdP에서 사용자 특성을 가져오고 헤더 또는 쿠키를 통해 사용자 특성을 애플리케이션에 전달할 수 있습니다. 이메일 주소, 이름, 성 등의 [사용자 특성을 헤더 기반 애플리케이션에 전달](https://docs.datawiza.com/step-by-step/step4.html)하는 방법에 대한 지침을 참조하세요. 

2. 사용자 특성을 성공적으로 구성한 후에는 각 사용자 특성에 대한 녹색 확인 기호가 표시되어야 합니다.

 ![전달된 사용자 특성을 표시하는 이미지](./media/partner-datawiza/pass-user-attributes.png)

## <a name="test-the-flow"></a>흐름 테스트

1. 온-프레미스 애플리케이션 URL로 이동합니다.

2. 사용자 흐름에서 구성한 페이지로 DAB를 리디렉션해야 합니다.

3. 페이지의 목록에서 IdP를 선택합니다.

4. IdP로 리디렉션된 후에는 Azure AD MFA(Multi-Factor Authentication) 토큰을 포함하여(해당 IdP가 요구하는 경우) 요청에 따라 자격 증명을 제공합니다.

5. 성공적으로 인증되면 애플리케이션 요청을 DAB 리디렉션 URI로 전달하는 Azure AD B2C로 리디렉션됩니다.

6. DAB는 정책을 평가하고 헤더를 계산하며 사용자를 업스트림 애플리케이션으로 보냅니다.  

7. 요청된 애플리케이션을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

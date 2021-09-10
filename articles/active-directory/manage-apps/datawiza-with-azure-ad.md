---
title: Azure AD 및 Datawiza를 사용하여 하이브리드 액세스 보호
description: 이 자습서에서는 안전한 하이브리드 액세스를 위해 Azure AD와 Datawiza를 통합하는 방법에 대해 알아봅니다.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/27/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf8b1d7a3f45cd49bdb2ea420084a242e3e2c959
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214232"
---
# <a name="tutorial-configure-datawiza-with-azure-active-directory-for-secure-hybrid-access"></a>자습서: 안전한 하이브리드 액세스를 위해 Azure Active Directory를 사용하여 Datawiza 구성하기

이 샘플 자습서에서는 안전한 하이브리드 액세스를 위해 Azure AD(Azure Active Directory)를 [Datawiza](https://www.datawiza.com/)와 통합하는 방법을 알아봅니다.

Datawiza의 [DAB(Datawiza Access Broker)](https://www.datawiza.com/access-broker)는 Azure AD를 확장하여 SSO(Single Sign-On) 및 세분화된 액세스 제어가 Oracle E-Business Suite, Microsoft IIS 및 SAP와 같은 온-프레미스 및 클라우드 호스트형 애플리케이션을 보호할 수 있도록 합니다.

이 솔루션을 사용하면 기업은 애플리케이션을 다시 작성하지 않고도 Symantec SiteMinder, NetIQ, Oracle 및 IBM과 같은 레거시 WAM(웹 액세스 관리자)에서 Azure AD로 신속하게 전환할 수 있습니다. 또한 기업은 Datawiza를 코드 없음 또는 낮은 코드 솔루션으로 사용하여 새 애플리케이션을 Azure AD에 통합할 수 있습니다. 이를 통해 엔지니어링 시간을 절약하고, 비용을 크게 절감하며, 안전한 방식으로 프로젝트를 제공할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [평가판](https://azure.microsoft.com/free/)을 사용할 수 있습니다.

- 사용자의 Azure 구독에 연결된 [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)입니다.

- DAB를 실행하려면 [Docker](https://docs.docker.com/get-docker/) 및 [docker-compose](https://docs.docker.com/compose/install/)가 필요합니다. 애플리케이션은 가상 머신 및 운영 체제 미설치 컴퓨터 등 모든 플랫폼에서 실행할 수 있습니다.

- 레거시 ID 시스템에서 Azure AD로 전환할 애플리케이션입니다. 이 예제에서 DAB는 애플리케이션이 있는 동일한 서버에 배포됩니다. 애플리케이션은 localhost: 3001에서 실행되고 DAB는 localhost: 9772를 통해 애플리케이션으로 트래픽을 프록시 처리합니다. 애플리케이션에 대한 트래픽은 먼저 DAB에 도달한 후 애플리케이션에 프록시 처리됩니다.

## <a name="scenario-description"></a>시나리오 설명

Datawiza 통합은 다음 구성 요소를 포함합니다.

- [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) - Microsoft 클라우드 기반 ID 및 액세스 관리 서비스로, 사용자가 내부 및 외부 리소스에 로그인하고 액세스할 수 있도록 합니다.

- DAB(Datawiza Access Broker) - 서비스 사용자가 로그인하고 HTTP 헤더를 통해 ID를 애플리케이션에 투명하게 전달합니다.

- DCMC(Datawiza Cloud Management Console) - DAB를 관리하는 중앙 집중식 관리 콘솔입니다. DCMC는 관리자가 DAB 구성 및 액세스 제어 정책을 관리할 수 있도록 UI 및 Restfull API를 제공합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![아키텍처 다이어그램을 보여주는 이미지](./media/datawiza-with-azure-active-directory/datawiza-architecture-diagram.png)

|단계| 설명|
|:----------|:-----------|
|  1. | 사용자가 온-프레미스 또는 클라우드 호스트형 애플리케이션에 대한 액세스를 요청합니다. DAB는 애플리케이션에 사용자의 요청을 프록시합니다.|
| 2. |DAB는 사용자의 인증 상태를 확인합니다. 세션 토큰이 수신되지 않거나 제공된 세션 토큰이 유효하지 않은 경우 인증을 위해 사용자를 Azure AD로 보냅니다.|
| 3. | Azure AD는 Azure AD 테넌트에서 DAB 애플리케이션 등록 중에 지정된 엔드포인트로 사용자 요청을 보냅니다.|
| 4. | DAB는 액세스 정책을 평가하고 애플리케이션에 전달된 HTTP 헤더에 포함할 특성 값을 계산합니다. 이 단계를 수행하는 동안 DAB는 ID 공급자를 호출하여 헤더 값을 설정하는 데 필요한 정보를 검색할 수 있습니다. DAB는 헤더 값을 설정하고 요청을 애플리케이션으로 보냅니다. |
| 5. |  이제 사용자가 인증되고 애플리케이션에 액세스할 수 있습니다.|

## <a name="onboard-with-datawiza"></a>Datawiza를 사용하여 온보딩

온-프레미스 또는 클라우드 호스트형 애플리케이션을 Azure AD와 통합하려면 [DCMC(Datawiza 클라우드 관리 콘솔)](https://console.datawiza.com/)에 로그인합니다.

## <a name="create-an-application-on-dcmc"></a>DCMC에서 애플리케이션 만들기

DCMC에서 [애플리케이션을 만들고](https://docs.datawiza.com/step-by-step/step2.html) 이 애플리케이션에 대한 `PROVISIONING_KEY` 및 `PROVISIONING_SECRET`의 키 쌍을 생성합니다.

Azure AD의 경우 Datawiza는 편리한 [원클릭 통합](https://docs.datawiza.com/tutorial/web-app-azure-one-click.html)을 제공합니다. Azure AD를 DCMC와 통합하는 이 방법은 Azure AD 테넌트에서 사용자 대신 애플리케이션 등록을 만들 수 있습니다.

![idp 구성을 보여주는 이미지](./media/datawiza-with-azure-active-directory/configure-idp.png)

대신 Azure AD 테넌트에서 기존 웹 애플리케이션을 사용하려는 경우 옵션을 사용하지 않도록 설정하고 양식의 필드를 채울 수 있습니다. 테넌트 ID, 클라이언트 ID 및 클라이언트 암호가 필요합니다. [웹 애플리케이션을 만들고 테넌트에서 이 값을 얻습니다](https://docs.datawiza.com/idp/azure.html).

![양식을 사용한 idp 구성을 보여주는 이미지](./media/datawiza-with-azure-active-directory/use-form.png)

## <a name="run-dab-with-a-header-based-application"></a>헤더 기반 애플리케이션으로 DAB 실행

1. Docker 또는 Kubernetes를 사용하여 DAB를 실행할 수 있습니다. 사용자가 샘플 헤더 기반 애플리케이션을 만들려면 Docker 이미지가 필요합니다. [DAB 및 SSO 통합을 구성합니다](https://docs.datawiza.com/step-by-step/step3.html). [Kubernetes를 통해 DAB를 배포합니다](https://docs.datawiza.com/tutorial/web-app-AKS.html). 다운로드하여 사용할 수 있는 샘플 Docker 이미지 `docker-compose.yml` 파일이 제공됩니다. [컨테이너 레지스트리에 로그인](https://docs.datawiza.com/step-by-step/step3.html#important-step)하여 DAB 및 헤더 기반 애플리케이션의 이미지를 다운로드합니다.

    ```YML
    services:
      datawiza-access-broker:
      image: registry.gitlab.com/datawiza/access-broker
      container_name: datawiza-access-broker
      restart: always
      ports:
      - "9772:9772"
      environment:
      PROVISIONING_KEY: #############################################
      PROVISIONING_SECRET: ##############################################
      
      header-based-app:
      image: registry.gitlab.com/datawiza/header-based-app
      restart: always
     ports:
     - "3001:3001"
   ```

2. `docker-compose -f docker-compose.yml up`을 실행한 후, 헤더 기반 애플리케이션은 Azure AD에서 SSO를 사용하도록 설정해야 합니다. 브라우저를 열고 `http://localhost:9772/`를 입력합니다.

3. Azure AD 로그인 페이지가 표시됩니다.

## <a name="pass-user-attributes-to-the-header-based-application"></a>헤더 기반 애플리케이션에 사용자 특성 전달

1. DAB는 IdP에서 사용자 특성을 가져오고 헤더 또는 쿠키를 통해 사용자 특성을 애플리케이션에 전달할 수 있습니다. 이메일 주소, 이름, 성 등의 [사용자 특성을 헤더 기반 애플리케이션에 전달](https://docs.datawiza.com/step-by-step/step4.html)하는 방법에 대한 지침을 참조하세요.

2. 사용자 특성을 성공적으로 구성한 후에는 각 사용자 특성에 대한 녹색 확인 기호가 표시되어야 합니다.

   ![datawiza 애플리케이션 홈페이지를 보여주는 이미지](./media/datawiza-with-azure-active-directory/datawiza-application-home-page.png)

## <a name="test-the-flow"></a>흐름 테스트

1. 애플리케이션 URL로 이동합니다.

2. DAB는 Azure AD 로그인 페이지로 리디렉션되어야 합니다.

3. 인증에 성공하면 DAB로 리디렉션되어야 합니다.

4. DAB는 정책을 평가하고 헤더를 계산하며 사용자를 업스트림 애플리케이션으로 보냅니다.

5. 사용자가 요청한 애플리케이션이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C를 사용하여 Datawiza 구성](https://docs.microsoft.com/azure/active-directory-b2c/partner-datawiza)

- [Datawiza 설명서](https://docs.datawiza.com)

---
title: Form Recognizer 샘플 레이블 지정 도구 배포 방법
titleSuffix: Azure Applied AI Services
description: 감독 학습에 도움이 되도록 Form Recognizer 샘플 레이블 지정 도구를 배포할 수 있는 다양한 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/02/2021
ms.author: lajanuar
ms.openlocfilehash: e0555050b6457cc25a3d66d902d7de9232247ae3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538850"
---
# <a name="deploy-the-sample-labeling-tool"></a>샘플 레이블 지정 도구 배포

Form Recognizer 샘플 레이블 지정 도구는 간단한 UI(사용자 인터페이스)를 제공하는 애플리케이션으로, 감독 학습을 위해 양식(문서)에 레이블을 수동으로 지정하는 데 사용할 수 있습니다. 이 문서에서는 다음을 수행하는 방법을 설명하는 링크 및 지침을 제공합니다.

* [로컬에서 샘플 레이블 지정 도구 실행](#run-the-sample-labeling-tool-locally)
* [샘플 레이블 지정 도구를 ACI(Azure Container Instance)에 배포](#deploy-with-azure-container-instances-aci)
* [오픈 소스 OCR 양식 레이블 지정 도구에 사용하고 참여](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>로컬에서 샘플 레이블 지정 도구 실행

데이터 레이블 지정을 시작하는 가장 빠른 방법은 샘플 레이블 지정 도구를 로컬에서 실행하는 것입니다. 다음 빠른 시작에서는 샘플 레이블 지정 도구에서 Form Recognizer REST API를 사용하여 수동으로 레이블이 지정된 데이터로 사용자 지정 모델을 학습시킵니다.

* [Azure Form Recognizer 시작](label-tool.md)

## <a name="deploy-with-azure-container-instances-aci"></a>ACI(Azure Container Instances)에 배포

시작하기 전에 샘플 레이블 지정 도구를 ACI(Azure Container Instance)에 배포하는 두 가지 방법이 있습니다. 두 옵션 모두 ACI에서 샘플 레이블 지정 도구를 실행하는 데 사용됩니다.

* [Azure Portal 사용](#azure-portal)
* [Azure CLI 사용](#azure-cli)

### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 새 리소스를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/signin/index/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다.
3. 다음으로 **웹앱** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![웹앱 선택](./media/quickstarts/create-web-app.png)

4. 먼저 **기본** 탭이 선택되어 있는지 확인합니다. 이제 몇 가지 정보를 제공해야 합니다.

   > [!div class="mx-imgBorder"]
   > ![기본 선택](./media/quickstarts/select-basics.png)
   * 구독 - 기존 Azure 구독을 선택합니다.
   * 리소스 그룹 - 기존 리소스 그룹을 다시 사용하거나 이 프로젝트에 대해 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만드는 것이 좋습니다.
   * 이름 - 웹앱에 이름을 지정합니다.
   * 게시 - **Docker 컨테이너** 를 선택합니다.
   * 운영 체제 - **Linux** 를 선택합니다.
   * 지역 - 적합한 지역을 선택합니다.
   * Linux 플랜 - 앱 서비스에 대한 가격 책정 계층/플랜을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![웹앱 구성](./media/quickstarts/select-docker.png)

5. 다음으로, **Docker** 탭을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![Docker 선택](./media/quickstarts/select-docker.png)

6. 이제 Docker 컨테이너를 구성하겠습니다. 별도의 언급이 없는 한 모든 필드가 필수 사항입니다.
<!-- markdownlint-disable MD025 -->

* 옵션 - **단일 컨테이너** 를 선택합니다.
* 이미지 원본 - **프라이빗 레지스트리** 를 선택합니다.
* 서버 URL - `https://mcr.microsoft.com`으로 설정
* 사용자 이름(선택 사항) - 사용자 이름을 만듭니다.
* 암호(선택 사항) - 기억할 수 있는 안전한 암호를 만듭니다.
* 이미지 및 태그 - `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1`로 설정
* 지속적인 배포 - 개발 팀에서 샘플 레이블 지정 도구를 변경할 때 자동 업데이트를 받으려면 **On** 으로 설정합니다.
* 시작 명령 - `./run.sh eula=accept`로 설정합니다.

> [!div class="mx-imgBorder"]
> ![Docker 구성](./media/quickstarts/configure-docker.png)

* 다음으로 **검토 + 만들기** 를 선택하고 **만들기** 를 선택하여 웹앱을 배포합니다. 완료되면 리소스에 대한 **개요** 에 제공된 URL에서 웹앱에 액세스할 수 있습니다.

### <a name="continuous-deployment"></a>연속 배포

웹앱을 만든 후 연속 배포 옵션을 사용할 수 있습니다.

* 왼쪽 창에서 **컨테이너 설정** 을 선택합니다.
* 기본 창에서 연속 배포로 이동하고 **켜기** 및 **끄기** 단추 간에 전환하여 기본 설정을 지정합니다.

:::image type="content" source="media/label-tool/continuous-deployment.png" alt-text="스크린샷: 지속적 배포를 위한 컨테이너 설정 확대" lightbox="media/label-tool/continuous-deployment-bigger.png":::

> [!NOTE]
> 웹앱을 만들 때 권한 부여/인증을 구성할 수도 있습니다. 시작하는 데에는 필요하지 않습니다.

> [!IMPORTANT]
> `https` 주소에서 보려면 웹앱에 대해 TLS를 사용하도록 설정해야 할 수 있습니다. 웹앱에 TLS/SSL을 사용하도록 설정하는 대신 사이드카 컨테이너를 설정하려면 [TLS 엔드포인트 사용](../../container-instances/container-instances-container-group-ssl.md)의 지침을 따르세요.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

Azure Portal을 사용하는 대신 Azure CLI를 사용하여 리소스를 만들 수 있습니다. 계속하기 전에 [Azure CLI](/cli/azure/install-azure-cli)를 설치해야 합니다. 이미 Azure CLI를 사용하는 경우 이 단계를 건너뛸 수 있습니다.

이 명령에 대해 알아야 할 몇 가지 사항이 있습니다.

* `DNS_NAME_LABEL=aci-demo-$RANDOM`은 임의의 DNS 이름을 생성합니다.
* 이 샘플에서는 리소스를 만드는 데 사용할 수 있는 리소스 그룹이 있다고 가정합니다. `<resource_group_name>`을 구독과 연결된 유효한 리소스 그룹으로 바꿉니다.
* 리소스를 만들 위치를 지정해야 합니다. `<region name>`을 웹앱에 대해 원하는 위치로 바꿉니다.
* 이 명령은 자동으로 EULA에 동의합니다.

Azure CLI에서 이 명령을 실행하여 샘플 레이블 지정 도구에 대한 웹앱 리소스를 만듭니다.

<!-- markdownlint-disable MD024 -->

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1 \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

### <a name="connect-to-azure-ad-for-authorization"></a>권한 부여를 위해 Azure AD에 연결

Azure AD(Azure Active Directory)에 웹앱을 연결하는 것이 좋습니다. 이 연결을 통해 유효한 자격 증명이 있는 사용자만 로그인하여 웹앱을 사용할 수 있습니다. [App Service 앱 구성](../../app-service/configure-authentication-provider-aad.md)의 지침을 따라 Azure Active Directory에 연결합니다.

## <a name="open-source-on-github"></a>GitHub의 오픈 소스

OCR 양식 레이블 지정 도구는 GitHub에서 오픈 소스 프로젝트로도 사용할 수 있습니다. 도구는 React + Redux를 사용하여 빌드한 웹 애플리케이션으로, TypeScript로 작성되었습니다. 자세히 알아보거나 기여하려면 [OCR 양식 레이블 지정 도구](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[레이블을 사용하여 학습](label-tool.md) 빠른 시작을 사용하여 교육 데이터에 수동으로 레이블을 지정하고 감독 학습을 수행하기 위해 도구를 사용하는 방법을 알아보세요.

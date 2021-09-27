---
title: Microsoft Azure의 Cloud Foundry에 첫 번째 앱 배포
description: Azure의 Cloud Foundry에 애플리케이션 배포
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 9ebfd3a9ce02ece7fc36fdce004dac29539f4fae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737100"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Microsoft Azure의 Cloud Foundry에 첫 번째 앱 배포

[Cloud Foundry](https://cloudfoundry.org)는 Microsoft Azure에서 사용할 수 있는 인기 있는 오픈 소스 애플리케이션 플랫폼입니다. 이 문서에서는 Azure 환경에서 Cloud Foundry의 애플리케이션을 배포 및 관리하는 방법을 보여줍니다.

## <a name="create-a-cloud-foundry-environment"></a>Cloud Foundry 환경 만들기

Azure에 Cloud Foundry 환경을 만들기 위한 여러 가지 옵션이 있습니다.

- Azure Marketplace에서 Pivotal Cloud Foundry 제품을 사용하여 PCF Ops Manager 및 Azure Service Broker를 포함하는 표준 환경을 만듭니다. Pivotal 설명서에서 마켓플레이스 제품을 배포하기 위한 [전체 지침][pcf-azuremarketplace-pivotaldocs]을 찾을 수 있습니다.
- [Pivotal Cloud Foundry를 수동으로 배포][pcf-custom]하여 사용자 지정된 환경을 만듭니다.
- [BOSH](https://bosh.io) 디렉터, Cloud Foundry 환경의 배포를 조정하는 VM을 설정하여 [오픈 소스 Cloud Foundry 패키지를 직접 배포][oss-cf-bosh]합니다.

> [!IMPORTANT] 
> Azure Marketplace에서 PCF를 배포하는 경우 Pivotal 앱 관리자에 액세스하는 데 필요한 SYSTEMDOMAINURL 및 관리자 자격 증명을 적어 둡니다. 둘은 마켓플레이스 배포 가이드에 설명되어 있습니다. 이 자습서를 완료하는 데 필요합니다. 마켓플레이스 배포의 경우 SYSTEMDOMAINURL은 `https://system.*ip-address*.cf.pcfazure.com` 형식입니다.

## <a name="connect-to-the-cloud-controller"></a>Cloud Controller에 연결

Cloud Controller는 애플리케이션 배포 및 관리를 위한 Cloud Foundry 환경에 대한 기본 진입점입니다. 핵심 CCAPI(Cloud Controller API)는 REST API이지만 다양한 도구를 통해 액세스할 수 있습니다. 이 경우 [Cloud Foundry CLI][cf-cli]를 통해 상호 작용합니다. Linux, macOS 또는 Windows에 CLI를 설치할 수 있지만 전혀 설치하지 않으려는 경우 [Azure Cloud Shell][cloudshell-docs]에 사전 설치할 수 있습니다.

로그인하려면 마켓플레이스 배포에서 가져온 SYSTEMDOMAINURL 앞에 `api`를 추가합니다. 기본 배포는 자체 서명된 인증서를 사용하므로 `skip-ssl-validation` 스위치를 포함해야 합니다.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Cloud Controller에 로그인하라는 메시지가 표시됩니다. 마켓플레이스 배포 단계에서 얻은 관리자 계정 자격 증명을 사용합니다.

Cloud Foundry는 네임스페이스로 *조직* 및 *공간* 을 제공하여 공유 배포 내에서 팀과 환경을 격리합니다. PCF 마켓플레이스 배포는 기본 *시스템* 조직 및 자동 크기 조정 서비스 및 Azure Service Broker와 같은 기본 구성 요소를 포함하도록 만들어진 공간 집합을 포함합니다. 현재로는 *시스템* 공간을 선택합니다.


## <a name="create-an-org-and-space"></a>조직 및 공간 만들기

`cf apps`를 입력하는 경우 시스템 조직 내에서 시스템 공간에 배포된 시스템 애플리케이션 세트이 표시됩니다. 

조직 및 공간을 만들어 샘플 애플리케이션을 저장하도록 시스템 애플리케이션에 대해 예약된 *시스템* 조직을 유지해야 합니다.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

대상 명령을 사용하여 새 조직 및 공간으로 전환합니다.

```bash
cf target -o testorg -s dev
```

이제 애플리케이션을 배포할 때 새 조직 및 공간에 자동으로 만들어집니다. 현재 새 조직/공간에 앱이 없는지 확인하려면 `cf apps`를 다시 입력합니다.

> [!NOTE] 
> 조직과 공간 및 Cloud Foundry RBAC(Cloud Foundry 역할 기반 액세스 제어)에 사용할 수 있는 방법에 관한 자세한 내용은 [Cloud Foundry 설명서][cf-orgs-spaces-docs]를 참조하세요.

## <a name="deploy-an-application"></a>애플리케이션 배포

Java로 작성되며 [Spring Framework](https://spring.io) 및 [Spring Boot](https://projects.spring.io/spring-boot/)를 기반으로 하는 Hello Spring Cloud라는 샘플 Cloud Foundry 애플리케이션을 사용해 봅시다.

### <a name="clone-the-hello-spring-cloud-repository"></a>Hello Spring Cloud 리포지토리 복제

Hello Spring Cloud 샘플 애플리케이션은 GitHub에서 사용할 수 있습니다. 사용자 환경에 복제하고 새 디렉터리로 변경합니다.

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>애플리케이션 빌드

[Apache Maven](https://maven.apache.org)을 사용하여 앱을 빌드합니다.

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>cf 밀어넣기로 애플리케이션 배포

`push` 명령을 사용하여 대부분의 애플리케이션을 Cloud Foundry에 배포할 수 있습니다.

```bash
cf push
```

애플리케이션을 *밀어넣는* 경우 Cloud Foundry는 애플리케이션의 유형을 검색하고(이 경우 Java 앱) 해당 종속성을 식별합니다(이 경우 Spring framework). 그런 다음 코드를 *드롭릿* 으로 알려진 독립 실행형 컨테이너 이미지로 실행하는 데 필요한 모든 요소를 패키지합니다. 마지막으로 Cloud Foundry는 사용자 환경에서 사용할 수 있는 머신 중 하나에서 애플리케이션을 예약하고 접근할 수 있는 URL을 만듭니다. 이는 명령의 출력에서 제공됩니다.

![cf 밀어넣기 명령의 출력][cf-push-output]

hello-spring-cloud 애플리케이션을 보려면 브라우저에서 제공된 URL을 엽니다.

![Hello Spring Cloud용 기본 UI][hello-spring-cloud-basic]

> [!NOTE] 
> `cf push` 처리 과정에 대한 자세한 내용을 보려면 Cloud Foundry 설명서에서 [애플리케이션 준비 방법][cf-push-docs]을 참조하세요.

## <a name="view-application-logs"></a>애플리케이션 로그 보기

Cloud Foundry CLI를 사용하여 해당 이름별로 애플리케이션에 대한 로그를 볼 수 있습니다.

```bash
cf logs hello-spring-cloud
```

기본적으로 로그 명령은 작성된 대로 새 로그를 보여 주는 *비상 로그* 를 사용합니다. 표시되는 새 로그를 보려면 브라우저에서 hello-spring-cloud 앱을 새로 고칩니다.

이미 작성된 로그를 보려면 `recent` 스위치를 추가합니다.

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>애플리케이션 크기 조정

기본적으로 `cf push`만 애플리케이션의 단일 인스턴스를 만듭니다. 고가용성을 보장하고 높은 처리량을 위해 규모 확장을 활성화하려면 일반적으로 애플리케이션의 둘 이상의 인스턴스를 실행합니다. `scale` 명령을 사용하여 이미 배포된 애플리케이션을 쉽게 확장할 수 있습니다.

```bash
cf scale -i 2 hello-spring-cloud
```

애플리케이션에서 `cf app` 명령을 실행하면 Cloud Foundry가 애플리케이션의 다른 인스턴스를 만들고 있음을 보여줍니다. 애플리케이션이 시작된 후 Cloud Foundry는 트래픽 부하 분산을 자동으로 시작합니다.


## <a name="next-steps"></a>다음 단계

- [Cloud Foundry 설명서 읽기][cloudfoundry-docs]
- [Cloud Foundry용 Azure DevOps Services 플러그인 설정][vsts-plugin]
- [Cloud Foundry용 Microsoft Log Analytics 노즐 구성][loganalytics-nozzle]

<!-- LINKS -->

[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/ops-manager/2-10/install/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png

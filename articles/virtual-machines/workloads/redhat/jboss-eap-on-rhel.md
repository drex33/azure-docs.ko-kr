---
title: 빠른 시작 - RHEL(Red Hat Enterprise Linux)에서 JBoss EAP(Enterprise Application Platform)를 Azure VM 및 가상 머신 확장 집합에 배포
description: Red Hat JBoss EAP를 사용하여 엔터프라이즈 Java 애플리케이션을 Azure RHEL VM 및 가상 머신 확장 집합에 배포하는 방법
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom: mode-api
ms.collection: linux
ms.openlocfilehash: 134e06e4ca4831d5e6995b330d527f2f5515856a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018390"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에서 JBoss EAP를 사용하여 엔터프라이즈 Java 애플리케이션을 Azure에 배포

**적용 대상:** :heavy_check_mark: Linux VM 

이 문서의 Azure 빠른 시작 템플릿은 [RHEL(Red Hat Enterprise Linux)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)에서 [JBoss EAP(Enterprise Application Platform)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform)를 Azure VM(Virtual Machines) 및 가상 머신 확장 집합에 배포하는 방법을 보여줍니다. 샘플 Java 앱을 사용하여 배포의 유효성을 검사합니다. 

JBoss EAP는 오픈 소스 애플리케이션 서버 플랫폼이며, Java 애플리케이션에 대한 엔터프라이즈급 보안, 확장성 및 성능을 제공합니다. RHEL은 오픈 소스 OS(운영 체제) 플랫폼입니다. 이를 통해 모든 환경에서 기존 앱을 크기 조정하고 새 기술을 롤아웃할 수 있습니다. 

JBoss EAP 및 RHEL에는 모든 환경에서 엔터프라이즈 Java 애플리케이션을 빌드, 실행, 배포 및 관리하는 데 필요한 모든 것이 포함되어 있습니다. 이 조합은 온-프레미스, 가상 환경 및 프라이빗, 퍼블릭 또는 하이브리드 클라우드를 위한 오픈 소스 솔루션입니다.

## <a name="prerequisites"></a>필수 구성 요소 

* 활성 구독이 있는 Azure 계정. Azure 구독을 얻으려면 [Visual Studio 구독자에 대한 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [체험 계정](https://azure.microsoft.com/pricing/free-trial)을 만듭니다.

* JBoss EAP 설치. JBoss EAP에 대한 RHSM(Red Hat Subscription Management) 자격이 있는 Red Hat 계정이 있어야 합니다. 이 자격을 통해 Red Hat에서 테스트되고 인증된 JBoss EAP 버전을 다운로드할 수 있습니다.  

  EAP 자격이 없는 경우 시작하기 전에 [JBoss EAP 평가 구독](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)을 받으세요. 새 Red Hat 구독을 만들려면 [Red Hat 고객 포털](https://access.redhat.com/)로 이동하여 계정을 설정합니다.

* [Azure CLI](/cli/azure/overview)

* RHEL 옵션. PAYG(종량제) 또는 BYOS(Bring Your Own Subscription)를 선택합니다. BYOS를 사용하는 경우 빠른 시작 템플릿을 배포하기 전에 [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image를 활성화해야 합니다.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE 및 Jakarta EE 애플리케이션 마이그레이션

### <a name="migrate-to-jboss-eap"></a>JBoss EAP로 마이그레이션
JBoss EAP 7.2 및 7.3은 Java EE(Java Enterprise Edition) 8 및 Java EE 8 사양의 인증된 구현입니다. JBoss EAP는 HA(고가용성) 클러스터링, 메시징 및 분산 캐싱과 같은 기능에 대한 미리 구성된 옵션을 제공합니다. JBoss EAP가 제공하는 다양한 API 및 서비스를 사용하여 사용자가 애플리케이션을 작성, 배포 및 실행할 수도 있습니다.  

JBoss EAP에 대한 자세한 내용은 [JBoss EAP 7.2 소개](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) 또는 [JBoss EAP 7.3 소개](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)를 참조하세요.

 #### <a name="applications-on-jboss-eap"></a>JBoss EAP의 애플리케이션

* **웹 서비스 애플리케이션**. 웹 서비스는 소프트웨어 애플리케이션 간에 상호 운용할 수 있는 표준 방법을 제공합니다. 각 애플리케이션은 서로 다른 플랫폼과 프레임워크에서 실행될 수 있습니다. 이러한 웹 서비스는 내부 및 다른 유형의 하위 시스템 통신을 용이하게 합니다. 

  자세한 내용은 [EAP 7.2에서 웹 서비스 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) 또는 [EAP 7.3에서 웹 서비스 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)을 참조하세요.

* **EJB(Enterprise Java Beans) 애플리케이션**. EJB 3.2는 분산형, 트랜잭션, 보안 및 이식 가능한 Java EE 및 Jakarta EE 애플리케이션을 개발하는 API입니다. EJB는 Enterprise Beans라는 서버 쪽 구성 요소를 사용하여 재사용을 권장하는 분리된 방식으로 애플리케이션의 비즈니스 논리를 구현합니다. 

  자세한 내용은 [EAP 7.2에서 EJB 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) 또는 [EAP 7.3에서 EJB 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)을 참조하세요.

* **Hibernate 애플리케이션**. 개발자와 관리자는 JBoss EAP를 사용하여 JPA(Java Persistence API) 및 Hibernate 애플리케이션을 개발하고 배포할 수 있습니다. Hibernate Core는 Java 언어용 개체 관계형 매핑 프레임워크입니다. 개체 지향 도메인 모델을 관계형 데이터베이스에 매핑하는 프레임워크를 제공하기 때문에 애플리케이션은 데이터베이스와의 직접 상호 작용을 피할 수 있습니다. 

  Hibernate Entity Manager는 [JPA 2.1 사양](https://www.jcp.org/en/jsr/overview)에서 정의한 프로그래밍 인터페이스 및 수명 주기 규칙을 구현합니다. 이 래퍼는 Hibernate Annotations와 함께 완성도 높은 Hibernate Core를 기반으로 하는 완전한(및 독립 실행형) JPA 솔루션을 구현합니다. 
  
  Hibernate에 대한 자세한 내용은 [JPA on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) 또는 [Jakarta Persistence on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)을 참조하세요.

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat MTA(Migration Toolkit for Applications)](https://developers.redhat.com/products/mta/overview)는 Java 애플리케이션 서버용 마이그레이션 도구입니다. 이 도구를 사용하여 다른 앱 서버에서 JBoss EAP로 마이그레이션합니다. Java용 [Eclipse IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) 및 [Visual Studio Code](https://code.visualstudio.com/docs/languages/java)에 대한 IDE 플러그 인에서 작동합니다. 

MTA는 무료 오픈 소스 도구이며:
* 애플리케이션 분석을 자동화합니다.
* 작업 예측을 지원합니다.
* 코드 마이그레이션을 가속화합니다.
* 컨테이너화를 지원합니다.
* Azure Workload Builder와 통합됩니다.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 JBoss EAP 마이그레이션
RHEL 기반 JBoss EAP에 대한 Azure Marketplace 제품은 Azure VM을 20분 이내에 설치하고 프로비저닝합니다. 이러한 제품은 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에서 액세스할 수 있습니다.

이러한 Azure Marketplace 제품에는 요구 사항을 지원하기 위해 EAP 및 RHEL 버전의 다양한 조합이 포함됩니다. JBoss EAP는 항상 BYOS이지만 RHEL OS의 경우 BYOS 또는 PAYG 중에 선택할 수 있습니다. Azure Marketplace 제품에는 RHEL 기반 JBoss EAP를 독립 실행형 VM 또는 클러스터형 VM으로 사용할 수 있는 계획 옵션이 포함되어 있습니다.

* RHEL 7.7 VM 기반 JBoss EAP 7.2(PAYG)
* RHEL 8.0 VM 기반 JBoss EAP 7.2(PAYG)
* RHEL 8.0 VM 기반 JBoss EAP 7.3(PAYG)
* RHEL 7.7 VM 기반 JBoss EAP 7.2(BYOS)
* RHEL 8.0 VM 기반 JBoss EAP 7.2(BYOS)
* RHEL 8.0 VM 기반 JBoss EAP 7.3(BYOS)

Azure Marketplace 제품과 함께 빠른 시작 템플릿을 사용하여 Azure 마이그레이션 과정을 시작할 수 있습니다. 이러한 빠른 시작에는 다양한 구성 및 버전 조합으로 RHEL 기반 JBoss EAP를 배포하도록 미리 빌드된 ARM(Azure Resource Manager) 템플릿 및 스크립트가 포함됩니다. 포함된 항목은 다음과 같습니다.

* 부하 분산 장치
* 부하 분산 및 VM에 대한 개인 IP
* 단일 서브넷이 있는 가상 네트워크
* VM 구성(클러스터 또는 독립 실행형)
* 샘플 Java 애플리케이션

이러한 템플릿에 대한 솔루션 아키텍처는 다음과 같습니다.

* 독립 실행형 RHEL VM의 JBoss EAP
* 여러 RHEL VM에 클러스터링된 JBoss EAP
* Azure가상 머신 크기 집합을 통해 클러스터링된 JBoss EAP

#### <a name="linux-workload-migration-for-jboss-eap"></a>JBoss EAP를 위한 Linux 워크로드 마이그레이션
Azure Workload Builder는 온-프레미스 Java 앱에 대한 개념 증명, 평가 및 마이그레이션 프로세스를 Azure로 간소화합니다. Workload Builder는 Azure Migrate 검색 도구와 통합되어 JBoss EAP 서버를 식별합니다. 그런 다음, JBoss EAP 서버 배포에 대한 Ansible 플레이북을 동적으로 생성합니다. Red Hat MTA 도구를 사용하여 서버를 다른 앱 서버에서 JBoss EAP로 마이그레이션합니다. 

마이그레이션을 간소화하는 단계는 다음과 같습니다.
1. **평가**. Azure VM 또는 가상 머신 확장 집합을 사용하여 JBoss EAP 클러스터를 평가합니다.
1. **평가**. 애플리케이션 및 인프라를 검사합니다.
1. **인프라 구성**. 워크로드 프로필을 만듭니다.
1. **배포 및 테스트**. 워크로드를 배포, 마이그레이션 및 테스트합니다.
1. **배포 후 구성**. 데이터, 모니터링, 보안, 백업 등과 통합합니다.

## <a name="server-configuration-choice"></a>서버 구성 선택

RHEL VM 배포의 경우 PAYG 또는 BYOS 중 하나를 선택할 수 있습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com)의 이미지는 PAYG로 기본 설정됩니다. 자체 RHEL OS 이미지가 있는 경우 BYOS 형식 RHEL VM을 배포합니다. VM 또는 가상 머신 확장 집합을 배포하기 전에 RHSM 계정에 Cloud Access를 통한 BYOS 자격이 있는지 확인합니다.

JBoss EAP에는 강력한 관리 기능이 있으며 애플리케이션에 기능과 API를 제공합니다. 이러한 관리 기능은 JBoss EAP를 시작하는 데 사용되는 운영 모드에 따라 달라집니다. RHEL 및 Windows Server에서 지원됩니다. JBoss EAP는 개별 인스턴스를 관리하기 위한 독립 실행형 서버 운영 모드를 제공합니다. 또한 단일 제어 지점에서 인스턴스 그룹을 관리하기 위한 관리형 도메인 운영 모드도 제공합니다. 

> [!NOTE]
> JBoss EAP 관리형 도메인은 Microsoft Azure에서 지원되지 않습니다. Azure 인프라 서비스가 HA 기능을 관리하기 때문입니다. 

환경 변수 `EAP_HOME`은 JBoss EAP 설치 경로를 나타냅니다. 다음 명령을 사용하여 독립 실행형 모드에서 JBoss EAP 서비스를 시작합니다.

```
$EAP_HOME/bin/standalone.sh
```
    
이 시작 스크립트는 EAP_HOME/bin/standalone.conf 파일을 사용하여 JVM 옵션과 같은 일부 기본 설정을 지정합니다. 이 파일에서 설정을 사용자 지정할 수 있습니다. JBoss EAP는 standalone.xml 구성 파일을 사용하여 기본적으로 독립 실행형 모드로 시작하지만 다른 모드를 사용하여 시작할 수 있습니다. 

사용 가능한 독립 실행형 구성 파일 및 이를 사용하는 방법에 대한 자세한 내용은 [EAP 7.2용 독립 실행형 서버 구성 파일](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) 또는 [EAP 7.3용 독립 실행형 서버 구성 파일](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)을 참조하세요. 

다른 구성을 사용하여 JBoss EAP를 시작하려면 `--server-config` 인수를 사용합니다. 다음은 그 예입니다. 
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
사용 가능한 모든 시작 스크립트 인수 및 용도의 전체 목록을 보려면 `--help` 인수를 사용합니다. 자세한 내용은 [EAP 7.2의 서버 런타임 인수](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) 또는 [EAP 7.3의 서버 런타임 인수](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)를 참조하세요.
    
또한 JBoss EAP는 클러스터 모드에서 작동할 수도 있습니다. JBoss EAP 클러스터 메시징을 사용하면 JBoss EAP 메시징 서버를 그룹화하여 메시지 처리 부하를 공유할 수 있습니다. 클러스터의 각 활성 노드는 자체 메시지를 관리하고 자체 연결을 처리하는 활성 JBoss EAP 메시징 서버입니다. 자세히 알아보려면 [EAP 7.2의 클러스터 개요](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) 또는 [EAP 7.3의 클러스터 개요](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)를 참조하세요. 

## <a name="support-and-subscription-notes"></a>지원 및 구독 정보
이러한 빠른 시작 템플릿은 다음과 같이 제공됩니다. 

- RHEL OS는 Red Hat RHEL Gold Image 모델을 통해 PAYG 또는 BYOS로 제공됩니다.
- JBoss EAP는 BYOS로만 제공됩니다.

#### <a name="using-rhel-os-with-the-payg-model"></a>PAYG 모델에서 RHEL OS 사용

이러한 빠른 시작 템플릿은 기본적으로 Azure Marketplace의 주문형 RHEL 7.7 또는 8.0 PAYG 이미지를 사용합니다. PAYG 이미지에는 일반적인 컴퓨팅, 네트워크 및 스토리지 비용 외에 추가 시간당 RHEL 구독 요금이 있습니다. 동시에 인스턴스가 Red Hat 구독에 등록됩니다. 즉, 자격 중 하나를 사용하게 됩니다. 

이 PAYG 이미지는 "이중 청구"로 이어집니다. 이 문제는 사용자 고유의 RHEL 이미지를 빌드하여 방지할 수 있습니다. 자세한 내용은 Red Hat KB(기술 자료) 문서 [Microsoft Azure용 RHEL VM을 프로비저닝하는 방법](https://access.redhat.com/articles/uploading-rhel-image-to-azure)을 참조하세요. 또는 [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image를 활성화합니다.

PAYG VM 가격 책정에 대한 자세한 내용은 [Red Hat Enterprise Linux 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)을 참조하세요. PAYG 모델에서 RHEL을 사용하려면 [RHEL 7.7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) 또는 [RHEL 8.0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)에 대해 지정된 결제 방법을 사용하는 Azure 구독이 필요합니다. 이러한 제품을 사용하려면 Azure 구독에서 결제 방법을 지정해야 합니다.

#### <a name="using-rhel-os-with-the-byos-model"></a>BYOS 모델에서 RHEL OS 사용

BYOS를 RHEL OS에 사용하려면 Azure에서 RHEL OS를 사용할 수 있는 자격이 있는 유효한 Red Hat 구독이 있어야 합니다. BYOS 모델에서 RHEL OS를 배포하기 전에 다음 필수 구성 요소를 완료합니다.

1. RHEL OS 및 JBoss EAP 자격이 Red Hat 구독에 연결되어 있는지 확인합니다.
2. RHEL BYOS 이미지를 사용할 수 있는 권한을 Azure 구독 ID에 부여합니다. [Red Hat Subscription Management 설명서](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1)에 따라 다음 단계가 포함된 프로세스를 완료합니다.

   1. Red Hat Cloud Access 대시보드에서 Microsoft Azure를 공급자로 사용하도록 설정합니다.

   1. Azure 구독 ID를 추가합니다.

   1. Microsoft Azure에서 Cloud Access에 대한 새 제품을 사용하도록 설정합니다.
    
   1. Azure 구독에 대한 Red Hat Gold Images를 활성화합니다. 자세한 내용은 [Microsoft Azure의 Red Hat Gold Images](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access#proc_using-gold-images-azure_cloud-access)를 참조하세요.

   1. Azure 구독에서 Red Hat Gold Images를 사용할 수 있을 때까지 기다립니다. 이러한 이미지는 일반적으로 제출 후 3시간 이내에 사용할 수 있습니다.
    
3. RHEL BYOS 이미지에 대한 Azure Marketplace 사용 약관에 동의합니다. 다음 Azure CLI 명령을 실행하여 이 프로세스를 완료할 수 있습니다. 자세한 내용은 [Azure의 RHEL BYOS Gold Images](./byos.md) 설명서를 참조하세요. 최신 Azure CLI 버전을 실행해야 합니다.

   1. Azure CLI 세션을 열고, Azure 계정으로 인증합니다. 도움이 필요하면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

   1. 다음 CLI 명령을 실행하여 구독에서 RHEL BYOS 이미지를 사용할 수 있는지 확인합니다. 여기서 결과를 얻지 못하면 Azure 구독이 RHEL BYOS 이미지에 대해 활성화되어 있는지 확인합니다.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. 다음 명령을 실행하여 RHEL 7.7 BYOS 및 RHEL 8.0 BYOS 각각에 대한 Azure Marketplace 사용 약관에 동의합니다.
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
이제 구독에서 RHEL 7.7 또는 8.0 BYOS를 Azure 가상 머신에 배포할 준비가 되었습니다.

#### <a name="using-jboss-eap-with-the-byos-model"></a>BYOS 모델에서 JBoss EAP 사용

JBoss EAP는 BYOS 모델을 통해서만 Azure에서 사용할 수 있습니다. 이 템플릿을 배포하는 경우, 유효한 EAP 자격이 있는 RHSM 풀 ID와 함께 RHSM 자격 증명을 제공해야 합니다. EAP 자격이 없는 경우 시작하기 전에 [JBoss EAP 평가 구독](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)을 확보합니다.

## <a name="deployment-options"></a>배포 옵션

템플릿은 다음과 같은 방법으로 배포할 수 있습니다.

- **PowerShell**. 다음 명령을 실행하여 템플릿을 배포합니다. 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Azure PowerShell 설치 및 구성에 대한 자세한 내용은 [PowerShell 설명서](/powershell/azure/)를 참조하세요.  

- **Azure CLI** 다음 명령을 실행하여 템플릿을 배포합니다.

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Azure CLI 설치 및 구성에 대한 자세한 내용은 [CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

- **Azure Portal**. 다음 섹션에 설명된 대로 Azure 빠른 시작 템플릿으로 이동하여 Azure Portal에 배포할 수 있습니다. 빠른 시작에 들어가면 **Azure에 배포** 또는 **GitHub에서 찾아보기** 단추를 클릭합니다.

## <a name="azure-quickstart-templates"></a>Azure 빠른 시작 템플릿

배포 목표를 충족하는 RHEL의 JBoss EAP에 대한 다음 빠른 시작 템플릿 중 하나를 사용하여 시작할 수 있습니다.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> RHEL 기반 JBoss EAP(독립 실행형 VM)</a>. Azure에서 JBoss-EAP라는 웹 애플리케이션을 RHEL 7.7 또는 8.0 VM에서 실행되는 JBoss EAP 7.2 또는 7.3에 배포합니다.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> RHEL 기반 JBoss EAP(클러스터형, 여러 VM)</a>. *n* 개의 RHEL 7.7 또는 8.0 VM에서 실행되는 JBoss EAP 7.2 또는 7.3 클러스터에 eap-session-replication이라는 웹 애플리케이션이 배포됩니다. *n* 값은 사용자가 결정합니다. 모든 VM이 부하 분산 장치의 백 엔드 풀에 추가됩니다.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/"> RHEL 기반 JBoss EAP(클러스터형, 가상 머신 확장 집합)</a> RHEL 7.7 또는 8.0 가상 머신 확장 집합에서 실행되는 JBoss EAP 7.2 또는 7.3 클러스터에 eap-session-replication이라는 웹 애플리케이션이 배포됩니다.

## <a name="resource-links"></a>리소스 링크

* [Azure 하이브리드 혜택](../../windows/hybrid-use-benefit-licensing.md)
* [Azure App Service용 Java 앱 구성](../../../app-service/configure-language-java.md)
* [Azure Red Hat OpenShift 기반 JBoss EAP](https://azure.microsoft.com/services/openshift/)
* [Azure App Service Linux 기반 JBoss EAP](../../../app-service/quickstart-java.md)
* [Azure App Service에 JBoss EAP 배포](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>다음 단계

* [JBoss EAP 7.2](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2)에 대해 자세히 알아봅니다.
* [JBoss EAP 7.3](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3)에 대해 자세히 알아봅니다.
* [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management)에 대해 자세히 알아봅니다.
* [Azure의 Red Hat 워크로드](./overview.md)에 대해 알아봅니다.
* [Azure Marketplace에서 RHEL VM 또는 가상 머신 확장 집합에 JBoss EAP](https://aka.ms/AMP-JBoss-EAP)를 배포합니다.
* [Azure 빠른 시작 템플릿에서 RHEL VM 또는 가상 머신 확장 집합에 JBoss EAP](https://aka.ms/Quickstart-JBoss-EAP)를 배포합니다.

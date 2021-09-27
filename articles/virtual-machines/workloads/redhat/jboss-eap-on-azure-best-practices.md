---
title: Azure의 Red Hat JBoss EAP 모범 사례
description: 이 가이드에서는 Microsoft Azure에서 Red Hat JBoss Enterprise Application Platform을 사용하는 모범 사례에 대한 정보를 제공합니다.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 195a0bfa-dff1-429b-b030-19ca95ee6abe
ms.date: 06/08/2021
ms.openlocfilehash: 909f3dcff9f8ad5af5f1128d1d067f2430a00525
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730118"
---
# <a name="red-hat-jboss-eap-on-azure-best-practices"></a>Azure의 Red Hat JBoss EAP 모범 사례

Microsoft Azure에서 Red Hat JBoss EAP(Enterprise Application Platform)를 사용하기 위한 Azure의 Red Hat JBoss EAP 모범 사례 가이드입니다. JBoss EAP는 Azure에서 JBoss EAP를 실행하기 위해 지원되는 특정 구성 내에서 사용하기만 하면 Microsoft Azure 플랫폼에서 사용할 수 있습니다. 클러스터된 JBoss EAP 환경을 수동으로 구성하는 경우 Azure에서 JBoss EAP 클러스터링 기능을 사용하는 데 필요한 특정 구성을 적용합니다. 이 가이드에서는 Microsoft Azure에서 JBoss EAP를 사용하는 지원되는 구성에 대해 자세히 설명합니다.

JBoss EAP는 웹 프로필 및 전체 플랫폼 사양 모두에 대한 Jakarta EE(Enterprise Edition) 8 호환 구현입니다. 또한 Java EE 8 사양의 인증된 구현이기도 합니다. JBoss EAP의 주 버전은 커뮤니티 프로젝트가 원하는 기능 완성도 수준에 도달했을 때 특정 지점에서 WildFly 커뮤니티 프로젝트에서 포크됩니다. 이 시점 이후에는 프로덕션 사용을 위해 JBoss EAP가 안정화되고, 인증되고, 개선되는 장기간의 테스트 및 제품화 기간이 진행됩니다. JBoss EAP 주 버전의 수명 동안 선택한 기능은 커뮤니티 프로젝트에서 cherry-pick되고 백포트될 수 있습니다. 그런 다음, 이러한 기능은 동일한 주 버전 제품군 내에서 부 릴리스를 향상시키는 기능의 seFries에서 사용할 수 있습니다.

## <a name="supported-and-unsupported-configurations-of-jboss-eap-on-azure"></a>Azure에서 JBoss EAP의 지원되는 구성 및 지원되지 않는 구성

OS(운영 체제), Java 플랫폼 및 EAP를 사용할 수 있는 기타 지원되는 플랫폼에 대한 자세한 내용은 [JBoss EAP 지원 구성](https://access.redhat.com/articles/2026253) 설명서를 참조하세요.

Red Hat Cloud Access 프로그램을 사용하면 JBoss EAP 구독을 사용하여 Microsoft Azure Marketplace에서 주문형 PAYG(종량제) 운영 체제인 Azure 가상 머신에 JBoss EAP를 설치할 수 있습니다. 가상 머신 운영 체제 구독, 이 경우 RHEL(Red Hat Enterprise Linux)은 JBoss EAP 구독과는 별개입니다. Red Hat Cloud Access는 Red Hat 인증 클라우드 인프라 공급자(예: Microsoft Azure)에서 JBoss EAP를 지원하는 Red Hat 구독 기능입니다. Red Hat Cloud Access를 사용하면 간단하고 비용 효율적인 방식으로 기존의 온-프레미스 서버와 퍼블릭 클라우드 기반 리소스 간에 구독을 이동할 수 있습니다.

[Customer Portal에서 Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)에 대한 자세한 내용을 확인할 수 있습니다. 참고로, Azure Marketplace의 PAYG 제품에서는 Red Hat Cloud Access가 필요하지 않습니다. 

모든 Red Hat JBoss EAP 릴리스는 다양한 업계 최고의 운영 체제, JVM(Java Virtual Machines) 및 데이터베이스 조합에서 테스트되고 지원됩니다. Red Hat은 구독 계약에 따라 지원되는 구성 및 테스트된 통합에 대한 프로덕션 및 개발 지원을 모두 제공합니다. 지원은 실제 환경과 가상 환경 모두에 적용됩니다. 위의 운영 체제 제한 사항 외에 지원되는 JDK(Java Development Kit) 공급업체 및 버전과 같은 [JBoss EAP에 대해 지원되는 구성](https://access.redhat.com/articles/2026253)을 확인하세요. 또한 7.0, 7.1, 7.2 및 7.3과 같은 다양한 JBoss EAP 버전의 지원되는 구성에 대한 정보를 제공합니다. 지원되는 RHEL 운영 체제, VM 최소 용량 요구 사항 및 기타 지원되는 Red Hat 제품에 대한 정보는 [Microsoft Azure 제품/구성 매트릭스](https://access.redhat.com/articles/product-configuration-for-azure)를 확인하세요. Microsoft Azure 작동하도록 인증된 Red Hat 소프트웨어 제품에 대해서는 [인증된 클라우드 공급자/Microsoft Azure](https://access.redhat.com/ecosystem/cloud-provider/2068823)를 확인하세요.

Microsoft Azure 환경에서 JBoss EAP를 사용할 때 지원되지 않는 몇 가지 기능이 있습니다. 여기에는 다음이 포함됩니다.

- **관리형 도메인** - 단일 제어 지점에서 여러 JBoss EAP 인스턴스를 관리하도록 허용합니다. JBoss EAP 관리형 도메인은 현재 Microsoft Azure에서 지원되지 않습니다. 독립 실행형 JBoss EAP 서버 인스턴스만 지원됩니다. 독립 실행형 JBoss EAP 서버를 사용하여 JBoss EAP 클러스터를 구성하는 것은 Azure에서 지원됩니다.

- **공유 저장소를 사용하는 ActiveMQ Artemis HA(고가용성)** - Artemis 공유 저장소를 사용하는 JBoss EAP 메시징 HA는 Microsoft Azure에서 지원되지 않습니다.

- **`mod_custer`보급** - Azure UDP(User Datagram Protocol) 멀티캐스트 제한으로 인해 기능적으로 mod_cluster 보급 알림이 지원되지 않으므로 JBoss EAP를 Undertow `mod_cluster` 프록시 부하 분산 장치로 사용할 수 없습니다.

## <a name="other-features-of-jboss-eap-on-azure"></a>Azure에서 JBoss EAP의 기타 기능

JBoss EAP는 HA 클러스터링, 메시징 및 분산 캐싱과 같은 기능에 대한 미리 구성된 옵션을 제공합니다. 또한 이를 통해 사용자가 JBoss EAP에서 제공하는 다양한 API 및 서비스를 사용하여 애플리케이션을 작성, 배포 및 실행할 수 있습니다.

- **Jakarta EE 호환** - 웹 프로필 및 전체 플랫폼 사양 둘 다에 대해 Jakarta EE 8과 호환됩니다.

- **Java EE 규격** - 웹 프로필 및 전체 플랫폼 사양 둘 다에 대해 Java EE 8이 인증되었습니다.

- **관리 콘솔 및 관리 CLI** - 독립 실행형 서버 관리 인터페이스에 사용됩니다. 관리 CLI에는 관리 작업을 스크립팅하고 자동화할 수 있는 일괄 처리 모드도 포함되어 있습니다. JBoss EAP XML 구성 파일을 직접 편집하는 것은 권장되지 않습니다.

- **간소화된 디렉터리 레이아웃** - 모듈 디렉터리에는 모든 애플리케이션 서버 모듈이 포함되어 있습니다. 독립 실행형 디렉터리에는 독립 실행형 배포를 위한 아티팩트 및 구성 파일이 포함되어 있습니다.

- **모듈식 클래스 로드 메커니즘** - 모듈은 주문형으로 로드 및 언로드됩니다. 모듈식 클래스 로드 메커니즘은 성능을 향상시키고, 보안상의 이점이 있으며, 시작 및 다시 시작 시간을 줄여줍니다.

- **간소화된 DataSource 관리** - 데이터베이스 드라이버가 다른 서비스처럼 배포됩니다. 또한 관리 콘솔 및 관리 CLI를 사용하여 DataSources를 만들고 관리합니다.

- **통합 보안 프레임워크** - Elytron은 독립 실행형 서버에 대한 액세스를 관리하고 구성할 수 있는 단일 통합 프레임워크를 제공합니다. 이 프레임워크는 JBoss EAP 서버에 배포된 애플리케이션에 대한 보안 액세스를 구성하는 데도 사용할 수 있습니다.

## <a name="creating-your-microsoft-azure-environment"></a>Microsoft Azure 환경 만들기

Microsoft Azure 환경에서 JBoss EAP 인스턴스를 호스트할 VM을 만듭니다. Standard_A2 이상의 Azure VM 크기를 사용합니다. Azure 주문형 PAYG 프리미엄 이미지를 사용하여 VM을 만들거나 사용자 자체 VM을 수동으로 만들 수 있습니다. 예를 들어, 다음과 같이 RHEL VM을 배포할 수 있습니다.

* Azure에서 주문형 Marketplace RHEL 이미지 사용 - Azure Marketplace에는 JBoss EAP를 설정하려는 RHEL VM을 선택할 수 있는 몇 가지 제품이 있습니다. [Azure Marketplace에서 RHEL 8 VM 배포](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/assembly_deploying-a-rhel-image-as-a-virtual-machine-on-microsoft-azure_cloud-content)를 참조하세요. Azure Marketplace에서 RHEL OS 라이선스를 선택하는 방법에는 두 가지가 있습니다. Red Hat Gold Image 모델을 통해 PAYG 또는 BYOS(Bring-Your-Own-Subscription)를 선택합니다. PAYG 플랜을 사용하여 RHEL VM을 배포한 경우 JBoss EAP 구독 세부 정보는 Red Hat 구독에 대한 결과 배포를 구독하는 데만 사용됩니다.

* [Azure용 RHEL 이미지를 수동으로 만들기 및 프로비저닝](https://access.redhat.com/articles/uploading-rhel-image-to-azure) 각 RHEL 주 버전의 최신 부 버전을 사용합니다.

Microsoft Windows Server VM의 경우 Azure에서 Windows Server VM을 만드는 방법에 대해서는 [Microsoft Azure 설명서](../../windows/overview.md)를 참조하세요.

## <a name="jboss-eap-installation"></a>JBoss EAP 설치

> [!NOTE]
>  Azure Marketplace를 통해 RHEL의 JBoss EAP 제품을 사용하는 경우 JBoss EAP가 Azure 환경에 대해 자동으로 설치되고 구성됩니다.

Microsoft Azure에 배포된 RHEL VM에 EAP를 수동으로 배포하는 경우 아래 단계가 적용됩니다.

VM이 설정되면 JBoss EAP를 설치할 수 있습니다. Red Hat KB(기술 자료) 및 구독 리소스에 대한 중앙 집중식 플랫폼인 [Red Hat Customer Portal](https://access.redhat.com)에 액세스해야 합니다. EAP 구독이 없는 경우 [Red Hat 개인 개발자 구독](https://developers.redhat.com/register)을 통해 무료 개발자 구독에 등록할 수 있습니다. 등록되면 [Red Hat Customer Portal](https://access.redhat.com/management/)의 구독 섹션에서 자격 증명(풀 ID)을 찾습니다. 이 구독은 프로덕션용이 아닙니다.

변수 ‘EAP_HOME’을 사용하여 JBoss EAP 설치 경로를 나타냈습니다. 이 변수를 JBoss EAP 설치의 실제 경로로 바꿉니다.

> [!IMPORTANT]
> JBoss EAP를 설치하는 방법에는 여러 가지가 있습니다. 각 방법은 특정 상황에 가장 적합합니다. Microsoft Azure Marketplace의 RHEL 주문형 VM을 사용하는 경우 ZIP 또는 설치 관리자 방법을 사용하여 JBoss EAP를 설치합니다. **RHSM(Red Hat Subscription Management)에 RHEL 주문형 가상 머신을 등록하지 않도록 합니다. 이렇게 하면 PAYG 청구 방법을 사용하기 때문에 해당 가상 머신에 대해 요금이 두 번 청구됩니다.

* **ZIP 설치** - ZIP 보관 파일은 지원되는 모든 OS에 설치하는 데 적합합니다. 인스턴스를 수동으로 추출하려면 ZIP 설치 방법을 사용해야 합니다. ZIP 설치는 JBoss EAP의 기본 설치와 설치 후 수행할 모든 구성을 제공합니다. JBoss EAP 패치를 배포하고 설치하기 위해 JBoss ON(Operations Network) 서버를 사용하려는 경우 ZIP 설치 방법을 사용하여 대상 JBoss EAP 인스턴스를 설치해야 합니다. 자세한 내용은 [Zip 설치](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#zip_installation)를 확인하세요.

* **JAR 설치 관리자** - JAR 설치 관리자를 콘솔에서 실행하거나 그래픽 마법사로 실행할 수 있습니다. 두 옵션 모두 서버 인스턴스의 설치 및 구성에 대한 단계별 지침을 제공합니다. JAR 설치 관리자가 지원되는 모든 플랫폼에 JBoss EAP를 설치하는 기본 방법입니다. 자세한 내용은 [JAR 설치 관리자 설치](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#installer_installation)를 확인하세요.

* **RPM 설치** - RHEL 6, RHEL 7 및 RHEL 8의 지원되는 설치에서 RPM 패키지를 사용하여 JBoss EAP를 설치할 수 있습니다. RPM 설치 방법은 Azure의 RHEL VM에서 EAP 설치를 자동화하려는 경우에 가장 적합합니다. JBoss EAP의 RPM 설치는 JBoss EAP를 서비스로 실행하는 데 필요한 모든 항목을 설치합니다. 자세한 내용은 [RPM 설치](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#rpm_installation)를 확인하세요.

## <a name="other-offers-by-azure-and-red-hat"></a>Azure 및 Red Hat의 기타 제품

Azure로 마이그레이션하기 위한 견고한 시작점을 제공하기 위해, Red Hat과 Microsoft는 파트너 관계를 맺고 Azure 솔루션 템플릿 세트를 Azure Marketplace에 제공합니다. 제품 목록에 대한 설명서를 참조하고 적합한 환경을 선택합니다.

### <a name="azure-marketplace-offers"></a>Azure Marketplace 제품

이러한 제품은 [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)에서 액세스할 수 있습니다.

이 Marketplace 제품에는 유연한 지원 구독 모델이 있는 JBoss EAP 및 RHEL 버전의 다양한 조합이 포함되어 있습니다. JBoss EAP는 항상 BYOS이지만 RHEL OS의 경우 BYOS 또는 PAYG 중에 선택할 수 있습니다. Azure Marketplace 제품에는 RHEL 기반 JBoss EAP를 독립 실행형 VM, 클러스터형 VM 및 클러스터형 가상 머신 확장 집합으로 사용할 수 있는 플랜 옵션이 포함되어 있습니다. 

6가지 플랜은 다음과 같습니다.

- RHEL 8.3 독립 실행형 VM 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 독립 실행형 VM 기반 JBoss EAP 7.3(BYOS)
- RHEL 8.3 클러스터형 VM 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 클러스터형 VM 기반 JBoss EAP 7.3(BYOS)
- RHEL 8.3 클러스터형 가상 머신 확장 집합 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 클러스터형 가상 머신 확장 집합 기반 JBoss EAP 7.3(BYOS)

:::image type="content" source="./media/red-hat-marketplace-image-1.png" alt-text="이미지는 GitHub 배포 링크를 사용하여 Red Hat 이미지를 배포하는 옵션을 보여 줍니다.":::

### <a name="azure-quickstart-templates"></a>Azure 빠른 시작 템플릿

Azure Marketplace 제품과 함께 Azure 기반 EAP를 시험 사용할 수 있도록 빠른 시작 템플릿을 사용할 수 있습니다. 이러한 빠른 시작에는 다양한 구성 및 버전 조합으로 Azure 기반 JBoss EAP를 배포하기 위해 사전 빌드된 ARM 템플릿 및 스크립트가 포함되어 있습니다. 

솔루션 아키텍처에는 다음이 포함됩니다.

* RHEL 기반 JBoss EAP 독립 실행형 VM
* RHEL 기반 JBoss EAP 클러스터형 VM
* RHEL 기반 JBoss EAP 클러스터형 가상 머신 확장 집합

    :::image type="content" source="./media/red-hat-marketplace-image.png" alt-text="Azure Marketplace를 통해 사용할 수 있는 Red Hat 제품을 보여 주는 이미지":::

RHEL 7.7과 8.0 중에서, JBoss EAP 7.2와 7.3 중에서 선택할 수 있습니다. 다음 배포 조합 중 하나를 선택할 수 있습니다.

- RHEL 7.7의 JBoss EAP 7.2
- RHEL 8.0의 JBoss EAP 7.2
- RHEL 8.0의 JBoss EAP 7.3

시작하려면 배포 목표를 충족하는 RHEL의 JBoss EAP 조합과 일치하는 빠른 시작 템플릿을 선택합니다. 사용할 수 있는 빠른 시작 템플릿의 목록은 다음과 같습니다.

* [RHEL 독립 실행형 VM의 JBoss EAP](https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/) - Azure 템플릿은 RHEL(버전 7.7 또는 8.0) VM에서 실행되는 JBoss EAP(버전 7.2 또는 7.3)에 Azure의 JBoss-EAP라는 웹 애플리케이션을 배포합니다.

* [RHEL 클러스터형 VM의 JBoss EAP](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/) - Azure 템플릿은 'n' 번호 RHEL VM에서 실행되는 JBoss EAP 클러스터에 eap-session-replication이라는 웹 애플리케이션을 배포합니다. 'n'은 처음에 설정한 VM의 시작 수입니다. 모든 VM이 부하 분산 장치의 백 엔드 풀에 추가됩니다.

* [RHEL 기반 JBoss EAP 클러스터된 가상 머신 확장 집합](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/) - eap-session-replication이라는 웹 애플리케이션을 RHEL 7.7 또는 8.0 가상 머신 확장 집합 인스턴스에서 실행되는 JBoss EAP 7.2 또는 7.3 클러스터에 배포합니다.

## <a name="configuring-jboss-eap-to-work-on-cloud-platforms"></a>클라우드 플랫폼에서 작동하도록 JBoss EAP 구성

VM에 JBoss EAP를 설치한 후에는 JBoss EAP를 서비스로 실행하도록 구성할 수 있습니다. JBoss EAP를 서비스로 실행하도록 구성하는 방법은 JBoss EAP 설치 방법 및 VM OS 유형에 따라 달라집니다. JBoss EAP의 RPM 설치는 JBoss EAP를 서비스로 실행하는 데 필요한 모든 항목을 설치합니다. 자세한 내용은 [서비스로 실행하도록 JBoss EAP 구성](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#configuring_jboss_eap_to_run_as_a_service)을 확인하세요.

### <a name="starting-and-stopping-jboss-eap"></a>JBoss EAP 시작 및 중지

#### <a name="starting-jboss-eap"></a>JBoss EAP 시작

JBoss EAP는 RHEL 및 Windows Server에서 지원되며 독립 실행형 서버 운영 모드에서만 실행됩니다. JBoss EAP를 시작하는 특정 명령은 기본 플랫폼에 따라 달라집니다. 서버는 처음에 일시 중단된 상태로 시작되며 모든 필수 서비스가 시작될 때까지 어떤 요청도 수락하지 않습니다. 이때 서버는 정상 실행 상태로 전환되고 요청 수락을 시작할 수 있습니다. 다음은 JBoss EAP를 독립 실행형 서버로 시작하는 명령입니다.

- RHEL VM에서 독립 실행형 서버로 JBoss EAP(ZIP 또는 설치 관리자 방법을 통해 설치됨)를 시작하는 명령:
    ```
    $EAP_HOME/bin/standalone.sh
    ```
- Windows Server의 경우 `EAP_HOME\bin\standalone.bat` 스크립트를 사용하여 JBoss EAP를 독립 실행형 서버로 시작합니다.
- JBoss EAP를 시작하는 것은 ZIP 또는 JAR 설치 관리자를 통한 설치와 비교할 때 RPM 설치와 다릅니다. 예를 들어, RHEL 7 이상에서는 다음 명령을 사용합니다.
    ```
    systemctl start eap7-standalone.service
    ```
JBoss EAP(ZIP 또는 설치 관리자 방법을 통해 설치됨)를 시작하는 데 사용되는 시작 스크립트는 `EAP_HOME/bin/standalone.conf` 파일을 사용하거나 Windows Server의 경우 `standalone.conf.bat`을 사용하여 JVM 옵션과 같은 몇 가지 기본 설정을 지정합니다. 이 파일의 설정을 사용자 지정합니다. JBoss EAP는 기본적으로 `standalone.xml` 구성 파일을 사용하지만 다른 구성 파일을 사용하여 시작할 수 있습니다. RPM 방법을 통해 설치된 JBoss EAP를 시작하는 데 사용되는 기본 구성 파일을 변경하려면 `/etc/opt/rh/eap7/wildfly/eap7-standalone.conf`를 사용합니다. 동일한 eap7-standalone.conf 파일을 사용하여 WildFly 바인딩 주소와 같은 다른 구성을 변경합니다.

사용 가능한 독립 실행형 구성 파일 및 사용 방법에 대한 자세한 내용은 [독립 실행형 서버 구성 파일](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#standalone_server_configuration_files)을 확인하세요.

다른 구성을 사용하여 JBoss EAP를 시작하려면 --server-config 인수를 사용합니다. 사용 가능한 모든 시작 스크립트 인수 및 해당 용도의 전체 목록을 보려면 --help 인수를 사용하거나 [서버 런타임 인수](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#reference_of_switches_and_arguments_to_pass_at_server_runtime)를 확인합니다.

#### <a name="stopping-jboss-eap"></a>JBoss EAP 중지

JBoss EAP를 중지하는 방법은 JBoss EAP가 시작된 방법에 따라 달라집니다. JBoss EAP가 시작된 터미널에서 `Ctrl+C`를 눌러 JBoss EAP의 대화형 인스턴스를 중지합니다. JBoss EAP의 백그라운드 인스턴스를 중지하려면 관리 CLI를 사용하여 실행 중인 인스턴스에 연결하고 서버를 종료합니다. 자세한 내용은 [JBoss EAP 중지](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#stopping_jboss_eap)를 확인하세요.

JBoss EAP를 중지하는 것은 ZIP 또는 설치 관리자를 통한 설치와 비교할 때 RPM 설치와 다릅니다. JBoss EAP의 RPM 설치를 중지하는 명령은 시작하려는 운영 모드와 실행 중인 RHEL 버전에 따라 달라집니다. 독립 실행형 모드는 Azure에서만 지원되는 모드입니다. 

- 예를 들어, RHEL 7 이상에서는 다음 명령을 사용합니다.
    ```
    systemctl stop eap7-standalone.service
    ```
또한 JBoss EAP를 일시 중단하거나 정상적으로 종료하여 새 요청을 수락하지 않고 활성 요청이 정상적으로 완료되도록 할 수 있습니다. 서버가 일시 중단되고 나면 서버가 종료되거나, 실행 중 상태로 다시 반환되거나, 유지 관리를 위해 일시 중단된 상태가 될 수 있습니다. 서버가 일시 중단된 동안 관리 요청은 여전히 처리됩니다. 관리 콘솔 또는 관리 CLI를 사용하여 서버를 일시 중단했다가 다시 시작할 수 있습니다.

### <a name="configuring-jboss-eap-subsystems-to-work-on-cloud-platforms"></a>클라우드 플랫폼에서 작동하도록 JBoss EAP 하위 시스템 구성

JBoss EAP에 배포된 애플리케이션에 노출되는 많은 API 및 기능은 하위 시스템으로 구성됩니다. 관리자는 애플리케이션의 목표에 따라 다른 동작을 제공하도록 이러한 하위 시스템을 구성할 수 있습니다. 하위 시스템에 대한 자세한 내용은 [JBoss EAP 하위 시스템](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#jboss_eap_subsystems)을 확인하세요.

일부 JBoss EAP 하위 시스템은 클라우드 플랫폼에서 제대로 작동하기 위해 구성이 필요합니다. JBoss EAP 서버가 클라우드 VM의 개인 IP 주소에 바인딩되어 있으므로 구성이 필요합니다. 프라이빗 IP는 클라우드 플랫폼 내에서만 볼 수 있습니다. 특정 하위 시스템인 경우 개인 IP 주소를 클라우드 외부에서 볼 수 있는 서버의 공용 IP 주소에 매핑해야 합니다. 이러한 하위 시스템을 수정하는 방법에 대한 자세한 내용은 [클라우드 플랫폼에서 작동하도록 JBoss EAP 하위 시스템 구성](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#configuring_subsystems_for_cloud_platforms)을 확인하세요.

## <a name="using-jboss-eap-high-availability-in-microsoft-azure"></a>Microsoft Azure에서 JBoss EAP 고가용성 사용

Azure는 UDP 멀티캐스트를 기준으로 하는 JGroups 검색 프로토콜을 지원하지 않습니다. JGroups는 기본적으로 UDP 스택을 사용하지만 Azure에서 UDP를 지원하지 않기 때문에 TCP로 변경해야 합니다. TCPPING, JDBC_PING과 같은 다른 JGroups 검색 프로토콜을 사용할 수 있지만 Azure_PING Azure용으로 개발된 공유 파일 검색 프로토콜인 *Azure_PING* 이 권장됩니다.

*AZURE_PING* 은 Microsoft Azure 스토리지 계정에서 공통 Blob 컨테이너를 사용합니다. AZURE_PING이 사용할 수 있는 Blob 컨테이너가 없는 경우 VM에서 액세스할 수 있는 Blob 컨테이너를 만듭니다. 자세한 내용은 [Microsoft Azure에서 JBoss EAP 고가용성 구성](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#using_jboss_eap_high_availability_in_microsoft_azure)을 확인하세요.

부하 분산 환경을 통해 JBoss EAP를 구성합니다. 모든 분산 장치와 작업자가 내부 Microsoft Azure VNet(Virtual Network)에서 허용되는 IP 주소에 바인딩되어 있는지 확인합니다. 부하 분산 구성에 대한 자세한 내용은 [Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성](https://access.redhat.com/articles/3252491)을 확인하세요.

## <a name="other-best-practices"></a>기타 모범 사례

- VM에서 JBoss EAP 설치 관리자이 경우 VM의 보안을 보장하는 것이 중요합니다. 게스트 및 호스트 OS가 악성 소프트웨어에 감염될 위험이 크게 낮아집니다. VM을 보호하면 JBoss EAP에 대한 공격과 JBoss EAP에서 호스트되는 애플리케이션의 오작동이 줄어듭니다. [Azure RBAC(역할 기반 액세스 제어)](../../../role-based-access-control/overview.md)에서 [Azure Policy](https://azure.microsoft.com/services/azure-policy/) 및 [Azure 기본 제공 역할](../../../role-based-access-control/built-in-roles.md) 등의 기능을 사용하여 Azure VM에 대한 엑세스를 컨트롤 합니다. Microsoft Antimalware 또는 Microsoft 파트너의 엔드포인트 보호 솔루션을 설치하고 맬웨어 방지 솔루션을 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합하고 보호 상태를 모니터링하여 맬웨어로부터 VM을 보호합니다. RHEL VM에서는 포트 전달을 차단하고 루트 로그인을 차단하여 VM을 보호할 수 있으며, 이는 */에서 사용하지 않도록 설정할 수 있습니다.


- 환경 변수를 사용하여 Azure VM에서 JBoss EAP를 쉽고 원활하게 사용할 수 있습니다. 예를 들어, EAP_HOME을 사용하여 여러 번 사용되는 JBoss EAP 설치 경로를 나타낼 수 있습니다. 이러한 경우 환경 변수가 유용하게 사용됩니다. 환경 변수는 서비스를 구성하고 웹 애플리케이션 비밀을 처리하는 일반적인 수단이기도 합니다. 내보내기 명령을 사용하여 셸에서 환경 변수를 설정하면 사용자 세션이 끝날 때 환경 변수의 존재도 끝납니다. 이러한 상황은 세션 간에 변수를 유지해야 하는 경우 문제가 됩니다. 특정 환경을 사용자 환경에 대해 영구적으로 만들기 위해 사용자의 프로필 스크립트에서 변수를 내보냅니다. bash_profile에서 유지하려는 모든 환경 변수에 대한 내보내기 명령을 추가합니다. VM에 액세스할 수 있는 모든 사용자에 대해 영구 전역 환경 변수를 설정하려면 기본 프로필에 추가할 수 있습니다. `/etc/profile.d`라는 디렉터리에 전역 환경 변수를 저장하는 것이 좋습니다. 이 디렉터리에는 전체 시스템에 대한 환경 변수를 설정하는 데 사용되는 파일 목록이 포함되어 있습니다. Windows Server 명령 프롬프트에서 set 명령을 사용하여 시스템 환경 변수를 설정해도 환경 변수가 영구적으로 설정되지는 않습니다. *setx* 명령 또는 제어판의 시스템 인터페이스를 사용합니다.

- VM 업데이트 및 업그레이드를 관리합니다. Azure Automation의 [업데이트 관리](../../../automation/update-management/overview.md) 솔루션을 사용하여 Azure에 배포된 Windows 및 Linux 컴퓨터의 운영 체제 업데이트를 관리합니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 대한 필수 업데이트를 설치하는 프로세스를 관리합니다. VM 소프트웨어를 업데이트하면 중요한 Microsoft Azure 패치, 하이퍼바이저 드라이버 및 소프트웨어 패키지가 최신 상태로 유지됩니다. 부 릴리스의 경우 현재 위치 업그레이드가 가능합니다. 예를 들어, RHEL 6.9에서 RHEL 6.10으로 또는 RHEL 7.3에서 RHEL 7.4로 업그레이드할 수 있습니다. *yum update* 명령을 실행하여 현재 위치 유형의 업그레이드를 수행할 수 있습니다. Microsoft Azure에서는 주 릴리스에 대한 현재 위치 업그레이드(예: RHEL 6에서 RHEL 7로)를 지원하지 않습니다.

- [Azure Monitor](../../../azure-monitor/data-platform.md)를 사용하여 리소스 상태에 대한 가시성을 얻을 수 있습니다. Azure Monitor 기능에는 [리소스 진단 로그 파일](../../../azure-monitor/essentials/platform-logs-overview.md)이 포함됩니다. VM 리소스를 모니터링하고 성능 및 가용성을 손상시킬 수 있는 잠재적인 문제를 식별하는 데 사용됩니다. [Azure Diagnostics 확장](../../../azure-monitor/agents/diagnostics-extension-overview.md)은 Windows VM에 모니터링 및 진단 기능을 제공할 수 있습니다. 확장을 Azure Resource Manager 템플릿의 일부로 포함하여 이러한 기능을 사용하도록 설정합니다. 부팅되지 않는 VM 문제를 해결할 때 사용할 중요한 도구인 부팅 진단을 사용하도록 설정합니다. 콘솔 출력 및 부팅 로그는 부팅 문제를 해결할 때 Red Hat 기술 지원에 많은 도움이 될 수 있습니다. VM을 만드는 동안에는 Microsoft Azure Portal에서 또는 기존 VM에서 부팅 진단을 사용하도록 설정합니다. 부팅 진단을 사용하도록 설정하면 VM에 대한 콘솔 출력을 보고, 문제 해결을 위해 부팅 로그를 다운로드할 수 있습니다.

- 보안 통신을 보장하는 또 다른 방법은 [VNet(가상 네트워크)](../../../virtual-network/virtual-networks-overview.md) 및 [VPN(가상 사설망)](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)에서 프라이빗 엔드포인트를 사용하는 것입니다. 개방형 네트워크는 외부 세계에 액세스할 수 있으며 악의적인 사용자의 공격에 취약합니다. VNet 및 VPN은 선택한 사용자로 액세스를 제한합니다. VNET은 개인 IP를 사용하여 동일한 범위 내의 서버 간에 격리된 통신 채널을 설정합니다. 격리된 통신을 사용하면 동일한 계정의 여러 서버가 퍼블릭 공간에 노출되지 않고 정보 및 데이터를 교환할 수 있습니다. 프라이빗 네트워크를 통해 로컬로 원격 서버에 연결합니다. 애플리케이션 서버의 경우와 동일한 VNet에서 JumpVM/JumpBox를 사용하거나 [Azure 가상 네트워킹 피어링](../../../virtual-network/virtual-network-peering-overview.md), [Azure Application Gateway](../../../application-gateway/overview.md), [Azure Bastion](https://azure.microsoft.com/services/azure-bastion) 등을 사용하는 것과 같은 다양한 방법이 있습니다. 이러한 모든 방법은 완벽하게 안전한 프라이빗 연결을 사용하고 여러 원격 서버에 연결할 수 있도록 합니다.

- [Azure NSG(네트워크 보안 그룹)](../../../virtual-network/network-security-groups-overview.md)를 사용하여 Azure VNet의 애플리케이션 서버와의 네트워크 트래픽을 필터링합니다. NSG에는 여러 종류의 Azure 리소스로의 인바운드 트래픽 또는 이러한 리소스로부터의 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙이 있습니다. 규칙마다 원본 및 대상, 포트, 프로토콜을 지정할 수 있습니다. 이러한 NSG 규칙을 사용하여 JBoss EAP에서 애플리케이션을 보호하고 인터넷에 대한 포트를 차단하거나 허용합니다.

- Azure의 JBoss EAP에서 실행되는 애플리케이션의 더 나은 기능을 위해 Azure에서 지원되는 HA 기능을 사용할 수 있습니다. Azure의 HA는 Load-Balancer, Application Gateway 또는 [Virtual Machine Scale Set](../../../virtual-machine-scale-sets/overview.md)와 같은 Azure 리소스를 사용하여 달성할 수 있습니다. 이러한 HA 방법은 중복성과 향상된 성능을 제공하므로 사용 가능한 다른 애플리케이션 인스턴스에 부하를 분산하여 유지 관리를 쉽게 수행하거나 애플리케이션 인스턴스를 업데이트할 수 있습니다. 추가 고객 요구에 맞추려면 애플리케이션을 실행하는 애플리케이션 인스턴스의 수를 늘려야 할 수도 있습니다. 가상 머신 확장 집합에는 수요 변화에 따라 애플리케이션을 자동으로 스케일 업 또는 스케일 다운할 수 있는 자동 스케일링 기능도 있습니다.

## <a name="optimizing-the-jboss-eap-server-configuration"></a>JBoss EAP 서버 구성 최적화

JBoss EAP 서버를 설치하고 관리 사용자를 만든 후에는 서버 구성을 최적화할 수 있습니다. 프로덕션 환경에서 애플리케이션을 배포할 때 서버 구성을 최적화하고 일반적인 문제를 방지하는 방법에 대한 [성능 조정 가이드](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/performance_tuning_guide/index)의 정보를 검토해야 합니다.

## <a name="resource-links-and-support"></a>리소스 링크 및 지원

지원, 문제 또는 사용자 지정 요구 사항과 관련된 지원은 [Red Hat 지원](https://access.redhat.com/support) 또는 [Microsoft Azure 지원](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

* [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/getting_started_with_jboss_eap_for_openshift_online/index)에 대해 자세히 알아봅니다.
* RHSM(Red Hat Subscription Manager) [Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* [ARO(Azure Red Hat OpenShift)](https://azure.microsoft.com/services/openshift/)
* [Azure의 Red Hat](./overview.md) Microsoft 문서
* [Azure의 RHEL BYOS 골드 이미지](./byos.md)
* Azure의 JBoss EAP [빠른 시작 비디오 자습서](https://www.youtube.com/watch?v=3DgpVwnQ3V4) 

## <a name="next-steps"></a>다음 단계

* [Azure의 JBoss EAP로 마이그레이션 조회](https://aka.ms/JavaCloud)
* [Azure App Service](/azure/developer/java/ee/jboss-on-azure)에서 JBoss EAP 실행
* [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)에서 RHEL VM/VM Scale Set에 JBoss EAP 배포
* [Azure 빠른 시작](https://aka.ms/Quickstart-JBoss-EAP)에서 RHEL VM/VM Scale Set에 JBoss EAP 배포
* Azure [App Service Migration 지원](https://azure.microsoft.com/services/app-service/migration-assistant/) 사용
* Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta) 사용
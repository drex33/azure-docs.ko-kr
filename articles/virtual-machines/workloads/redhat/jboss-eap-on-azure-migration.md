---
title: JBoss EAP에서 Azure 가상 머신으로 가상 머신 확장 집합 마이그레이션 가이드
description: 이 가이드에서는 엔터프라이즈 Java 애플리케이션을 다른 애플리케이션 서버에서 JBoss EAP로 마이그레이션하고 기존 온-프레미스 서버에서 Azure RHEL VM 및 가상 머신 확장 집합으로 마이그레이션하는 방법에 대한 정보를 제공합니다.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 06/08/2021
ms.openlocfilehash: 1671b0c4ba57faa6dc631c50cc273678c72b379b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730127"
---
# <a name="how-to-migrate-java-applications-to-jboss-eap-on-azure-vms-and-virtual-machine-scale-sets"></a>Azure VM 기반 JBoss EAP 및 가상 머신 확장 집합에서 Java 애플리케이션을 마이그레이션하는 방법

이 가이드에서는 클라우드 전략이 Java 애플리케이션을 그대로 “리프트 앤 시프트”하는 것인 경우 [Red Hat JBoss Enterprise Application Platform(EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform)의 엔터프라이즈 Java 애플리케이션을 기존 온-프레미스 서버에서 Azure RHEL(Red Hat Enterprise Linux) VM(Virtual Machines)과 가상 머신 확장 집합으로 마이그레이션하는 방법에 대한 정보를 제공합니다. 하지만 “리프트 앤 옵티마이즈” 전략을 원하는 경우 컨테이너화된 애플리케이션을 Red Hat Gallery의 JBoss EAP 이미지와 함께 [Azure Red Hat OpenShift(ARO)](https://azure.microsoft.com/services/openshift/)로 마이그레이션하거나 Azure 기반 JBoss EAP App Service 인스턴스에 직접 Java 앱 코드를 배치할 수 있습니다.

## <a name="best-practice-starting-with-azure-marketplace-offers-and-quickstarts"></a>Azure Marketplace 제품 및 빠른 시작으로 시작하기 모범 사례

Azure로 마이그레이션하기 위한 견고한 시작점을 제공하기 위해, Red Hat과 Microsoft는 파트너 관계를 맺고 [Azure 기반 JBoss EAP Marketplace 제품](https://aka.ms/AMP-JBoss-EAP) 세트를 제공합니다. 설명서를 참조하여 기존 배포와 가장 일치하는 제품과 플랜을 선택하세요. [Azure 기반 JBoss EAP 모범 사례](./jboss-eap-on-azure-best-practices.md)

좋은 시작점으로 사용할 기존 제품이 없는 경우 Azure VM 및 기타 사용 가능한 리소스를 사용하여 배포를 수동으로 재현할 수 있습니다. 자세한 내용은 [IaaS란?](https://azure.microsoft.com/overview/what-is-iaas/)을 참조하세요.

### <a name="azure-marketplace-offers"></a>Azure Marketplace 제품

Microsoft와 파트너 관계를 맺은 Red Hat은 Azure Marketplace 다음 제품을 게시했습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/) 또는 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 이러한 제품을 사용할 수 있습니다. [Azure Marketplace 제품을 사용하여 Azure 기반 Red Hat JBoss EAP VM 및 가상 머신 확장 집합을 배포](./jboss-eap-marketplace-image.md)하는 방법에 대한 문서에서 자세한 내용을 확인하세요.

이 Marketplace 제품에는 유연한 지원 구독 모델이 있는 JBoss EAP 및 RHEL 버전의 다양한 조합이 포함되어 있습니다. JBoss EAP는 BYOS(Bring-Your-Own-Subscription)이지만, RHEL의 경우 BYOS 또는 PAYG(종량제) 중에서 선택할 수 있습니다. Azure Marketplace 제품에는 RHEL 기반 JBoss EAP를 독립 실행형 VM, 클러스터형 VM 및 클러스터형 가상 머신 확장 집합으로 사용할 수 있는 플랜 옵션이 포함되어 있습니다. 6개 플랜은 다음과 같습니다.

- RHEL 8.3 독립 실행형 VM 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 독립 실행형 VM 기반 JBoss EAP 7.3(BYOS)
- RHEL 8.3 클러스터형 VM 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 클러스터형 VM 기반 JBoss EAP 7.3(BYOS)
- RHEL 8.3 클러스터형 가상 머신 확장 집합 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 클러스터형 가상 머신 확장 집합 기반 JBoss EAP 7.3(BYOS)

### <a name="azure-quickstart-templates"></a>Azure 빠른 시작 템플릿

Azure Marketplace 제품과 함께 Azure 기반 EAP를 시험 사용할 수 있도록 빠른 시작 템플릿을 사용할 수 있습니다. 이러한 빠른 시작에는 다양한 구성 및 버전 조합으로 Azure 기반 JBoss EAP를 배포하기 위해 사전 빌드된 ARM 템플릿 및 스크립트가 포함되어 있습니다. 솔루션 아키텍처에는 다음이 포함됩니다.

- RHEL 기반 JBoss EAP 독립 실행형 VM
- RHEL 기반 JBoss EAP 클러스터형 VM
- RHEL 기반 JBoss EAP 클러스터형 가상 머신 확장 집합

빠르게 시작하려면 RHEL 기반 JBoss EAP 버전 조합에 근접하게 일치하는 빠른 시작 템플릿 중 하나를 선택하세요. 자세한 내용은 [Azure 기반 JBoss EAP 빠른 시작](./jboss-eap-on-rhel.md)을 확인하세요. 

## <a name="prerequisites"></a>사전 요구 사항

* **활성 구독이 있는 Azure 계정** - Azure 구독이 없는 경우 [Visual Studio 구독 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)(이전 MSDN)을 활성화하거나 [무료로 계정을 만들 수 있습니다](https://azure.microsoft.com/pricing/free-trial).

- **JBoss EAP 설치** - JBoss EAP에 대한 RHSM(Red Hat Subscription Management) 자격이 있는 Red Hat 계정이 있어야 합니다. 이 자격을 통해 Red Hat에서 테스트되고 인증된 JBoss EAP 버전을 다운로드할 수 있습니다.  EAP 자격이 없는 경우 [Red Hat 개인 개발자 구독](https://developers.redhat.com/register)을 통해 무료 개발자 구독에 등록할 수 있습니다. 등록되면 [Red Hat 고객 포털](https://access.redhat.com/management/)에서 필요한 자격 증명(풀 ID)을 찾을 수 있습니다.

- **RHEL 옵션** - PAYG(종량제) 또는 BYOS(Bring-Your-Own-Subscription) 중에서 선택합니다. BYOS를 사용하는 경우 솔루션 템플릿을 사용하여 Marketplace 제품을 배포하기 전에 [Red Hat Cloud Access](https://access.redhat.com/) [RHEL Gold Image](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/)를 활성화해야 합니다. 다음 [지침](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide)에 따라 RHEL Gold Image를 Microsoft Azure에서 사용할 수 있도록 설정합니다.

- **[Azure CLI(명령줄 인터페이스)](/cli/azure/overview)** .

- **Java 소스 코드 및 [JDK(Java Development Kit) 버전](https://www.oracle.com/java/technologies/javase-downloads.html)**

- **[JBoss EAP 7.2 기반 Java 애플리케이션](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/index)** 또는 **[JBoss EAP 7.3 기반 Java 애플리케이션](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/development_guide/index#get_started_developing_applications)** .

**RHEL 옵션** - PAYG 또는 BYOS 중에서 선택합니다. BYOS의 경우 Azure Marketplace 제품을 사용하기 위해 [Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) RHEL Gold Image를 활성화해야 합니다. BYOS 제품은 Azure Portal의 프라이빗 제품 섹션에 표시됩니다. 

**제품 버전**

* [JBoss EAP 7.2](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2)
* [JBoss EAP 7.3](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3)
* [RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM)
* [RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)

## <a name="migration-flow-and-architecture"></a>마이그레이션 흐름 및 아키텍처

이 섹션에서는 JBoss EAP 애플리케이션을 JBoss EAP에서 실행하기 위해 다른 애플리케이션 서버에서 마이그레이션하고 기존 온-프레미스 서버에서 Microsoft Azure 클라우드 환경으로 마이그레이션하기 위한 무료 도구를 간략하게 설명합니다. 

### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat MTA(Migration Toolkit for Applications)

EAP 관련 마이그레이션 프로젝트를 실행하기 전에 계획 주기를 시작할 때 Java 애플리케이션을 마이그레이션하는 데 Red Hat MTA를 사용하는 것이 좋습니다. MTA는 [광범위한 변환 및 사용 사례](https://developers.redhat.com/products/mta/use-cases)에서 대규모 Java 애플리케이션 현대화 및 마이그레이션 프로젝트를 지원하는 도구의 모음입니다. 애플리케이션 코드 분석을 가속화하고, 예상 작업량을 지원하고, 코드 마이그레이션을 가속화하며, 애플리케이션을 클라우드 및 컨테이너로 이동하는 데 도움이 됩니다.

:::image type="content" source="./media/migration-toolkit.png" alt-text="이미지는 마이그레이션 도구 키트 앱의 대시보드 페이지를 보여줍니다.":::

Red Hat MTA를 사용하면 다른 애플리케이션 서버에서 Red Hat JBoss EAP로 애플리케이션을 마이그레이션할 수 있습니다.

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 성공적으로 수행하려면 시작하기 전에 다음 섹션에서 설명하는 평가 및 인벤토리 단계를 완료합니다.

### <a name="validate-the-compatibility"></a>호환성 유효성 검사

마이그레이션을 계획하기 전에 현재 배포 모델 및 버전의 유효성을 검사하는 것이 좋습니다. 현재 버전이 지원되지 않는 경우 애플리케이션을 크게 변경해야 할 수 있습니다.

MTA는 Oracle WebLogic Server와 같은 타사 엔터프라이즈 애플리케이션 서버에서 JBoss EAP로의 마이그레이션 및 JBoss EAP 최신 릴리스로의 업그레이드를 지원합니다.

다음 표에서는 가장 일반적으로 지원되는 마이그레이션 경로에 대해 설명합니다.

**표 - 지원되는 마이그레이션 경로: 원본에서 대상**

|원본 플랫폼 ⇒ | JBoss EAP 6 | JBoss EAP 7 | Red Hat OpenShift | OpenJDK 8 & 11 | Apache Camel 3 | Spring Boot on RH Runtimes | Quarkus
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Oracle WebLogic Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| IBM WebSphere Application Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 4 | &#x2714; | &#x2714; | X<sup>1</sup> | &#x2714; | - | - | - |
| JBoss EAP 5 | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 7 | 해당 없음 | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| Oracle JDK | - | - | &#x2714; | &#x2714; | - | - | - |
| Apache Camel 2 | - | - | &#x2714; | &#x2714; | &#x2714; | - | - |
| SpringBoot | - | - | &#x2714; | &#x2714; | - | &#x2714; | &#x2714; |
| Java 애플리케이션 | - | - | &#x2714; | &#x2714; | - | - | - |

<sup>1</sup> MTA는 현재 이 마이그레이션 경로에 대한 규칙을 제공하지 않지만 Red Hat Consulting은 모든 원본 플랫폼에서 JBoss EAP 7로의 마이그레이션을 지원할 수 있습니다.

MTA에 대한 [시스템 요구 사항](https://access.redhat.com/documentation/en/migration_toolkit_for_applications/5.0/html-single/introduction_to_the_migration_toolkit_for_applications/index#system_requirements_getting-started-guide)을 확인할 수도 있습니다.

마이그레이션을 계획하기 전에 [JBoss EAP 7.3 지원되는 구성](https://access.redhat.com/articles/2026253#EAP_73) 및 [JBoss EAP 7.2 지원되는 구성](https://access.redhat.com/articles/2026253#EAP_72)을 확인합니다.

현재 Java 버전을 가져오려면 프로덕션 서버에 로그인하고 다음 명령을 실행합니다.

```
java -version
```

### <a name="validate-operating-mode"></a>운영 모드 유효성 검사

JBoss EAP는 RHEL, Windows Server 및 Oracle Solaris에서 지원됩니다. JBoss EAP는 개별 인스턴스를 관리하기 위한 독립 실행형 서버 운영 모드 또는 단일 제어 지점에서 인스턴스 그룹을 관리하기 위한 관리되는 도메인 운영 모드로 실행됩니다.

JBoss EAP 관리되는 도메인은 Microsoft Azure에서 지원되지 않습니다. 독립 실행형 JBoss EAP 서버 인스턴스만 지원됩니다. 독립 실행형 JBoss EAP 서버를 사용하여 JBoss EAP 클러스터를 구성하는 것은 Azure에서 지원되며, 이를 통해 Azure Marketplace 제품으로 클러스터형 VM 또는 가상 머신 확장 집합을 만들 수 있습니다.

### <a name="inventory-server-capacity"></a>서버 용량 인벤토리화

현재 프로덕션 서버의 하드웨어(메모리, CPU, 디스크 등)와 평균/최대 요청 수 및 리소스 사용량을 문서화합니다. 선택한 마이그레이션 경로에 관계없이 이 정보가 필요합니다. 크기에 대한 자세한 내용은 [클라우드 서비스 크기](../../../cloud-services/cloud-services-sizes-specs.md)를 참조하세요.

### <a name="inventory-all-secrets"></a>모든 비밀 인벤토리화

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 또는 [Azure App Configuration](https://azure.microsoft.com/services/app-configuration/) 같은 "서비스로 구성" 기술이 등장하기 전에는 "비밀"에 대한 개념이 잘 정의되지 않았습니다. 그 대신 우리가 현재 "비밀"이라고 부르는 기능을 효율적으로 수행하는 별도의 구성 세트가 있었습니다. JBoss EAP 같은 앱 서버를 사용하는 경우 이러한 비밀이 다양한 구성 파일 및 구성 저장소에 있습니다. 프로덕션 서버의 모든 속성 및 구성 파일에 비밀과 암호가 있는지 확인합니다. WAR 파일에서 *jboss-web.xml* 파일을 확인합니다. 애플리케이션 내에서도 암호 또는 자격 증명을 포함하는 구성 파일을 찾을 수 있습니다. Azure Key Vault 대한 자세한 내용은 [Azure Key Vault 기본 개념](../../../key-vault/general/basic-concepts.md)을 참조하세요.

### <a name="inventory-all-certificates"></a>모든 인증서 인벤토리화

공용 SSL 엔드포인트에 사용되는 모든 인증서를 문서화합니다. 다음 명령을 실행하여 프로덕션 서버에서 모든 인증서를 볼 수 있습니다.

```cli
keytool -list -v -keystore <path to keystore>
```

### <a name="inventory-jndi-resources"></a>JNDI 리소스 인벤토리화

- 모든 JNDI(Java Naming and Directory Interface) 리소스를 인벤토리화합니다. Java Message Service(JMS) 브로커와 같은 일부 리소스에는 마이그레이션 또는 재구성이 필요할 수 있습니다.

### <a name="insideyyour-application"></a>InsideyYour 애플리케이션 

WEB-INF/jboss-web.xml 및/또는 WEB-INF/web.xml 파일을 검사합니다.

### <a name="document-data-sources"></a>데이터 원본 문서화

애플리케이션에서 데이터베이스를 사용하는 경우 다음 정보를 캡처해야 합니다.

* DataSource 이름은 무엇입니까?
* 연결 풀 구성이란?
* Java Database Connectivity(JDBC) 드라이버 JAR 파일은 어디에서 찾을 수 있나요?

자세한 내용은 JBoss EAP 설명서의 [JBoss EAP DataSources 정보](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.4/html/configuration_guide/datasource_management)를 참조하세요.

### <a name="determine-whether-and-how-the-file-system-is-used"></a>파일 시스템의 사용 여부 및 방법 확인

애플리케이션 서버에서 파일 시스템을 사용하려면 재구성하거나 드물게 아키텍처 변경이 필요합니다. 파일 시스템은 JBoss EAP 모듈 또는 애플리케이션 코드에서 사용할 수 있습니다. 다음 섹션에서 설명하는 시나리오의 일부 또는 전부를 식별할 수 있습니다.

**읽기 전용 정적 콘텐츠**

애플리케이션에서 현재 정적 콘텐츠를 제공하는 경우 이를 대체할 위치가 필요합니다. 정적 콘텐츠를 Azure Blob Storage로 이동하고 전역적으로 빠른 다운로드를 위해 [Azure CDN(Content Delivery Network)](../../../cdn/index.yml)을 추가하는 것이 좋습니다. 자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](../../../storage/blobs/storage-blob-static-website.md) 및 [빠른 시작: Azure CDN과 Azure Storage 계정 통합](../../../cdn/cdn-create-a-storage-account-with-cdn.md)을 참조하세요.

**동적으로 게시된 정적 콘텐츠**

애플리케이션이 애플리케이션에서 업로드/생성되었지만 생성 후 변경할 수 없는 정적 콘텐츠를 허용하는 경우, 위에서 설명한 대로 [Azure Blob Storage](../../../storage/blobs/index.yml) 및 Azure CDN과 [Azure Function](../../../azure-functions/index.yml)을 사용하여 업로드 및 CDN 새로 고침을 처리할 수 있습니다. [Azure Functions를 사용하여 정적 콘텐츠 업로드 및 CDN 사전 로드](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn)에서 사용할 샘플 구현을 제공했습니다.

**동적 또는 내부 콘텐츠**

애플리케이션에서 자주 쓰고 읽는 파일(예: 임시 데이터 파일) 또는 애플리케이션에만 표시되는 정적 파일의 경우 [Azure Storage](../../../storage/index.yml) 공유를 영구 볼륨으로 탑재할 수 있습니다. 자세한 내용은 [Azure Kubernetes Service에서 Azure Files를 사용하여 영구 볼륨을 동적으로 만들어 사용](../../../aks/azure-files-dynamic-pv.md)을 참조하세요.

### <a name="determine-whether-a-connection-to-on-premises-is-needed"></a>온-프레미스 연결이 필요한지 확인

애플리케이션에서 온-프레미스 서비스에 액세스해야 하는 경우 Azure의 연결 서비스 중 하나를 프로비저닝해야 합니다. 자세한 내용은 [온-프레미스 네트워크를 Azure에 연결](/azure/architecture/reference-architectures/hybrid-networking/)을 참조하세요. 또는 온-프레미스 리소스에서 노출하는 공개적으로 사용 가능한 API를 사용하도록 애플리케이션을 리팩터링해야 합니다.

### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>JMS 큐 또는 토픽을 사용 중인지 확인

애플리케이션에서 JMS 큐 또는 토픽을 사용하는 경우 외부에서 호스팅되는 JMS 서버로 마이그레이션해야 합니다. Azure Service Bus 및 AMQP(고급 메시지 큐 프로토콜)는 JMS를 사용하는 애플리케이션에 매우 유용한 마이그레이션 전략이 될 수 있습니다. 자세한 내용은 [Azure Service Bus 및 AMQP 1.0에서 JMS 사용](../../../service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp.md) 또는 [Azure Service Bus 큐에서 메시지 보내기 및 받기(Java)](../../../service-bus-messaging/service-bus-java-how-to-use-queues.md)를 참조하세요.

JMS 영구 저장소가 구성된 경우 해당 구성을 캡처하여 마이그레이션 후에 적용해야 합니다.

### <a name="determine-whether-your-application-is-composed-of-multiple-wars"></a>애플리케이션이 여러 WAR로 구성되었는지 확인

애플리케이션이 여러 WAR로 구성된 경우 각 WAR를 별도의 애플리케이션으로 처리하고 각각에 대해 이 가이드를 수행해야 합니다.

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>애플리케이션이 EAR로 패키징되는지 확인

애플리케이션이 EAR 파일로 패키지되는 경우 application.xml 파일을 검사하고 해당 구성을 캡처합니다.

### <a name="identify-all-outside-processes-and-daemons-running-on-the-production-servers"></a>프로덕션 서버에서 실행되는 모든 외부 프로세스 및 디먼 확인

디먼 모니터링과 같이 애플리케이션 서버 외부에서 실행되는 프로세스가 있는 경우 이러한 프로세스를 제거하거나 다른 곳으로 마이그레이션해야 합니다.


## <a name="migration"></a>마이그레이션

### <a name="provision-the-target-infrastructure"></a>대상 인프라 프로비전

마이그레이션을 시작하려면 먼저 JBoss EAP 인프라를 배포해야 합니다. 여러 배포 옵션이 있습니다.

- [**Azure Virtual Machine**](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)
- [**Azure Virtual Machine Scale Set**](../../../virtual-machine-scale-sets/overview.md)
- [**Azure App Service**](/azure/developer/java/ee/jboss-on-azure)
- [**컨테이너용 Azure Red Hat OpenShift(ARO)**](https://azure.microsoft.com/services/openshift)
- [**Azure Container Service**](https://azure.microsoft.com/product-categories/containers/)

환경을 빌드하기 전에 배포 인프라를 평가하려면 Azure Marketplace 시작하기 섹션을 참조하세요.

### <a name="perform-the-migration"></a>마이그레이션 수행

마이그레이션에 도움이 될 수 있는 도구가 있습니다.

* [마이그레이션을 위해 애플리케이션을 분석하는 Red Hat Application Migration Toolkit](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#use_windup_to_analyze_applications_for_migration)
* [서버 구성을 마이그레이션하기 위한 JBoss Server Migration Tool](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migration_tool_server_migration_tool)

이전 JBoss EAP 버전에서 최신 JBoss EAP 버전으로 서버 구성을 마이그레이션하려면 [JBoss Server Migration Tool](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migrate_server_migration_tool_option)을 사용하거나 [관리 CLI 마이그레이션 작업](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migrate__migrate_operation_option)을 사용하여 수동 마이그레이션을 수행할 수 있습니다.

### <a name="run-red-hat-application-migration-toolkit"></a>Red Hat Application Migration Toolkit 실행

[대화형 모드에서 JBoss Server Migration Tool을 실행](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/using_the_jboss_server_migration_tool/index#migration_tool_server_run_interactive_mode)할 수 있습니다. 기본적으로 대화형 모드에서 JBoss Server Migration Tool을 실행할 수 있습니다. 이 모드를 사용하면 마이그레이션할 서버 구성을 정확히 선택할 수 있습니다.

또한 [비대화형 모드에서 JBoss Server Migration Tool을 실행](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/using_the_jboss_server_migration_tool/running_the_server_migration_tool#migration_tool_server_run_noninteractive_mode)할 수 있습니다. 이 모드에서는 프롬프트 없이 실행할 수 있습니다.

### <a name="review-the-result-of-jboss-server-migration-toolkit-execution"></a>JBoss Server Migration Toolkit 실행의 결과 검토

마이그레이션이 완료되면 *EAP_HOME/standalone/configuration/* 및 *EAP_HOME/domain/configuration/* 디렉터리에서 마이그레이션된 서버 구성 파일을 검토합니다. 자세한 내용은 [JBoss Server Migration Tool 실행 결과 검토](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/using_the_jboss_server_migration_tool/running_the_server_migration_tool#migration_tool_server_results)를 참조하세요.

### <a name="expose-the-application"></a>애플리케이션 공개

환경에 적합한 방법을 사용하여 애플리케이션을 노출할 수 있습니다.

* [공용 IP를 생성](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)하여 서버 및 애플리케이션에 액세스합니다.
* 동일한 VNet의 다른 서브넷(새 서브넷)에 있는 [동일한 VNet(Virtual Network)에 점프 VM을 생성](../../windows/quick-create-portal.md#create-virtual-machine)하고 점프 VM을 통해 서버에 액세스합니다. 이 점프 VM을 사용하여 애플리케이션을 노출할 수 있습니다.
* 다른 Virtual Network에서 [VNet 피어링을 사용하여 점프 VM을 생성](../../windows/quick-create-portal.md#create-virtual-machine)하고 [가상 네트워크 피어링](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks)을 사용하여 서버에 액세스하고 애플리케이션을 노출합니다.
* [Application Gateway](../../../application-gateway/quick-create-portal.md#create-an-application-gateway)를 사용하여 애플리케이션 노출
* ELB([외부 부하 분산 장치](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources))를 사용하여 애플리케이션을 노출합니다.

## <a name="post-migration"></a>마이그레이션 후 작업

사전 마이그레이션 단계에서 정의한 마이그레이션 목표에 도달한 후에는 엔드투엔드 수용 테스트를 수행하여 모든 것이 예상대로 작동하는지 확인해야 합니다. 다음은 마이그레이션 후 기능 향상에 대한 토픽 중 일부이며, 이 외에도 다른 토픽이 더 있습니다.

* Azure Storage를 사용하여 VM에 탑재된 정적 콘텐츠 제공. 자세한 내용은 [데이터 디스크를 VM에 연결 또는 분리](../../../devtest-labs/devtest-lab-attach-detach-data-disk.md)를 참조하세요.
* Azure DevOps를 사용하여 마이그레이션된 JBoss 클러스터에 애플리케이션 배포. 자세한 내용은 [Azure DevOps 시작 설명서](/azure/devops/get-started/?view=azure-devops)를 참조하세요.
* [Application Gateway](../../../application-gateway/index.yml) 사용 고려.
* 고급 부하 분산 서비스를 사용하여 네트워크 토폴로지 향상. 자세한 내용은 [Azure에서 부하 분산 서비스 사용](../../../traffic-manager/traffic-manager-load-balancing-azure.md)을 참조하세요.
* Azure 관리 ID를 관리형 비밀에 활용하고 Azure 리소스에 대한 RBAC(역할 기반 액세스 제어) 할당. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.
* Azure Key Vault를 사용하여 "비밀"로 작동하는 모든 정보 저장. 자세한 내용은 [Azure Key Vault 기본 개념](../../../key-vault/general/basic-concepts.md)을 참조하세요.

## <a name="resource-links-and-support"></a>리소스 링크 및 지원

지원, 문제 또는 사용자 지정 요구 사항과 관련된 지원은 [Red Hat 지원](https://access.redhat.com/support) 또는 [Microsoft Azure 지원](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

* [JBoss EAP](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/getting_started_with_jboss_eap_for_openshift_online/introduction)에 대해 자세히 알아보기
* [Red Hat Subscription Manager(Cloud Access)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)에 대해 자세히 알아보기
* [Azure Virtual Machines](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)에 대해 자세히 알아보기
* [Azure Virtual Machine Scale Set](../../../virtual-machine-scale-sets/overview.md)에 대해 자세히 알아보기
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)에 대해 자세히 알아보기
* [Azure App Service on Linux](../../../app-service/overview.md#app-service-on-linux)에 대해 자세히 알아보기
* [Azure Storage](../../../storage/common/storage-introduction.md)에 대해 자세히 알아보기
* [Azure 네트워킹](../../../networking/fundamentals/networking-overview.md)에 대해 자세히 알아보기

## <a name="next-steps"></a>다음 단계
* [Azure Marketplace에서 RHEL 기반 VM/가상 머신 확장 집합에 JBoss EAP 배포](https://aka.ms/AMP-JBoss-EAP)
* [Azure App Service용 Java 앱 구성](../../../app-service/configure-language-java.md)
* [JBoss EAP를 Azure App Service에 배포하는 방법](https://github.com/JasonFreeberg/jboss-on-app-service) 자습서
* [Azure App Service Migration Assistance 사용](https://azure.microsoft.com/services/app-service/migration-assistant/)
* [Red Hat Migration Toolkit for Applications 사용](https://developers.redhat.com/products/mta)

---
title: Azure Red Hat Enterprise Linux VM(Virtual Machine) 및 가상 머신 확장 집합 기반 Red Hat JBoss EAP의 Azure Marketplace 제품
description: Azure Marketplace 제품을 사용하여 Azure RHEL(Red Hat Enterprise Linux) VM 및 가상 머신 확장 집합 기반 Red Hat JBoss EAP를 배포하는 방법입니다.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 05/25/2021
ms.openlocfilehash: 6682c5301861732189d532641060e136250957b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261484"
---
# <a name="deploy-red-hat-jboss-enterprise-platform-eap-on-azure-vms-and-virtual-machine-scale-sets-using-the-azure-marketplace-offer"></a>Azure Marketplace 제품을 사용하여 Azure VM 및 가상 머신 확장 집합 기반 Red Hat JBoss EAP(Enterprise Platform) 배포

Azure [RHEL(Red Hat Enterprise Linux)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) 기반 [Red Hat JBoss Enterprise Application Platform](https://www.redhat.com/en/technologies/jboss-middleware/application-platform)의 Azure Marketplace 제품은 [Red Hat](https://www.redhat.com/)과 Microsoft의 공동 솔루션입니다. Red Hat은 [Java](https://www.java.com/) 표준, [OpenJDK](https://openjdk.java.net/), [MicroProfile](https://microprofile.io/), [Jakarta EE](https://jakarta.ee/) 및 [Quarkus](https://quarkus.io/)를 비롯한 최고의 오픈 소스 솔루션 공급자이자 기여자입니다. JBoss EAP는 웹 프로필과 전체 플랫폼 모두에서 Java EE Certified 및 Jakarta EE Compliant인 최고의 Java 애플리케이션 서버 플랫폼입니다. 모든 JBoss EAP 릴리스는 다양한 업계 최고의 운영 체제, JVM(Java Virtual Machines) 및 데이터베이스 조합에서 테스트되고 지원됩니다.  JBoss EAP 및 RHEL에는 모든 환경에서 엔터프라이즈 Java 애플리케이션을 빌드, 실행, 배포 및 관리하는 데 필요한 모든 것이 포함되어 있습니다.  온-프레미스, 가상 환경 및 프라이빗, 퍼블릭 또는 하이브리드 클라우드가 포함되어 있습니다. Red Hat과 Microsoft의 공동 솔루션에는 통합 지원 및 소프트웨어 라이선스 유연성이 포함되어 있습니다.

## <a name="jboss-eap-on-azure-integrated-support"></a>Azure의 JBoss EAP 통합 지원

Azure Marketplace 제품의 JBoss EAP는 Red Hat과 Microsoft의 공동 솔루션이며 통합 지원 및 소프트웨어 라이선스 유연성이 포함되어 있습니다. Microsoft와 Red Hat 모두에 연결하여 지원 티켓을 제출할 수 있습니다.  공동으로 이슈를 공유하고 해결하므로 공급업체마다 티켓을 여러 개 제출할 필요는 없습니다. 통합 지원은 모든 Azure 인프라 및 Red Hat 애플리케이션 서버 수준 지원 이슈를 다룹니다.    

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정 - Azure 구독이 없는 경우 [Visual Studio Subscription 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)(이전에는 MSDN)을 활성화하거나 [무료로 계정을 만들](https://azure.microsoft.com/pricing/free-trial) 수 있습니다.

* JBoss EAP 설치- JBoss EAP에 대한 RHSM(Red Hat Subscription Management) 자격이 있는 Red Hat 계정이 있어야 합니다. 이 자격을 통해 Red Hat에서 테스트되고 인증된 JBoss EAP 버전을 다운로드할 수 있습니다.  EAP 자격이 없으면 [Red Hat 개인 개발자 구독](https://developers.redhat.com/register)을 통해 무료 개발자 구독에 등록합니다. 등록되면 [Red Hat 고객 포털](https://access.redhat.com/management/)에서 필요한 자격 증명(풀 ID)을 찾을 수 있습니다.

* RHEL 옵션 - PAYG(종량제) 또는 BYOS(Bring-Your-Own-Subscription) 중에서 선택합니다. BYOS를 사용하는 경우 솔루션 템플릿을 사용하여 Marketplace 제품을 배포하기 전에 [Red Hat Cloud Access](https://access.redhat.com/) [RHEL Gold Image](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/)를 활성화해야 합니다. [다음 지침](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)에 따라 RHEL Gold Image를 Microsoft Azure에서 사용할 수 있도록 설정합니다.

* Azure CLI(명령줄 인터페이스).

## <a name="azure-marketplace-offer-subscription-options"></a>Azure Marketplace 제품 구독 옵션

RHEL 기반 JBoss EAP의 Azure Marketplace 제품은 Azure VM/가상 머신 확장 집합 배포를 10분 이내에 설치하고 프로비전합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/)에서 이러한 제품에 액세스할 수 있습니다.

Marketplace 제품에는 요구 사항을 지원하도록 EAP 및 RHEL 버전의 다양한 조합이 포함됩니다. JBoss EAP는 항상 BYOS이지만 RHEL OS(운영 체제)의 경우 BYOS 또는 PAYG 중에 선택할 수 있습니다. 제품에는 RHEL 기반 JBoss EAP를 독립 실행형, 클러스터형 VM 또는 클러스터형 가상 머신 확장 집합으로 사용할 수 있는 플랜 구성 옵션이 포함되어 있습니다. 

사용 가능한 계획에는 다음과 같은 6가지가 있습니다. 

- RHEL 8.3 독립 실행형 VM 기반 Boss EAP 7.3(PAYG)
- RHEL 8.3 독립 실행형 VM 기반 JBoss EAP 7.3(BYOS)
- RHEL 8.3 클러스터형 VM 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 클러스터형 VM 기반 JBoss EAP 7.3(BYOS)
- RHEL 8.3 클러스터형 가상 머신 확장 집합 기반 JBoss EAP 7.3(PAYG)
- RHEL 8.3 클러스터형 가상 머신 확장 집합 기반 JBoss EAP 7.3(BYOS)

## <a name="using-rhel-os-with-payg-model"></a>PAYG 모델에서 RHEL OS 사용

Azure Marketplace 제품을 사용하면 RHEL을 주문형 PAYG VM/가상 머신 확장 집합으로 배포할 수 있습니다. PAYG 플랜에는 일반적인 Azure 인프라 컴퓨팅, 네트워크 및 스토리지 비용을 기반으로 하는 추가 시간당 RHEL 구독 요금이 청구됩니다.

PAYG 모델에 대한 자세한 내용은 [Red Hat Enterprise Linux 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)을 참조하세요. PAYG 모델에서 RHEL을 사용하려면 Azure 구독이 필요합니다. ([Azure Marketplace 기반 RHEL](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.rhel-20190605) 제품을 사용하려면 Azure 구독에서 결제 방법을 지정해야 합니다.)

## <a name="using-rhel-os-with-byos-model"></a>BYOS 모델에서 RHEL OS 사용

RHEL을 BYOS VM/가상 머신 확장 집합으로 사용하려면 Azure에서 RHEL을 사용할 수 있는 자격이 있는 유효한 Red Hat 구독이 있어야 합니다. 이러한 RHEL BYOS 플랜 기반 JBoss EAP를 [Azure 프라이빗 제품](../../../marketplace/private-offers.md)으로 사용할 수 있습니다. Azure Marketplace에서 RHEL BYOS 제품 플랜을 배포하려면 다음 필수 구성을 완료해야 합니다. 

1. RHEL OS 및 JBoss EAP 자격이 Red Hat 구독에 연결되어 있는지 확인합니다.
2. RHEL BYOS 이미지를 사용할 수 있는 권한을 Azure 구독 ID에 부여합니다. [RHSM(Red Hat Subscription Management) 설명서](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)에 따라 다음 단계가 포함된 프로세스를 완료합니다.
    1. Red Hat Cloud Access 대시보드에서 Microsoft Azure를 공급자로 사용하도록 설정합니다.
    2. Azure 구독 ID를 추가합니다.
    3. Microsoft Azure에서 Cloud Access에 대한 새 제품을 사용하도록 설정합니다.
    4. Azure 구독에 대한 Red Hat Gold Images를 활성화합니다. 자세한 내용은 [Cloud Access에 대한 구독 사용 및 유지 관리](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#using-gold-images-on-azure_cloud-access#using-gold-images-on-azure_cloud-access) 챕터를 참조하세요. 
    5. Azure 구독에서 Red Hat Gold Images를 사용할 수 있을 때까지 기다립니다. 이러한 골드 이미지는 일반적으로 제출 후 3시간 이내에 Azure 프라이빗 제품으로 제공됩니다.

3. RHEL BYOS 이미지의 Azure Marketplace 사용 약관에 동의합니다. 동의하려면 다음과 같이 [Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli) 명령을 실행합니다. 자세한 내용은 [Azure의 RHEL BYOS Gold Images](./byos.md) 설명서를 참조하세요. 최신 Azure CLI 버전을 실행해야 합니다.
    1. Azure CLI 세션을 시작하고 Azure 계정으로 인증합니다. 도움이 필요하면 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요. 계속 진행하기 전에 최신 Azure CLI 버전을 실행하고 있는지 확인하세요.
    2. 다음 CLI 명령을 실행하여 구독에서 RHEL BYOS 플랜을 사용할 수 있는지 확인합니다. 여기에 결과가 없는 경우 #2단계를 참조하세요. Azure 구독이 RHEL BYOS 플랜 기반 JBoss EAP에 대한 자격으로 활성화되었는지 확인합니다.

    ```cli
    az vm image list --offer rhel-byos --all #use this command to verify the availability of RHEL BYOS images
    ```

    3. 다음 명령을 실행하여 RHEL BYOS 플랜 기반 JBoss EAP에 필요한 대로 Azure Marketplace T&C를 수락합니다.

    ```cli
    az vm image terms accept --publisher redhat --offer jboss-eap-rhel --plan $PLANID
    ```

    4. 여기서 `$PLANID`는 다음 중 하나입니다(사용하려는 각 Marketplace 제품 플랜에 대해서는 #3단계 반복).

    ```cli
    jboss-eap-73-byos-rhel-80-byos
    jboss-eap-73-byos-rhel-8-byos-clusteredvm
    jboss-eap-73-byos-rhel-80-byos-vmss
    jboss-eap-73-byos-rhel-80-payg
    jboss-eap-73-byos-rhel-8-payg-clusteredvm
    jboss-eap-73-byos-rhel-80-payg-vmss
    ```

4. 이제 구독에서 RHEL BYOS 플랜 기반 EAP를 배포할 수 있습니다. 배포하는 동안 구독은 배포 중에 제공된 자격 증명과 함께 `subscription-manager`를 사용하여 자동으로 연결됩니다.

## <a name="using-jboss-eap-with-byos-model"></a>BYOS 모델에서 JBoss EAP 사용

JBoss EAP는 BYOS 모델을 통해서만 Azure에 제공됩니다. RHEL 플랜 기반 JBoss EAP를 배포할 때 유효한 EAP 자격을 사용하여 RHSM 자격 증명을 RHSM 풀 ID와 함께 제공해야 합니다. EAP 자격이 없으면 [개인용 Red Hat 개발자 구독](https://developers.redhat.com/register)을 가져옵니다. 등록하면 구독 탐색 메뉴에서 필요한 자격 증명(풀 ID)을 찾을 수 있습니다. 배포하는 동안 구독은 배포 중에 제공된 자격 증명과 함께 `subscription-manager`를 사용하여 자동으로 연결됩니다.

## <a name="template-solution-architectures"></a>템플릿 솔루션 아키텍처

이러한 제품 플랜은 RHEL 기반 JBoss EAP 설정을 실행하는 모든 Azure 컴퓨팅 리소스를 만듭니다. 템플릿 솔루션에서 다음 리소스를 만듭니다.

- RHEL VM
- Load Balancer 하나(클러스터형 구성의 경우)
- 단일 서브넷이 있는 Virtual Network
- RHEL VM/가상 머신 확장 집합(독립 실행형 또는 클러스터형)에서 JBoss EAP 설정
- 샘플 Java 애플리케이션
- 스토리지 계정!

## <a name="after-a-successful-deployment"></a>성공적으로 배포한 후

1. 다른 Virtual Network에서 [VNet 피어링을 사용하여 점프 VM을 생성](../../windows/quick-create-portal.md#create-virtual-machine)하고 [가상 네트워크 피어링](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks)을 사용하여 서버에 액세스하고 애플리케이션을 노출합니다.
2. [공용 IP를 만들어](../../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address) 서버와 애플리케이션에 액세스합니다.
3. 동일한 VNet의 다른 서브넷(새 서브넷)에 있는 [동일한VNet(Virtual Network)에 점프 VM을 생성](../../windows/quick-create-portal.md#create-virtual-machine)하고 점프 VM을 통해 서버에 액세스합니다. 점프 VM을 사용하여 애플리케이션을 노출할 수 있습니다.
4. [Application Gateway](../../../application-gateway/quick-create-portal.md#create-an-application-gateway)를 사용하여 애플리케이션을 노출합니다.
5. ELB(외부 부하 분산 장치)를 사용하여 애플리케이션을 노출합니다.
6. [Azure Bastion을 사용](../../../bastion/bastion-overview.md)하여 브라우저와 Azure Portal을 통해 RHEL VM에 액세스합니다. 

### <a name="1-create-a-jump-vm-in-a-different-virtual-network-and-access-the-rhel-vm-using-virtual-network-peering-recommended-method"></a>1. 다른 Virtual Network에서 점프 VM을 만들고 가상 네트워크 피어링을 사용하여 RHEL VM에 액세스(권장 방법)

1. [Windows Virtual Machine 만들기](../../windows/quick-create-portal.md#create-virtual-machine) - 새 Azure 리소스 그룹에서 Windows VM을 만듭니다. 이 VM은 RHEL VM과 동일한 지역에 있어야 합니다. 필요한 세부 정보를 제공하고 새 Virtual Network에 점프 VM을 만들 때 다른 구성을 기본값으로 둡니다.
2. [가상 네트워크 피어링](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) - 피어링은 RHEL VM을 점프 VM과 연결하는 방법입니다. Virtual Network 피어링이 성공하면 두 VM이 서로 통신할 수 있습니다.
3. 점프 VM 세부 정보 페이지로 이동하여 공용 IP를 복사합니다. 공용 IP를 사용하여 점프 VM에 로그인합니다.
4. 출력 페이지에서 RHEL VM의 개인 IP를 복사하고 이를 사용하여 점프 VM에서 RHEL VM에 로그인합니다.
5. 출력 페이지에서 복사한 앱 URL을 점프 VM 내부의 브라우저에 붙여넣습니다. 이 브라우저에서 Azure 기반 JBoss EAP 웹 페이지를 봅니다.
6. JBoss EAP 관리 콘솔에 액세스 - 출력 페이지에서 복사한 관리 콘솔 URL을 점프 VM 내 브라우저에 붙여넣고 로그인할 JBoss EAP 사용자 이름과 암호를 입력합니다.

### <a name="2-create-a-public-ip-to-access-the-rhel-vm-and-jboss-eap-admin-console"></a>2. RHEL VM 및 JBoss EAP 관리 콘솔에 액세스하는 공용 IP 만들기

1. 만든 RHEL VM에 공용 IP가 연결되어 있지 않습니다. VM에 액세스할 수 있는 [공용 IP를 만들고](../../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address) [공용 IP를 VM에 연결](../../../virtual-network/ip-services/associate-public-ip-address-vm.md)할 수 있습니다. Azure Portal 또는 [Azure PowerShell](/powershell/) 명령이나 [Azure CLI](/cli/azure/install-azure-cli) 명령을 사용하여 공용 IP를 만들 수 있습니다.
2. VM의 공용 IP 가져오기 - VM 세부 정보 페이지로 이동하여 공용 IP를 복사합니다. 공용 IP를 사용하여 VM과 JBoss EAP 관리 콘솔에 액세스합니다.
3. Azure 기반 JBoss EAP 웹 페이지 보기 - 웹 브라우저를 열고 *http://<PUBLIC_HOSTNAME>:8080/* 으로 이동합니다. 그러면 기본 EAP 시작 페이지가 표시되어야 합니다.
4. JBoss EAP 관리 콘솔에 로그인 - 웹 브라우저를 열고 *http://<PUBLIC_HOSTNAME>:9990* 으로 이동합니다. 로그인할 JBoss EAP 사용자 이름과 암호를 입력합니다.

### <a name="3-create-a-jump-vm-in-a-different-subnet-new-subnet-in-the-same-vnet-and-access-the-rhel-vm-via-a-jump-vm"></a>3. 동일한 VNet의 다른 서브넷(새 서브넷)에 점프 VM을 만들고 점프 VM을 통해 RHEL VM에 액세스합니다.

1. RHEL VM이 포함된 기존 Virtual Network에 [새 서브넷을 추가](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)합니다.
2. RHEL VM과 동일한 RG(리소스 그룹)의 Azure에 [Windows Virtual Machine을 만듭니다](../../windows/quick-create-portal.md#create-virtual-machine). 필요한 세부 정보를 제공하고 다른 구성(VNet 및 서브넷 제외)을 기본값으로 둡니다. 점프 VM이 되도록 RG에서 기존 VNet을 선택하고 위 단계에서 만든 서브넷을 선택했는지 확인합니다.
3. 점프 VM 공용 IP 액세스 - 성공적으로 배포되면 VM 세부 정보 페이지로 이동하여 공용 IP를 복사합니다. 공용 IP를 사용하여 점프 VM에 로그인합니다.
4. RHEL VM에 로그인 - 출력 페이지에서 RHEL VM의 개인 IP를 복사하고 이를 사용하여 점프 VM에서 RHEL VM에 로그인합니다.
5. JBoss EAP 시작 페이지에 액세스 - 점프 VM에서 브라우저를 열고 배포의 출력 페이지에서 복사한 앱 URL을 붙여넣습니다.
6. JBoss EAP 관리 콘솔에 액세스 - 출력 페이지에서 복사한 관리 콘솔 URL을 점프 VM 내 브라우저에 붙여넣어 JBoss EAP 관리 콘솔에 액세스하고 로그인할 JBoss EAP 사용자 이름과 암호를 입력합니다.

### <a name="4-expose-the-application-using-an-external-load-balancer"></a>4. 외부 Load Balancer를 사용하여 애플리케이션 노출

1. [Application Gateway 만들기](../../../application-gateway/quick-create-portal.md#create-an-application-gateway) - RHEL VM의 포트에 액세스하고 다른 서브넷에 Application Gateway를 만듭니다. 서브넷에는 Application Gateway만 포함되어야 합니다.
2. *프런트 엔드* 매개 변수 설정 - 공용 IP 또는 둘 다 선택하고 필요한 세부 정보를 제공했는지 확인합니다. *백 엔드* 섹션에서 **백 엔드 풀 추가** 옵션을 선택하고 RHEL VM을 Application Gateway의 백 엔드 풀에 추가합니다.
3. 액세스 포트 설정 - *구성* 섹션에서 라우팅 규칙을 추가하여 RHEL VM의 포트 8080 및 9990에 액세스합니다.
4. Application Gateway 공용 IP 복사 - 필요한 구성을 사용하여 Application Gateway를 만든 후 개요 페이지로 이동하여 Application Gateway의 공용 IP를 복사합니다.
5. Azure 기반 JBoss EAP 웹 페이지 보기 - 웹 브라우저를 연 다음, *http://<PUBLIC_IP_AppGateway>:8080* 으로 이동합니다. 그러면 기본 EAP 시작 페이지가 표시되어야 합니다.
6. JBoss EAP 관리 콘솔에 로그인하기 - 웹 브라우저를 연 다음, *http://<PUBLIC_IP_AppGateway>:9990* 으로 이동합니다. 로그인할 JBoss EAP 사용자 이름과 암호를 입력합니다.

### <a name="5-use-an-external-load-balancer-elb-to-access-your-rhel-vmvirtual-machine-scale-sets"></a>5. ELB(외부 Load Balancer)를 사용하여 RHEL VM/가상 머신 확장 집합에 액세스

1. [Load Balancer를 만들어](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) RHEL VM 포트에 액세스합니다. 필요한 세부 정보를 제공하여 외부 Load Balancer를 배포하고 기타 구성을 기본값으로 둡니다. ELB 구성의 경우 SKU를 기본으로 둡니다.
2. Load Balancer 규칙 추가 - 부하 분산 장치가 성공적으로 생성되면 [Load Balancer 리소스를 만든](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) 다음, Load Balancer 규칙을 추가하여 RHEL VM의 포트 8080 및 9990에 액세스합니다.
3. RHEL VM을 Load Balancer 백 엔드 풀에 추가 - 설정 섹션에서 *백 엔드 풀* 을 클릭한 다음, 위 단계에서 만든 백 엔드 풀을 선택합니다. *연결 대상* 옵션에 해당하는 VM을 선택한 다음, RHEL VM을 추가합니다.
4. Load Balancer 공용 IP 가져오기 - Load Balancer 개요 페이지로 이동하여 Load Balancer의 공용 IP를 복사합니다.
5. Azure 기반 JBoss EAP 웹 페이지 보기 - 웹 브라우저를 연 다음, *http://<PUBLIC_IP_LoadBalancer>:8080* 으로 이동합니다. 그러면 기본 EAP 시작 페이지가 표시되어야 합니다.
6. JBoss EAP 관리 콘솔에 로그인하기 - 웹 브라우저를 연 다음, *http://<PUBLIC_IP_LoadBalancer>:9990* 으로 이동합니다. 로그인할 JBoss EAP 사용자 이름과 암호를 입력합니다.

### <a name="6-use-azure-bastion-to-connect"></a>6. Azure Bastion을 사용하여 연결 

1. RHEL VM이 있는 VNet의 Azure Bastion 호스트를 만듭니다. Bastion 서비스는 SSH를 사용하여 RHEL VM에 자동으로 연결합니다.
2. VM, VM의 개인 IP가 있는 NIC 및 Azure Bastion 리소스에서 Reader 역할을 만듭니다.
3. SSH(22)의 RHEL 인바운드 포트가 열려 있습니다. 
4. Azure Portal에서 사용자 이름과 암호를 사용하여 연결합니다. "연결"을 클릭하고 드롭다운에서 "Bastion"을 선택합니다. 그런 다음, "연결"을 선택하여 RHEL VM에 연결합니다. 프라이빗 키 파일이나 Azure Key Vault에 저장된 프라이빗 키를 사용하여 연결할 수 있습니다.

## <a name="azure-platform"></a>Azure 플랫폼

- **유효성 검사 실패** - 매개 변수 조건이 충족되지 않는 경우(예: 관리자 암호 조건이 충족되지 않음) 또는 매개 변수 섹션에서 필수 매개 변수가 제공되지 않은 경우에는 배포가 시작되지 않습니다. *만들기* 를 클릭하기 전에 매개 변수 세부 정보를 검토할 수 있습니다.
- **리소스 프로비저닝 실패** - 배포가 시작되면 배포 중인 리소스가 배포 페이지에 표시됩니다. 매개 변수 유효성 검사 프로세스 후 배포가 실패하면 보다 자세한 오류 메시지가 제공됩니다. 
- **VM 프로비저닝 실패** - **VM 사용자 지정 스크립트 확장** 리소스 지점에서 배포가 실패하면 VM 로그 파일에서 보다 자세한 오류 메시지가 제공됩니다. 문제 해결에 대한 자세한 내용은 다음 섹션을 참조하세요.

## <a name="troubleshooting-eap-deployment-extension"></a>EAP 배포 확장 문제 해결

이러한 제품은 VM 사용자 지정 스크립트 확장을 사용하여 JBoss EAP를 배포하고 JBoss EAP 관리 사용자를 구성합니다. 다음과 같은 여러 가지 이유로 인해 배포가 실패할 수 있습니다.

- 잘못된 RHSM 또는 EAP 자격
- 잘못된 JBoss EAP 또는 RHEL OS 자격 풀 ID
- Azure Marketplace T&C가 RHEL VM에 허용되지 않음

이슈를 해결하려면 다음 단계를 수행합니다.

1. SSH를 통해 프로비전된 가상 머신에 로그인합니다. Azure Portal VM 개요 페이지에서 VM의 공용 IP를 검색할 수 있습니다.
1. 루트 사용자로 전환

    ```cli
    sudo su -
    ```

1. 메시지가 표시되면 VM 관리자 암호를 입력합니다.
1. 디렉터리를 로깅 디렉터리로 변경합니다.

    ```cli
    cd /var/lib/waagent/custom-script/download/0 #for EAP on RHEL stand-alone VM
    ```

    ```cli
    cd /var/lib/waagent/custom-script/download/1 #for EAP on RHEL clustered VM
    ```

1. eap.log 로그 파일의 로그를 검토합니다.

    ```cli
    more eap.log
    ```

로그 파일에는 배포 실패 이유와 가능한 해결 방법이 포함된 세부 정보가 있습니다. RHSM 계정이나 자격으로 인해 배포가 실패한 경우 '지원 및 구독 참고 사항' 섹션을 참조하여 필수 구성 요소를 완료합니다. 그런 다음, 다시 시도하세요. RHEL 클러스터형 플랜에 EAP를 배포할 때 배포가 할당량 한도에 도달하지 않았는지 확인합니다. 배포에 인스턴스 수를 제공하기 전에 지역 vCPU 및 VM 시리즈 vCPU 할당량을 확인해야 합니다. 구독이나 지역의 할당량 한도가 부족하면 Azure Portal에서 [할당량을 요청](../../../azure-portal/supportability/regional-quota-requests.md)합니다.

VM 사용자 지정 스크립트 확장 문제 해결에 대한 자세한 내용은 [Linux VM에서 Azure 사용자 지정 스크립트 확장 버전 2 사용](../../extensions/custom-script-linux.md)을 참조하세요.

## <a name="resource-links-and-support"></a>리소스 링크 및 지원

지원, 문제 또는 사용자 지정 요구 사항과 관련된 지원은 [Red Hat 지원](https://access.redhat.com/support) 또는 [Microsoft Azure 지원](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

* [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/)에 대해 자세히 알아보기
* [Azure Red Hat OpenShift 기반 JBoss EAP](https://azure.microsoft.com/services/openshift/)
* [Azure App Service의 JBoss EAP](/azure/developer/java/ee/jboss-on-azure) 
* [Azure 하이브리드 혜택](../../windows/hybrid-use-benefit-licensing.md)
* [Red Hat 구독 관리](https://access.redhat.com/products/red-hat-subscription-management)
* [Azure의 Red Hat에 대한 Microsoft 문서](./overview.md)

## <a name="next-steps"></a>다음 단계

* [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)에서 RHEL VM/Virtual Machine Scale Sets 기반 JBoss EAP 배포
* [Azure 빠른 시작](https://aka.ms/Quickstart-JBoss-EAP)에서 RHEL VM/Virtual Machine Scale Sets 기반 JBoss EAP 배포
* [Azure App Service](../../../app-service/configure-language-java.md)용 Java 앱 구성
* [JBoss EAP를 Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)에 배포하는 방법 자습서
* Azure [App Service Migration Assistance](https://azure.microsoft.com/services/app-service/migration-assistant/) 사용
* Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta) 사용
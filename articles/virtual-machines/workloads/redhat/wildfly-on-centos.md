---
title: 빠른 시작 - CentOS 기반 WildFly
description: Java 애플리케이션을 CentOS VM 기반 WildFly에 배포합니다.
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom: mode-other
ms.collection: linux
ms.openlocfilehash: 50338148bb17b0045d9ed64da535b07f62a18f05
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056829"
---
# <a name="quickstart-wildfly-on-centos-8"></a>빠른 시작: CentOS 8 기반 WildFly

**적용 대상:** :heavy_check_mark: Linux VM 

이 빠른 시작에서는 CentOS 8 VM의 WildFly 독립 실행형 노드를 배포하는 방법을 보여 줍니다. 이는 Azure에서 엔터프라이즈 Java 애플리케이션을 개발하고 테스트하는 데 적합합니다. 애플리케이션 서버 구독은 이 빠른 시작을 배포하는 데 필요하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. Azure 구독이 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [체험 계정](https://azure.microsoft.com/pricing/free-trial)을 만들 수 있습니다.

## <a name="use-case"></a>사용 사례

WildFly는 Azure에서 엔터프라이즈 Java 애플리케이션을 개발하고 테스트하는 데 적합합니다. WildFly 18 서버 구성 프로필에서 사용할 수 있는 기술 목록은 [WildFly 시작 가이드](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)에서 제공됩니다.

WildFly는 사용 사례에 따라 독립 실행형 또는 클러스터 모드에서 사용할 수 있습니다. 노드 클러스터에서 WildFly를 통해 중요한 Jakarta EE 애플리케이션의 고가용성을 보장할 수 있습니다. 적은 수의 애플리케이션 구성을 변경한 다음, 해당 애플리케이션을 클러스터에 배포합니다. 이에 대한 자세한 내용은 [WildFly 고가용성 가이드](https://docs.wildfly.org/18/High_Availability_Guide.html)를 확인하세요.

## <a name="configuration-choice"></a>구성 선택

WildFly는 **독립 실행형 서버** 모드에서 부팅할 수 있습니다. 독립 실행형 서버 인스턴스는 JBoss AS(Application Server) 3, 4, 5 또는 6 인스턴스와 매우 비슷한 독립적인 프로세스입니다. 독립 실행형 인스턴스는 standalone.sh 또는 standalone.bat 시작 스크립트를 통해 시작할 수 있습니다. 둘 이상의 독립 실행형 인스턴스를 사용하는 경우 사용자가 서버 간에 다중 서버 관리를 조정해야 합니다.

또한 구성 폴더에서 사용할 수 있는 구성 파일을 사용하여 WildFly 인스턴스를 대체 구성으로 시작할 수 있습니다.

독립 실행형 서버 구성 파일은 다음과 같습니다.

- standalone.xml(기본값) - WildFly 인스턴스를 시작하는 데 사용되는 기본 파일입니다. 여기에는 필요한 기술을 갖춘 Jakarta Web Profile 인증 구성이 포함되어 있습니다.
   
- standalone-ha.xml - 고가용성을 갖춘 Jakarta EE Web Profile 8 인증 구성 파일입니다(웹 애플리케이션을 대상으로 함).
   
- standalone-full.xml - Jakarta EE 애플리케이션을 호스팅하는 데 필요한 모든 기술을 포함한 Jakarta EE Platform 8 인증 구성 파일입니다.

- standalone-full-ha.xml - Jakarta EE 애플리케이션을 호스팅하기 위한 고가용성을 갖춘 Jakarta EE Platform 8 인증 구성 파일입니다.

제공된 다른 구성을 사용하여 독립 실행형 WildFly 서버를 시작하려면 server-config 파일과 함께 --server-config 인수를 사용합니다.

예를 들어 클러스터링 기능이 있는 Jakarta EE Platform 8을 사용하려면 다음 명령을 사용합니다.

```
./standalone.sh --server-config=standalone-full-ha.xml
```

구성에 대한 자세한 내용은 [WildFly 시작 가이드](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)를 확인하세요.

## <a name="licensing-support-and-subscription-notes"></a>라이선스, 지원 및 구독 정보

Azure CentOS 8 이미지는 PAYG(종량제) VM 이미지이며 사용자가 라이선스를 얻을 필요가 없습니다. VM을 처음 시작하면 VM의 OS 라이선스가 자동으로 활성화되고 시간당 요금이 청구됩니다. 이는 Microsoft의 Linux 시간당 VM 요금에 추가됩니다. 자세한 내용을 보려면 [Linux VM 가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux)을 클릭합니다. WildFly는 무료로 다운로드하여 사용할 수 있으며 Red Hat 구독 또는 라이선스가 필요하지 않습니다.

## <a name="how-to-consume"></a>사용 방법

템플릿을 배포할 수 있는 세 가지 방법은 다음과 같습니다.

- PowerShell 사용 - 다음 명령을 실행하여 템플릿을 배포합니다. (Azure PowerShell 설치 및 구성에 대한 자세한 내용은 [Azure PowerShell](/powershell/azure/)을 확인하세요.)

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/wildfly/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Azure CLI 사용 - 다음 명령을 실행하여 템플릿을 배포합니다. (Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성에 대한 자세한 내용은 [Azure 플랫폼 간 명령줄](/cli/azure/install-azure-cli)을 확인하세요.)

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/wildfly/wildfly-standalone-centos8/azuredeploy.json
    ```

- Azure Portal 사용 - <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fwildfly%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">여기</a>를 클릭하여 템플릿을 배포하고 Azure Portal에 로그인합니다.

## <a name="arm-template"></a>ARM 템플릿

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/wildfly/wildfly-standalone-centos8" target="_blank">CentOS 8(독립 실행형 VM) 기반 WildFly 18</a> - CentOS 8 VM 기반 WildFly 18.0.1.Final의 독립 실행형 노드를 VM, Virtual Network 및 진단 스토리지 계정에 대한 개인 IP가 포함된 RG(리소스 그룹)에 만드는 빠른 시작 템플릿입니다. 또한 JBoss-EAP라는 Java 애플리케이션 샘플을 WildFly의 Azure에 배포합니다.

## <a name="resource-links"></a>리소스 링크

* [WildFly 18](https://docs.wildfly.org/18/)에 대한 자세한 정보
* [Azure의 Linux 배포판](../../linux/endorsed-distros.md)에 대한 자세한 정보
* [Java용 Azure 개발자 설명서](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>다음 단계

프로덕션 환경의 경우 Red Hat JBoss EAP Azure 빠른 시작 ARM 템플릿을 확인하세요.

애플리케이션 샘플이 포함된 독립 실행형 RHEL 가상 머신:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/jboss/jboss-eap-standalone-rhel" target="_blank"> RHEL 기반 JBoss EAP(독립 실행형 VM)</a>

애플리케이션 샘플이 포함된 클러스터된 RHEL 가상 머신:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/jboss/jboss-eap-clustered-multivm-rhel" target="_blank"> RHEL 기반 JBoss EAP(클러스터된 VM)</a>

애플리케이션 샘플이 포함된 클러스터된 RHEL Virtual Machine Scale Set:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/jboss/jboss-eap-clustered-vmss-rhel" target="_blank"> RHEL 기반 JBoss EAP(클러스터된 Virtual Machine Scale Set)</a>

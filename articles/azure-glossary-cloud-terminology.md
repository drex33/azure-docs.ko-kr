---
title: Azure 용어집- Azure 사전 | Microsoft Docs
description: Azure 플랫폼에 대한 클라우드 용어를 이해하려면 Azure 용어집을 사용합니다. 이 간단한 Azure 사전에서 Azure의 일반적인 클라우드 용어에 대한 정의를 제공합니다.
keywords: Azure 사전, 클라우드 용어, Azure 용어집, 용어 정의, 클라우드 용어
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2021
ms.author: monicar
ms.openlocfilehash: 223e75d0a02997187eec609324014493e2fa34df
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660992"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure 용어집: Azure 플랫폼의 클라우드 용어 사전

Microsoft Azure 용어집은 Azure 플랫폼용 클라우드 용어의 간단한 사전입니다. 참고 항목:

* [Microsoft Azure 및 Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Azure 서비스 및 해당 AWS 서비스 정의<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [클라우드 컴퓨팅 용어](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - 업계의 일반적인 클라우드 용어
* [Azure 기본 개념](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts) - Azure용 Microsoft 클라우드 채택 프레임워크.

## <a name="account"></a>account
Azure 구독에 액세스하고 구독을 관리하는 데 사용되는 계정입니다. Azure 계정이라 불리지만, 계정은 기존 회사, 학교나 개인 Microsoft 계정 중 하나일 수 있습니다. [평가판](https://azure.microsoft.com)에 등록할 때 Azure 구독을 관리하기 위해 계정을 만들 수도 있습니다.  
[Microsoft 365 계정을 사용하여 Azure 구독에 등록](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) 및 [로그인하는 데 사용할 수 있는 계정](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="api-app"></a>API 앱
[App Service 앱](#app-service-app)의 다른 이름입니다.

## <a name="app-service-app"></a>App Service 앱
[Azure App Service](app-service/overview.md)에서 웹 사이트 또는 웹 애플리케이션, 웹 API 또는 [모바일 앱 백 엔드](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)를 호스팅하기 위해 제공하는 컴퓨팅 리소스입니다. App Service 앱은 *App Services*, *웹앱*, *API 앱* 및 *모바일 앱* 이라고도 합니다.

## <a name="availability-set"></a>가용성 집합
애플리케이션 중복성 및 안정성을 제공하기 위해 함께 관리되는 가상 머신의 컬렉션입니다. 가용성 집합을 사용하면 계획되거나 계획되지 않은 유지 관리 이벤트 중에 하나 이상의 가상 머신을 사용할 수 있도록 합니다.  
[Windows 가상 머신의 가용성 관리](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Linux 가상 머신의 가용성 관리](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Azure 클래식 배포 모델
Azure의 리소스를 배포하는 데 사용하는 두 [배포 모델](./azure-resource-manager/management/deployment-models.md) 중 하나입니다(새 모델은 Azure Resource Manager임). 일부 Azure 서비스는 Resource Manager 배포 모델만 지원하고 일부는 클래식 배포 모델만 지원하며 일부는 둘 모두를 지원합니다. 각 Azure 서비스에 대한 설명서에는 지원되는 모델이 지정되어 있습니다.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure CLI(명령줄 인터페이스)
Windows, macOS 및 Linux에서 Azure 서비스를 관리하는 데 사용할 수 있는 명령줄 인터페이스입니다.  일부 서비스 또는 서비스 기능은 PowerShell 또는 CLI를 통해서만 관리할 수 있습니다. [Azure CLI](/cli/azure)를 참조하세요.

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Windows PC에서 명령줄을 통해 Azure 서비스를 관리하는 명령줄 인터페이스입니다. 일부 서비스 또는 서비스 기능은 PowerShell 또는 CLI를 통해서만 관리할 수 있습니다.
[Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager 배포 모델
Microsoft Azure의 리소스를 배포하는 데 사용하는 두 [배포 모델](./azure-resource-manager/management/deployment-models.md) 중 하나입니다(다른 모델은 클래식 배포 모델임). 일부 Azure 서비스는 Resource Manager 배포 모델만 지원하고 일부는 클래식 배포 모델만 지원하며 일부는 둘 모두를 지원합니다. 각 Azure 서비스에 대한 설명서에는 지원되는 모델이 지정되어 있습니다.

## <a name="fault-domain"></a>장애 도메인
동시에 실패할 가능성이 있는 가용성 집합의 가상 머신 컬렉션입니다. 예를 들어 랙에서 공통된 전원 및 네트워크 스위치를 공유하는 컴퓨터 그룹입니다. Azure에서 가용성 집합의 가상 머신은 여러 장애 도메인에서 자동으로 분리됩니다.  
[Windows 가상 머신의 가용성 관리](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux 가상 머신의 가용성 관리](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.  

## <a name="geo"></a>지역
일반적으로 두 개 이상의 영역을 포함하는 데이터 상주에 대해 정의된 경계입니다. 경계는 국경 내에 있거나 초과될 수 있으며 세금 규정의 영향을 받습니다. 모든 지역에는 하나 이상의 하위 지역이 있습니다. 지역의 예로 아시아 태평양 및 일본이 있습니다. *지리* 라고도 합니다.  
[Azure 지역](best-practices-availability-paired-regions.md)을 참조하세요.

## <a name="geo-replication"></a>지역에서 복제
지역 쌍 내의 Blob, 테이블 및 큐와 같은 콘텐츠를 자동으로 복제하는 프로세스입니다.  
[Azure SQL Database의 활성 지역 복제](./azure-sql/database/auto-failover-group-overview.md)를 참조하세요.
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>이미지
개수에 관계없이 여러 가상 머신을 만드는 데 사용할 수 있는 운영 체제 및 애플리케이션 구성을 포함하는 파일입니다. Azure에는 두 가지 유형의 이미지, 즉 VM 이미지와 OS 이미지가 있습니다. VM 이미지에는 운영 체제 및 이미지를 만들 때 가상 머신에 연결된 모든 디스크가 포함됩니다. OS 이미지에는 데이터 디스크 구성 없이 일반화된 운영 체제만 포함되어 있습니다.  
[PowerShell 또는 CLI로 Azure의 Windows 가상 머신 이미지 이동 및 선택](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

## <a name="limits"></a>제한
만들 수 있는 리소스 수 또는 도달할 수 있는 성능 벤치마크입니다. 제한은 일반적으로 구독, 서비스 및 제안에 연결됩니다.  
[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

## <a name="load-balancer"></a>부하 분산 장치
들어오는 트래픽을 네트워크의 컴퓨터 간에 분산시키는 리소스입니다. Azure에서 부하 분산 장치는 부하 분산 장치 집합에 정의된 가상 머신에 트래픽을 분산시킵니다. [부하 분산 장치](load-balancer/load-balancer-overview.md) 는 인터넷에 연결되거나 내부에 있을 수 있습니다.  

## <a name="mobile-app"></a>모바일 앱
[App Service 앱](#app-service-app)의 다른 이름입니다.

## <a name="offer"></a>제품
Azure 구독에 적용되는 가격 책정, 신용 및 관련 조건입니다.  
[Azure 제안 세부 정보 페이지](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Azure 서비스를 배포하고 관리하는 데 사용하는 보안 웹 포털입니다.

## <a name="region"></a>region
국경을 넘지 않으면서 하나 이상의 데이터 센터를 포함하는 지역 내의 영역입니다. 가격 책정, 지역 서비스 및 제공 사항 유형은 지역 수준에서 노출됩니다. 지역은 일반적으로 최대 수백 킬로미터까지도 떨어질 수 있는 다른 지역과 쌍으로 연결되어 있습니다. 지역 쌍은 재해 복구 및 고가용성 시나리오에 대한 메커니즘으로 사용할 수 있습니다. *위치* 라고도 합니다.  
[Azure 지역](best-practices-availability-paired-regions.md)을 참조하세요.

## <a name="resource"></a>resource
Azure 솔루션에 속한 항목입니다. 각 Azure 서비스를 사용하면 데이터베이스 또는 가상 머신과 같은 다양한 유형의 리소스를 배포할 수 있습니다.   
[Azure Resource Manager 개요](azure-resource-manager/management/overview.md)를 참조하세요.

## <a name="resource-group"></a>리소스 그룹
리소스 관리자에서 애플리케이션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 모든 애플리케이션에 대한 리소스 또는 논리적으로 그룹화된 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 어떻게 리소스 그룹에 리소스를 할당할지 결정할 수 있습니다.  
[Azure Resource Manager 개요](azure-resource-manager/management/overview.md)를 참조하세요.

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager 템플릿
하나 이상의 Azure 리소스를 선언적으로 정의하고 배포된 리소스 간의 종속성을 정의하는 JSON 파일입니다. 템플릿은 리소스를 일관되고 반복적으로 배포하는 데 사용할 수 있습니다.  
[Azure Resource Manager 템플릿 작성](./azure-resource-manager/templates/syntax.md)을 참조하세요.

## <a name="resource-provider"></a>리소스 공급자
리소스 관리자를 통해 배포하고 관리할 수 있는 리소스를 제공하는 서비스입니다. 각 리소스 공급자는 배포된 리소스로 작업하기 위한 작업을 제공합니다. 리소스 공급자는 Azure 포털, Azure PowerShell 및 몇 가지 프로그래밍 SDK를 통해 액세스할 수 있습니다.  
[Azure Resource Manager 개요](azure-resource-manager/management/overview.md)를 참조하세요.

## <a name="role"></a>역할(role)
사용자, 그룹 및 서비스에 할당할 수 있는 액세스를 제어하는 방법입니다. 역할은 Azure 리소스에서 만들기, 관리 및 읽기와 같은 작업을 수행할 수 있습니다.  
[RBAC: 기본 제공 역할](role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="service-level-agreement-sla"></a><a name="sla"></a>SLA(서비스 수준 계약)
작동 시간 및 연결에 대 한 Microsoft의 약정을 설명 하는 규약입니다. 각 Azure 서비스에는 특정 SLA가 있습니다.  
[서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## <a name="shared-access-signature-sas"></a><a name="sas"></a>SAS(공유 액세스 서명)
계정 키를 노출하지 않고 리소스에 대한 제한된 액세스 권한을 부여할 수 있는 서명입니다. 예를 들어 [Azure Storage는 SAS를 사용하여](./storage/common/storage-sas-overview.md) BLOB과 같은 개체에 대한 클라이언트 액세스 권한을 부여합니다. [IoT Hub는 SAS를 사용하여](iot-hub/iot-hub-dev-guide-sas.md#security-tokens) 원격 분석을 보낼 수 있는 권한을 디바이스에 부여합니다.

## <a name="storage-account"></a>스토리지 계정 만들기
Azure Storage의 Azure Blob, 큐, 테이블 및 파일 서비스에 대한 액세스 권한을 제공하는 계정입니다. 스토리지 계정 이름은 Azure Storage 데이터 개체의 고유한 네임스페이스를 정의합니다.  
[Azure Storage 계정 정보](./storage/common/storage-account-create.md)를 참조하세요.

## <a name="subscription"></a>subscription
Azure 서비스를 받을 수 있도록 하는, 고객과 Microsoft와의 계약입니다. 구독 가격 책정 및 관련 조건은 구독에 대해 선택된 제안에 따라 제어됩니다.
[Microsoft 온라인 정기가입 계약](https://azure.microsoft.com/support/legal/subscription-agreement/) 및 [Azure 구독과 Azure Active Directory의 연관 관계](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

## <a name="tag"></a>tag
관리 또는 청구에 대한 요구 사항에 따라 리소스를 분류할 수 있도록 하는 인덱싱 용어입니다. 복잡한 리소스 컬렉션이 있는 경우 태그를 사용하여 이러한 자산을 가장 적합한 방식으로 시각화할 수 있습니다. 예를 들어 조직에서 비슷한 역할을 제공하거나 동일한 부서에 속한 리소스를 태그로 지정할 수 있습니다.  
[태그를 사용하여 Azure 리소스 구성](./azure-resource-manager/management/tag-resources.md)을 참조하세요.

## <a name="tenant"></a>테넌트
테 넌 트는 제품, 서비스 또는 응용 프로그램의 인스턴스에 대 한 특정 권한으로 액세스를 공유 하는 사용자 또는 조직의 그룹입니다. Azure Active Directory 테 넌 트는 Microsoft 365 같은 클라우드 응용 프로그램에 등록할 때 조직이 받는 Azure Active Directory 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다. 배포할지에는 여러 조직에서 공유 하는 응용 프로그램의 인스턴스를 나타내며, 각각 인스턴스에 대 한 별도의 액세스 권한이 있습니다.

## <a name="update-domain"></a>업데이트 도메인
동시에 업데이트되는 가용성 집합에 있는 가상 머신의 컬렉션입니다. 동일한 업데이트 도메인의 가상 머신은 계획된 유지 관리 동안 함께 다시 시작됩니다. Azure는 한 번에 둘 이상의 업데이트 도메인을 다시 시작하지 않습니다. 또한 업그레이드 도메인이라고도 합니다.  
[Windows 가상 머신의 가용성 관리](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Linux 가상 머신의 가용성 관리](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="virtual-machine"></a><a name="vm"></a>가상 컴퓨터
운영 체제를 실행하는 물리적 컴퓨터의 소프트웨어 구현입니다. 같은 하드웨어에서 복수의 가상 머신이 동시에 실행될 수 있습니다. Azure에서 가상 머신은 다양한 크기로 사용할 수 있습니다.  
[Virtual Machines 설명서](https://azure.microsoft.com/documentation/services/virtual-machines/)를 참조하세요.

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>가상 컴퓨터 확장
다른 프로그램의 작동을 돕거나 실행 중인 컴퓨터를 조작할 수 있는 기능을 제공하는 동작 또는 기능을 구현하는 리소스입니다. 예를 들어 Azure 가상 머신에서 VM 액세스 확장을 사용하여 원격 액세스 값을 다시 설정하거나 수정할 수 있습니다.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
[가상 머신 확장 및 기능 정보(Windows)](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [가상 머신 확장 및 기능 정보(Linux)](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="virtual-network"></a><a name="vnet"></a>가상 네트워크
다른 모든 Azure 테넌트에서 격리된 Azure 리소스 간의 연결을 제공하는 네트워크입니다. [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md)를 통해 가상 네트워크 간 연결과 가상 네트워크 및 온-프레미스 네트워크 간 연결을 설정할 수 있습니다. 사용자는 가상 네트워크 내부의 IP 주소 블록, DNS 설정, 보안 정책 및 경로 테이블을 완벽하게 제어할 수 있습니다.  
[Virtual Network 개요](virtual-network/virtual-networks-overview.md)를 참조하세요.  

## <a name="web-app"></a>웹앱
[App Service 앱](#app-service-app)의 다른 이름입니다.

## <a name="see-also"></a>참고 항목

* [Azure 시작](https://azure.microsoft.com/get-started/)
* [클라우드 리소스 센터](https://azure.microsoft.com/resources/)  
* [비즈니스 애플리케이션을 위한 Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [데이터 센터의 Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)

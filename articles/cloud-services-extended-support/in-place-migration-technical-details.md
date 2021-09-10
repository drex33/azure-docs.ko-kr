---
title: Azure Cloud Services(확장 지원)로 마이그레이션하는 경우에 대한 기술 세부 정보 및 요구 사항
description: Azure Cloud Services(클래식)에서 Azure Cloud Services(확장 지원)로 마이그레이션하는 경우에 대한 기술 세부 정보 및 요구 사항을 제공합니다.
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.openlocfilehash: 55ce5305962562876a97dfd7677e6af5e1eb9e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536734"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Azure Cloud Services(확장 지원)로 마이그레이션하는 경우에 대한 기술 세부 정보   

이 문서에서는 Cloud Services(클래식)와 관련된 마이그레이션 도구에 대한 기술 세부 정보에 대해 설명합니다. 

## <a name="details-about-feature--scenarios-supported-for-migration"></a>마이그레이션에 지원되는 기능/시나리오에 대한 세부 정보 


### <a name="extensions-and-plugin-migration"></a>확장 및 플러그 인 마이그레이션 
- 사용하도록 설정되고 지원되는 모든 확장이 마이그레이션됩니다. 
- 사용하지 않도록 설정된 확장은 마이그레이션되지 않습니다. 
- 플러그 인은 레거시 개념이므로 마이그레이션하기 전에 제거해야 합니다. 마이그레이션에 지원되지만 마이그레이션 후에 확장을 사용하도록 설정해야 하는 경우 확장을 설치하기 전에 플러그 인을 먼저 제거해야 합니다. 원격 데스크톱 플러그 인 및 확장이 이에 가장 큰 영향을 받습니다.   
 
### <a name="certificate-migration"></a>인증서 마이그레이션
- Cloud Services(확장 지원)에서는 인증서가 Key Vault에 저장됩니다. 마이그레이션 과정에서 클라우드 서비스 이름이 있는 고객의 Key Vault를 만들고, Azure Service Manager에서 Key Vault로 모든 인증서를 전송합니다. 
- 이 Key Vault에 대한 참조는 템플릿에 지정되거나 PowerShell 또는 Azure CLI를 통해 전달됩니다. 

### <a name="service-configuration-and-service-definition-files"></a>서비스 구성 및 서비스 정의 파일
- Cloud Services(확장 지원)를 사용하려면 사소한 변경 내용으로 .cscfg 및 .csdef 파일을 업데이트해야 합니다. 
- 가상 네트워크 및 VM SKU와 같은 리소스의 이름이 다릅니다. [마이그레이션 후 리소스의 변환과 명명 규칙](#translation-of-resources-and-naming-convention-post-migration)을 참조하세요.
- 고객은 [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 및 [Rest API](/rest/api/compute/cloudservices/get)를 통해 새 배포를 검색할 수 있습니다. 

### <a name="cloud-service-and-deployments"></a>클라우드 서비스 및 배포
- 각 Cloud Services(확장 지원) 배포는 독립된 클라우드 서비스입니다. 배포는 더 이상 슬롯을 사용하여 클라우드 서비스로 그룹화되지 않습니다.
- 클라우드 서비스(클래식)에 두 슬롯이 있는 경우 슬롯 하나(준비)를 삭제하고 마이그레이션 도구를 사용하여 다른 (프로덕션) 슬롯을 Azure Resource Manager로 이동해야 합니다. 
- 클라우드 서비스 배포의 공용 IP 주소는 Azure Resource Manager로 마이그레이션한 후에도 동일하게 유지되며 기본 SKU IP(동적 또는 정적) 리소스로 노출됩니다. 
- 마이그레이션된 클라우드 서비스의 DNS 이름 및 도메인(cloudapp.azure.net)은 동일하게 유지됩니다. 

### <a name="virtual-network-migration"></a>가상 네트워크 마이그레이션
- Cloud Services 배포가 가상 네트워크에 있는 경우 마이그레이션 중에 모든 Cloud Services 및 연결된 가상 네트워크 리소스가 함께 마이그레이션됩니다. 
- 마이그레이션 후 가상 네트워크는 클라우드 서비스와 다른 리소스 그룹에 배치됩니다. 
- 여러 Cloud Services를 포함하는 가상 네트워크의 경우 각 클라우드 서비스가 하나씩 차례로 마이그레이션됩니다. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>가상 네트워크에 없는 배포 마이그레이션
- 2017에서는 Azure가 고객이 지정한 가상 네트워크를 사용하지 않고 플랫폼에서 생성한 “기본” 가상 네트워크에 새 배포를 자동으로 만들기 시작했습니다. 이러한 기본 가상 네트워크는 고객에게 보이지 않습니다.   
- 이 기본 가상 네트워크는 마이그레이션 중에 Azure Resource Manager에서 고객에게 한 번 노출됩니다. Azure Resource Manager에서 배포를 관리하거나 업데이트하려면 .cscfg 파일의 NetworkConfiguration 섹션에 이 가상 네트워크 정보를 추가해야 합니다.    
- 기본 가상 네트워크는 Azure Resource Manager로 마이그레이션될 때 클라우드 서비스와 동일한 리소스 그룹에 배치됩니다.
- 그 전에 생성된 Cloud Services는 가상 네트워크에 있지 않으며 도구를 사용하여 마이그레이션할 수 없습니다. Azure Resource Manager에서 직접 이러한 Cloud Services를 다시 배포하는 것이 좋습니다. 
- 배포를 마이그레이션할 수 있는지 확인하려면 배포에서 Validate API를 실행합니다. Validate API의 결과에는 이 배포를 마이그레이션할 수 있는지 여부를 명시적으로 언급하는 오류 메시지가 포함됩니다.     

### <a name="load-balancer"></a>Load Balancer   
- 퍼블릭 엔드포인트를 사용하는 클라우드 서비스의 경우 클라우드 서비스와 연결된 플랫폼 생성 부하 분산 장치가 Azure Resource Manager의 고객 구독 내에 노출됩니다. 부하 분산 장치는 읽기 전용 리소스이며 업데이트는 서비스 구성(.cscfg) 및 서비스 정의(.csdef) 파일을 통해서만 제한됩니다. 

### <a name="key-vault"></a>Key Vault
- Azure는 마이그레이션 과정에서 자동으로 새 Key Vault를 만들고 모든 인증서를 Key Vault로 마이그레이션합니다. 이 도구에서는 기존 Key Vault를 사용할 수 없습니다. 
- Cloud Services(확장 지원)에는 동일한 지역 및 구독에 있는 Key Vault가 필요합니다. 이 Key Vault는 마이그레이션 과정에서 자동으로 생성됩니다. 

## <a name="resources-and-features-not-available-for-migration"></a>마이그레이션에 사용할 수 없는 리소스 및 기능
이러한 시나리오는 리소스, 기능 및 Cloud Services의 조합과 관련된 상위 수준 시나리오입니다. 이 목록은 전체 목록이 아닙니다. 

| 리소스 | 다음 단계/해결 방법 | 
|---|---|
| 자동 크기 조정 규칙 | 마이그레이션은 진행되지만 규칙이 삭제됩니다. Cloud Services(확장 지원)에 대한 마이그레이션 후 [규칙을 다시 만듭니다](./configure-scaling.md). | 
| 경고 | 마이그레이션은 진행되지만 경고가 삭제됩니다. Cloud Services(확장 지원)에 대한 마이그레이션 후 [규칙을 다시 만듭니다](./enable-alerts.md). | 
| VPN Gateway | 마이그레이션을 시작하기 전에 VPN Gateway를 제거한 후 마이그레이션이 완료되면 VPN Gateway를 다시 만듭니다. | 
| ExpressRoute 게이트웨이(Virtual Network와 동일한 구독에서만) | 마이그레이션을 시작하기 전에 게이트웨이를 제거한 다음, 마이그레이션이 완료되면 ExpressRoute 게이트웨이를 다시 만듭니다. | 
| 할당량  | 할당량은 마이그레이션되지 않습니다. 유효성 검사가 성공하려면 마이그레이션하기 전에 Azure Resource Manager에서 [새 할당량을 요청](../azure-resource-manager/templates/error-resource-quota.md#solution)합니다. | 
| 선호도 그룹 | 지원 안 됨 마이그레이션하기 전에 선호도 그룹을 제거합니다.  | 
| [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 사용하는 가상 네트워크| 피어링된 가상 네트워크를 다른 가상 네트워크로 마이그레이션하기 전에 피어링을 삭제하고, 가상 네트워크를 Resource Manager로 마이그레이션하고, 피어링을 다시 만듭니다. 이로 인해 아키텍처에 따라 가동 중지 시간이 발생할 수 있습니다. | 
| App Service 환경이 포함된 가상 네트워크 | 지원되지 않음 | 
| HDInsight Services가 포함된 가상 네트워크 | 지원 안 됨 
| Azure API Management 배포가 포함된 가상 네트워크 | 지원 안 됨 <br><br> 가상 네트워크를 마이그레이션하려면 API Management 배포의 가상 네트워크를 변경합니다. 가동 중지 시간이 없는 작업입니다. | 
| 클래식 ExpressRoute 회로 | 지원 안 됨 <br><br>이러한 회로는 PaaS 마이그레이션을 시작하기 전에 Azure Resource Manager로 마이그레이션해야 합니다. 자세한 내용은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](../expressroute/expressroute-howto-move-arm.md)을 참조하세요. |  
| 역할 기반 Access Control | 마이그레이션 후 리소스의 URI가 `Microsoft.ClassicCompute`에서 `Microsoft.Compute`로 변경됩니다. 마이그레이션 후 RBAC 정책을 업데이트해야 합니다. | 
| Application Gateway | 지원되지 않습니다. <br><br> 마이그레이션을 시작하기 전에 Application Gateway를 제거한 다음, Azure Resource Manager로의 마이그레이션이 완료되면 Application Gateway를 다시 만듭니다. | 

## <a name="unsupported-configurations--migration-scenarios"></a>지원되지 않는 구성/마이그레이션 시나리오

| 구성/시나리오  | 다음 단계/해결 방법 | 
|---|---|
| 가상 네트워크에 없는 일부 이전 배포 마이그레이션 | 가상 네트워크에 없는 일부 클라우드 서비스 배포는 마이그레이션에 지원되지 않습니다. <br><br> 1. 유효성 검사 API를 사용하여 배포를 마이그레이션할 수 있는지 확인합니다. <br> 2. 자격이 있는 경우 배포가 "DefaultRdfeVnet" 접두사가 있는 가상 네트워크 아래의 Azure Resource Manager로 이동됩니다. | 
| 동적 IP 주소를 사용하여 프로덕션 및 스테이징 슬롯 배포가 모두 포함된 배포 마이그레이션 | 2슬롯 Cloud Service를 마이그레이션하려면 스테이징 슬롯을 삭제해야 합니다. 스테이징 슬롯이 삭제되면 Azure Resource Manager에서 프로덕션 슬롯을 독립 Cloud Service(확장 지원)로 마이그레이션합니다. 그런 다음, 스테이징 환경을 새 Cloud Service(확장 지원)로 다시 배포하고, 첫 번째 서비스와 스왑할 수 있게 합니다. | 
| 예약된 IP 주소를 사용하여 프로덕션 및 스테이징 슬롯 배포가 모두 포함된 배포 마이그레이션 | 지원 안 됨 | 
| 다른 가상 네트워크에서 프로덕션 및 스테이징 배포 마이그레이션|2슬롯 Cloud Service를 마이그레이션하려면 스테이징 슬롯을 삭제해야 합니다. 스테이징 슬롯이 삭제되면 Azure Resource Manager에서 프로덕션 슬롯을 독립 Cloud Service(확장 지원)로 마이그레이션합니다. 그런 다음, 새 Cloud Services(확장 지원) 배포를 스왑 가능한 속성을 사용하도록 설정한 마이그레이션된 배포에 연결할 수 있습니다. 이전 스테이징 슬롯 배포의 배포 파일을 다시 사용하여 이 새 스왑 가능한 배포를 만들 수 있습니다. | 
| 빈 Cloud Service 마이그레이션(배포가 없는 Cloud Service) | 지원 안 됨 | 
| 원격 데스크톱 플러그 인 및 원격 데스크톱 확장이 포함된 배포 마이그레이션 | 옵션 1: 마이그레이션하기 전에 원격 데스크톱 플러그 인을 제거합니다. 이렇게 하려면 배포 파일을 변경해야 합니다. 그러면 마이그레이션이 진행됩니다. <br><br> 옵션 2: 원격 데스크톱 확장을 제거하고 배포를 마이그레이션합니다. 마이그레이션 후 플러그 인을 제거하고 확장을 설치합니다. 이렇게 하려면 배포 파일을 변경해야 합니다. <br><br> 마이그레이션하기 전에 플러그 인 및 확장을 제거합니다. 플러그 인은 Cloud Services(확장 지원)에서 [사용하지 않는 것이 좋습니다](./deploy-prerequisite.md#required-service-definition-file-csdef-updates).| 
| PaaS 및 IaaS 배포가 모두 포함된 가상 네트워크 |지원되지 않음 <br><br> PaaS 또는 IaaS 배포를 다른 가상 네트워크로 이동합니다. 이로 인해 가동 중지 시간이 발생합니다. | 
레거시 역할 크기(예: Small 또는 ExtraLarge)를 사용하는 Cloud Service 배포 | 마이그레이션이 완료되지만, 최신 역할 크기를 사용하도록 역할 크기가 업데이트됩니다. 비용 또는 SKU 속성은 변경되지 않으며, 이 변경으로 인해 가상 머신이 다시 부팅되지 않습니다. 이러한 최신 역할 크기를 참조하도록 모든 배포 아티팩트를 업데이트합니다. 자세한 내용은 [사용 가능한 VM 크기](available-sizes.md)를 참조하세요.|
| 다른 가상 네트워크로 Cloud Service 마이그레이션 | 지원되지 않음 <br><br> 1. 마이그레이션하기 전에 배포를 다른 클래식 가상 네트워크로 이동합니다. 이로 인해 가동 중지 시간이 발생합니다. <br> 2. 새 가상 네트워크를 Azure Resource Manager로 마이그레이션합니다. <br><br> 또는 <br><br> 1. 가상 네트워크를 Azure Resource Manager로 마이그레이션합니다. <br>2. Cloud Service를 새 가상 네트워크로 이동합니다. 이로 인해 가동 중지 시간이 발생합니다. | 
| 가상 네트워크의 Cloud Service이지만 명시적 서브넷이 할당되지 않음 | 지원 안 됨 완화에는 역할을 서브넷으로 이동하는 작업이 포함되며, 이 경우 역할을 다시 시작해야 합니다(가동 중지 시간). | 

## <a name="translation-of-resources-and-naming-convention-post-migration"></a>마이그레이션 후 리소스의 변환과 명명 규칙
마이그레이션 과정에서 리소스 이름이 변경되고 몇몇 Cloud Services 기능이 Azure Resource Manager 리소스로 노출됩니다. 이 표에는 Cloud Services 마이그레이션과 관련된 변경 내용이 요약되어 있습니다.

| Cloud Services(클래식) <br><br> 리소스 이름 | Cloud Services(클래식) <br><br> Syntax| Cloud Services(추가 지원) <br><br> 리소스 이름| Cloud Services(추가 지원) <br><br> Syntax | 
|---|---|---|---|
| 클라우드 서비스 | `cloudservicename` | 연결되지 않음| 연결되지 않음 |
| 배포(포털에서 생성) <br><br> 배포(포털 외부에서 생성)  | `deploymentname` | Cloud Services(추가 지원) | `cloudservicename` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> 연결되지 않음 |  Virtual Network(포털 외부에서 생성) <br><br> Virtual Network(포털에서 생성) <br><br> 가상 네트워크(기본값) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `VNet-cloudservicename`|
| 연결되지 않음 | 연결되지 않음 | Key Vault | `KV-cloudservicename` | 
| 연결되지 않음 | 연결되지 않음 | 클라우드 서비스 배포에 대한 리소스 그룹 | `cloudservicename-migrated` | 
| 연결되지 않음 | 연결되지 않음 | Virtual Network의 리소스 그룹 | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| 연결되지 않음 | 연결되지 않음 | 공용 IP(동적) | `cloudservicenameContractContract` | 
| 예약된 IP 이름 | `reservedipname` | 예약된 IP(포털 외부에서 생성) <br><br> 예약된 IP(포털에서 생성) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| 연결되지 않음| 연결되지 않음 | Load Balancer | `LB-cloudservicename`|



## <a name="migration-issues-and-how-to-handle-them"></a>마이그레이션 문제 및 이러한 문제를 처리하는 방법 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>마이그레이션이 특정 작업을 진행하는 도중에 중단되었습니다. 
- 배포 수에 따라 커밋, 준비 및 중단 시간이 오래 걸릴 수 있습니다. 24시간이 지나면 작업이 만료됩니다.   
- 커밋, 준비 및 중단 작업은 idempotent입니다. 대부분의 문제는 다시 시도하면 수정할 수 있습니다. 일시적인 오류가 있을 수 있으며 몇 분 후면 사라집니다. 시간 간격을 두고 다시 시도하는 것이 좋습니다. Azure Portal을 사용하여 마이그레이션하는 경우 작업이 "진행 중 상태"에서 중단되면 PowerShell을 사용하여 작업을 다시 시도합니다. 
- 백 엔드에서 배포를 마이그레이션하거나 롤백하려면 고객 지원팀에 문의하세요. 

### <a name="migration-failed-in-an-operation"></a>마이그레이션이 특정 작업 도중 실패했습니다. 
- 유효성 검사가 실패한 경우 배포 또는 가상 네트워크에 지원되지 않는 시나리오/기능/리소스가 포함되어 있기 때문입니다. 지원되지 않는 시나리오 목록을 사용하여 문서에서 해결 방법을 찾을 수 있습니다.  
- 준비 작업은 먼저 비용이 많이 드는 검증(유효성 검사에서 다루지 않음)을 비롯한 검증을 수행합니다. 지원되지 않는 시나리오 때문에 준비가 실패할 수 있습니다. 공개 문서에서 시나리오와 해결 방법을 찾아보세요. 원래 상태로 돌아가 업데이트 및 삭제 작업을 위해 배포의 잠금을 해제하려면 중단을 호출해야 합니다.
- 중단이 실패하면 작업을 다시 시도하세요. 다시 시도가 실패하면 고객 지원팀에게 문의하세요.
- 커밋이 실패하면 작업을 다시 시도하세요. 다시 시도가 실패하면 고객 지원팀에 문의하세요. 커밋 오류가 발생할 경우에도 배포에 데이터 평면 문제가 없어야 합니다. 배포에서 문제 없이 고객 트래픽을 처리할 수 있어야 합니다. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>준비 후 포털이 새로 고침되었습니다. 환경이 다시 시작된 후 커밋 또는 중단이 더 이상 표시되지 않습니다. 
- 포털은 마이그레이션 정보를 로컬에 저장하므로 새로 고침 후 클라우드 서비스가 준비 단계에 있더라도 유효성 검사 단계에서 시작됩니다.  
- 포털을 사용하여 유효성 검사 및 준비 단계를 다시 진행하여 중단 및 커밋 단추를 노출할 수 있습니다. 오류가 발생하지 않습니다.
- 고객은 PowerShell 또는 Rest API를 사용하여 중단하거나 커밋할 수 있습니다. 

### <a name="how-much-time-can-the-operations-takebr"></a>작업에 소요되는 시간은 얼마나 됩니까?<br>
유효성 검사는 빠르게 진행되도록 설계되었습니다. 준비는 가장 오래 실행되며 마이그레이션하는 역할 인스턴스의 총 수에 따라 다소 시간이 소요됩니다. 중단 및 커밋도 시간이 걸릴 수 있지만 준비보다는 시간이 덜 걸립니다. 모든 작업은 24시간 후 만료됩니다.

## <a name="next-steps"></a>다음 단계
Cloud Services (클래식) 배포를 Cloud Services (연장 지원)로 마이그레이션하는 데 도움이 필요한 경우 [고객 지원 및 문제 해결](support-help.md) 방문 페이지를 참조 하세요.
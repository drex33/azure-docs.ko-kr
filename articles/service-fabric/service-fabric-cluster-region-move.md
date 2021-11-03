---
title: Azure Service Fabric 클러스터를 새 지역으로 이동
description: Azure Service Fabric 클러스터 및 애플리케이션을 다른 지역으로 마이그레이션하는 방법입니다.
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: micraft
ms.openlocfilehash: 3cbd2e21508296b6174a2322559973fb98728e2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083482"
---
# <a name="move-an-azure-service-fabric-cluster-to-a-new-region"></a>Azure Service Fabric 클러스터를 새 지역으로 이동

Service Fabric 클러스터 리소스 범위는 기본적으로 지역으로 지정됩니다. 이 범위는 먼저 대상 지역에 새 클러스터를 만들고, 기존 워크로드를 마이그레이션한 다음, 해당 새 대상 지역으로 트래픽을 이동하여 현재 “지역 이동”을 수행함을 의미합니다. 이 문서에서는 이 마이그레이션을 수행하는 데 필요한 단계를 간략하게 설명합니다. 몇 가지 의사 결정 요소에 따라 마이그레이션을 더 복잡해지거나 덜 복잡해질 수 있습니다. 이러한 입력에는 클러스터 및 애플리케이션을 설정하고 구성한 방법, 클러스터에서 통신이 작동하는 방법, 워크로드가 상태 비저장인지, 상태 저장인지 또는 둘의 혼합인지 여부가 포함됩니다.  


## <a name="steps-to-follow-for-a-region-migration"></a>지역 마이그레이션을 위해 수행할 단계

지역 마이그레이션에 참여하기 전에 테스트 환경을 설정하고 이러한 단계를 연습하는 것이 좋습니다. 

## <a name="create-new-cluster"></a>새 클러스터 만들기
1. 클러스터 및 인프라 토폴로지에 대한 기존 ARM 템플릿의 용도를 변경하여 [새 지역에 클러스터를 설정](./service-fabric-cluster-creation-via-arm.md#use-your-own-custom-template)합니다. 현재 클러스터를 설명하는 ARM 템플릿이 없는 경우 [Azure Resource Explorer](https://resources.azure.com/)에서 현재 ARM 템플릿을 검색하는 것이 좋습니다. Azure Resource Explorer는 기존 환경의 복제본을 반복적으로 배포할 수 있는 하나 이상의 ARM 템플릿을 만드는 데 사용할 수 있는 현재 배포된 리소스 및 해당 구성 정보를 검색하는 데 도움이 됩니다. 계속하기 전에 이 단계에서 기존 환경의 복제본을 배포할 수 있는 ARM 템플릿이 작동하는지 테스트하고 확인합니다. 

## <a name="move-applications-and-traffic"></a>애플리케이션 및 트래픽 이동
1. [ARM을 통해 기존 애플리케이션 및 서비스를 배포](service-fabric-application-arm-resource.md)합니다. 수행한 애플리케이션 매개 변수 또는 구성 사용자 지정을 유지하도록 주의하세요. 예를 들어 애플리케이션에 기본값이 5인 “count” 매개 변수가 있지만 원본 환경에서 해당 매개 변수의 값을 7로 업그레이드한 경우에는 새 지역의 애플리케이션 배포에서 값이 7로 설정되도록 해야 합니다. ARM을 사용하여 애플리케이션 및 서비스 인스턴스를 관리하지 않는 경우 현재 지역에서 실행되는 애플리케이션 및 서비스의 현재 세트 및 해당 구성을 식별하고 새 지역/클러스터에서 해당 애플리케이션 및 서비스를 복제해야 합니다. 

2. 서비스 마이그레이션  
   -  상태 저장 워크로드의 경우: 
      * <p>상태 저장 서비스가 안정적인 지점에 도달했는지 확인하려면 먼저 해당 서비스로 들어오는 트래픽을 중지했는지 확인합니다. 이 작업을 수행하는 방법은 트래픽이 서비스에 전달되는 방법에 따라 달라집니다. 예를 들어 적절한 라우팅 또는 전달 규칙을 제거하여 Event Hubs에서 서비스를 차단하거나 APIM 또는 Azure Network Load Balancer 같은 서비스가 트래픽을 서비스로 라우팅하지 못하도록 해야 할 수 있습니다. 트래픽이 중단되고 서비스가 해당 요청과 관련된 백그라운드 작업을 완료하면 계속할 수 있습니다. </p>
      
      * [백업 복원 서비스](service-fabric-reliable-services-backup-restore.md)를 사용하고 [주문형 백업](service-fabric-backup-restore-service-ondemand-backup.md)을 수행하여 상태 저장 서비스에서 백업을 수행합니다. 이 작업은 트래픽이 중단되고 백그라운드 처리 작업이 완료된 후에 수행해야 합니다. 완료되면 새 지역 및 클러스터의 상태 저장 서비스로 [데이터를 복원](service-fabric-backup-restore-service-trigger-restore.md)할 수 있습니다. 백업을 수행하는 데 사용된 Azure Storage 계정은 새 지역에서 액세스할 수 있어야 합니다.

   -  상태 비저장 서비스의 경우: 
      * <p>새 클러스터에 서비스를 배포하는 것 이외의 추가 작업이 없어야 합니다. 이상적으로는 2단계에서 수행된 ARM 애플리케이션 배포의 일부로 수행되고 원본 클러스터와 동일하게 구성되어야 합니다.</p>

   -  모든 서비스의 경우:  
      * <p>클라이언트와 서비스 간의 모든 통신 단계가 원본 클러스터와 유사하게 구성되어야 합니다. 예를 들어 이 유효성 검사에는 Event Hubs, 네트워크 부하 분산 장치, App Gateway, API Management 같은 매개체가 클러스터로 트래픽이 흐르도록 허용하는 데 필요한 규칙으로 설정되도록 하는 것이 포함될 수 있습니다.</p>  

3. 이전 지역에서 새 지역으로 트래픽을 리디렉션합니다. 다양한 [라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)을 제공하는 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 마이그레이션에 사용하는 것이 좋습니다. 트래픽 라우팅 규칙을 정확히 업데이트하는 방법은 기존 지역을 유지할지 사용하지 않을지에 따라 달라지며 애플리케이션 내에서 트래픽이 흐르는 방식에 따라서도 달라집니다. 다른 지역의 서로 다른 Azure 리소스 간에 개인/공용 IP 또는 DNS 이름을 이동할 수 있는지 여부를 조사해야 할 수 있습니다. Service Fabric은 시스템의 이 부분을 인식하지 못하므로 트래픽 흐름에 관련된 Azure 팀을 조사하고 필요한 경우 참여해야 하며, 특히 워크로드가 더 복잡하거나 워크로드 대기 시간이 중요한 경우는 더욱 그렇습니다. [사용자 지정 도메인 구성](../api-management/configure-custom-domain.md), [공용 IP 주소](../virtual-network/ip-services/public-ip-addresses.md), [DNS 영역 및 레코드](../dns/dns-zones-records.md) 같은 문서가 도움이 될 수 있으며 이러한 정보는 트래픽 흐름 및 프로토콜에 따라 필요할 수 있습니다. 다음은 트래픽 라우팅 업데이트에 어떻게 접근할 수 있는지를 보여 주는 두 가지 예제 시나리오입니다.  
   * 기존 원본 지역을 유지하지 않으려는 경우 원래 원본 클러스터에 호출을 전달하는 네트워크 부하 분산 장치의 공용 IP와 연결된 DNS/CNAME이 있습니다. 새 지역에 있는 새 네트워크 부하 분산 장치의 새 공용 IP와 연결되도록 DNS/CNAME을 업데이트합니다. 이 전송을 완료하면 기존 클러스터를 사용하는 클라이언트가 새 클러스터를 사용하도록 전환됩니다. 
  
   * 기존 원본 지역을 유지하려는 경우 원래 원본 클러스터에 호출을 전달하는 네트워크 부하 분산 장치의 공용 IP와 연결된 DNS/CNAME이 있습니다. Azure Traffic Manager의 인스턴스를 설정한 다음, DNS 이름을 해당 Azure Traffic Manager 인스턴스와 연결합니다. 그런 다음, 각 지역 내의 개별 네트워크 부하 분산 장치로 라우팅하도록 Azure Traffic Manager를 구성할 수 있습니다. 

4. 두 지역을 모두 유지하려는 경우 일반적으로 일종의 “백업 동기화”가 있을 수 있습니다. 이 경우 데이터 소스는 SQL, CosmosDB, Blob 또는 File Storage 같은 일부 원격 저장소에 유지되었다가 지역 간에 동기화됩니다. 워크로드에 적용되는 경우 지역 간에 데이터가 예상대로 흐르는지 확인하는 것이 좋습니다.  

## <a name="final-validation"></a>최종 유효성 검사
1. 최종 유효성 검사로 트래픽이 예상대로 흐르는지, 새 지역(및 잠재적으로 이전 지역)의 서비스가 예상대로 작동하는지 확인합니다. 

2. 원래 원본 지역을 유지하지 않으려는 경우 이 단계에서 해당 지역의 리소스를 제거할 수 있습니다. 원래 원본 지역으로 롤백해야 하는 몇 가지 문제가 발생하는 경우 잠시 기다렸다가 리소스를 삭제하는 것이 좋습니다.  

## <a name="next-steps"></a>다음 단계
이제 클러스터 및 애플리케이션을 새 지역으로 이동했으므로 필요한 데이터를 보호하기 위해 백업이 설정되어 있는지 확인해야 합니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 백업 설정](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
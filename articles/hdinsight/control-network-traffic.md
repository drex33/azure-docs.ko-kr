---
title: Azure HDInsight에서 네트워크 트래픽 제어
description: Azure HDInsight 클러스터에 대한 인바운드 및 아웃바운드 트래픽을 제어하는 기법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 78d61c1d775b2e710448283e283252b1cb85c802
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857895"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Azure HDInsight에서 네트워크 트래픽 제어

Azure Virtual Networks의 네트워크 트래픽은 다음 방법을 사용하여 제어할 수 있습니다.

* **NSG(네트워크 보안 그룹)** 를 통해 네트워크로의 인바운드 및 아웃바운드 트래픽을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/network-security-groups-overview.md) 문서를 참조하세요.

* **NVA**(네트워크 가상 어플라이언스)는 아웃바운드 트래픽에만 사용할 수 있습니다. NVA는 방화벽 및 라우터와 같은 디바이스의 기능을 복제합니다. 자세한 내용은 [네트워크 어플라이언스](https://azure.microsoft.com/solutions/network-appliances) 문서를 참조하세요.

관리형 서비스인 HDInsight는 VNET을 드나드는 트래픽의 HDInsight 상태 및 관리 서비스에 제한 없이 액세스할 수 있어야 합니다. NSG를 사용하는 경우 이러한 서비스가 HDInsight 클러스터와 계속 통신할 수 있는지 확인해야 합니다.

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Azure 사용자 지정 VNET에서 만든 HDInsight 엔터티 다이어그램" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>네트워크 보안 그룹을 사용한 HDInsight

네트워크 트래픽을 제어하는 데 **네트워크 보안 그룹** 을 사용할 계획인 경우 HDInsight를 설치하기 전에 다음 작업을 수행합니다.

1. HDInsight에 대해 사용할 Azure 지역을 식별합니다.

2. 해당 지역에서 HDInsight에 필요한 서비스 태그를 확인합니다. 이러한 서비스 태그를 가져오는 방법에는 여러 가지가 있습니다.
    1. [Azure HDInsight에 대한 NSG (네트워크 보안 그룹) 서비스 태그](hdinsight-service-tags.md)에서 게시된 서비스 태그의 목록을 참조하세요. 
    2. 지역이 목록에 없는 경우 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api)를 사용하여 해당 지역에 대한 서비스 태그를 찾습니다.
    3. API를 사용할 수 없는 경우 [서비스 태그 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 다운로드하고 원하는 지역을 검색합니다.


3. HDInsight를 설치하려는 서브넷에 대한 네트워크 보안 그룹을 만들거나 수정합니다.

    * __네트워크 보안 그룹__: IP 주소에서 포트 __443__ 에 __인바운드__ 트래픽을 허용합니다. 이렇게 하면 HDInsight 관리 서비스가 가상 네트워크 외부에서 클러스터에 도달할 수 있습니다. __Kafka REST 프록시__ 사용 클러스터의 경우 포트 __9400__ 에서 __인바운드__ 트래픽도 허용합니다. 이로써 Kafka REST 프록시 서버에 도달할 수 있습니다.

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/network-security-groups-overview.md)를 참조하세요.

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight 클러스터에서 아웃바운드 트래픽 제어

HDInsight 클러스터에서 아웃바운드 트래픽을 제어하는 방법에 대한 자세한 내용은 [Azure HDInsight 클러스터에 대한 아웃바운드 네트워크 트래픽 제한 구성](hdinsight-restrict-outbound-traffic.md)을 참조하세요.

### <a name="forced-tunneling-to-on-premises"></a>온-프레미스에 강제 터널링

강제 터널링은 서브넷의 모든 트래픽이 특정 네트워크 또는 위치(예: 온-프레미스 네트워크 또는 방화벽)로 적용되는 사용자 정의 라우팅 구성입니다. 온-프레미스로 다시 전송되는 모든 데이터의 강제 터널링은 대용량의 데이터 전송 및 잠재적인 성능 영향 때문에 ‘권장되지 않습니다’.

강제 터널링을 설정하려는 고객은 [사용자 지정 메타스토어](./hdinsight-use-external-metadata-stores.md)를 사용하고 클러스터 서브넷 또는 온-프레미스 네트워크에서 이러한 사용자 지정 메타스토어에 적절한 연결을 설정해야 합니다.

Azure Firewall을 사용한 UDR 설정의 예를 보려면 [Azure HDInsight 클러스터에 대한 아웃바운드 네트워크 트래픽 제한 구성](hdinsight-restrict-outbound-traffic.md)을 참조하세요.

## <a name="required-ports"></a>필수 포트

**방화벽** 을 사용하여 특정 포트 외부에서 클러스터에 액세스하려는 경우 시나리오에 필요한 포트에서 트래픽을 허용해야 합니다. 이전 섹션에서 설명한 Azure 관리 트래픽이 포트 443의 클러스터에 도달할 수 있는 한 기본적으로 포트에 대한 특별한 필터링이 필요하지 않습니다.

특정 서비스에 대한 포트 목록은 [HDInsight의 Apache Hadoop 서비스에서 사용되는 포트](hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

가상 어플라이언스의 방화벽 규칙에 대한 자세한 내용은 [가상 어플라이언스 시나리오](../virtual-network/virtual-network-scenario-udr-gw-nva.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 코드 샘플 및 Azure 가상 네트워크 만들기 예제는 [Azure HDInsight 클러스터에 대한 가상 네트워크 만들기](hdinsight-create-virtual-network.md)를 참조하세요.
* 온-프레미스 네트워크에 연결하기 위해 HDInsight를 구성하는 엔드투엔드 예제는 [HDInsight를 온-프레미스 네트워크에 연결](./connect-on-premises-network.md)을 참조하세요.
* Azure 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.
* 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.
* 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.
* 가상 네트워크에 대한 자세한 내용은 [HDInsight에 대한 VNET 계획](./hdinsight-plan-virtual-network-deployment.md)을 참조하세요.

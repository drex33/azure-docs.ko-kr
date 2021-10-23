---
title: Azure HDInsight에서 네트워크 가상 어플라이언스 구성
description: Azure HDInsight에서 네트워크 가상 어플라이언스에 대한 다양 한 추가 기능을 구성하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: f92dc402323e8285f9a2c23ba9e4a229d72718a0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254776"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight에서 네트워크 가상 어플라이언스 구성

> [!Important]
> 다음 정보는 [Azure Firewall](./hdinsight-restrict-outbound-traffic.md) 이외의 NVA(네트워크 가상 어플라이언스)를 구성하려는 경우에 **만** 필요합니다.

Azure Firewall FQDN 태그는 여러 가지 중요한 FQDN에 대한 트래픽을 허용하도록 자동으로 구성됩니다. 다른 네트워크 가상 어플라이언스를 사용하려면 몇 가지 추가 기능을 구성해야 합니다. 네트워크 가상 어플라이언스를 구성할 때 다음 요소를 염두에 두어야 합니다.

* 일반적으로 비용 또는 성능 고려 사항에 대해 NVA를 우회 하는 서비스 끝점을 사용 하 여 서비스 끝점을 구성할 수 있습니다.
* ResourceProviderConnection이 ‘outbound’로 설정된 경우 스토리지로 프라이빗 엔드포인트를 사용하고 메타스토어로 SQL 서버를 사용할 수 있으며 NVA에 이러한 항목을 추가하지 않아도 됩니다.
* IP 주소 종속성은 HTTP/S가 아닌 트래픽(TCP 및 UDP 모두)에 대한 것입니다.
* FQDN HTTP/HTTPS 엔드포인트는 NVA 디바이스에서 승인될 수 있습니다.
* 만든 경로 테이블을 HDInsight 서브넷에 할당합니다.

## <a name="service-endpoint-capable-dependencies"></a>서비스 엔드포인트 지원 종속성

필요에 따라 NVA를 우회하는 다음 서비스 엔드포인트를 하나 이상 사용하도록 설정할 수 있습니다. 이 옵션은 비용을 절약하고 성능 최적화를 위해 많은 양의 데이터를 전송하는 데 유용할 수 있습니다. 

| **엔드포인트** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 주소 종속성

| **엔드포인트** | **세부 정보** |
|---|---|
| [여기](hdinsight-management-ip-addresses.md)에 게시된 IP | 이러한 IP는 HDInsight 리소스 공급자를 위한 것이며 비대칭 라우팅을 방지하기 위해 UDR에 포함해야 합니다. 이 규칙은 ResourceProviderConnection이 *인바운드* 로 설정된 경우에만 필요합니다. ResourceProviderConnection이 *아웃바운드* 로 설정된 경우 이러한 IP는 UDR에 필요하지 않습니다.  |
| AAD-DS 개인 IP | VNET이 피어링되지 않은 경우 ESP 클러스터에만 필요합니다.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성

[이](https://github.com/Azure-Samples/hdinsight-fqdn-lists/)리포지토리에서 네트워크 가상 어플라이언스를 구성 하기 위해 종속 fqdn (대부분 Azure Storage 및 Azure Service Bus)의 목록을 가져올 수 있습니다. 지역 목록에 대해서는 [여기](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/main/Public)를 참조하세요. 이러한 종속성은 HDInsight RP(리소스 공급자)에서 클러스터를 성공적으로 만들고 모니터링/관리하는 데 사용됩니다. 여기에는 원격 분석/진단 로그, 프로 비전 메타 데이터, 클러스터 관련 구성, 스크립트 등이 포함 됩니다. 이 FQDN 종속성 목록은 향후 HDInsight 업데이트 해제로 변경 될 수 있습니다.

아래 목록에서는 클러스터를 만든 이후 및 클러스터 작업의 수명 동안 OS 및 보안 패치나 인증서 유효성 검사에 필요할 수 있는 몇 가지 FQDN만 제공합니다.

| **런타임 종속성 FQDN**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>다음 단계

* [아웃바운드 트래픽을 제한하는 방화벽 사용](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)

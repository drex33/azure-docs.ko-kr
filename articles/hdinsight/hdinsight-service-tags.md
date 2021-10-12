---
title: Azure HDInsight용 NSG(네트워크 보안 그룹) 서비스 태그
description: HDInsight 서비스 태그를 사용하여 NSG에 IP 주소를 추가하지 않고도 상태 및 관리 서비스 노드에서 클러스터로 인바운드 트래픽을 허용할 수 있습니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 06/23/2021
ms.openlocfilehash: 988e3249580408147b74409856bea1e45df4e966
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857116"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight용 NSG 서비스 태그

NSG(네트워크 보안 그룹)에 Azure HDInsight 서비스 태그는 상태 및 관리 서비스를 위한 IP 주소 그룹입니다. 이러한 그룹은 보안 규칙 만들기의 복잡성을 최소화하는 데 유용합니다. [서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags)를 사용하여 NSG에 각각의 [관리 IP 주소](hdinsight-management-ip-addresses.md)를 입력하지 않고도 특정 IP에서 인바운드 트래픽을 허용합니다.

HDInsight 서비스는 이러한 서비스 태그를 관리합니다. 사용자 고유의 서비스 태그를 만들거나 기존 태그를 수정할 수는 없습니다. Microsoft는 서비스 태그에 맞는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

특정 지역을 사용하고 서비스 태그가 이 페이지에 아직 문서화되지 않은 경우 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api)를 사용하여 서비스 태그를 찾을 수 있습니다. 그뿐만 아니라 [서비스 태그 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 다운로드해서 원하는 지역을 검색할 수도 있습니다.

## <a name="get-started-with-service-tags"></a>서비스 태그 시작

네트워크 보안 그룹에서 서비스 태그를 사용하는 방법에는 두 가지가 있습니다.

- **단일 글로벌 HDInsight 서비스 태그 사용**: 이 옵션은 HDInsight 서비스가 모든 지역에서 클러스터를 모니터링하는 데 사용하는 모든 IP 주소에 가상 네트워크를 개방합니다. 이 옵션은 가장 간단한 방법이지만 엄격한 보안 요구 사항을 적용하는 경우에는 적합하지 않을 수 있습니다.

- **여러 지역 서비스 태그 사용**: 이 옵션은 HDInsight가 해당 특정 지역에서 사용하는 IP 주소에만 가상 네트워크를 개방합니다. 그러나 여러 지역을 사용하는 경우 가상 네트워크에 여러 서비스 태그를 추가해야 합니다.

## <a name="use-a-single-global-hdinsight-service-tag"></a>단일 전체 HDInsight 서비스 태그 사용

HDInsight 클러스터에 서비스 태그 사용을 시작하는 가장 쉬운 방법은 NSG 규칙에 `HDInsight` 전체 태그를 추가하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에서 네트워크 보안 그룹을 선택합니다.

1. **설정** 아래에서 **인바운드 보안 규칙** 을 선택한 다음, **추가** 를 선택합니다.

1. **원본** 드롭다운 목록에서 **서비스 태그** 를 선택합니다.

1. **원본 서비스 태그** 드롭다운 목록에서 **HDInsight** 를 선택합니다.

    :::image type="content" source="./media/hdinsight-service-tags/azure-portal-add-service-tag.png" alt-text="Azure Portal에서 서비스 태그를 추가합니다":::

이 태그에는 HDInsight를 사용할 수 있는 모든 지역의 상태 및 관리 서비스 IP 주소가 포함됩니다. 태그를 사용하면 클러스터가 만들어진 위치와 상관 없이 클러스터가 필요한 상태 및 관리 서비스와 통신할 수 있습니다.

## <a name="use-regional-hdinsight-service-tags"></a>지역 HDInsight 서비스 태그 사용

더 엄격한 권한이 필요해서 전체 태그 옵션이 작동하지 않는 경우 해당 지역에 적용되는 서비스 태그만 허용할 수 있습니다. 클러스터가 만들어지는 지역에 따라 여러 서비스 태그가 있을 수 있습니다.

해당 지역에 추가할 서비스 태그를 확인하려면 문서의 다음 섹션을 참조하세요.

### <a name="use-a-single-regional-service-tag"></a>단일 지역 서비스 태그 사용

이 표에 나열된 지역에 클러스터가 있는 경우 NSG에 단일 지역 서비스 태그를 추가하기만 하면 됩니다.

| 국가 | 지역 | 서비스 태그 |
| ---- | ---- | ---- |
| 오스트레일리아 | 오스트레일리아 동부 | HDInsight.AustraliaEast |
| &nbsp; | 오스트레일리아 남동부 | HDInsight.AustraliaSoutheast |
| &nbsp; | 오스트레일리아 중부 | HDInsight.AustraliaCentral |
| 브라질 | 브라질 남부 | HDInsight.BrazilSouth |
| &nbsp; | 브라질 남동부 | HDInsight.BrazilSoutheast |
| 중국 | 중국 동부 2 | HDInsight.ChinaEast2 |
| &nbsp; | 중국 북부 2 | HDInsight.ChinaNorth2 |
| 일본 | 일본 서부 | HDInsight.JapanWest |
| 한국 | 한국 중부 | HDInsight.KoreaCentral |
| &nbsp; | 한국 남부 | HDInsight.KoreaSouth |
| 인도 | 인도 중부 | HDInsight.CentralIndia |
| &nbsp; | JIO 인도 서부 | HDInsight.JioIndiaWest |
| &nbsp; | 인도 남부 | HDInsight.SouthIndia |
| 남아프리카 | 남아프리카 북부 | HDInsight.SouthAfricaNorth |
| 아랍에미리트 | 아랍에미리트 북부 | HDInsight.UAENorth |
| &nbsp; | 아랍에미리트 중부 | HDInsight.UAECentral |
| 프랑스 | 프랑스 중부| HDInsight.FranceCentral |
| 독일 | 독일 중서부| HDInsight.GermanyWestCentral |
| 노르웨이 | 노르웨이 동부 | HDInsight.NorwayEast |
| 스위스 | 스위스 북부 | HDInsight.SwitzerlandNorth |
| &nbsp; | 스위스 서부 | HDInsight.SwitzerlandWest |
| 영국 | 영국 남부 | HDInsight.UKSouth |
| &nbsp; | 영국 서부 | HDInsight.UKWest |
| 미국 | 미국 중북부 | HDInsight.NorthCentralUS |
| &nbsp; | 미국 서부 2 | HDInsight.WestUS2 |
| &nbsp; | 미국 서부 3 | HDInsight.WestUS3 |
| &nbsp; | 미국 중서부 | HDInsight.WestCentralUS |
| Canada | 캐나다 동부 | HDInsight.CanadaEast |
| &nbsp; | 캐나다 중부 | HDInsight.CanadaCentral |
| Azure Government | 미국 국방부 중부 | HDInsight.USDoDCentral |
| &nbsp; | USGov 텍사스 | HDInsight.USGovTexas |
| &nbsp; | UsDoD 동부 | HDInsight.USDoDEast |
| &nbsp; | USGov 애리조나 | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>여러 지역 서비스 태그 사용

클러스터가 만들어진 지역이 앞의 표의 목록에 없는 경우 여러 지역 서비스 태그를 허용해야 합니다. 다양한 지역의 리소스 공급자마다 방식의 차이가 있으므로 둘 이상의 지역을 사용해야 합니다.

나머지 지역은 사용하는 지역 서비스 태그에 따라 그룹으로 구분됩니다.

#### <a name="group-1"></a>그룹 1

다음 표에 있는 지역 중 하나에서 클러스터를 만든 경우 서비스 태그 `HDInsight.WestUS`과 `HDInsight.EastUS`를 허용하세요. 또한 지역 서비스 태그가 나열됩니다. 이 섹션의 지역에는 세 가지 서비스 태그가 필요합니다.

예를 들어 클러스터를 `East US 2` 지역에서 만든 경우 네트워크 보안 그룹에 다음 서비스 태그를 추가해야 합니다.

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| 국가 | 지역 | 서비스 태그 |
| ---- | ---- | ---- |
| 미국 | 미국 동부 2 | HDInsight.EastUS2 |
| &nbsp; | 미국 중부 | HDInsight.CentralUS |
| &nbsp; | NorthCentral US | HDInsight. NorthCentralUS |
| &nbsp; | 미국 중남부 | HDInsight.SouthCentralUS |
| &nbsp; | 미국 동부 | HDInsight.EastUS |
| &nbsp; | 미국 서부 | HDInsight.WestUS |
| 일본 | 일본 동부 | HDInsight.JapanEast |
| 유럽 | 북유럽 | HDInsight.NorthEurope |
| &nbsp; | 서유럽| HDInsight.WestEurope |
| 아시아 | 동아시아 | HDInsight.EastAsia |
| &nbsp; | 동남아시아 | HDInsight.SoutheastAsia |
| 오스트레일리아 | 오스트레일리아 동부 | HDInsight.AustraliaEast |

#### <a name="group-2"></a>그룹 2

*중국 북부* 및 *중국 동부* 지역의 클러스터는 `HDInsight.ChinaNorth`와 `HDInsight.ChinaEast`라는 두 가지 서비스 태그를 허용해야 합니다.

#### <a name="group-3"></a>그룹 3

*US Gov 아이오와* 및 *US Gov 버지니아* 지역의 클러스터는 `HDInsight.USGovIowa`와 `HDInsight.USGovVirginia`라는 두 가지 서비스 태그를 허용해야 합니다.

#### <a name="group-4"></a>그룹 4

*독일 중부* 및 *독일 북동부* 지역의 클러스터는 `HDInsight.GermanyCentral`와 `HDInsight.GermanyNortheast`라는 두 가지 서비스 태그를 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹: 서비스 태그](../virtual-network/network-security-groups-overview.md#security-rules)
- [Azure HDInsight 클러스터에 가상 네트워크 만들기](hdinsight-create-virtual-network.md)

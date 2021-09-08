---
title: '연결 공급자 및 위치: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 연결 공급자에 따라 정렬됩니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 538f5cbb66cd76c31bf89db70417fa64e64e63cd
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123213889"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>ExpressRoute 연결 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서에는 ExpressRoute 지리적 범위 및 위치, ExpressRoute 연결 공급자 및 ExpressRoute SI(시스템 통합 업체)에 대한 정보를 제공하는 표가 있습니다.

> [!Note]
> Azure 지역 및 ExpressRoute 위치는 서로 다른 두 가지 개념이며, 두 가지 간의 차이점을 이해하는 것은 Azure 하이브리드 네트워킹 연결을 탐색하는 데 중요합니다. 
>
>

## <a name="azure-regions"></a>Azure 지역
Azure 지역은 Azure 컴퓨팅, 네트워킹 및 스토리지 리소스가 있는 글로벌 데이터 센터입니다. Azure 리소스를 만들 때 고객은 리소스 위치를 선택해야 합니다. 리소스 위치는 리소스를 만들 Azure 데이터 센터(또는 가용성 영역)를 결정합니다.

## <a name="expressroute-locations"></a>ExpressRoute 위치
ExpressRoute 위치(예: 피어링 위치 또는 Meet-Me 위치)는 MSEE(Microsoft Enterprise 에지) 디바이스가 있는 공동 배치 시설입니다. ExpressRoute 위치는 Microsoft 네트워크에 대한 진입점으로, 전역적으로 분산되어 있어 전 세계의 Microsoft 네트워크에 연결할 수 있는 기회를 고객에게 제공합니다. 이러한 위치는 ExpressRoute 파트너와 ExpressRoute Direct 고객이 Microsoft 네트워크에 대한 교차 연결이 발생하는 위치입니다. 일반적으로 ExpressRoute 위치는 Azure 지역과 일치하지 않아도 됩니다. 예를 들어, 고객은 *시애틀* 피어링 위치에서 *미국 동부* 리소스 위치로 ExpressRoute 회로를 만들 수 있습니다.

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다.

[!INCLUDE [expressroute-azure-regions-geopolitical-region](../../includes/expressroute-azure-regions-geopolitical-region.md)]

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 연결 공급자

다음 테이블에서는 서비스 공급자별 위치를 보여 줍니다. 위치별 사용 가능한 공급자를 보려는 경우 [위치별 서비스 공급자](expressroute-locations-providers.md)를 참조하세요.


### <a name="global-commercial-azure"></a>글로벌 상업용 Azure

| **서비스 공급자** | **Microsoft Azure** | **Microsoft 365**  | **위치** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Airtel](https://www.airtel.in/business/#/)** | 지원됨 | 지원됨 | 첸나이2, 뭄바이2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | 지원됨 | 지원됨 | 방콕 |
| **[Aryaka Networks](https://www.aryaka.com/)** |지원됨 |지원됨 |암스테르담, 시카고, 달라스, 홍콩 특별 행정구, 상파울루, 시애틀, 실리콘밸리, 싱가포르, 도쿄, 워싱턴 DC |
| **[Ascenty Data Cente](https://www.ascenty.com/en/cloud/microsoft-express-route)** |지원됨 |지원 여부 | 캄피나스, 상파울루 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 프랑크푸르트, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 토론토, 워싱턴 DC |
| **[AT TOKYO](https://www.attokyo.com/connectivity/azure.html)** | 지원됨 | 지원 여부 | 오사카, 도쿄2 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/bics-cloud-connect-an-official-microsoft-azure-technology-partner/)** | 지원 여부 | 지원 여부 | 암스테르담2, 런던2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | 지원됨 | 지원됨 | 오사카, 도쿄 |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |지원됨 |지원됨 |케이프타운, 요하네스버그|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |지원됨 |지원 여부 |몬트리올, 토론토, 퀘벡 시티, 밴쿠버 |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |지원됨 |지원 여부 |암스테르담, 암스테르담2, 시카고, 프랑크푸르트, 홍콩 특별 행정구, 요하네스버그, 런던, 런던2, 뉴포트(웨일스), 파리, 상파울루, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[BSNL](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |지원 여부 |지원됨 |첸나이, 뭄바이 |
| **[C3ntro](https://www.c3ntro.com/)** |지원됨 |지원됨 |마이애미 |
| **CDC** | 지원됨 | 지원됨 | 캔버라, 캔버라2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |지원됨 |지원 여부 |암스테르담2, 시카고, 더블린, 프랑크푸르트, 홍콩, 라스베이거스, 런던, 런던2, 뉴욕, 파리, 샌안토니오, 실리콘밸리, 싱가포르2, 도쿄, 토론토, 워싱턴 DC, 워싱턴 DC2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |지원됨 |지원됨 |홍콩, 타이베이 |
| **China Mobile International** |지원됨 |지원 여부 | 홍콩, 홍콩2, 싱가포르 |
| **China Telecom Global** |지원됨 |지원됨 |홍콩, 홍콩2 |
| **[China Unicom Global](https://cloudbond.chinaunicom.cn/home-en)** |지원 여부 |지원 여부 | 홍콩, 싱가포르2, 도쿄2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |지원됨 |지원됨 |타이베이 |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |지원됨 |지원됨 |마이애미 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |지원됨 |지원 여부 |시카고, 댈러스, 미니애폴리스, 몬트리올, 토론토, 밴쿠버, 워싱턴 DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |지원됨 |지원 여부 |암스테르담, 암스테르담2, 베를린, 시카고, 더블린, 프랑크푸르트, 제네바, 홍콩, 런던, 런던2, 마르세유, 밀라노, 뮌헨, 뉴포트, 뉴욕, 오사카, 파리, 실리콘밸리, 실리콘밸리2, 싱가포르2, 도쿄, 워싱턴 DC, 취리히 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |지원됨 |지원 여부 |시카고, 시카고2, 덴버, 로스앤젤레스, 뉴욕, 실리콘밸리, 실리콘밸리2, 워싱턴 DC, 워싱턴 DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | 지원됨 |지원 여부 |암스테르담2, 두바이2, 프랑크푸르트, 마르세유, 뭄바이, 뮌헨, 뉴욕 |
| **[Devoli](https://devoli.com/expressroute)** | 지원됨 |지원됨 | 오클랜드, 멜버른, 시드니 |
| **[Deutsche Telekom AG IntraSelect](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** | 지원 여부 |지원 여부 |프랑크푸르트 |
| **[Deutsche Telekom AG](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** | 지원 여부 |지원 여부 |Frankfurt2 |
| **du datamena** |지원됨 |지원됨 | 두바이2 |
| **eir** |지원됨 |지원됨 |더블린|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |지원됨 |지원됨 |싱가포르, 싱가포르 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원 여부 |암스테르담, 암스테르담2, 애틀랜타, 베를린, 보고타, 캔버라2, 시카고, 댈러스, 두바이2, 더블린, 프랑크푸르트, 프랑크푸르트2, 제네바, 홍콩 특별행정구, 런던, 런던2, 로스앤젤레스*, 로스앤젤레스2, 멜버른, 마이애미, 밀라노, 뉴욕, 오사카, 파리, 퀘백 시티, 리우데자네이루, 상파울로, 시애틀, 서울, 실리콘밸리, 싱가포르, 싱가포르2, 스톡홀름, 시드니, 도쿄, 토론토, 워싱턴 DC, 취리히</br></br> **새로운 ExpressRoute 회로는 로스앤젤레스의 Equinix에서 더 이상 지원되지 않습니다. 로스앤젤레스2에서 새 회로를 만드세요.* |
| **Etisalat UAE** |지원됨 |지원됨 |두바이|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |지원됨 |지원 여부 |암스테르담, 암스테르담2, 더블린, 프랑크푸르트, 런던 |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |지원 여부 |지원됨 |타이베이|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | 지원 여부 |지원 여부 |Milan|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |지원 여부 |지원 여부 |Montreal|
| **[GBI](https://www.gbiinc.com/microsoft-azure/)** |지원 여부 |지원 여부 |두바이2, 프랑크푸르트|
| **[GÉANT](https://www.geant.org/Networks)** |지원 여부 |지원 여부 |암스테르담, 암스테르담2, 더블린, 프랑크푸르트, 마르세유 |
| **[GlobalConnect](https://www.globalconnect.no/tjenester/nettverk/cloud-access)** | 지원 여부 |지원 여부 | 오슬로, 스타방에르 | 
| **GTT** |지원 여부 |지원 여부 |런던2 |
| **[GCX(Global Cloud Xchange)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | 지원됨| 지원됨 | 첸나이, 뭄바이 |
| **[iAdvantage](https://www.scx.sunevision.com/)** | 지원 여부 | 지원 여부 | 홍콩2 |
| **Intelsat** | 지원됨 | 지원됨 | 워싱턴 DC2 |
| **[InterCloud](https://www.intercloud.com/)** |지원됨 |지원 여부 |암스테르담, 시카고, 프랑크푸르트, 홍콩, 런던, 뉴욕, 파리, 실리콘밸리, 싱가포르, 도쿄, 워싱턴 DC, 취리히 |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |지원됨 |지원됨 |시카고, 댈러스, 실리콘밸리, 워싱턴 DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |지원됨 |지원됨 |케이프타운, 요하네스버그, 런던 |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |지원됨 |지원 여부 |암스테르담, 암스테르담2, 코펜하겐, 더블린, 더블린2, 프랑크푸르트, 런던, 마드리드, 마르세이유, 파리, 취리히 |
| **[IRIDEOS](https://irideos.it/)** |지원 여부 |지원 여부 |Milan |
| **Iron Mountain** | 지원 여부 |지원됨 |워싱턴 DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|지원됨 |지원됨 | 암스테르담, 런던2, 실리콘밸리, 토론토, 워싱턴 DC |
| **Jaguar Network** |지원됨 |지원 여부 |마르세이유, 파리 |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |지원됨 |지원 여부 |런던, 뉴포트(웨일스) |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |지원됨 |지원됨 |서울 |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | 지원됨 |지원됨 |오클랜드, 시드니 |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | 지원됨 | 지원됨 | 암스테르담 |
| **[KT](https://cloud.kt.com/)** | 지원됨 | 지원됨 | 서울 |
| **[Level 3 Communications](https://www.lumen.com/en-us/hybrid-it-cloud/cloud-connect.html)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 뉴포트(웨일스), 상파울루, 시애틀, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **LG CNS** |지원됨 |지원됨 |부산, 서울 |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |지원됨 |지원됨 |케이프타운, 요하네스버그 |
| **[LGUplus](http://www.uplus.co.kr/)** |지원 여부 |지원됨 |서울 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 |지원 여부 |암스테르담, 애틀랜타, 오클랜드, 첸나이, 시카고, 댈러스, 덴버, 두바이2, 더블린, 프랑크푸르트, 제네바, 홍콩, 홍콩2, 라스베이거스, 런던, 런던2, 로스앤젤레스, 마드리드, 멜버른, 마이애미, 미니애폴리스, 몬트리올, 뮌헨, 뉴욕, 오사카, 오슬로, 파리, 퍼스, 피닉스, 퀘벡 시티, 샌안토니오, 시애틀, 실리콘밸리, 싱가포르, 싱가포르2, 스타방에르, 스톡홀름, 시드니, 시드니2, 도쿄, 도쿄2, 토론토, 밴쿠버, 워싱턴 DC, 워싱턴 DC2, 취리히 |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |지원됨 |지원됨 |London |
| **MTN Global Connect** |지원 여부 |지원 여부 |케이프타운, 요하네스버그|
| **[National Telecom](https://www.nc.ntplc.co.th/cat/category/264/855/CAT+Direct+Cloud+Connect+for+Microsoft+ExpressRoute?lang=en_EN)** |지원 여부 |지원됨 |방콕 |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |지원됨 |지원됨 |댈러스, 로스앤젤레스, 마이애미, 상파울루, 워싱턴 DC |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |지원됨 |지원됨 |뉴포트(웨일스) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |지원됨 |지원됨 |멜버른, 퍼스, 시드니, 시드니2 |
| **[NOS](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |지원 여부 |지원 여부 |암스테르담2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |지원됨 |지원됨 |암스테르담, 홍콩 특별 행정구, 런던, 로스앤젤레스, 오사카, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |지원됨 |지원됨 |도쿄 |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |지원됨 |지원 여부 |암스테르담2, 베를린, 프랑크푸르트, 런던2 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |지원됨 |지원됨 |오사카 |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |지원됨 |지원됨 |마르세이유 |
| **[Optus](https://www.optus.com.au/enterprise/)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |지원됨 |지원됨 |암스테르담, 암스테르담2, 두바이2, 프랑크푸르트, 홍콩 특별 행정구, 요하네스버그, 런던, 파리, 상파울루, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | 지원됨 | 지원됨 | 두바이2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |지원됨 |지원 여부 |시카고, 댈러스, 덴버, 라스베이거스, 실리콘밸리, 워싱턴 DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |지원됨 |지원됨 |시카고, 홍콩, 홍콩2, 런던, 싱가포르2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | 지원 여부 | 지원 여부 | 오클랜드 |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | 지원 여부 | 지원 여부 | Milan | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |지원됨 |지원됨 |서울 |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | 지원됨 |지원 여부 | 런던2, 워싱턴 DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |지원됨 |지원됨 |첸나이, 뭄바이2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |지원됨 |지원 여부 |홍콩2, 싱가포르, 싱가포르2 |
| **[SK 텔레콤](http://b2b.tworld.co.kr/bizts/solution/solutionTemplate.bs?solutionId=0085)** |지원 여부 |지원됨 |서울 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |지원 여부 |지원 여부 |런던2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |지원됨 |지원됨 |오클랜드, 시드니 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | 지원됨 | 지원 여부 | 제네바, 취리히 |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |지원됨 |지원됨 |암스테르담, 첸나이, 홍콩 특별 행정구, 런던, 뭄바이, 상파울루, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **[Telefonica](https://www.telefonica.com/es/home)** |지원됨 |지원됨 |암스테르담, 상파울루 |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |지원됨 |지원 여부 |런던, 런던2, 싱가포르2, 도쿄 |
| **Telenor** |지원됨 |지원됨 |암스테르담, 런던, 오슬로 |
| **[Telia Carrier](https://www.teliacarrier.com/)** | 지원됨 | 지원됨 |암스테르담, 시카고, 댈러스, 프랑크푸르트, 홍콩, 런던, 오슬로, 파리, 실리콘밸리, 스톡홀름, 워싱턴 DC |
| **[Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | 지원 여부 | 지원 여부 |자카르타 |
| **Telmex Uninet**| 지원됨 | 지원됨 | 댈러스 |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |지원됨 |지원됨 |멜버른, 싱가포르, 시드니 |
| **[Telus](https://www.telus.com)** |지원됨 |지원 여부 |몬트리올, 시애틀, 퀘벡 시티, 토론토, 밴쿠버 |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |지원됨 |지원됨 |케이프타운, 요하네스버그 |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | 지원됨 | 지원됨 | 콸라룸푸르 |
| **[Tokai Communications](https://www.tokai-com.co.jp/en/)** | 지원 여부 | 지원 여부 | 오사카, 도쿄2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |지원됨 |지원 여부 |댈러스, 케레타로(멕시코)|
| **[T 시스템](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** |지원 여부 |지원 여부 |프랑크푸르트|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |지원됨 |지원됨 |상파울루 |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | 지원 여부 | 지원됨 | 방콕 |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 홍콩 특별 행정구, 런던, 뭄바이, 실리콘밸리, 싱가포르, 시드니, 도쿄, 토론토, 워싱턴 DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | 지원됨 | 지원됨 | 워싱턴 DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | 지원됨 | 지원됨 | 오클랜드, 시드니 |
| **Vodacom** |지원 여부 |지원됨 |케이프타운, 요하네스버그|
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |지원됨 |지원됨 |암스테르담2, 런던, 싱가포르 |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | 지원됨 | 지원 여부 | 뭄바이2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |지원됨 |지원 여부 |암스테르담, 시카고, 댈러스, 덴버, 런던, 로스앤젤레스, 몬트리올, 뉴욕, 파리, 피닉스, 시애틀, 실리콘밸리, 토론토, 워싱턴 DC, 워싱턴 DC2 |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environment"></a>국가별 클라우드 환경

Azure 국가별 클라우드는 서로 격리되며 전 세계 상용 Azure에서 격리됩니다. 한 Azure 클라우드의 ExpressRoute는 다른 Azure 지역에 연결할 수 없습니다. 

### <a name="us-government-cloud"></a>미국 정부 클라우드

| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |시카고, Phoenix, 실리콘밸리, 워싱턴 DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |지원됨 |지원됨 |뉴욕, Phoenix, 샌안토니오, 워싱턴 DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원 여부 |애틀란타, 시카고, 댈러스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC |
| **[Internet2]()** |지원됨 |지원됨 |댈러스 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 | 지원됨 | 시카고, 댈러스, 샌안토니오, 시애틀, 워싱턴 DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 실리콘밸리, 워싱턴 DC |

### <a name="china"></a>중국

| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **China Telecom** |지원됨 |지원되지 않음 |베이징, 베이징2, 상하이, 상하이2 |
| **China Unicom** | 지원됨 | 지원되지 않음 | 베이징2, 상하이2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |지원됨 |지원되지 않음 |베이징2, 상하이2 |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)를 참조하세요.

### <a name="germany"></a>독일

| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |지원됨 |지원되지 않음 |베를린 |
| **Interxion** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨  | 지원되지 않음 | 베를린 |
| **[T 시스템](https://geschaeftskunden.telekom.de/vernetzung-digitalisierung/produkt/intraselect)** |지원됨 |지원되지 않음 |베를린 |

## <a name="connectivity-through-exchange-providers"></a>Exchange 공급자를 통한 연결

연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix 클라우드 Exchange](https://www.equinix.com/interconnection-services/equinix-fabric)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-satellite-operators"></a>위성 운영자를 통한 연결
원격으로 파이버 연결이 없거나 다른 연결 옵션을 탐색하려는 경우 다음 위성 운영자를 확인할 수 있습니다. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>추가 서비스 공급자를 통한 연결

| **연결 공급자** | **Exchange** | **위치** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |싱가포르 |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | 토론토, 몬트리올 |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |뉴욕, 워싱턴 DC |
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | 몬트리올, 토론토 |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |도쿄 |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |댈러스|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://www.bics.com/services/capacity-solutions/cloud-connect/)** | Equinix | 암스테르담, 프랑크푸르트, 런던, 싱가포르, 워싱턴 DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 도쿄 |
| **[C3ntro Telecom](https://www.c3ntro.com/)** | Equinix, Megaport | 댈러스 |
| **[Chief](https://www.chief.com.tw/)** | Equinix | 홍콩 특별 행정구 |
| **[Cinia](https://www.cinia.fi/palvelutiedotteet)** | Equinix, Megaport | 프랑크푸르트, 함부르크 |
| **[CloudXpress](https://www2.telenet.be/content/www-telenet-be/fr/business/sme-le/aanbod/internet/cloudxpress)** | Equinix | 암스테르담 | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | 싱가포르 | 
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | 댈러스, 실리콘밸리, 워싱턴 DC |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | 애틀랜타, 시카고, 댈러스, 로스앤젤레스, 뉴욕, 워싱턴 DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 댈러스 |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | 런던, 싱가포르, 워싱턴 DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 암스테르담 |
| **[지수 E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 암스테르담 |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | 퀘벡 시티 |
| **[FPT Telecom International](https://cloudconnect.vn/en)** |Equinix |싱가포르|
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | 워싱턴 DC |
| **[걸프 브리지 인터내셔널](https://gbiinc.com/)** | Equinix | 암스테르담 |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | 런던, 슬라우 |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |암스테르담 |
| **LGA Telecom** |Equinix |싱가포르|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |홍콩 특별 행정구 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 시드니 |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | 암스테르담 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 워싱턴 DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | 케이프타운, 요하네스버그 |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | 암스테르담, 프랑크푸르트 |
| **[Oncore Cloud Services Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | 암스테르담 |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | 암스테르담, 더블린, 런던, 파리 |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | 프랑크푸르트 |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | 암스테르담 |
| **Rogers** | Cologix, Equinix | 몬트리올, 토론토 |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | 시카고, 댈러스, 로스앤젤레스, 뉴욕, 실리콘밸리 | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[Tata Teleservices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | 첸나이, 뭄바이 |
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | 암스테르담 | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | 암스테르담 |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | 암스테르담, 프랑크푸르트 |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | 암스테르담 |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway(UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | 싱가포르 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | 상파울루 |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | 뉴욕 |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | 시카고, 실리콘밸리, 워싱턴 DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |실리콘밸리, 실리콘밸리2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Level 3 | 마드리드 |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | 몬트리올, 토론토 |

## <a name="connectivity-through-datacenter-providers"></a>데이터 센터 공급자를 통한 연결

| **공급자** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire 데이터 센터](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[T5 데이터 센터](https://t5datacenters.com/)** | IX Reach |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>NREN(National Research and Education Networks)을 통한 연결

| **공급자**|
| --- |
| **AARNET**| 
| **DeIC(GÉANT를 통해)**|
| **GARR(GÉANT를 통해)**|
| **GÉANT**|
| **HEAnet(GÉANT를 통해)**|
| **Internet2**|
| **JISC**|
| **RedIRIS(GÉANT를 통해)**|
| **SINET**|
| **Surfnet(GÉANT를 통해)**|

* 연결 공급자가 목록에 없는 경우 위에 나열된 ExpressRoute Exchange 파트너 중 하나에 연결되어 있는지 확인해 주세요.

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 프라이빗 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **시스템 통합 업체** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | 유럽 |
| **[Avanade Inc.](https://www.avanade.com/)** | 아시아, 유럽, 북아메리카, 남아메리카 |
| **[Bright Skies GmbH](https://www.rackspace.com/bright-skies)** | 유럽
| **[Ensyst](https://www.ensyst.com.au)** | 아시아
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | 북아메리카 |
| **[FlexManage](https://www.flexmanage.com/cloud)** | 북아메리카 |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | 북아메리카 |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | 오스트레일리아 |
| **[MOQdigital](https://www.moqdigital.com/insights)** | 오스트레일리아 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 유럽(독일) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | 유럽 |
| **[새 서명](https://newsignature.com/technologies/express-route/)** | 유럽 |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | 아시아 |
| **[Orange Networks](https://www.orange-networks.com/blog/88-azureexpressroute)** | 유럽 |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 북아메리카 |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | 북아메리카 |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | 유럽 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | 남아메리카 |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | 오스트레일리아 |

## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"

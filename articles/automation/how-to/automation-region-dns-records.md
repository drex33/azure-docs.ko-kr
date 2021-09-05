---
title: Azure Automation에서 사용하는 Azure 데이터 센터 DNS 레코드 | Microsoft Docs
description: 이 문서에서는 해당 Automation 계정을 호스트하는 특정 Azure 지역에 대한 통신을 제한하는 경우 Azure Automation 기능에 필요한 DNS 레코드를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 06/29/2021
ms.topic: conceptual
ms.openlocfilehash: 04c7b6cc8cbcaaadf2002c5b7b00cdaab52913a6
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113491824"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Azure Automation에서 사용되는 Azure 지역에 대한 DNS 레코드

[Azure Automation](../automation-intro.md) 서비스는 서비스에 연결하는 기능을 위해 여러 DNS 레코드를 사용합니다. 특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 방화벽 뒤에서 호스트되는 경우 다음과 같은 Automation 기능을 사용할 수 있도록 해당 레코드를 알아야 할 수 있습니다.

* Hybrid Runbook Worker
* 상태 구성
* Webhook

>[!NOTE]
>Linux Hybrid Runbook Worker 등록은 버전이 1.6.10.2 이상이 아닌 경우 새 레코드를 사용하면 실패합니다. 컴퓨터에서 작업자 역할의 업데이트된 버전을 받고 이러한 새 레코드를 사용하려면 최신 버전의 [Linux용 Log Analytics 에이전트](../../azure-monitor/agents/agent-linux.md)로 업그레이드해야 합니다. 기존 컴퓨터는 문제없이 계속 작동합니다.  

## <a name="dns-records-per-region"></a>지역별 DNS 레코드

다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

>[!NOTE]
>여기에서 제공하는 Automation DNS 레코드의 목록은 사용이 중지되었지만 [Private Link 지원](#support-for-private-link)에 나열된 새 레코드로 마이그레이션할 수 있는 시간을 허용하고 자동화 프로세스의 실패를 방지하기 위해 계속 작동합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 오스트레일리아 중부 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동부 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 스위스 북부 |stzn-jobruntimedata-prod-su1.azure-automation.net</br>stzn-agentservice-prod-su1.azure-automation.net|
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 미국 서부 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Private Link에 대한 지원

Azure Automation의 [Private Link](../../private-link/private-link-overview.md)를 지원하기 위해 지원되는 모든 데이터 센터에 대한 DNS 레코드가 업데이트되었습니다. URL은 지역 특정 URL이 아닌 Automation 특정 계정입니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 남아프리카 북부 |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| 동아시아 |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| 동남아시아 |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| 오스트레일리아 중부 |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| 오스트레일리아 중부 2  |`https://<accountId>.webhook.cbr2.azure-automation.net`<br>`https://<accountId>.agentsvc.cbr2.azure-automation.net`<br>`https://<accountId>.jrds.cbr2.azure-automation.net` |
| 오스트레일리아 동남부 |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| 오스트레일리아 동부 |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| 브라질 남부 |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| 브라질 남동부 |`https://<accountId>.webhook.brse.azure-automation.net`<br>`https://<accountId>.agentsvc.brse.azure-automation.net`<br>`https://<accountId>.jrds.brse.azure-automation.net` |
| 캐나다 중부 |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| 중국 동부 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| 중국 북부 |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| 중국 북부 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| 서유럽 |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| 북유럽 |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| 프랑스 중부 |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| 프랑스 남부 |`https://<accountId>.webhook.mrs.azure-automation.net`<br>`https://<accountId>.agentsvc.mrs.azure-automation.net`<br>`https://<accountId>.jrds.mrs.azure-automation.net` |
| 독일 중서부 |`https://<accountId>.webhook.dewc.azure-automation.de`<br>`https://<accountId>.agentsvc.dewc.azure-automation.de`<br>`https://<accountId>.jrds.dewc.azure-automation.de` |
| 인도 중부 |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| 인도 남부  |`https://<accountId>.webhook.ma.azure-automation.net`<br>`https://<accountId>.agentsvc.ma.azure-automation.net`<br>`https://<accountId>.jrds.ma.azure-automation.net` |
| 일본 동부 |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| 일본 서부 |`https://<accountId>.webhook.jpw.azure-automation.net`<br>`https://<accountId>.agentsvc.jpw.azure-automation.net`<br>`https://<accountId>.jrds.jpw.azure-automation.net` |
| 한국 중부 |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| 한국 남부 |`https://<accountId>.webhook.ps.azure-automation.net`<br>`https://<accountId>.agentsvc.ps.azure-automation.net`<br>`https://<accountId>.jrds.ps.azure-automation.net` |
| 노르웨이 동부 |`https://<accountId>.webhook.noe.azure-automation.net`<br>`https://<accountId>.agentsvc.noe.azure-automation.net`<br>`https://<accountId>.jrds.noe.azure-automation.net` |
| 노르웨이 서부  |`https://<accountId>.webhook.now.azure-automation.net`<br>`https://<accountId>.agentsvc.now.azure-automation.net`<br>`https://<accountId>.jrds.now.azure-automation.net` |
| 스위스 서부 |`https://<accountId>.webhook.stzw.azure-automation.net`<br>`https://<accountId>.agentsvc.stzw.azure-automation.net`<br>`https://<accountId>.jrds.stzw.azure-automation.net` |
| 아랍에미리트 중부 |`https://<accountId>.webhook.auh.azure-automation.net`<br>`https://<accountId>.agentsvc.auh.azure-automation.net`<br>`https://<accountId>.jrds.auh.azure-automation.net` |
| 아랍에미리트 북부 |`https://<accountId>.webhook.uaen.azure-automation.net`<br>`https://<accountId>.agentsvc.uaen.azure-automation.net`<br>`https://<accountId>.jrds.uaen.azure-automation.net` |
| 영국 서부 |`https://<accountId>.webhook.cw.azure-automation.net`<br>`https://<accountId>.agentsvc.cw.azure-automation.net`<br>`https://<accountId>.jrds.cw.azure-automation.net` |
| 영국 남부 |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| 미국 중부 |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| 미국 동부 |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| 미국 동부 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| 미국 중북부 |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| 미국 중남부 |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| 미국 중서부 |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| 미국 서부 |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| 미국 서부 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| 미국 서부 3 |`https://<accountId>.webhook.usw3.azure-automation.net`<br>`https://<accountId>.agentsvc.usw3.azure-automation.net`<br>`https://<accountId>.jrds.usw3.azure-automation.net` |
| US Gov 버지니아 |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov 텍사스 |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov 애리조나 |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

DNS 레코드의 `<accountId>`를 값 **URL** 에서 Automation 계정 ID를 나타내는 GUID로 바꿉니다. Azure Portal의 **계정 설정** 아래에 있는 **키** 에서 필요한 ID를 얻을 수 있습니다.

![Automation 계정 기본 키 페이지](./media/automation-region-dns-records/automation-account-keys.png)

**URL** 필드 - `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`에서 *accounts/* 뒤의 값을 복사합니다.

> [!NOTE]
> Private Link를 지원하기 위해 모든 Webhook 및 agentservice DNS 레코드가 새 스타일의 DNS 레코드로 업데이트되었습니다. JRDS DNS 레코드의 경우 이전 스타일과 새 스타일 DNS 레코드 모두 지원됩니다. Private Link를 사용하지 않는 경우에는 이전 스타일의 DNS 레코드가 표시되고 Private Link를 사용하는 경우에는 새로운 스타일의 DNS 레코드가 표시됩니다.

[예외](../automation-runbook-execution.md#exceptions)를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. 지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 다음 클라우드 환경에 대한 JSON 파일을 다운로드하세요.

* [Azure IP 범위 및 서비스 태그 - Azure 퍼블릭](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP 범위 및 서비스 태그 - Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP 범위 및 서비스 태그 – Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP 범위 및 서비스 태그 - Azure 중국 21Vianet](https://www.microsoft.com/download/details.aspx?id=57062)

IP 주소 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이는 컴퓨팅, SQL 및 스토리지 범위를 포함하며 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 반영합니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.

새 IP 주소 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다.

> [!NOTE]
> Azure ExpressRoute 사용자는 IP 주소 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

## <a name="next-steps"></a>다음 단계

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](../troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.

* 상태 구성 문제를 해결하는 방법을 알아보려면 [상태 구성 문제 해결](../troubleshoot/desired-state-configuration.md)을 참조하세요.
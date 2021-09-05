---
title: SAP용 Microsoft Azure 인증 | Microsoft Docs
description: Azure 플랫폼의 SAP에 대한 현재 구성 및 인증의 업데이트된 목록입니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/21/2021
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: a367c3753ae6d02dd4267a0135960ed630ef78c0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720657"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure에서 실행되는 SAP 인증 및 구성

SAP와 Microsoft는 고객에게 상호 혜택을 주는 강력한 파트너 관계 속에서 장기간 함께 협력해 왔습니다. Microsoft는 Microsoft Azure가 SAP 작업을 실행할 최상의 플랫폼이 되도록 하기 위해 지속적으로 플랫폼을 업데이트하고 SAP에 새로운 인증 세부 정보를 제출하고 있습니다. 다음 표에서는 지원되는 Azure 구성 및 증가하는 SAP 인증 목록을 간략히 설명합니다. 해당 목록은 공식 SAP 목록에서 여기저기 상이한 부분이 있을 수 있는 개요 목록입니다. 상세한 데이터를 가져오는 방법은 [Azure 배포를 위해 지원되는 SAP 소프트웨어](./sap-supported-product-on-azure.md) 문서에 설명되어 있습니다.

## <a name="sap-hana-certifications"></a>SAP HANA 인증
참조:

- [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure): 네이티브 Azure VM 및 HANA 대규모 인스턴스에 대한 SAP HANA 지원

| SAP 제품 | 지원되는 OS | Azure 제품 |
| --- | --- | --- |
| HANA의 Business One | SUSE Linux Enterprise | [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:120) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:125) |
| HANA Enterprise for BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:105) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:105) |


## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 인증
Microsoft Azure는 다음과 같은 SAP 제품에서 인증되었고 Microsoft와 SAP의 전폭적인 지원을 받고 있습니다.
참조:

- [1928533 - Azure의 SAP 애플리케이션: 지원되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533): SAP TREX, SAP LiveCache 및 SAP Content Server를 포함한 모든 SAP NetWeaver 기반 애플리케이션 모든 데이터베이스(SAP HANA 제외)


| SAP 제품 | 게스트 OS | RDBMS | Virtual Machine 유형 |
| --- | --- | --- | --- |
| SAP Business Suite 소프트웨어 | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5~A11, D11~D14, DS11~DS14, DS11_v2~DS15_v2, GS1~GS5, D2s_v3~D64s_v3, D2as_v4~D64as_v4, E2s_v3~E64s_v3, E2as_v4~E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5~A11, D11~D14, DS11~DS14, DS11_v2~DS15_v2, GS1~GS5, D2s_v3~D64s_v3, D2as_v4~D64as_v4, E2s_v3~E64s_v3, E2as_v4~E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP BusinessObjects BI | Windows |해당 없음 |A5~A11, D11~D14, DS11~DS14, DS11_v2~DS15_v2, GS1~GS5, D2s_v3~D64s_v3, D2as_v4~D64as_v4, E2s_v3~E64s_v3, E2as_v4~E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5~A11, D11~D14, DS11~DS14, DS11_v2~DS15_v2, GS1~GS5, D2s_v3~D64s_v3, D2as_v4~D64as_v4, E2s_v3~E64s_v3, E2as_v4~E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Azure에서 지원되는 다른 SAP 워크로드

| SAP 제품 | 게스트 OS | RDBMS | Virtual Machine 유형 |
| --- | --- | --- | --- |
| SQL Server의 SAP Business One | Windows  | SQL Server | 모든 NetWeaver 인증 VM 유형<br /> [SAP 참고 #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows 및 Linux | | 모든 NetWeaver 인증 VM 유형<br /> SAP Note #2451795 |
| SAP 비즈니스 개체 BI 플랫폼 | Windows 및 Linux | | SAP Note #2145537 |
| SAP Data Services 4.2 | | | SAP Note #2288344 |
| SAP Hybris 상거래 플랫폼  | Windows | SQL Server, Oracle | 모든 NetWeaver 인증 VM 유형 <br /> [Hybris 설명서](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris 상거래 플랫폼  | SLES 12 이상 | SAP HANA | 모든 NetWeaver 인증 VM 유형 <br /> [Hybris 설명서](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris 상거래 플랫폼  | RHEL 7 이상 | SAP HANA | 모든 NetWeaver 인증 VM 유형 <br /> [Hybris 설명서]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP(Hybris) 상거래 플랫폼 1811 이상  | Windows나 SLES 또는 RHEL | SQL Azure DB | 모든 NetWeaver 인증 VM 유형 <br /> [Hybris 설명서](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |

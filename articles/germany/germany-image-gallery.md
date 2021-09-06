---
title: Azure 독일 이미지 | Microsoft Docs
description: 이 문서에서는 Azure 독일 Marketplace에 포함된 이미지의 개요를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: dd43ef428426037696fb2fcfdf6bac58c9b27dc4
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "122535456"
---
# <a name="azure-germany-images"></a>Azure 독일 이미지

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="overview"></a>개요
Azure 독일에서 새 가상 서버를 배포할 때 고객은 Microsoft에서 미리 빌드된 이미지를 배포하거나 자체 VHD를 업로드할 수 있습니다. 이러한 유연성 덕분에 필요한 경우 고유한 표준화된 이미지를 배포할 수 있습니다.

## <a name="variations"></a>변형
파트너의 Marketplace 이미지는 아직 Azure 독일에서 지원되지 않습니다.

다음은 Azure 독일 Marketplace에서 사용할 수 있는 이미지 목록입니다. 각 이미지에는 여기에 나열되지 않은 다른 버전이 있을 수 있습니다. 일부 미리 빌드된 이미지에는 특정 소프트웨어에 대한 종량제 라이선스가 포함되어 있습니다. 자세한 지침은 [Azure 가격 책정](https://azure.microsoft.com/pricing/) 페이지를 검토하고, Microsoft 계정 팀 또는 대리점에 문의하세요.

## <a name="images-for-deployments-with-azure-resource-manager"></a>Azure Resource Manager 배포용 이미지

|게시자|제안|SKU|버전|
| --- | --- | --- | --- |
| Canonical | UbuntuServer | 12.04.5-LTS | 12.04.201603150 12.04.201605160 12.04.201608290 12.04.201610201 12.04.201701100 |
| Canonical | UbuntuServer | 14.04.4-LTS | 14.04.201604060 14.04.201605160 14.04.201608300 |
| Canonical | UbuntuServer | 14.04.5-LTS | 14.04.201610200 14.04.201701100 |
| Canonical | UbuntuServer | 16.04-LTS | 16.04.201701130 |
| Canonical | UbuntuServer | 16.04.0-LTS | 16.04.201604203 16.04.201605161 16.04.201609071 16.04.201610200 |
| Canonical | UbuntuServer | 16.10 | 16.10.201701030 |
| cloudera | cloudera-centos-os | 6_7 | 1.0.1 |
| CoreOS | Container-Linux | 알파 |  |
| CoreOS | Container-Linux | 베타 |  |
| CoreOS | Container-Linux | Stable |  |
| CoreOS | CoreOS | 알파 | 1068.0.0 1081.2.0 1097.0.0 1122.0.0 1151.0.0 1153.0.0 1164.0.0 1164.1.0 1180.0.0 1185.0.0 1192.0.0 1192.1.0 1192.2.0 1207.0.0 1214.0.0 1221.0.0 1235.0.0 1248.0.0 1248.1.0 1262.0.0 1284.1.0 1284.2.0 1298.1.0 1325.1.0 1339.0.0 1353.1.0 |
| CoreOS | CoreOS | 베타 | 1010.4.0 1068.3.0 1081.3.0 1122.1.0 1153.3.0 1153.4.0 1185.1.0 1185.2.0 1192.2.0 1235.1.0 1235.2.0 1248.3.0 1248.4.0 1298.4.0 1325.2.0 1353.2.0 |
| CoreOS | CoreOS | Stable | 1010.5.0 1010.6.0 1068.10.0 1068.6.0 1068.8.0 1068.9.0 1122.2.0 1122.3.0 1185.3.0 1185.5.0 1235.12.0 1235.5.0 1235.6.0 1235.8.0 1298.5.0 1298.6.0 |
| credativ | Debian | 7 | 7.0.201604200 7.0.201606240 7.0.201606280 7.0.201609120 7.0.201611020 7.0.201701180 |
| credativ | Debian | 8 | 8.0.201604200 8.0.201606240 8.0.201606280 8.0.201609120 8.0.201611020 8.0.201701180 8.0.201703150 |
| credativ | Debian | 8-backports | 8.0.201702060 |
| credativ | Debian | 9-beta | 9.0.201702080 9.0.201703150 |
| GE-SRS-Prod-GalleryImages | Process-Server | Windows-2012-R2-Datacenter | 201703.01.00 |
| MicrosoftOSTC | FreeBSD | 10.3 | 10.3.20170112 |
| MicrosoftOSTC | FreeBSD | 11.0 | 11.0.20161223 11.0.20170111 |
| MicrosoftSharePoint | SharePoint2016 | SharePoint_Server_2016_Trial | 16.0.4351 |
| MicrosoftSQLServer | sql2014sp1-ws2012r2 | Enterprise | 12.0.4100 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Enterprise | 12.0.50000 12.0.50001 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Express | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 표준 | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 웹 | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Enterprise | 12.0.50000 |
| MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 표준 | 12.0.50000 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | Enterprise | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | Express | 13.0.31641 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | SQLDEV | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | 표준 | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2 | 웹 | 13.0.31640 |
| MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Enterprise | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | Enterprise | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | SQLDEV | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | Standard | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016 | 웹 | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016-BYOL | Enterprise | 13.0.21640 |
| MicrosoftSQLServer | SQL2016-WS2016-BYOL | Standard | 13.0.21640 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | Enterprise | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | Express | 13.0.400111 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | SQLDEV | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | 표준 | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016 | 웹 | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Enterprise | 13.0.400110 |
| MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 표준 | 13.0.400110 |
| MicrosoftVisualStudio | VisualStudio | VS-2015-Comm-AzureSDK-29-WS2012R2 | 2017.02.16 |
| MicrosoftVisualStudio | VisualStudio | VS-2015-Ent-VSU3-AzureSDK-29-WS2012R2 | 2017.02.16 |
| MicrosoftVisualStudio | VisualStudio | VS-2017-Ent-WS2016 | 2017.03.06 |
| MicrosoftVisualStudio | VisualStudio | VS-2017-RC3-Comm-WS2016 | 2017.02.14 |
| MicrosoftVisualStudio | VisualStudio | VS-2017-RC3-Ent-WS2016 | 2017.02.14 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | 2.0.20160125 2.0.20160229 2.0.20160430 2.0.20160617 2.0.20160721 2.0.20160812 2.0.20161214 2.0.20170110 2.0.20170316 2.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 3.0.20160125 3.0.20160229 3.0.20160430 3.0.20160617 3.0.20160721 3.0.20160812 3.0.20161214 3.0.20170111 3.0.20170316 3.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 4.0.20160125 4.0.20160229 4.0.20160430 4.0.20160617 4.0.20160721 4.0.20160812 4.0.20161012 4.0.20161214 4.0.20170111 4.0.20170316 4.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | 2016.0.20161010 2016.0.20161213 2016.0.20170314 2016.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-Server-Core | 2016.0.20170314 2016.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | 2016.0.20161012 2016.0.20161025 2016.0.20161213 2016.0.20170314 2016.127.20170406 |
| MicrosoftWindowsServer | WindowsServer | 2016-Nano-Server | 2016.0.20161012 2016.0.20161109 |
| MicrosoftWindowsServer | WindowsServer | 2016-Nano-Server-Technical-Preview |  |
| MicrosoftWindowsServer | WindowsServer | 2016-Technical-Preview-with-Containers |  |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |  |
| OpenLogic | CentOS | 6.5 | 6.5.20170207 |
| OpenLogic | CentOS | 6.7 | 6.7.20160310 |
| OpenLogic | CentOS | 6.8 | 6.8.20160620 6.8.20161026 6.8.20170105 |
| OpenLogic | CentOS | 7.2 | 7.2.20160325 7.2.20160620 7.2.20161026 7.2.20161116 7.2.20170105 |
| OpenLogic | CentOS | 7.3 | 7.3.20161221 |
| RedHat | RHEL | 6.8 | 6.8.20161028 6.8.20161214 6.8.20170224 6.8.2017032020 |
| RedHat | RHEL | 6.9 | 6.9.2017032807 |
| RedHat | RHEL | 7.2 | 7.2.20161026 7.2.20170203 7.2.20170224 7.2.2017032020 |
| RedHat | RHEL | 7.3 | 7.3.20161104 7.3.20170202 7.3.20170224 7.3.2017032020 |
| RedHat | RHEL-SAP-APPS | 6.8 | 6.8.201703130 |
| RedHat | RHEL-SAP-APPS | 7.3 | 7.3.201703130 |
| RedHat | RHEL-SAP-HANA | 6.7 | 6.7.20170310 |
| RedHat | RHEL-SAP-HANA | 7.2 | 7.2.20170310 |
| SUSE | 인프라 | SMT |  |
| SUSE | openSUSE-Leap | 42.1 | 2016.04.15 2016.11.21 |
| SUSE | openSUSE-Leap | 42.2 | 2017.01.24 2017.03.20 |
| SUSE | SLES | 11-SP4 | 2016.03.01 2016.08.12 2016.10.21 2016.12.21 2017.03.20 |
| SUSE | SLES | 12-SP1 | 2016.03.01 2016.08.11 2016.10.21 |
| SUSE | SLES | 12-SP2 | 2016.11.03 2017.03.20 |
| SUSE | SLES-BYOS | 11-SP4 | 2016.09.16 2017.03.20 |
| SUSE | SLES-BYOS | 12-SP2 | 2016.11.03 2017.03.20 |
| SUSE | SLES-SAP-BYOS | 12-SP1 | 2016.12.16 2017.03.20 |
| SUSE | SLES-SAP-BYOS | 12-SP2 | 2016.11.04 2017.03.20 |
| SUSE | SLES-SAPCAL | 11-SP4 |  |
| SUSE | SUSE-Manager-Proxy-BYOS | 3.0 | 2017.03.20 |
| SUSE | SUSE-Manager-Server-BYOS | 3.0 | 2017.03.20 |


## <a name="images-for-deployments-with-azure-service-manager-classic"></a>Azure Service Manager("클래식") 배포용 이미지
| Publisher | 이미지 이름 | OS |
|  ---  |  ---  |  ---  |
|  Credativ  |  Debian 7 "Wheezy"  |  Linux  |
|  Credativ  |  Debian 8 "Jessie"  |  Linux  |
|  Credativ  |  Debian 8 "Jessie", 백포트 포함  |  Linux  |
|  Credativ  |  Debian 9 "Stretch"  |  Linux  |
|  Visual Studio  |  Windows Server 2012 R2에 대한 Azure SDK 2.9를 포함한 Visual Studio Community 2015 업데이트 3  |  Windows  |
|  Visual Studio  |  Windows Server 2012 R2의 Azure SDK 2.9가 있는 Visual Studio Enterprise 2015 업데이트 3  |  Windows  |
|  Visual Studio  |  Windows Server 2016(x64)의 Visual Studio Enterprise 2017  |  Windows  |
|  Visual Studio  |  Windows Server 2016(x64)의 Visual Studio Community 2017 RC  |  Windows  |
|  Visual Studio  |  Windows Server 2016(x64)의 Visual Studio Enterprise 2017 RC  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 1월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 2월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 6월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 7월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 8월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2016년 12월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2017년 3월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2008 R2 SP1, 2017년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 1월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 2월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 6월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 7월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 8월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2016년 12월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2017년 3월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 Datacenter, 2017년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 1월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 2월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 6월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 7월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 8월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 10월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2016년 12월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2017년 3월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2012 R2 Datacenter, 2017년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 2016년 10월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 2016년 12월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 2017년 3월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 2017년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter - Server 코어, 2017년 3월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter - Server 코어, 2017년 4월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 - Nano 서버, 2016년 10월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 - Nano 서버, 2016년 12월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 컨테이너 포함, 2016년 10월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 컨테이너 포함, 2016년 12월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 컨테이너 포함, 2017년 3월  |  Windows  |
|  Microsoft Windows Server 제품 그룹  |  Windows Server 2016 Datacenter, 컨테이너 포함, 2017년 4월  |  Windows  |
|  SUSE  |  SUSE Manager 3.0 Proxy (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  SUSE Manager 3.0 Server (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  openSUSE Leap 42.2  |  Linux  |
|  SUSE  |  openSUSE Leap 42.1  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 11 SP4 (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 11 SP4  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 12 SP1  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 12 SP2 (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server 12 SP2  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP1 (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP1(프리미엄 이미지) (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP2 (Bring Your Own Subscription)  |  Linux  |
|  SUSE  |  SUSE Linux Enterprise Server for SAP Applications 12 SP2(프리미엄 이미지) (Bring Your Own Subscription)  |  Linux  |
|  OpenLogic  |  OpenLogic 6.5  |  Linux  |
|  OpenLogic  |  OpenLogic 6.7  |  Linux  |
|  OpenLogic  |  OpenLogic 6.8  |  Linux  |
|  OpenLogic  |  OpenLogic 7.2  |  Linux  |
|  OpenLogic  |  OpenLogic 7.3  |  Linux  |
|  Microsoft Open Source Technology Center for FreeBSD  |  FreeBSD 10.3  |  Linux  |
|  Microsoft Open Source Technology Center for FreeBSD  |  FreeBSD 11.0  |  Linux  |
|  Microsoft SharePoint Server 제품 그룹  |  SharePoint Server 2016 평가판  |  Windows  |
|  RedHat  |  Red Hat Enterprise Linux 6.7  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 6.8  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 6.9  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 7.2  |  Linux  |
|  RedHat  |  Red Hat Enterprise Linux 7.3  |  Linux  |
|  coreos  |  CoreOS Alpha  |  Linux  |
|  coreos  |  CoreOS Beta  |  Linux  |
|  coreos  |  CoreOS Stable  |  Linux  |
|  Cloudera, Inc.  |  Cloudera CentOS 6.7  |  Linux  |
|  Canonical  |  Ubuntu Server 12.04.5-LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 14.04.4-LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 14.04.5-LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 16.04 LTS  |  Linux  |
|  Canonical  |  Ubuntu Server 16.10  |  Linux  |
|  Microsoft SQL Server 제품 그룹  |  SQL Server 2014 SP2 Enterprise Windows Server 2012 R2  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2 기반 SQL Server 2014 SP2 Express  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2 기반 SQL Server 2014 SP2 Standard  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  SQL Server 2014 SP2 Web Windows Server 2012 R2  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 SP1 Developer  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016의 SQL Server 2016 SP1 Enterprise  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 SP1 Express  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 SP1 Standard  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 SP1 Web  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2 기반 SQL Server 2016 Developer  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 RTM Developer  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2에 대한 SQL Server 2016 RTM Enterprise  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 RTM Enterprise  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2 기반 SQL Server 2016 Express  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2 기반 SQL Server 2016 Standard  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 Standard  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2 기반 SQL Server 2016 Web  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2016 기반 SQL Server 2016 Web  |  Windows  |
|  Microsoft SQL Server 제품 그룹  |  Windows Server 2012 R2의 SQL Server 2014 SP1 Enterprise  |  Windows  |
|  GE-SRS-Prod-GalleryImages  |  Microsoft Azure Site Recovery 프로세스 서버 V2  |  Windows  |



<!--Working with quickstart templates missing here. needs modification of quickstart repo -->



## <a name="next-steps"></a>다음 단계
Azure 독일을 사용하는 경우 엔드포인트에 대한 프로그래밍 방식의 차이점을 확인하려면 [Azure 독일 개발자 가이드](./germany-developer-guide.md)를 참조하세요.

Marketplace에서 배포하거나 고유한 VHD를 만드는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.  

* [Windows 가상 머신 배포](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Windows 가상 머신 FAQ](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linux VM 사용자 지정 이미지 만들기](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)





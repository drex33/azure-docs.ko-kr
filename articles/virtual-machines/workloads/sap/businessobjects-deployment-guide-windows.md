---
title: Windows용 Azure에서 SAP BusinessObjects BI 플랫폼 배포 가이드 | Microsoft Docs
description: Windows용 Azure에서 SAP BusinessObjects BI 플랫폼을 배포하고 구성합니다.
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: d230c000eedf4cb6a4d01b7dc20f48e5712862c4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225089"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Azure의 Windows용 SAP BusinessObjects BI 플랫폼 배포 가이드

이 문서에서는 Windows용 Azure에서 SAP BOBI(SAP BusinessObjects Business Intelligence) 플랫폼을 배포하는 전략을 설명합니다. 이 예제에서는 Azure 프리미엄 SSD 관리 디스크가 설치 디렉터리로 장착된 두 개의 VM(가상 머신)을 구성합니다. PaaS(Platform as a service) 제품인 Azure SQL Database는 CMS(중앙 관리 서버) 및 감사 데이터베이스에 사용됩니다. SMB 프로토콜인 Azure Premium Files는 두 VM에서 공유되는 파일 저장소로 사용됩니다. 기본 Tomcat Java 웹 애플리케이션 및 BI(비즈니스 인텔리전스) 플랫폼 애플리케이션이 두 VM에 함께 설치됩니다. 사용자 요청의 부하를 분산하기 위해 기본 TLS/SSL 오프로딩 기능이 탑재된 Azure Application Gateway가 사용됩니다.

이 유형의 아키텍처는 소규모 배포 또는 비프로덕션 환경에 효과적입니다. 프로덕션 또는 대규모 배포의 경우 웹 애플리케이션용 호스트를 분리해야 하며, 여러 개의 SAP BOBI 애플리케이션 호스트를 두고 서버에서 추가 정보를 처리하도록 할 수 있습니다.

![Windows용 Azure의 SAP BOBI 배포를 보여 주는 다이어그램](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

이 예제에서는 다음 제품 버전과 파일 시스템 레이아웃이 사용됩니다.

- SAP BusinessObjects 플랫폼 4.3 SP01 패치 1
- Windows Server 2019
- SQL Database(버전: 12.0.2000.8)
- Microsoft ODBC 드라이버 - msodbcsql.msi(버전: 13.1)

| 파일 시스템        | Description                                                                                                               | 크기(GB)             | 필요한 액세스  | 스토리지                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | SAP BOBI 인스턴스, 기본 Tomcat 웹 애플리케이션 및 데이터베이스 드라이버(필요한 경우)를 설치하기 위한 파일 시스템 | SAP 크기 조정 지침 | 로컬 관리자 권한 | Azure 프리미엄 SSD 관리 디스크|
| \\\azusbobi.file.core.windows.net\frsinput  | 탑재 디렉터리는 입력 파일 저장 디렉터리로 사용되는 모든 SAP BOBI 호스트의 공유 파일을 위한 것입니다. | 비즈니스 요구         | 로컬 관리자 권한 | Azure NetApp Files         |
| \\\azusbobi.file.core.windows.net\frsoutput | 탑재 디렉터리는 출력 파일 저장 디렉터리로 사용되는 모든 SAP BOBI 호스트의 공유 파일을 위한 것입니다. | 비즈니스 요구         | 로컬 관리자 권한 | Azure NetApp Files         |

## <a name="deploy-a-windows-virtual-machine-via-the-azure-portal"></a>Azure Portal을 통한 Windows 가상 머신 배포

이 섹션에서는 SAP BOBI 플랫폼용 Windows 운영 체제(OS) 이미지를 사용하여 두 개의 VM을 만듭니다. VM을 만드는 대략적인 단계는 다음과 같습니다.

1. [리소스 그룹](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)을 만듭니다.

1. [가상 네트워크](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만듭니다.

   - SAP BI 플랫폼 배포의 모든 Azure 서비스에 단일 서브넷을 사용하지 마세요. SAP BI 플랫폼 아키텍처에 따라 여러 서브넷을 만들어야 할 수 있습니다. 이 배포에서는 두 개의 서브넷, 즉 BI 애플리케이션 서브넷과 Application Gateway 서브넷을 만듭니다.
   - SAP Note [2276646](https://launchpad.support.sap.com/#/notes/2276646)에 따라 다양한 구성 요소에 걸친 SAP BOBI 플랫폼 통신에 맞는 포트를 식별합니다.
   - SQL Database는 포트 1433을 통해 통신합니다. 1433 포트를 통한 아웃바운드 트래픽은 SAP BOBI 애플리케이션 서버에서 허용되어야 합니다.
   - Azure에서 Application Gateway는 별도의 서브넷에 있어야 합니다. 자세한 내용은 [Application Gateway 구성 개요](../../../application-gateway/configuration-overview.md)를 참조하세요.
   - Azure Files 대신 Azure NetApp Files를 파일 저장소로 사용할 경우 Azure NetApp Files에 대해 별도의 서브넷을 만듭니다. 자세한 내용은 [Azure NetApp Files 네트워크 계획 지침](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)을 참조하세요.

1. 가용성 집합을 만듭니다.

   - 다중 인스턴스 배포의 각 계층에서 중복성을 얻으려면 가용성 집합의 계층마다 VM을 배치합니다. 아키텍처에 따라 각 계층의 가용성 집합을 분리해야 합니다.

1. 가상 머신 1(azuswinboap1)을 만듭니다.

   - 사용자 지정 이미지를 사용해도 되고 Azure Marketplace에서 이미지를 선택해도 됩니다. 필요에 따라 [SAP용 Azure Marketplace에서 VM 배포](deployment-guide.md) 또는 [SAP용 사용자 지정 이미지로 VM 배포](deployment-guide.md)를 참조하세요.

1. 가상 머신 2(azuswinboap2)를 만듭니다.
1. 프리미엄 SSD 디스크 하나를 추가합니다. 이 디스크는 SAP BOBI 설치 디렉터리로 사용됩니다.

## <a name="provision-azure-premium-files"></a>Azure Premium Files 프로비저닝

Azure Files 설정을 진행하기 전에 [Azure Files](../../../storage/files/storage-files-introduction.md) 설명서를 숙지하세요.

Azure Files는 HDD 기반 하드웨어에서 호스트되는 표준 파일 공유와 SSD 기반 하드웨어에서 호스트되는 프리미엄 파일 공유를 제공합니다. SAP BusinessObjects 파일 저장소의 경우 Azure Premium Files를 사용합니다.

Azure 프리미엄 파일 공유는 지역의 하위 집합에서 로컬 및 영역 중복으로 사용할 수 있습니다. 지역에서 현재 프리미엄 파일 공유를 사용할 수 있는지 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조하세요. 영역 중복 스토리지(ZRS)를 지원하는 지역에 대한 자세한 내용은 [Azure Storage redundancy](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)(Azure Storage 중복성)를 참조하세요.

### <a name="deploy-an-azure-files-storage-account-and-nfs-shares"></a>Azure Files 스토리지 계정 및 NFS 공유 배포

Azure 파일 공유는 공유 스토리지 풀을 나타내는 최상위 개체인 스토리지 계정에 배포됩니다. 이 스토리지 풀을 사용하여 여러 파일 공유를 배포할 수 있습니다. Azure는 고객에게 발생할 수 있는 여러 가지 스토리지 시나리오를 위해 다양한 스토리지 계정 유형을 지원합니다. SAP BusinessObjects 파일 스토리지의 경우 FileStorage 계정을 만들어야 합니다. 이를 사용하여 프리미엄 SSD 기반 하드웨어에 Azure 파일 공유를 배포합니다.

> [!NOTE]
> FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. Blob, 컨테이너, 큐, 테이블 등의 다른 스토리지 리소스는 FileStorage 계정으로 배포할 수 없습니다.

스토리지 계정은 [프라이빗 엔드포인트](../../../storage/files/storage-files-networking-endpoints.md)를 통해 액세스되고 SAP BOBI 플랫폼의 동일한 가상 네트워크에 배포됩니다. 이 설정을 사용하면 SAP 시스템의 트래픽이 가상 네트워크 보안 경계를 벗어나지 않습니다. SAP 시스템은 중요한 중요 비즈니스용 데이터를 포함하므로 가상 네트워크의 경계 내에서 머무는 것은 많은 고객에게 중요한 보안 고려 사항입니다.

다른 가상 네트워크에서 스토리지 계정에 액세스해야 하는 경우 [Azure Virtual Network 피어링](../../../virtual-network/virtual-network-peering-overview.md)을 사용할 수 있습니다.

#### <a name="azure-files-storage-account"></a>Azure Files 스토리지 계정

1. Azure Portal을 통해 스토리지 계정을 만들려면 **리소스 만들기** > **스토리지** > **스토리지 계정** 을 선택합니다.

1. **기본** 탭에서 모든 필수 필드를 작성하여 스토리지 계정을 만듭니다.

   1. **구독**  >  **리소스 그룹**  >  **지역** 을 선택합니다.

   1. **스토리지 계정 이름** 을 입력합니다. 예를 들어 **azusbobi** 를 입력합니다. 이 이름은 전역적으로 고유해야 하지만, 그 외에는 원하는 대로 지정할 수 있습니다.

   1. 성능 계층으로 **Premium** 을 선택하고 계정 종류로 **FileStorage** 를 선택합니다.

   1. **복제** 레이블의 경우 중복성 수준을 선택합니다. **LRS(로컬 중복 스토리지)** 를 선택합니다.

      Premium FileStorage의 경우 LRS 및 ZRS는 유일하게 사용할 수 있는 옵션입니다. 배포 전략(가용성 집합 또는 가용성 영역)에 따라 적절한 중복성 수준을 선택합니다. 자세한 내용은 [Azure Storage 중복성](../../../storage/common/storage-redundancy.md)을 참조하세요.

   1. **다음** 을 선택합니다.

1. **네트워킹** 탭에서 연결 방법으로 [프라이빗 엔드포인트](../../../storage/files/storage-files-networking-endpoints.md)를 선택합니다. 자세한 내용은 [Azure Files 네트워킹 고려 사항](../../../storage/files/storage-files-networking-overview.md)을 참조하세요.

   1. 프라이빗 엔드포인트 섹션에서 **추가** 를 선택합니다.

   1. **구독** > **리소스 그룹** > **위치** 를 선택합니다.

   1. 프라이빗 엔드포인트의 **이름** 을 입력합니다. 예를 들어 **azusbobi-pe** 를 입력합니다.

   1. **스토리지 하위 리소스** 에서 **파일** 을 선택합니다.

   1. **네트워킹** 섹션에서 SAP BusinessObjects BI 애플리케이션이 배포된 **가상 네트워크** 및 **서브넷** 을 선택합니다.

   1. **프라이빗 DNS 영역과 통합** 에 대해 **기본값(예)** 을 적용합니다.

   1. 드롭다운 목록에서 **프라이빗 DNS 영역** 을 선택합니다.

   1. **확인** 을 선택하여 **스토리지 계정 만들기** 의 **네트워킹** 탭으로 돌아갑니다.

1. **데이터 보호** 탭에서 스토리지 계정의 Azure 파일 공유에 대한 일시 삭제 정책을 구성합니다. 일시 삭제 기능은 기본적으로 꺼져 있습니다. 일시 삭제에 대해 자세히 알아보려면 [Azure 파일 공유의 우발적 삭제 방지](../../../storage/files/storage-files-prevent-file-share-deletion.md)를 참조하세요.

1. **고급** 탭에서 다른 보안 옵션을 선택합니다.

   **보안 전송 필요** 필드는 스토리지 계정에 대한 통신을 위해 전송 중 암호화가 스토리지 계정에 필요한지 여부를 나타냅니다. SMB 2.1 지원이 필요한 경우에는 이 필드를 사용하지 않도록 설정해야 합니다. SAP BOBI 플랫폼의 경우 **기본값(사용 설정됨)** 을 유지합니다.

1. 계속 진행하고 스토리지 계정을 만듭니다.

스토리지 계정을 만드는 방법에 대한 자세한 내용은 [FileStorage 스토리지 계정 만들기](../../../storage/files/storage-how-to-create-file-share.md)를 참조하세요.

#### <a name="create-azure-file-shares"></a>Azure 파일 공유 만들기

다음 단계는 스토리지 계정에서 Azure 파일을 만드는 것입니다. Azure 파일은 프리미엄 파일 공유에 대해 프로비저닝된 모델을 사용합니다. 프로비저닝된 비즈니스 모델에서 사용한 내용에 따라 비용이 청구되는 것이 아니라 Azure Files에 스토리지 요구 사항을 사전에 지정합니다. 이 모델에 대한 자세한 내용은 [프로비저닝된 모델](../../../storage/files/understanding-billing.md#provisioned-model)을 참조하세요. 이 예제에서는 SAP BOBI 파일 저장소용 frsinput(256GB)과 frsoutput(256GB)이라는 두 개의 Azure 파일을 만듭니다.

1. 스토리지 계정 **azusbobi** > **파일 공유** 로 이동합니다.
1. **새 파일 공유** 를 선택합니다.
1. 파일 공유의 **이름** 을 입력합니다. 예를 들어 **frsinput** 또는 **frsouput** 을 입력합니다.
1. **프로비저닝된 용량** 에 필요한 파일 공유 크기를 삽입합니다. 예를 들어 **256GB** 를 입력합니다.
1. **프로토콜** 로 **SMB** 를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-a-data-disk-on-a-windows-virtual-machine"></a>Windows 가상 머신에서 데이터 디스크 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

**[A]** : 이 단계는 모든 호스트에 적용됩니다.

### <a name="initialize-a-new-data-disk"></a>새 데이터 디스크 초기화

SAP BusinessObjects BI 애플리케이션에는 이진 파일을 설치할 수 있는 파티션이 필요합니다. OS 파티션(C:)에 SAP BOBI 애플리케이션을 설치할 수 있지만 배포 및 OS를 위한 공간이 충분해야 합니다. 임시 파일 및 웹 애플리케이션에 사용할 수 있는 2GB 이상의 용량을 확보하는 것이 좋습니다. 또한 SAP BOBI 설치 이진 파일을 별도의 파티션에 분리하는 것이 좋습니다.

이 예제에서는 SAP BOBI 애플리케이션이 별도의 파티션(F:)에 설치됩니다. VM 프로비저닝 중에 연결한 프리미엄 SSD 디스크를 초기화합니다.

1. **[A]** VM에 연결된 데이터 디스크가 없는 경우(azuswinboap1 and azuswinboap2) [데이터 디스크 추가](../../windows/attach-managed-disk-portal.md#add-a-data-disk)의 단계에 따라 새 관리형 데이터 디스크를 연결합니다.
1. **[A]** 관리 디스크를 VM에 연결한 후 [새 데이터 디스크 초기화](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk)의 단계에 따라 디스크를 초기화합니다.

### <a name="mount-azure-premium-files"></a>Azure Premium Files 탑재

Azure Files를 파일 저장소로 사용하려면 이 서비스를 탑재해야 합니다. 즉, 드라이브 문자 또는 탑재 지점 경로를 할당해야 합니다.

**[A]** Azure 파일 공유를 탑재하려면 [Azure 파일 공유 탑재](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share)의 단계를 따릅니다.

Windows 서버에 Azure 파일 공유를 탑재하려면 포트 445가 열려 있어야 합니다. SMB 프로토콜의 경우 TCP 포트 445가 열려 있어야 합니다. 포트 445가 차단되면 연결이 실패합니다. 방화벽이 [문제 해결](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) 가이드에 언급된 `Test-NetConnection` cmdlet으로 포트 445를 차단하고 있는지 확인할 수 있습니다.

## <a name="configure-a-cms-database-azure-sql"></a>CMS 데이터베이스 구성: Azure SQL

이 섹션에서는 Azure Portal을 사용하여 Azure SQL을 프로비저닝하는 방법을 자세히 설명합니다. 또한 SAP BOBI 플랫폼용 CMS 및 감사 데이터베이스를 만들고 데이터베이스에 액세스하기 위한 사용자 계정을 만드는 방법에 대한 지침을 제공합니다.

이 지침은 SQL Database를 사용하는 경우에만 적용됩니다. 다른 데이터베이스의 경우 SAP 또는 데이터베이스 관련 설명서의 지침을 참조하세요.

### <a name="create-a-sql-database-server"></a>SQL Database 서버 만들기

SQL Database는 단일 데이터베이스, 탄력적 풀 및 데이터베이스 서버와 같은 다양한 배포 옵션을 제공합니다. SAP BOBI 플랫폼의 경우 CMS 및 감사라는 두 가지 데이터베이스가 필요합니다. 두 가지 단일 데이터베이스를 만드는 대신 단일 데이터베이스와 탄력적 풀 그룹을 관리할 수 있는 SQL Database 서버를 만들 수 있습니다. SQL Database 서버를 만들려면 다음 단계를 수행합니다.

1. [SQL 배포 옵션 선택](https://portal.azure.com/#create/Microsoft.AzureSQL) 페이지로 이동합니다.
1. **SQL 데이터베이스** 에서 **리소스 종류** 를 **데이터베이스 서버** 로 변경합니다. **만들기** 를 선택합니다.
1. **기본** 탭에서 **SQL Database 서버 만들기** 에 대한 모든 필수 필드를 입력합니다.

   1. **프로젝트 세부 정보** 에서 **구독** 과 **리소스 그룹** 을 선택합니다.

   1. **서버 이름** 을 입력합니다. 예를 들어 **azussqlbodb** 를 입력합니다. 이 서버 이름은 전역적으로 고유해야 하지만, 그 외에는 원하는 대로 지정할 수 있습니다.

   1. **위치** 를 선택합니다.

   1. **서버 관리자 로그인** 을 입력합니다. 예를 들어 **boadmin** 을 입력합니다. 그런 다음 **암호** 를 입력합니다.

1. **네트워킹** 탭에서 **방화벽 규칙** 에 따라 **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 을 **아니요** 로 변경합니다.
1. **추가 설정** 에서 기본 설정을 유지합니다.
1. 계속해서 **SQL Database 서버** 를 만듭니다.

다음 단계에서는 SQL Database 서버(azussqlbodb.database.windows.net)에 CMS 및 감사 데이터베이스를 만듭니다.

### <a name="create-the-cms-and-the-audit-database"></a>CMS 및 감사 데이터베이스 만들기

SQL Database 서버를 프로비저닝한 후 리소스 azussqlbodb로 이동합니다. 그런 다음, 이 단계에 따라 CMS 및 감사 데이터베이스를 만듭니다.

1. **azussqlbodb 개요** 페이지에서 **데이터베이스 만들기** 를 선택합니다.
1. **기본** 탭에서 필요한 모든 필드를 입력합니다.

   1. **데이터베이스 이름** 을 입력합니다. 예를 들어 **bocms** 또는 **boaudit** 을 입력합니다.

   1. **컴퓨팅 + 스토리지** 옵션에서 **데이터베이스 구성** 을 선택합니다. 크기 조정 결과에 따라 적절한 모델을 선택합니다. 옵션에 대한 인사이트는 [Azure SQL Database의 크기 조정 모델](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database)을 참조하세요.
1. **네트워킹** 탭에서 연결 방법으로 [프라이빗 엔드포인트](../../../private-link/tutorial-private-endpoint-sql-portal.md)를 선택합니다. 프라이빗 엔드포인트는 구성된 가상 네트워크 내에서 SQL Database에 액세스하는 데 사용됩니다.

   1. **프라이빗 엔드포인트 추가** 를 선택합니다.

   1. **구독** > **리소스 그룹** > **위치** 를 선택합니다.

   1. 프라이빗 엔드포인트의 **이름** 을 입력합니다. 예를 들어 **azusbodb-pe** 를 입력합니다.

   1. **대상 하위 리소스** 에서 **SqlServer** 를 선택합니다.

   1. **네트워킹** 섹션에서 SAP BusinessObjects BI 애플리케이션이 배포된 **가상 네트워크** 및 **서브넷** 을 선택합니다.

   1. **프라이빗 DNS 영역과 통합** 에 대해 **기본값(예)** 을 적용합니다.

   1. 드롭다운 목록에서 **프라이빗 DNS 영역** 을 선택합니다.

   1. **확인** 을 선택하여 **SQL 데이터베이스 만들기** 의 **네트워킹** 탭으로 돌아갑니다.
1. **추가 설정** 탭에서 **데이터 정렬** 설정을 **SQL_Latin1_General_CP850_BIN2** 로 변경합니다.
1. 계속하여 CMS 데이터베이스를 만듭니다.

마찬가지로 감사 데이터베이스를 만들 수 있습니다. 예를 들어 **boaudit** 을 입력합니다.

### <a name="download-and-install-an-odbc-driver"></a>ODBC 드라이버 다운로드 및 설치

SAP BOBI 애플리케이션 서버에서 CMS 또는 감사 데이터베이스에 액세스하려면 데이터베이스 클라이언트/드라이버가 필요합니다. Microsoft ODBC 드라이버는 SQL Database에서 실행 중인 CMS 및 감사 데이터베이스에 액세스하는 데 사용됩니다. 이 섹션에서는 Windows에서 ODBC 드라이버를 다운로드하고 설정하는 방법에 대한 지침을 제공합니다. 

1. [SAP BusinessObjects BI 플랫폼의 PAM(제품 가용성 매트릭스)](https://support.sap.com/pam)에 있는 **OS별 CMS + 감사 리포지토리 지원** 섹션에서 SQL Database와 호환되는 데이터베이스 커넥터를 확인하세요.
1. [링크](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15)에서 ODBC 드라이버 버전을 다운로드합니다. 이 예제에서는 ODBC 드라이버 [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131)을 다운로드합니다.
1. 모든 BI 서(azuswinboap1 및 azuswinboap2)에 ODBC 드라이버를 설치합니다.
1. **azuswinboap1** 에 드라이버를 설치한 후 **시작** > **Windows 관리 도구** > **ODBC 데이터 원본(64비트)** 로 이동합니다.
1. **시스템 DSN** 탭으로 이동합니다. 
1. **추가** 를 선택하여 CMS 데이터베이스에 연결합니다.
1. **ODBC Driver 13 for SQL Server** 를 선택하고 **완료** 를 선택합니다.
1. 다음과 같이 CMS 데이터베이스의 정보를 입력하고 **다음** 을 선택합니다.
   - **이름**: “CMS 및 감사 데이터베이스 만들기” 섹션에서 만든 데이터베이스의 이름입니다. 예를 들어 **bocms** 또는 **boaudit** 을 입력합니다.
   - **설명**: 데이터 원본에 대한 설명입니다. 예를 들어 **CMS 데이터베이스** 또는 **감사 데이터베이스** 를 입력합니다.
   - **서버**: “SQL Database 서버 만들기” 섹션에서 만든 서버의 이름입니다. 예를 들어 azussqlbodb.database.windows.net을 입력합니다.
1. **사용자가 입력한 로그인 ID 및 암호를 사용하는 SQL Server 인증 사용** 을 선택하여 Azure SQL Server의 신뢰성을 인증합니다. SQL Database 서버 연결 시 만든 사용자 자격 증명을 입력합니다. 예를 들어 **boadmin** 을 입력합니다. **다음** 을 선택합니다.
1. **기본 데이터베이스** 를 **bocms** 로 변경하고 다른 모든 항목을 기본값으로 유지합니다. **다음** 을 선택합니다.
1. **데이터에 강한 암호화 사용** 확인란을 선택하고 다른 모든 항목을 기본값으로 유지합니다. **마침** 을 선택합니다.
1. CMS 데이터베이스에 대한 데이터 원본이 만들어졌습니다. 이제 **데이터 원본 테스트** 를 선택하여 CMS 데이터베이스에 대한 BI 애플리케이션 연결의 유효성을 검사할 수 있습니다. 이 검사는 성공적으로 완료해야 합니다. 실패할 경우 연결 문제를 [해결](../../../azure-sql/database/troubleshoot-common-errors-issues.md)합니다.

>[!Note]
>SQL Database는 포트 1433을 통해 통신합니다. 1433 포트를 통한 아웃바운드 트래픽은 SAP BOBI 애플리케이션 서버에서 허용되어야 합니다.

이전 단계를 반복하여 azuswinboap1 서버에서 감사 데이터베이스에 대한 연결을 만듭니다. 마찬가지로 모든 BI 애플리케이션 서버(azuswinboap2)에서 모든 ODBC 데이터 원본(bocms 및 boaudit)을 설치하고 구성합니다. 

## <a name="server-preparation"></a>서버 준비

SAP의 최신 가이드에 따라 BI 플랫폼 설치를 위한 서버를 준비합니다. 최신 정보는 [Windows용 SAP Business Intelligence 플랫폼 설치 가이드](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html)의 ‘준비’ 섹션을 참조하세요.

## <a name="installation"></a>설치

Windows 호스트에 BI 플랫폼을 설치하려면 로컬 관리자 권한이 있는 사용자로 로그인합니다.

SAP BusinsessObjects BI 플랫폼의 미디어로 이동한 다음 `setup.exe`를 실행합니다.

사용 중인 버전과 관련이 있는 [Windows용 SAP Business Intelligence 플랫폼 설치 가이드](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html)의 지침을 따릅니다. 다음은 Windows에 SAP BOBI 플랫폼을 설치하는 동안의 몇 가지 유의 사항입니다.

- **대상 폴더 구성** 화면에서 BI 플랫폼을 설치할 대상 폴더를 제공합니다. 예를 들어 **F:\SAP BusinessObjects\*** 를 입력합니다. 
- **제품 등록 구성** 화면에서 SAP 참고 [1288121](https://launchpad.support.sap.com/#/notes/1288121)의 SAP BusinessObjects 솔루션용 임시 라이선스 키를 사용할 수도 있고 SAP Service Marketplace에서 라이선스 키를 생성할 수 있습니다.
- **설치 유형 선택** 화면에서 첫 번째 서버(azuswinboap1)에 대해 **전체** 설치를 선택합니다. 다른 서버(azuswinboap2)의 경우 기존 SAP BOBI 설정을 확장하는 **맞춤 설정/확장** 을 선택합니다.
- **기본 또는 기존 데이터베이스 선택** 화면에서 **기존 데이터베이스 구성** 을 선택합니다. 그러면 CMS 및 감사 데이터베이스를 선택하라는 메시지가 표시됩니다. **CMS 데이터베이스** 유형 및 **감사 데이터베이스** 유형에 대해 **ODBC 사용 Microsoft SQL Server** 를 선택합니다.

  설치 중에 감사를 구성하지 않으려면 **감사 데이터베이스 없음** 을 선택합니다.

- SAP BOBI 아키텍처에 따라 **Java 웹 애플리케이션 서버 선택** 화면에서 적절한 옵션을 선택합니다. 이 예제에서는 동일한 SAP BOBI 플랫폼에 Tomcat 서버를 설치하는 옵션 1을 선택했습니다.
- **CMS 리포지토리 데이터베이스 구성 - SQL Server(ODBC)** 에 CMS 데이터베이스 정보를 입력합니다. 다음 이미지는 Windows 설치를 위한 CMS 데이터베이스 정보의 입력 예시를 보여 줍니다.
  
  ![Windows의 CMS 데이터베이스 정보를 보여 주는 스크린샷](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)
- (선택 사항) **감사 데이터베이스 구성 - SQL Server(ODBC)** 에서 감사 데이터베이스 정보를 입력합니다. 다음 이미지는 Windows 설치를 위한 감사 데이터베이스 정보의 입력 예시를 보여 줍니다.
  
  ![Windows의 감사 데이터베이스 정보를 보여 주는 스크린샷](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- 지침을 따르고 설치를 완료하는 데 필요한 정보를 입력합니다.

다중 인스턴스 배포의 경우 두 번째 호스트(azuswinboap2)에서 설치 프로그램을 실행합니다. **설치 유형 선택** 화면에서 기존 SAP BOBI 설정을 확장하는 **사용자 지정/확장** 을 선택합니다. 자세한 내용은 SAP 블로그 [Azure SQL Database를 사용한 SAP BusinessObjects Business Intelligence 플랫폼 설정](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/)을 참조하세요.

> [!IMPORTANT]
> SQL Server 및 SQL Database의 데이터베이스 엔진 버전 번호는 서로 비교할 수 없습니다. 해당 개별 제품의 내부 빌드 번호입니다. SQL Database용 데이터베이스 엔진은 SQL Server 데이터베이스 엔진과 동일한 코드 베이스를 기준으로 합니다. 가장 중요한 사실은 SQL Database의 데이터베이스 엔진에 항상 최신 SQL 데이터베이스 엔진 비트가 있다는 것입니다. SQL Database 버전 12는 SQL Server 버전 15보다 최신 버전입니다.

현재 SQL Database 버전을 확인하기 위해서는 CMC(중앙 관리 콘솔)의 설정을 확인하거나 [sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) 또는 [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15)를 사용하여 다음 쿼리를 실행하면 됩니다. 기본 호환성에 대한 SQL 버전의 맞춤은 [데이터베이스 호환성 수준](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15) 문서에서 찾을 수 있습니다.

![CMC의 데이터베이스 정보를 보여 주는 스크린샷](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

```sql
1> select @@version as version;
2> go
version
------------------------------------------------------------------------------------------
Microsoft SQL Azure (RTM) - 12.0.2000.8
        Feb 20 2021 17:51:58
        Copyright (C) 2019 Microsoft Corporation

(1 rows affected)

1> select name, compatibility_level from sys.databases;
2> go
name                                                                  compatibility_level
--------------------------------------------------------------------- -------------------
master                                                                                150
bocms                                                                                 150
boaudit                                                                               150

(3 rows affected)
```

## <a name="post-installation"></a>설치 후

SAP BOBI 플랫폼의 다중 인스턴스를 설치한 후에는 애플리케이션 고가용성을 지원하기 위해 더 많은 사후 구성 단계를 수행해야 합니다.

### <a name="configure-a-cluster-name"></a>클러스터 이름 구성

SAP BOBI 플랫폼의 다중 인스턴스 배포에서는 하나의 클러스터에서 여러 CMS 서버를 함께 실행하려고 합니다. 클러스터는 공통 CMS 시스템 데이터베이스에 대해 함께 작동하는 둘 이상의 CMS 서버로 구성됩니다. CMS에서 실행되는 노드가 실패하면 다른 CMS가 있는 노드는 BI 플랫폼 요청을 계속 처리합니다. 기본적으로 SAP BOBI 플랫폼에서 클러스터 이름은 설치한 첫 번째 CMS의 호스트 이름을 반영합니다. 

Windows에서 클러스터 이름을 구성하려면 [SAP Business Intelligence 플랫폼 관리자 가이드](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US)의 지침을 따르세요. 클러스터 이름을 구성한 후 SAP Note [1660440](https://launchpad.support.sap.com/#/notes/1660440)에 따라 CMC 또는 BI 실행 패드 로그인 페이지에서 기본 시스템 항목을 설정합니다. 

### <a name="configure-the-input-and-output-filestore-location-to-azure-premium-files"></a>Azure Premium Files에 대한 입력 및 출력 파일 저장 위치 구성

파일 저장소는 실제 SAP BusinessObjects BI 파일이 위치하는 디스크 디렉터리를 참조합니다. SAP BOBI 플랫폼용 파일 리포지토리 서버의 기본 위치는 로컬 설치 디렉터리에 있습니다. 다중 인스턴스 배포에서는 모든 스토리지 계층 서버에서 액세스할 수 있도록 Azure Premium Files 또는 Azure NetApp Files 같은 공유 스토리지에 파일 저장소를 설정하는 것이 중요합니다.

1. 만들지 않은 경우 이전 섹션인 “Azure Premium Files 프로비저닝”에 제공된 지침에 따라 Azure Premium Files를 만들고 탑재합니다.

   > [!Tip]
   > VM의 배포(가용성 집합 또는 가용성 영역)에 따라 Azure Premium Files(LRS 또는 ZRS)에 대한 스토리지 중복성을 선택합니다.

1. SAP Note [2512660](https://launchpad.support.sap.com/#/notes/0002512660)에 따라 파일 리포지토리 경로를 변경합니다(입력 및 출력).

### <a name="tomcat-clustering-session-replication"></a>Tomcat 클러스터링: 세션 복제

Tomcat은 세션 복제 및 장애 조치(failover)를 위해 2개 이상의 애플리케이션 서버를 클러스터링하는 기능을 지원합니다. SAP BOBI 플랫폼 세션이 직렬화될 경우 애플리케이션 서버에서 오류가 발생하더라도 사용자 세션이 Tomcat의 다른 인스턴스로 원활하게 장애 조치될 수 있습니다. SAP BI 애플리케이션에서 폴더 계층을 탐색하는 동안 실패하는 웹 서버에 사용자가 연결된 경우를 예로 들 수 있습니다. 올바르게 구성된 클러스터에서 사용자가 로그인 페이지로 리디렉션되지 않고 폴더 계층 구조를 계속 탐색할 수 있습니다.

SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)에는 멀티캐스트를 사용하여 Tomcat 클러스터링을 구성하는 단계가 제공됩니다. 하지만 Azure에서는 멀티캐스트가 지원되지 않습니다. Tomcat 클러스터가 Azure에서 작동하게 하려면 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership)(SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907))를 사용해야 합니다. Azure에서 Tomcat 클러스터를 설정하려면 SAP 블로그에서 [SAP BusinessObjects BI 플랫폼에 정적 멤버 자격을 사용하여 Tomcat 클러스터링](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)을 참조하세요.

### <a name="load-balance-a-web-tier-of-an-sap-bi-platform"></a>SAP BI 플랫폼의 웹 계층 부하 분산

SAP BOBI 다중 인스턴스 배포에서는 Java 웹 애플리케이션 서버(웹 계층)가 두 개 이상의 호스트에서 실행됩니다. 사용자 부하를 웹 서버 간에 균등하게 분산하려면 최종 사용자와 웹 서버 간에 부하 분산 장치를 사용하면 됩니다. Azure Load Balancer 또는 Application Gateway를 사용하여 웹 애플리케이션 서버에 대한 트래픽을 관리할 수 있습니다. 해당 제품에 대해서는 다음 섹션에서 설명합니다.

* [Load Balancer](../../../load-balancer/load-balancer-overview.md)는 대기 시간이 짧은 고성능 계층 4(TCP, UDP) 부하 분산 장치로, 정상적인 VM 간에 트래픽을 분산합니다. 부하 분산 장치 상태 프로브가 각 VM에서 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다. 인터넷에서 SAP BI 플랫폼에 액세스할 수 있게 할 것인지 여부에 따라 퍼블릭 부하 분산 장치 또는 내부 부하 분산 장치를 선택할 수 있습니다. 영역 중복은 가용성 영역에서 고가용성을 보장합니다.

   다음 그림에서 웹 애플리케이션 서버가 기본 Tomcat HTTP 포트인 8080 포트에서 실행되고 상태 프로브는 이 포트를 모니터링하는 “내부 부하 분산 장치” 섹션을 살펴보세요. 사용자로부터 들어오는 모든 요청은 백 엔드 풀의 웹 애플리케이션 서버(azuswinboap1 또는 azuswinboap2)로 리디렉션됩니다. Load Balancer는 TLS/SSL 오프로딩이라고도 하는 TLS/SSL 종료를 지원하지 않습니다. Load Balancer를 사용하여 웹 서버 간에 트래픽을 분산하는 경우 표준 Load Balancer를 사용하는 것이 좋습니다.

   > [!NOTE]
   > 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Load Balancer의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 정보는 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.

   ![웹 서버 간에 트래픽을 분산하는 데 사용되는 Load Balancer를 보여 주는 스크린샷](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

* [Application Gateway](../../../application-gateway/overview.md)는 애플리케이션에서 사용자 트래픽을 하나 이상의 웹 애플리케이션 서버로 전달하는 데 사용되는 애플리케이션 제공 컨트롤러를 서비스로 제공합니다. 이 서비스는 TLS/SSL 오프로딩, Web Application Firewall, 애플리케이션의 쿠키 기반 세션 선호도와 같은 다양한 계층 7 부하 분산 기능을 제공합니다.

   SAP BI 플랫폼에서 Application Gateway는 애플리케이션 웹 트래픽을 백 엔드 풀의 지정된 리소스로 보냅니다. 이 경우 azuswinboap1 또는 azuswinboap2입니다. 사용자는 수신기를 포트에 할당하고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가합니다. 다음 그림에서 프라이빗 프런트 엔드 IP 주소(10.31.3.25)를 사용하는 Application Gateway는 사용자의 진입점으로 작동하고, 들어오는 TLS/SSL(HTTPS - TCP/443) 연결을 처리하며, TLS/SSL의 암호를 해독하고, 요청(HTTP - TCP/8080)을 백엔드 풀의 서버에 전달합니다. 기본 제공되는 TLS/SSL 종료 기능을 사용하면 애플리케이션 게이트웨이에서 TLS/SSL 인증서를 하나만 유지하면 되기 때문에 작업이 간단해집니다.

   ![웹 서버 간 트래픽을 분산하는 데 사용되는 Application Gateway를 보여 주는 스크린샷](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   SAP BOBI 웹 서버에 대한 Application Gateway를 구성하려면 SAP 블로그의 [Application Gateway를 사용하여 SAP BOBI 웹 서버의 부하 분산](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)을 참조하세요.

   > [!NOTE]
   > SSL 오프로딩, 서버의 암호화 및 암호 해독 오버헤드를 줄이는 SSL 관리 중앙 집중화, 트래픽을 분산하는 라운드 로빈 알고리즘, Web Application Firewall, 고가용성 등의 기능을 제공하는 Application Gateway를 사용하여 웹 서버 간에 트래픽 부하를 분산합니다.

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Azure의 SAP BusinessObjects BI 플랫폼 안정성

SAP BusinessObjects BI 플랫폼에는 특정 작업 및 연산에 최적화된 여러 계층이 있습니다. 한 계층의 구성 요소를 사용할 수 없게 되면 SAP BOBI 애플리케이션에 액세스할 수 없게 되거나 애플리케이션의 특정 기능이 작동하지 않습니다. 비즈니스 중단 없이 애플리케이션이 계속 작동하도록 각 계층을 안정적으로 설계해야 합니다.

이 가이드에서는 SAP BOBI 플랫폼 구성과 함께 Azure의 기본 기능이 SAP 배포의 가용성을 향상시키는 방법을 살펴봅니다. 이 섹션에서는 Azure의 SAP BOBI 플랫폼 안정성에 대한 다음 옵션을 중점적으로 다룹니다.

- **백업 및 복원**: 이 프로세스는 주기적으로 별도의 위치에 데이터 및 애플리케이션의 복사본을 만듭니다. 원래 데이터나 애플리케이션이 손실되거나 손상된 경우 복사본을 사용하여 이전 상태로 복원하거나 복구할 수 있습니다.
- **고가용성**: 서버 중 하나를 사용할 수 없게 되어도 애플리케이션이 계속 작동하도록 고가용성 플랫폼은 Azure 지역 내에 두 개 이상의 서버를 운영합니다.
- **재해 복구(DR)** : 이 프로세스는 치명적인 손실이 발생할 경우 애플리케이션 기능을 복원합니다. 예를 들어 자연재해로 인해 전체 Azure 지역을 사용할 수 없게 되는 경우가 있습니다.

이 솔루션의 구현 방법은 Azure에서 설정하는 시스템 특성에 따라 다릅니다. 비즈니스 요구 사항에 따라 백업 및 복원, 고가용성 및 DR 솔루션을 조정해야 합니다.

## <a name="backup-and-restore"></a>백업 및 복원

백업 및 복원은 원래 데이터나 애플리케이션이 손실되거나 손상된 경우 이전 상태로 복원 또는 복구될 수 있도록 별도의 위치에 데이터 및 애플리케이션의 복사본을 주기적으로 만드는 프로세스입니다. 또한 모든 비즈니스 DR 전략의 필수 구성 요소입니다. 백업을 사용하면 구성된 보존 기간 내의 특정 시점으로 애플리케이션과 데이터베이스를 복원할 수 있습니다.

SAP BOBI 플랫폼에 대한 포괄적인 백업 및 복원 전략을 개발하려면 시스템 가동 중지 시간 또는 애플리케이션 중단을 야기하는 구성 요소를 확인합니다. 애플리케이션을 보호하려면 SAP BOBI 플랫폼에서 다음 구성 요소를 백업해야 합니다.

- SAP BOBI 설치 디렉터리(관리형 프리미엄 디스크)
- Filestore(Azure Premium Files 또는 분산 설치를 위한 Azure NetApp Files)
- CMS 및 감사 데이터베이스(SQL Database, Azure Database for MySQL 또는 Azure Virtual Machines의 데이터베이스)

다음 섹션에서는 SAP BOBI 플랫폼의 각 구성 요소에 대한 백업 및 복원 전략을 구현하는 방법을 설명합니다.

### <a name="backup-and-restore-for-an-sap-bobi-installation-directory"></a>SAP BOBI 설치 디렉터리의 백업 및 복원

Azure에서 VM 및 연결된 모든 디스크를 백업하는 가장 간단한 방법은 [Azure Backup](../../../backup/backup-azure-vms-introduction.md)을 사용하는 것입니다. VM에서 의도하지 않은 데이터 소멸을 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 복구 지점에 대한 기본 제공 관리를 사용하여 Recovery Services 자격 증명 모음에 저장됩니다. 구성 및 스케일링은 간단합니다. 백업은 최적화되어 있으며 필요할 경우 쉽게 복원할 수 있습니다.

백업 프로세스의 일부로 스냅샷이 생성됩니다. 데이터는 프로덕션 워크로드에 영향을 주지 않고도 Recovery Services 자격 증명 모음으로 전송됩니다. 스냅샷은 [스냅샷 일관성](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)에 설명된 대로 여러 수준의 일관성을 제공합니다. Backup은 [Azure VM 백업](../../../backup/backup-azure-vms-introduction.md) 솔루션에 더해 [Azure 디스크 백업](../../../backup/disk-backup-overview.md)을 사용하여 관리 디스크의 백업에 대한 병렬 지원을 제공합니다. 하루에 한 번 일정하게 VM을 백업해야 하거나 OS 디스크를 보다 자주 백업해야 하는 경우 또는 크래시 일관적인 특정 데이터 디스크를 백업해야 하는 경우에 유용합니다. 자세한 내용은 [Azure VM 백업 정보](../../../backup/backup-azure-vms-introduction.md), [Azure 디스크 백업](../../../backup/disk-backup-overview.md), [FAQ: Azure VM 백업](../../../backup/backup-azure-vm-backup-faq.yml)을 참조하세요.

### <a name="backup-and-restore-for-filestore"></a>파일 저장소의 백업 및 복원

배포에 따라 SAP BOBI 플랫폼의 파일 저장소는 Azure NetApp Files 또는 Azure Files에 있을 수 있습니다. 파일 저장소에 사용되는 스토리지에 따라 백업 및 복원에 대한 다음 옵션 중에서 선택합니다.

* **Azure NetApp Files**: Azure NetApp Files의 경우 스냅샷 정책을 사용하여 주문형 스냅샷을 만들고 자동 스냅샷을 예약할 수 있습니다. 스냅샷 복사는 Azure NetApp Files 볼륨의 특정 시점 복사본을 제공합니다. 자세한 내용은 [Azure NetApp Files를 사용하여 스냅샷 관리](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)를 참조하세요.
* **Azure Files**: Azure Files 백업은 기본 [Backup](../../../backup/backup-overview.md) 인스턴스와 통합되어 VM 백업과 함께 백업 및 복원 기능을 중앙 집중화하고 연산 작업을 간소화합니다. 자세한 내용은 [Azure 파일 공유 백업](../../../backup/azure-file-share-backup-overview.md) 및 [FAQ: Azure Files 백업](../../../backup/backup-azure-files-faq.yml)을 참조하세요.

별도의 NFS 서버를 만든 경우 동일한 서버에 대해 백업 및 복원 전략을 구현해야 합니다.

### <a name="backup-and-restore-for-the-cms-and-audit-database"></a>CMS 및 감사 데이터베이스의 백업 및 복원

Windows VM에서 실행되는 SAP BOBI 플랫폼의 경우 Azure에서 SAP BOBI 플랫폼을 계획하고 구현하는 방법에 대한 가이드의 [지원 매트릭스](businessobjects-deployment-guide.md#support-matrix)에 설명된 바에 따라 CMS 및 감사 데이터베이스를 지원되는 모든 데이터베이스에서 실행할 수 있습니다. 따라서 CMS 및 감사 데이터 스토리지에 사용된 데이터베이스를 기반으로 백업 및 복원 전략을 채택하는 것이 중요합니다.

* **SQL Database** 는 SQL Server 기술을 사용하여 매주 [전체 백업](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15), 12~24시간마다 [차등 백업](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15), 5~10분마다 [트랜잭션 로그](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15) 백업을 만듭니다. 트랜잭션 로그 백업의 빈도는 컴퓨팅 크기와 데이터베이스 작업의 양을 기준으로 합니다.
 
   사용자는 LRS, ZRS 또는 GRS blob 간에 백업 스토리지 중복성을 구성하는 옵션을 선택할 수 있습니다. 스토리지 중복성 메커니즘은 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연재해 등의 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 여러 복사본을 저장합니다. 기본적으로 SQL Database는 [쌍을 이루는 지역](../../../best-practices-availability-paired-regions.md)에 복제되는 [GRS blob](../../../storage/common/storage-redundancy.md)에 백업을 저장합니다. LRS 또는 ZRS blob에 대한 비즈니스 요구 사항에 따라 변경될 수 있습니다. SQL Database 백업 일정, 보존 및 스토리지 사용에 대한 최신 정보는 [자동화된 백업: Azure SQL Database 및 Azure SQL Managed Instance](../../../azure-sql/database/automated-backups-overview.md)를 참조하세요.

* **Azure Database for MySQL** 은 사용자 구성 LRS 또는 GRS에서 자동으로 서버 백업을 만들고 저장합니다. Azure Database for MySQL은 데이터 파일과 트랜잭션 로그를 백업합니다. 지원되는 최대 스토리지 크기에 따라 전체 및 차등 백업(최대 4TB 스토리지 서버) 또는 스냅샷 백업(최대 16TB 스토리지 서버)을 수행합니다. 백업을 사용하면 서버를 구성된 백업 보존 기간 내의 특정 시점으로 복원할 수 있습니다. 기본 백업 보존 기간은 7일이며 [필요에 따라 최대 35일까지 구성](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)할 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다. 백업 파일은 사용자에게 노출되지 않으며 내보낼 수 없습니다. 이러한 백업은 Azure Database for MySQL의 복원 작업에만 사용할 수 있습니다. [mysqldump](../../../mysql/concepts-migrate-dump-restore.md)를 사용하여 데이터베이스를 복사할 수 있습니다. 자세한 내용은 [Azure Database for MySQL의 백업 및 복원](../../../mysql/concepts-backup.md)을 참조하세요.

* **Azure VM에 설치된 데이터베이스** 의 경우 지원되는 데이터베이스에 표준 백업 도구 또는 [Backup](../../../backup/sap-hana-db-about.md)을 사용할 수 있습니다. 또한 Azure 서비스 및 도구가 요구 사항을 충족하지 않는 경우 모든 SAP BOBI 플랫폼 구성 요소를 위한 백업 및 복구 에이전트를 제공하도록 지원되는 타사 백업 도구를 사용할 수도 있습니다.

## <a name="high-availability"></a>고가용성

고가용성이란 동일한 데이터 센터 내에서 중복성, 내결함성 또는 장애 조치(failover) 보호 구성 요소를 통해 애플리케이션 또는 서비스의 비즈니스 연속성을 제공하여 IT 중단을 최소화하는 기술 세트를 말합니다. Microsoft의 경우에는 데이터 센터가 한 Azure 지역 내에 있습니다. [SAP용 고가용성 아키텍처 및 시나리오](sap-high-availability-architecture-scenarios.md) 문서에서는 SAP용 Azure 애플리케이션에서 제공하는 다양한 고가용성 기술과 권장 사항에 대한 인사이트를 제공하며, 이 섹션의 지침을 보완합니다.

SAP BOBI 플랫폼의 크기 조정 결과에 따라 환경을 디자인하고 Azure VM 및 서브넷에 BI 구성 요소를 어떻게 분산할 것인지 결정해야 합니다. 분산 아키텍처의 중복성 수준은 비즈니스에 필요한 RTO(복구 시간 목표) 및 RPO(복구 지점 목표)에 따라 달라집니다. SAP BOBI 플랫폼에는 여러 계층이 있으며 각 계층의 구성 요소는 중복성을 얻을 수 있도록 설계되어야 합니다. 그래야 한 구성 요소에서 오류가 발생하더라도 SAP BOBI 애플리케이션 중단이 거의 또는 전혀 없습니다. 예를 들면 다음과 같습니다.

- BI 애플리케이션 서버 및 웹 서버와 같은 중복 애플리케이션 서버
- CMS 데이터베이스, 파일 저장소 및 부하 분산 장치와 같은 고유한 구성 요소

다음 섹션에서는 SAP BOBI 플랫폼의 각 구성 요소에서 고가용성을 구현하는 방법을 설명합니다.

### <a name="high-availability-for-application-servers"></a>애플리케이션 서버의 고가용성

BI 및 웹 애플리케이션 서버는 별도로 설치하든 함께 설치하든 특정 고가용성 솔루션이 필요 없습니다. 중복성을 통해, 즉, 다양한 Azure VM에서 BI 및 웹 서버의 여러 인스턴스를 구성하여 고가용성을 달성할 수 있습니다. 비즈니스에 필요한 RTO에 따라 [가용성 집합](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set) 또는 [가용성 영역](sap-high-availability-architecture-scenarios.md#azure-availability-zones)에서 VM을 배포할 수 있습니다. 가용성 영역 간 배포의 경우 SAP BOBI 플랫폼의 다른 모든 구성 요소가 영역 중복으로 설계되어야 합니다.

현재 모든 Azure 지역에서 가용성 영역을 제공하는 것은 아니므로 지역에 따라 배포 전략을 채택해야 합니다. 영역을 제공하는 Azure 지역은 [Azure 가용성 영역](../../../availability-zones/az-overview.md)에 명시되어 있습니다.

> [!Important]
> Azure 가용성 영역과 Azure 가용성 집합의 개념은 함께 사용할 수 없습니다. 즉, 한 쌍 또는 여러 VM을 특정 가용성 영역 또는 Azure 가용성 집합에 배포할 수 있지만 둘 다 배포할 수는 없습니다.

### <a name="high-availability-for-the-cms-database"></a>CMS 데이터베이스의 고가용성

CMS 및 감사 데이터베이스의 솔루션으로 Azure 데이터베이스를 사용하는 경우 기본적으로 로컬 중복 고가용성 프레임워크가 제공됩니다. 추가 구성 요소를 구성할 필요 없이 지역과 서비스에 고유한 고가용성, 중복성 및 복원력 기능을 선택합니다. SAP BOBI 플랫폼의 배포 전략이 가용성 영역 전반에 적용되는 경우 CMS 및 감사 데이터베이스에 대한 영역 중복성을 구현해야 합니다. Azure에서 지원되는 데이터베이스 제품의 고가용성에 대한 자세한 내용은 [Azure SQL Database의 고가용성](../../../azure-sql/database/high-availability-sla.md) 및 [Azure Database for MySQL의 고가용성](../../../mysql/concepts-high-availability.md)을 참조하세요. 

CMS 데이터베이스에 대한 다른 데이터베이스 관리 시스템(DBMS) 배포의 경우 [SAP 워크로드를 위한 DBMS 배포 가이드](dbms_guide_general.md)에서 다양한 DBMS 배포와 고가용성을 구현하는 접근 방식에 대한 인사이트를 확인하세요.

### <a name="high-availability-for-filestore"></a>파일 저장소의 고가용성

파일 저장소는 보고서, 유니버스, 연결 등의 콘텐츠가 저장되는 디스크 디렉터리를 나타내며 해당 시스템의 모든 애플리케이션 서버에서 공유됩니다. 따라서 다른 SAP BOBI 플랫폼 구성 요소와 함께 고가용성이 확보되는지 항상 확인해야 합니다.

Windows에서 실행되는 SAP BOBI 플랫폼의 경우 [Azure Premium Files](../../../storage/files/storage-files-introduction.md) 또는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 파일 저장소로 선택할 수 있습니다. 파일 저장소는 본래 고가용성과 고내구성을 갖추도록 설계되었습니다. Azure Premium Files는 ZRS를 지원하며, 이는 SAP BOBI 플랫폼의 영역 간 배포에 유용할 수 있습니다. 자세한 내용은 Azure Files의 [중복성](../../../storage/files/storage-files-planning.md#redundancy) 섹션을 참조하세요.

모든 지역에서 파일 공유 서비스를 사용할 수는 없으므로 [지역에서 사용할 수 있는 제품](https://azure.microsoft.com/global-infrastructure/services/) 목록에서 최신 정보를 확인하세요. 현재 지역에서 이 서비스를 사용할 수 없는 경우 파일 시스템을 SAP BOBI 애플리케이션에 공유할 수 있는 NFS 서버를 만들면 됩니다. 하지만 고가용성도 고려해야 합니다.

### <a name="high-availability-for-the-load-balancer"></a>부하 분산 장치의 고가용성

웹 서버에 트래픽을 분산하기 위해 Load Balancer 또는 Application Gateway를 사용할 수 있습니다. 다음과 같은 두 가지 부하 분산 장치의 중복성은 배포에서 선택하는 SKU를 기반으로 달성할 수 있습니다.

* **Load Balancer**: 표준 Load Balancer 프런트 엔드를 영역 중복으로 구성하여 중복성을 달성할 수 있습니다. 자세한 내용은 [표준 Load Balancer 및 가용성 영역](../../../load-balancer/load-balancer-standard-availability-zones.md)을 참조하세요.
* **Application Gateway**: 배포 중에 선택하는 계층 종류에 따라 고가용성을 달성할 수 있습니다.
   * v1 SKU는 둘 이상의 인스턴스를 배포한 경우 고가용성 시나리오를 지원합니다. 모든 인스턴스가 동시에 실패하는 일이 없도록, Azure는 이러한 인스턴스를 업데이트 및 장애 도메인에 배포합니다. 이 SKU를 선택하면 영역 내에서 중복성을 달성할 수 있습니다.
   * v2 SKU의 경우에는 여러 장애 도메인과 업데이트 도메인에 새 인스턴스가 자동으로 분산됩니다. 영역 중복을 선택하면 영역 장애 시의 복원력을 제공하기 위해 최신 인스턴스도 여러 가용성 영역에 분산됩니다. 자세한 내용은 [자동 스케일링 및 영역 중복 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)를 참조하세요.

### <a name="reference-high-availability-architecture-for-the-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 플랫폼의 고가용성 참조 아키텍처

다음 참조 아키텍처는 Windows 서버에서 실행되는 가용성 영역 전반에 걸쳐 SAP BOBI 플랫폼을 설정하는 방법을 설명합니다. 아키텍처는 Application Gateway, Azure Premium Files(파일 저장소), SQL Database(CMS 및 감사 데이터베이스)와 같이 다양한 Azure 서비스의 사용을 보여 줍니다. SAP BOBI 플랫폼은 기본 제공 영역 중복성을 통해 다양한 고가용성 솔루션을 관리하는 복잡성을 줄여줍니다.

다음 그림에서 수신 트래픽(HTTPS - TCP/443)은 여러 가용성 영역을 아우르는 Application Gateway v2 SKU를 사용하여 부하 분산됩니다. Application gateway는 여러 가용성 영역에 분산된 웹 서버에 사용자 요청을 분산합니다. 웹 서버는 가용성 영역 전반에 걸친 별도의 VM에 배포된 관리 및 처리 서버 인스턴스에 요청을 전달합니다. ZRS를 사용하는 Azure 프리미엄 파일은 관리 및 스토리지 계층 VM에 대한 프라이빗 링크를 통해 연결되어 있어, 보고서, 유니버스, 연결과 같은 콘텐츠에 액세스할 수 있습니다. 애플리케이션은 SQL Database의 영역 중복 데이터베이스 인스턴스에서 실행 중인 CMS 및 감사 데이터베이스에 액세스하며, 이를 통해 Azure 지역 내의 여러 물리적 위치에 데이터베이스가 복제됩니다.

![Windows의 SAP BOBI 플랫폼에 대한 고가용성 아키텍처를 보여 주는 다이어그램](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

앞의 아키텍처는 Azure에서 SAP BOBI를 배포하는 방법에 대한 인사이트를 제공합니다. 하지만 Azure에서 가능한 SAP BOBI 플랫폼의 모든 구성 옵션을 다루지는 않습니다. Load Balancer, 파일 리포지토리 서버, DBMS 등의 구성 요소에 사용할 여러 제품 또는 서비스를 선택하여 비즈니스 요구 사항에 맞게 배포를 조정할 수 있습니다.

선택한 지역에서 가용성 영역을 사용할 수 없는 경우 가용성 집합에 Azure VM을 배포할 수 있습니다. Azure의 가용성 집합 내에 배치한 VM은 다수의 물리적 서버, 컴퓨팅 랙, 스토리지 단위 및 네트워크 스위치에서 실행됩니다. 하드웨어 또는 소프트웨어 한 곳에 장애가 발생해도 VM의 하위 집합만 영향을 받고 전체 솔루션은 계속 작동합니다.

## <a name="disaster-recovery"></a>재해 복구

이 섹션에서는 SAP BOBI 플랫폼에 DR 보호를 제공하는 전략을 설명하며, 전반적인 SAP DR 접근 방식의 기본 리소스를 보여 주는 [SAP의 재해 복구](../../../site-recovery/site-recovery-sap.md) 문서를 보완합니다. SAP BOBI 플랫폼의 경우 DR 환경을 안전하게 구현하는 다음의 방법을 설명하는 SAP Note [2056228](https://launchpad.support.sap.com/#/notes/2056228)을 참조하세요.

 * 수명 주기 관리 또는 페더레이션을 완전히 또는 선택적으로 사용하여 기본 시스템의 콘텐츠를 승격하거나 배포합니다.
 * 정기적으로 CMS 및 FRS 콘텐츠를 복사합니다.

이 가이드에서는 DR 환경을 구현하는 두 번째 옵션에 대해 설명합니다. DR에 가능한 모든 구성 옵션의 전체 목록을 다루지는 않습니다. SAP BOBI 플랫폼 구성과 함께 기본 Azure 서비스를 제공하는 솔루션을 다룹니다.

>[!Important]
>SAP BOBI 플랫폼에서 각 구성 요소의 가용성은 보조 지역에 팩터링되어야 합니다. 전체 DR 전략은 철저한 테스트를 거쳐야 합니다.

### <a name="reference-dr-architecture-for-an-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 플랫폼의 참조 DR 아키텍처

이 참조 아키텍처는 애플리케이션 서버가 중복되는 SAP BOBI 플랫폼의 다중 인스턴스 배포를 실행합니다. DR의 경우 SAP BOBI 플랫폼의 모든 구성 요소를 보조 지역으로 장애 조치(failover)해야 합니다. 다음 그림에서 Azure Premium Files는 파일 저장소로 사용되고, SQL Database는 CMS 및 감사 리포지토리로 사용되며, Application Gateway는 트래픽 부하를 분산하는 데 사용됩니다. 구성 요소마다 DR 보호를 구현하는 전략은 서로 다르며, 이에 대해서는 다음 섹션에서 설명합니다.

![Windows용 SAP BusinessObjects BI 플랫폼 DR을 보여 주는 다이어그램](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

Load Balancer는 SAP BOBI 플랫폼의 웹 애플리케이션 서버 간에 트래픽을 분산하는 데 사용됩니다. Azure에서 Load Balancer 또는 Application Gateway를 사용하여 웹 서버 간에 트래픽 부하를 분산할 수 있습니다. 부하 분산 장치 서비스에 DR을 구현하려면 보조 지역에 다른 부하 분산 장치 또는 Application Gateway를 구현해야 합니다. DR 장애 조치(failover) 후 동일한 URL을 유지하려면 DNS의 항목을 변경하고 보조 지역에서 실행되는 부하 분산 서비스를 가리킵니다.

### <a name="virtual-machines-that-run-web-and-bi-application-servers"></a>웹 및 BI 애플리케이션 서버를 실행하는 가상 머신

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)를 사용하여 웹 및 BI 애플리케이션 서버를 실행하는 VM을 보조 지역에 복제할 수 있습니다. 보조 지역에 서버와 연결된 모든 관리 디스크를 복제하므로 재해로 인해 중단 발생 시 복제 환경으로 쉽게 장애 조치(failover)하여 작업을 계속할 수 있습니다. 모든 SAP 애플리케이션 VM을 Azure DR 데이터 센터로 복제하는 작업을 시작하려면 [Azure로 가상 머신 복제](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)의 지침을 따릅니다.

### <a name="filestore"></a>파일 저장소

파일 저장소는 보고서, BI 문서와 같은 실제 파일이 저장되는 디스크 디렉터리입니다. 파일 저장소의 모든 파일이 DR 지역에 동기화되는 것이 중요합니다. Windows에서 실행 중인 SAP BOBI 플랫폼에 사용되는 파일 공유 서비스 유형에 따라 콘텐츠를 동기화하는 데 필요한 DR 전략을 채택해야 합니다. 예를 들면 다음과 같습니다.

- **Azure Premium Files** 는 LRS와 ZRS만 지원합니다. Azure Premium Files DR 전략의 경우 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 또는 [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0)을 사용하여 다른 지역의 다른 스토리지 계정에 파일을 복사할 수 있습니다. 자세한 내용은 [재해 복구 및 저장소 계정 장애 조치(failover)](../../../storage/common/storage-disaster-recovery-guidance.md)를 참조하세요.
- **Azure NetApp Files** 는 NFS 및 SMB 볼륨을 제공하므로 모든 파일 기반 복사 도구를 사용하여 Azure 지역 간에 데이터를 복제할 수 있습니다. 다른 지역의 Azure NetApp Files 볼륨을 복사하는 방법에 대한 자세한 내용은 [Azure NetApp Files에 대한 FAQ](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)를 참조하세요.

  Azure NetApp Files 지역 간 복제를 사용할 수 있습니다. NetApp SnapMirror 기술을 사용하는 이 복제 기능은 현재 [미리 보기](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)로 제공됩니다. 이 기술을 사용하면 변경된 블록만이 압축된 효과적인 형태로 네트워크를 통해 전송됩니다. 이 독점적 기술은 지역 간에 복제하는 데 필요한 데이터 양을 최소화하여 데이터 전송 비용을 절감합니다. 복제 시간이 단축되므로 RPO를 줄일 수도 있습니다. 자세한 내용은 [지역 간 복제 사용에 대한 요구 사항 및 고려 사항](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)을 참조하세요.

### <a name="cms-database"></a>CMS 데이터베이스

DR 지역의 CMS 및 감사 데이터베이스는 주 지역에서 실행 중인 데이터베이스의 복사본이어야 합니다. 데이터베이스 유형을 기준으로 비즈니스에 필요한 RTO 및 RPO에 따라 DR 지역에 데이터베이스를 복사하는 것이 중요합니다. 이 섹션에서는 Windows에서 실행되는 SAP BOBI 애플리케이션에 대해 지원되는 Azure의 각 데이터베이스 솔루션에 사용할 수 있는 다양한 옵션을 설명합니다.

#### <a name="azure-sql-database"></a>Azure SQL Database

[SQL Database](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) DR 전략의 경우 두 가지 옵션으로 데이터베이스를 보조 지역에 복사할 수 있습니다. 두 가지 복구 옵션 모두 서로 다른 수준의 RTO 및 RPO를 제공합니다. 각 복구 옵션의 RTO 및 RPO에 대한 자세한 내용은 [기존 서버에 데이터베이스 복구](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server)를 참조하세요.

옵션 1: [지역 중복 데이터베이스 백업 복원](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   기본적으로 SQL Database는 [쌍을 이루는 지역](../../../best-practices-availability-paired-regions.md)에 복제되는 [GRS blob](../../../storage/common/storage-redundancy.md)에 데이터를 저장합니다. SQL Database의 백업 스토리지 중복도는 CMS 및 감사 데이터베이스 생성 시에 구성하거나 기존 데이터베이스를 업데이트할 수 있습니다. 기본 데이터베이스에 대한 변경 사항은 향후 백업에만 적용됩니다. 가장 최근의 지역 복제 백업에서 Azure 지역에 있는 SQL Database의 데이터베이스를 복원할 수 있습니다. 지리적 복원에서는 지역에서 복제된 백업을 원본으로 사용합니다. 백업을 만들 때와 다른 지역에 있는 Azure Blob으로 지역 복제하는 사이에 지연이 있습니다. 따라서 복원된 데이터베이스는 원본 데이터베이스보다 최대 1시간 늦을 수 있습니다.

   >[!Important]
   >지역 복원은 지역 중복 [백업 스토리지](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy)가 구성된 SQL Database에 사용할 수 있습니다.

옵션 2: [지역 복제](../../../azure-sql/database/active-geo-replication-overview.md) 또는 [자동 장애 조치(failover) 그룹](../../../azure-sql/database/auto-failover-group-overview.md)

   지역 복제는 동일하거나 다른 지역의 서버에 개별 데이터베이스의 읽기 가능한 보조 데이터베이스를 만들 수 있는 SQL Database 기능입니다. CMS 및 감사 데이터베이스에 지역 복제를 사용하는 경우 애플리케이션이 다른 Azure 지역에서 보조 데이터베이스에 대한 장애 조치(failover)를 시작할 수 있습니다. 지역 복제는 개별 데이터베이스에 대해 사용 설정되어 있지만 SAP BOBI 애플리케이션에 여러 데이터베이스(CMS 및 감사)의 투명하고 조율된 장애 조치를 사용 설정하려면 자동 장애 조치 그룹을 사용하는 것이 좋습니다. 활성 지역 복제를 바탕으로 그룹 의미 체계를 제공하며, 이는 전체 SQL 서버(모든 데이터베이스)가 개별 데이터베이스가 아닌 다른 지역에 복제된다는 의미입니다. [장애 조치 그룹과 지역 복제를 비교하는](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups) 기능 표를 확인합니다.

   자동 장애 조치 그룹은 장애 조치하는 동안 변경되지 않는 읽기/쓰기 및 읽기 전용 수신기 엔드포인트를 제공합니다. 읽기/쓰기 엔드포인트는 CMS 및 감사 데이터베이스의 ODBC 연결 항목에서 수신기로 유지 관리할 수 있습니다. 따라서 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치(failover)가 완료되면 엔드포인트를 새 지역으로 리디렉션하도록 DNS 레코드가 자동으로 업데이트됩니다. 읽기/쓰기 엔드포인트가 ODBC 연결에서 수신기로 유지되기 때문에 애플리케이션은 CMS 데이터베이스에 자동으로 연결됩니다.

   다음 이미지에서는 미국 동부 2 지역에서 실행 중인 SQL 서버(azussqlbodb)의 자동 장애 조치 그룹이 미국 동부 보조 지역(DR 사이트)에 복제됩니다. 읽기/쓰기 수신기 엔드포인트는 Windows에서 실행 중인 BI 애플리케이션 서버의 ODBC 연결에서 수신기로 유지됩니다. 장애 조치 이후에도 엔드포인트는 동일하게 유지됩니다. BI 애플리케이션을 보조 지역의 SQL Database에 연결하는 데 직접 개입할 필요가 없습니다.

   ![SQL Database 자동 장애 조치(failover) 그룹을 보여 주는 스크린샷](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   이 옵션은 옵션 1보다 RTO 및 RPO가 낮습니다. 이 옵션에 대한 자세한 내용은 [Use autofailover groups to enable transparent and coordinated failover of multiple databases](../../../azure-sql/database/auto-failover-group-overview.md)(자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치 사용)를 참조하세요.

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL는 재해 발생 시 데이터베이스를 복구하는 옵션을 제공합니다. 비즈니스에 적합한 옵션을 선택하세요.

* 지역 간 읽기 복제본을 사용하여 비즈니스 연속성 및 DR 계획을 향상합니다. 원본 서버에서 최대 5개의 복제본으로 복제할 수 있습니다. 읽기 복제본은 Azure Database for MySQL 이진 로그 복제 기술을 사용하여 비동기 방식으로 업데이트됩니다. 복제본은 일반 Azure Database for MySQL 서버와 비슷한 방식으로 관리하는 새로운 서버입니다. 복제본 읽기, 사용 가능한 지역, 제한 사항 및 장애 조치(failover) 방법을 자세히 알아보려면 [Azure Database for MySQL의 읽기 복제본](../../../mysql/concepts-read-replicas.md)을 참조하세요.

* 지역 중복 백업을 사용하여 서버를 복원하는 Azure Database for MySQL의 지역 복원 기능을 사용합니다. 이러한 백업은 서버를 호스트하는 지역이 오프라인인 경우에도 액세스가 가능합니다. 이러한 백업에서 다른 지역으로 복원하여 서버를 다시 온라인 상태로 만들 수 있습니다.

  > [!Important]
  > 지역 복원은 지역 중복 백업 스토리지로 서버를 프로비전한 경우에만 가능합니다. 서버를 만든 후에는 백업 중복 옵션을 변경할 수 없습니다. 자세한 내용은 [백업 중복성](../../../mysql/concepts-backup.md#backup-redundancy-options)을 참조하세요.

다음 표에는 이 예제에서 사용되는 각 계층의 DR에 대한 권장 사항이 나와 있습니다.

| SAP BOBI 플랫폼 계층                          | 권장                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure Application Gateway 또는 Azure Load Balancer | 보조 지역에서 Application Gateway 병렬 설정    |
| 웹 애플리케이션 서버                          | Azure Site Recovery를 사용하여 복제                             |
| BI 애플리케이션 서버                           | Site Recovery를 사용하여 복제                             |
| Azure Premium Files                              | AzCopy ‘또는’ Azure PowerShell                               |
| Azure NetApp Files                               | 보조 지역 ‘또는’ Azure NetApp Files 지역 간 복제(미리 보기)로 데이터를 복제하는 파일 기반 복사 도구 |
| Azure SQL Database                               | 지역 복제/자동 장애 조치(failover) 그룹 ‘또는’ 지역 복원     |
| Azure Database for MySQL                         | 지역 간 읽기 복제본 ‘또는’ 지역 중복 백업에서 백업 복원 |

## <a name="next-steps"></a>다음 단계

- [다중 계층 SAP 앱 배포를 위한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md)
- [SAP용 Azure Virtual Machines 계획 및 구현](planning-guide.md)
- [SAP용 Azure Virtual Machines 배포](deployment-guide.md)
- [SAP용 Azure Virtual Machines DBMS 배포](./dbms_guide_general.md)

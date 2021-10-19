---
title: Azure Portal을 사용하여 Windows 가상 머신 프로비전
description: 이 가이드에서는 Azure Portal을 사용하여 Windows 가상 머신에서 SQL Server를 프로비전하는 데 사용할 수 있는 옵션을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: pamela
ms.reviewer: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 2bc4efc0be1e216a10328f5476f592a3dc1156f0
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167086"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Azure Portal을 사용하여 SQL Server로 Windows 가상 머신을 프로비전하는 방법

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 가이드에서는 Azure Portal을 사용하여 Windows VM(가상 머신)에서 SQL Server를 프로비전하는 데 사용할 수 있는 옵션을 설명합니다. 이 문서에서는 단일 구성에 더 중점을 둔 [SQL Server VM 빠른 시작](sql-vm-create-portal-quickstart.md)보다 더 많은 구성 옵션을 다룹니다. 

이 가이드를 사용하여 고유한 SQL Server VM을 만듭니다. 또는 그것을 Azure Portal에서 사용 가능한 옵션에 대한 참조로 사용합니다.

> [!TIP]
> SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>SQL Server 가상 머신 갤러리 이미지

SQL Server 가상 머신을 만들 때 가상 머신 갤러리에서 미리 구성된 여러 이미지 중 하나를 선택할 수 있습니다. 다음 단계에서는 SQL Server 2017 이미지 중 하나를 선택하는 방법을 보여줍니다.

1. Azure Portal의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색 상자에 *Azure SQL* 을 입력합니다. 

   **Azure SQL** 옆에 있는 별표를 선택하여 즐겨찾기로 저장하고 왼쪽 탐색에서 항목으로 추가합니다. 

1. **+ 추가** 를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. **세부 정보 표시** 를 선택하여 추가 정보를 볼 수 있습니다. 
1. **SQL 가상 머신** 타일의 SQL Server 이미지 검색 상자에 *2017* 을 입력한 다음, 드롭다운에서 **무료 SQL Server 라이선스: Windows Server 2016의 SQL Server 2017 Developer** 를 선택합니다. 

   ![SQL VM 이미지 선택](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > 개발자 버전은 개발 테스트 목적으로 무료로 제공되는 SQL Server의 모든 기능을 갖춘 버전이므로 이 문서에서 사용됩니다. VM 실행 비용에 대해서만 비용을 지불합니다. 그러나 이 연습에 사용할 이미지를 자유롭게 선택할 수 있습니다. 사용 가능한 이미지에 대한 설명은 [SQL Server Windows Virtual Machines 개요](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)를 참조하세요.

   > [!TIP]
   > SQL Server의 라이선스 비용은 사용자가 만드는 VM의 초당 가격에 통합되며 에디션과 코어에 따라 다릅니다. 그러나 SQL Server Developer 버전은 프로덕션이 아닌 개발 및 테스트에 무료로 사용할 수 있습니다. 또한 SQL Express는 경량 워크로드(1GB 미만의 메모리, 10GB 미만의 스토리지)에 대해 무료입니다. 또한 BYOL(사용자 라이선스 필요)할 수 있으며 VM에 대해서만 지불에 대해서만 지불합니다. 이러한 이미지 이름에는 접두사 {BYOL}이 붙습니다. 
   >
   > 이러한 옵션에 대한 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](pricing-guidance.md)을 참조하세요.


1. **만들기** 를 선택합니다.


## <a name="1-configure-basic-settings"></a>1. 기본 설정 구성

**기본** 탭에서 다음 정보를 제공합니다.

* **프로젝트 세부 정보** 에서 올바른 구독이 선택되어 있는지 확인합니다. 
* **리소스 그룹** 섹션의 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 내 관련 리소스의 컬렉션입니다(가상 머신, 스토리지 계정, 가상 네트워크 등). 

  ![Subscription](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > 새 리소스 그룹을 사용하면 Azure에서 SQL Server 배포를 테스트하거나 알아보는 경우에 유용합니다. 테스트를 완료한 후 리소스 그룹을 삭제하면 VM과 해당 리소스 그룹과 연결된 모든 리소스가 자동으로 삭제됩니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../../active-directory-b2c/overview.md)를 참조하세요.


* **인스턴스 세부 정보** 에서

    1. 고유한 **가상 머신 이름** 을 입력합니다.  
    1. **영역** 에 위치를 선택합니다. 
    1. 이 가이드에서는 **가용성 옵션** 을 _인프라 중복성 필요 없음_ 으로 설정합니다. 가용성 옵션에 대한 자세한 내용을 알아보려면 [가용성](../../../virtual-machines/availability.md)을 참조하세요. 
    1. **이미지** 목록에서 _무료 SQL Server 라이선스: Windows Server 2016의 SQL Server 2017 Developer_ 이미지를 선택합니다.  
    1. 가상 머신의 **크기** 에서 **크기 변경** 을 선택하고 **A2 Basic** 제품을 선택합니다. 리소스를 다 사용했으면 예기치 않은 요금이 청구되지 않도록 리소스를 삭제하세요. 프로덕션 워크로드의 경우 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](./performance-guidelines-best-practices-checklist.md)에서 권장하는 컴퓨터 크기 및 구성을 참조하세요.

    ![인스턴스 세부 정보](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> **크기 선택** 창에 표시된 월별 예상 비용에는 SQL Server 라이선스 비용이 포함되지 않습니다. 이 예상 비용은 VM만의 비용입니다. SQL Server의 Express 및 개발자 버전의 경우, 이 예상 비용은 총 예상 비용입니다. 다른 버전의 경우 [Windows Virtual Machines 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)를 참조하여 SQL Server의 대상 버전을 선택하세요. 또한 [SQL Server Azure VM에 대한 가격 책정 지침](pricing-guidance.md)과 [가상 머신의 크기](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

* **관리 계정** 에서 사용자 이름과 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../../../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다.

   ![관리자 계정](./media/create-sql-vm-portal/basics-administrator-account.png)

* **인바운드 포트 규칙** 에서 **선택한 포트 허용** 을 선택한 다음, 드롭다운에서 **RDP(3389)** 를 선택합니다. 

   ![인바운드 포트 규칙](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. 선택적 기능 구성

### <a name="disks"></a>디스크

**디스크** 탭에서 디스크 옵션을 구성합니다. 

* **OS 디스크 유형** 아래의 드롭다운에서 OS에 사용할 디스크 유형을 선택합니다. 프리미엄은 프로덕션 시스템에 권장되지만 기본 VM에는 사용할 수 없습니다. 프리미엄 SSD를 사용하려면 가상 머신 크기를 변경합니다. 
* **고급** 의 **Managed Disks** 에서 **예** 를 선택합니다.

   > [!NOTE]
   > SQL Server에 Managed Disks를 사용하는 것이 좋습니다. Managed Disks는 배후에서 스토리지를 처리해줍니다. 또한 Managed Disks가 있는 가상 머신이 동일한 가용성 집합에 속할 경우 Azure는 스토리지 리소스를 배포하여 적절한 중복성을 제공합니다. 자세한 내용은 [Azure Managed Disks 개요](../../../virtual-machines/managed-disks-overview.md)를 참조하세요. 가용성 집합의 Managed Disks에 대한 구체적인 내용을 보려면 [가용성 집합에서 VM에 Managed Disks 사용](../../../virtual-machines/availability.md)을 참조하세요.

![SQL VM 디스크 설정](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>네트워킹

**네트워킹** 탭에서 네트워킹 옵션을 구성합니다. 

* 새 **가상 네트워크** 를 만들거나 SQL Server VM에 기존 가상 네트워크를 사용합니다. **서브넷** 도 지정합니다. 

* **NIC 네트워크 보안 그룹** 에서 기본 보안 그룹 또는 고급 보안 그룹을 선택합니다. 기본 옵션을 선택하면 **기본** 탭에 구성된 값과 동일한 SQL Server VM에 대한 인바운드 포트를 선택할 수 있습니다. 고급 옵션을 선택하면 기존 네트워크 보안 그룹을 선택하거나 새 네트워크 보안 그룹을 만들 수 있습니다. 

* 네트웨크 설정을 변경해도 되고 기본 값을 그대로 유지해도 됩니다.

![SQL VM 네트워킹 설정](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>모니터링

**모니터링** 탭에서 모니터링과 자동 종료를 구성합니다. 

* Azure에서는 VM에 지정된 것과 동일한 스토리지 계정을 통해 **부팅 진단** 이 기본적으로 사용됩니다. 이 탭에서 이러한 설정을 변경하고 **OS 게스트 진단** 을 사용하도록 설정할 수 있습니다. 
* 이 탭에서 **시스템 할당 관리 ID** 와 **자동 종료** 를 사용하도록 설정할 수도 있습니다. 

![SQL VM 관리 설정](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. SQL Server 설정 구성

**SQL Server 설정** 탭에서 SQL Server에 대한 특정 설정과 최적화를 구성합니다. SQL Server에 대해 다음 설정을 구성할 수 있습니다.

- [연결](#connectivity)
- [인증](#authentication)
- [Azure Key Vault 통합](#azure-key-vault-integration)
- [스토리지 구성](#storage-configuration)
- [자동화된 패치](#automated-patching)
- [자동화된 백업](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>연결

**SQL 연결** 에서 VM의 SQL Server 인스턴스에 대해 원하는 액세스 유형을 지정합니다. 이 연습에서는 **공개(인터넷)** 를 지정하여 인터넷 상의 컴퓨터 또는 서비스에서 SQL Server로의 연결을 허용합니다. 이 옵션을 선택하면 Azure에서는 선택한 포트에서 트래픽을 허용하도록 방화벽 및 네트워크 보안 그룹을 자동으로 구성합니다.

> [!TIP]
> 기본적으로 SQL Server는 잘 알려진 포트 **1433** 에서 수신 대기합니다. 보안 향상을 위해 이전 대화 상자의 포트를 기본 이외 포트(예: 1401)를 수신하도록 변경하세요. 포트를 변경할 경우 SSMS(SQL Server Management Studio)와 같은 클라이언트 도구에서 해당 포트를 사용하여 연결해야 합니다.

![SQL VM 보안](./media/create-sql-vm-portal/azure-sqlvm-security.png)

인터넷을 통해 SQL Server에 연결하려면 SQL Server 인증을 사용하도록 설정해야 합니다. 이 내용은 다음 섹션에 설명되어 있습니다.

인터넷을 통해 데이터베이스 엔진에 대한 연결을 사용하도록 설정하지 않으려면 다음 옵션 중 하나를 선택합니다.

* **로컬(VM 내부만)** 을 선택합니다.
* **프라이빗(Virtual Network 내부)** 은 동일한 가상 네트워크의 머신 또는 서비스에서 SQL Server로의 연결을 허용합니다.

일반적으로, 시나리오에 허용되는 가장 제한적인 연결을 선택하여 보안을 개선합니다. 하지만 모든 옵션은 NSG(네트워크 보안 그룹) 규칙 및 SQL/Windows 인증을 통해 보호할 수 있습니다. VM이 생성된 후 NSG를 편집할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](security-considerations-best-practices.md)을 참조하세요.

### <a name="authentication"></a>인증

SQL Server 인증이 필요한 경우 **SQL Server 설정** 탭의 **SQL 인증** 아래에서 **사용** 을 선택합니다.

![SQL Server 인증](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> 인터넷(공용 연결 옵션)을 통해 SQL Server에 액세스하려는 경우 여기에서 SQL 인증을 사용해야 합니다. SQL Server에 대한 공용 액세스에는 SQL 인증이 필요합니다.

SQL Server 인증을 사용하도록 설정하는 경우 **로그인 이름** 및 **암호** 를 지정합니다. 이 로그인 이름은 SQL Server 인증 로그인과 **sysadmin** 고정 서버 역할의 구성원으로 구성됩니다. 인증 모드에 대한 자세한 내용은 [인증 모드 선택](/sql/relational-databases/security/choose-an-authentication-mode) 을 참조하세요.

SQL Server 인증을 사용하도록 설정하지 않으려면 VM의 로컬 관리자 계정을 사용하여 SQL Server 인스턴스에 연결하면 됩니다.

### <a name="azure-key-vault-integration"></a>Azure Key Vault 통합

암호화를 위해 Azure에 보안 비밀을 저장하려면 **SQL Server 설정** 을 선택하고 **Azure Key Vault 증명 모음 통합** 으로 스크롤합니다. **사용** 을 선택하고 요청된 정보를 입력합니다. 

![Azure Key Vault 통합](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

다음 표에서는 AKV(Azure Key Vault) 통합을 구성하는 데 필요한 매개 변수를 나열합니다.

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| **주요 자격 증명 모음 URL** |주요 자격 증명 모음의 위치입니다. |`https://contosokeyvault.vault.azure.net/` |
| **주체 이름** |Azure Active Directory 서비스 주체 이름. 이 이름을 클라이언트 ID라고도 합니다. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **주체 암호** |Azure Active Directory 서비스 주체 암호입니다. 이 암호를 클라이언트 암호라고도 합니다. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **자격 증명 이름** |**자격 증명 이름**: AKV 통합은 SQL Server 내에 자격 증명을 만들고 VM이 키 자격 증명 모음에 액세스할 수 있도록 합니다. 이 자격 증명의 이름을 선택하세요. |`mycred1` |

자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure Key Vault 통합 구성](azure-key-vault-integration-configure.md)을 참조하세요.

### <a name="storage-configuration"></a>스토리지 구성

**SQL Server 설정** 탭의 **스토리지 구성** 에서 **구성 변경** 을 선택하여 성능 최적화 스토리지 구성 페이지를 열고 스토리지 요구 사항을 지정합니다.

![스토리지 구성을 변경할 수 있는 위치를 강조 표시하는 스크린샷.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

**다음에 대해 스토리지 최적화** 에서 다음 옵션 중 하나를 선택합니다.

* **일반** 은 기본 설정이며 대부분의 워크로드를 지원합니다.
* **트랜잭션 처리** 는 기존의 데이터베이스 OLTP 워크로드용으로 스토리지를 최적화합니다.
* **데이터 웨어하우징** 은 분석 및 보고 워크로드용으로 스토리지를 최적화합니다.

![SQL VM 스토리지 구성](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

값을 기본값으로 유지하도록 선택하거나 IOPS 요구에 맞게 스토리지 토폴로지를 수동으로 변경할 수 있습니다. 자세한 내용은 [스토리지 구성](storage-configuration.md)을 참조하세요. 

### <a name="sql-server-license"></a>SQL Server 라이선스

Software Assurance 고객의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 사용자 고유의 SQL Server 라이선스를 가져와 리소스를 절약할 수 있습니다. 

![SQL VM 라이선스](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>자동화된 패치

**Automated patching** 이 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. 요일, 시간 및 유지 관리 기간에 대한 날짜를 지정합니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘을 사용합니다. Azure에서 SQL Server와 운영 체제를 자동으로 패치하지 않으려면 **사용 안 함** 을 선택합니다.  

![SQL VM 자동화된 패치](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

자세한 내용은 [Azure Virtual Machines에서 SQL Server의 자동화된 패치](automated-patching.md)를 참조하세요.

### <a name="automated-backup"></a>자동화된 백업

**자동화된 백업** 에서 모든 데이터베이스에 대해 자동 데이터베이스 백업을 사용하도록 설정합니다. 자동화된 백업은 기본적으로 사용하지 않도록 설정됩니다.

SQL 자동화된 백업을 사용하도록 설정하면 다음 설정을 구성할 수 있습니다.

* 백업에 대한 보존 기간(일)
* 백업에 사용할 Storage 계정
* 백업을 위한 암호화 옵션 및 암호
* 시스템 데이터베이스 Backup
* 백업 일정 구성

백업을 암호화하려면 **사용** 을 선택합니다. 그 다음 **암호** 를 지정합니다. Azure에서는 백업을 암호화할 인증서를 만들고 지정된 암호를 사용하여 인증서를 보호합니다. 기본적으로 일정은 자동으로 설정되지만 **수동** 을 선택하여 수동 일정을 만들 수 있습니다. 

![SQL VM 자동화된 백업](./media/create-sql-vm-portal/automated-backup.png)

자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업](automated-backup-sql-2014.md)을 참조하세요.


### <a name="machine-learning-services"></a>Machine Learning Services

[Machine Learning Services](/sql/advanced-analytics/)를 사용하도록 설정하는 옵션이 있습니다. 이 옵션을 사용하면 SQL Server 2017에서 Python 및 R과 함께 기계 학습을 사용할 수 있습니다. **SQL Server 설정** 창에서 **사용** 을 선택합니다.


## <a name="4-review--create"></a>4. 검토 + 만들기

**리뷰 + 만들기** 탭에서 다음을 수행합니다.
1. 요약을 검토합니다.
1. **만들기** 를 선택하여 이 VM에 대해 지정된 SQL Server, 리소스 그룹 및 리소스를 만듭니다.

Azure Portal에서 배포를 모니터링할 수 있습니다. 화면 맨 위에 있는 **알림** 단추는 배포의 기본 상태를 표시합니다.

> [!NOTE]
> Azure에서 SQL Server VM을 배포하는 시간의 예: 기본 설정으로 미국 동부 지역에 프로비전된 테스트 SQL Server VM을 완료하는 데 약 12분이 걸립니다. 지역 및 선택한 설정에 따라서 배포 시간이 더 빠르거나 늦을 수 있습니다.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> 원격 데스크톱을 사용하여 VM 열기

다음 단계를 사용하여 RDP(원격 데스크톱 프로토콜)로 SQL Server 가상 머신에 연결합니다.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server 가상 머신에 연결된 후에, SQL Server Management Studio를 시작하고 로컬 관리자 자격 증명을 사용하여 Windows 인증으로 연결할 수 있습니다. SQL Server 인증을 사용하도록 설정한 경우에는, 프로비전 중에 구성해 놓은 SQL 로그인 및 암호를 사용하여 SQL 인증에 연결할 수 있습니다.

컴퓨터에 연결하면 요구 사항에 따라 컴퓨터와 SQL Server 설정을 직접 변경할 수 있습니다. 예를 들어, 방화벽 설정을 구성하거나 SQL Server 구성 설정을 변경할 수 있습니다.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> 원격으로 SQL Server에 연결

이 연습에서는 가상 머신과 **SQL Server 인증** 에 대해 **공용** 액세스를 선택했습니다. 이러한 설정은 가상 머신이 인터넷을 통한 모든 클라이언트의 SQL Server 연결을 허용하도록 자동으로 구성합니다(올바른 SQL 로그인이 있다는 가정 하에).

> [!NOTE]
> 프로비전 중에 공용을 선택하지 않은 경우 프로비전 후 포털을 통해 SQL 연결 설정을 변경할 수 있습니다. 자세한 내용은 [SQL 연결 설정 변경](ways-to-connect-to-sql.md#change)을 참조하세요.

다음 섹션은 인터넷을 통해 SQL Server VM 인스턴스에 연결하는 방법을 보여줍니다.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > 이 예제에서는 공통 포트 1433을 사용합니다. 그러나 SQL Server VM 배포 중 다른 포트(예: 1401)가 지정된 경우 이 값을 수정해야 합니다. 


## <a name="next-steps"></a>다음 단계

Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure Virtual Machines의 SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) 및 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
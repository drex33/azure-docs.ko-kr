---
title: Azure Data Encryption-at-Rest - Azure Security
description: 이 문서에서는 미사용 Azure Data Encryption의 개요, 전반적인 기능 및 일반적인 고려 사항을 제공합니다.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: a73965d0ec5d0d3fbcf665d648137e1153506721
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529165"
---
# <a name="azure-data-encryption-at-rest"></a>Azure 미사용 데이터 암호화

Microsoft Azure에는 회사의 보안 및 규정 준수 요구 사항에 따라 데이터를 보호하는 도구가 포함되어 있습니다. 이 문서는 다음 내용을 집중적으로 다룹니다.

- Microsoft Azure에서 미사용 데이터를 보호하는 방법
- 데이터 보호 구현에 참여하는 다양한 구성 요소에 대해 설명
- 여러 키 관리 보호 방법의 장단점 검토

미사용 데이터 암호화는 일반적인 보안 요구 사항입니다. Azure에서는 조직이 사용자 지정 키 관리 솔루션과 관련된 위험 또는 비용 없이 미사용 데이터를 암호화할 수 있습니다. 조직에는 Azure에서 미사용 데이터 암호화를 완전히 관리할 수 있도록 하는 옵션이 제공됩니다. 또한 암호화 또는 암호화 키를 면밀하게 관리할 수 있는 다양한 옵션도 제공됩니다.

## <a name="what-is-encryption-at-rest"></a>미사용 데이터 암호화란?

암호화는 데이터의 기밀성을 보호하는 데 사용되는 데이터의 보안 인코딩입니다. Azure의 미사용 데이터 암호화 디자인에서는 다음과 같이 대칭 암호화를 사용하여 간단한 개념적 모델에 따라 많은 양의 데이터를 빠르게 암호화하고 암호 해독합니다.

- 데이터가 스토리지에 쓰여질 때 대칭 암호화 키를 사용하여 데이터를 암호화합니다.
- 동일한 암호화 키를 사용하여 메모리에서 사용하도록 준비된 데이터를 암호 해독합니다.
- 데이터는 분할될 수 있고, 각 파티션마다 다른 키가 사용될 수 있습니다.
- 키는 ID 기반 액세스 제어 및 감사 정책으로 안전하게 보호되는 위치에 저장해야 합니다. 보안 위치 외부에 저장된 데이터 암호화 키는 안전한 위치에 보관된 키 암호화 키로 암호화됩니다.

실제로 확장성과 가용성 보장뿐만 아니라 키 관리 및 제어 시나리오에는 추가 구성이 필요합니다. Microsoft Azure 미사용 데이터 암호화의 개념 및 구성 요소에 대해서는 아래에서 설명합니다.

## <a name="the-purpose-of-encryption-at-rest"></a>미사용 데이터 암호화의 목적

미사용 데이터 암호화는 저장된 데이터(미사용)에 대한 데이터 보호를 제공합니다. 미사용 데이터에 대한 공격에는 데이터가 저장된 하드웨어에 대한 물리적 액세스를 얻은 다음 포함된 데이터를 손상시키는 시도가 포함됩니다. 이러한 공격에서, 서버의 하드 드라이브가 유지 관리 도중 잘못 처리되어 공격자가 하드 드라이브를 제거할 수 있습니다. 나중에 공격자는 하드 드라이브를 자신의 통제 하에 있는 컴퓨터에 넣어 데이터에 액세스하려고 시도합니다.

미사용 데이터 암호화는 디스크에 있을 때 데이터를 암호화하여 공격자가 암호화되지 않은 데이터에 액세스하지 못하도록 설계되었습니다. 데이터가 암호화된 하드 드라이브를 공격자가 획득했지만 암호화 키를 획득하지 못한 경우 공격자는 데이터를 읽으려면 암호화를 풀어야 합니다. 이 공격은 하드 드라이브의 암호화되지 않은 데이터에 액세스하는 것보다 훨씬 복잡하고 더 많은 리소스를 소모합니다. 이러한 이유로 미사용 데이터 암호화는 적극적으로 권장되며 많은 조직에서 우선 순위가 높은 요구 사항입니다.

또한 조직의 데이터 거버넌스 및 규정 준수 노력에 따른 미사용 암호화가 필요할 수 있습니다. HIPAA, PCI, FedRAMP 등의 산업 및 정부 규정에는 데이터 보호 및 암호화 요구 사항과 관련된 구체적인 보호 조치가 명시되어 있습니다. 미사용 데이터 암호화는 이러한 규정 중 일부를 사용하는 규정 준수에 필수적인 조치입니다. FIPS 140-2 유효성 검사에 대한 Microsoft의 접근 방식에 대한 자세한 내용은 [FIPS(Federal Information Processing Standard) 간행물 140-2](/microsoft-365/compliance/offering-fips-140-2)를 참조하세요.

규정 준수 및 규제 요구 사항 충족 외에도, 저장 데이터 암호화는 심층 방어 보호를 제공합니다. Microsoft Azure는 서비스, 애플리케이션 및 데이터를 위한 규격 플랫폼을 제공합니다. 또한 포괄적인 시설과 물리적 보안, 데이터 액세스 제어 및 감사를 제공합니다. 그러나 다른 보안 조치 중 하나가 실패하고 미사용 암호화가 이러한 보안 조치를 제공할 경우 추가적인 "중복" 보안 조치를 제공하는 것이 중요합니다.

Microsoft는 클라우드 서비스에서 저장 데이터 암호화 옵션을 제공하고 고객에게 암호화 키 및 키 사용 로그를 제공하기 위해 최선을 다하고 있습니다. 또한 Microsoft는 기본적으로 고객의 모든 저장 데이터를 암호화하기 위해 노력하고 있습니다.

## <a name="azure-encryption-at-rest-components"></a>Azure 미사용 데이터 암호화의 구성 요소

앞에서 설명한 대로 미사용 데이터 암호화의 목표는 디스크에 유지되는 데이터가 비밀 암호 키로 암호화된다는 것입니다. 이 목표를 달성하려면 보안 키 생성, 스토리지, 액세스 제어 및 암호화 키 관리가 제공되어야 합니다. 세부 정보는 다를 수 있지만, Azure 서비스의 미사용 데이터 암호화 구현은 다음 다이어그램에서 보여 주는 개념 측면에서 설명될 수 있습니다.

![구성 요소](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

암호화 키의 스토리지 위치와 해당 키에 대한 액세스 제어는 미사용 데이터 암호화 모델의 핵심입니다. 키는 철저히 보안되어야 하지만, 특정 사용자가 관리하고 특정 서비스에서 사용할 수 있어야 합니다. Azure 서비스의 경우 Azure Key Vault는 권장되는 키 스토리지 솔루션이며 서비스 전반에서 공통된 관리 환경을 제공합니다. 키는 키 자격 증명 모음에 저장되어 관리되며, 사용자 또는 서비스에 키 자격 증명 모음에 대한 액세스 권한을 부여할 수 있습니다. Azure Key Vault는 고객이 관리하는 암호화 키 시나리오에서 사용할 고객 키 생성 또는 고객 키 가져오기를 지원합니다.

### <a name="azure-active-directory"></a>Azure Active Directory

미사용 데이터의 암호화 및 암호 해독을 위해 Azure Key Vault에 저장된 키를 사용하여 이러한 데이터를 관리하거나 액세스하는 권한은 Azure Active Directory 계정에 부여할 수 있습니다.

### <a name="key-hierarchy"></a>키 계층 구조

두 가지 이상의 암호화 키가 미사용 데이터 암호화 구현에 사용됩니다. Azure Key Vault에 암호화 키를 저장하면 안전하게 키에 액세스하고 키를 중앙에서 관리할 수 있습니다. 그러나 암호화 키에 대한 서비스 로컬 액세스는 모든 데이터 작업에 대해 Key Vault와 상호 작용하는 것보다 대량 암호화 및 암호 해독에 더 효율적이므로 암호화와 성능이 향상됩니다. 단일 암호화 키의 사용을 제한하면 키가 손상될 위험과 키를 교체해야 할 때의 재암호화 비용이 줄어듭니다. 미사용 Azure 암호화 모델은 이러한 모든 요구를 해결하기 위해 다음과 같은 형식의 키로 구성된 키 계층 구조를 사용합니다.

- **DEK(데이터 암호화 키)** - 파티션이나 데이터 블록을 암호화하는 데 사용되는 대칭 AES256 키입니다.  단일 리소스에는 많은 파티션과 많은 데이터 암호화 키가 있을 수 있습니다. 서로 다른 키로 각 데이터 블록을 암호화하면 암호 분석 공격이 더욱 어려워집니다. DEK에 대한 액세스는 특정 블록을 암호화하고 암호 해독하는 리소스 공급자 또는 애플리케이션 인스턴스에 필요합니다. DEK가 새 키로 대체되면 연결된 블록의 데이터만 새 키로 다시 암호화해야 합니다.
- **키 암호화 키(KEK)** – 데이터 암호화 키를 암호화하는 데 사용되는 암호화 키. 키 암호화 키를 사용하면 Key Vault를 벗어나지 않고 데이터 암호화 키 자체를 암호화하고 제어할 수 있습니다. KEK에 액세스할 수 있는 엔터티는 DEK가 필요한 엔터티와 다를 수 있습니다. 엔터티는 특정 파티션에 대한 각 DEK의 액세스를 제한하기 위해 DEK에 액세스하는 broker일 수 있습니다. KEK는 DEK를 암호 해독하는 데 필요하므로 KEK는 실질적으로 KEK를 삭제함으로써 DEK를 효과적으로 삭제할 수 있는 단일 지점이 됩니다.

리소스 공급자 및 애플리케이션 인스턴스는 키 암호화 키로 암호화된 데이터 암호화 키를 종종 데이터 암호화 키로 보호되는 데이터에 대한 메타데이터로 저장합니다. 키 암호화 키에 대한 액세스 권한이 있는 엔터티만 이러한 데이터 암호화 키를 해독할 수 있습니다. 다양한 키 스토리지 모델이 지원됩니다. 자세한 내용은 [데이터 암호화 모델](encryption-models.md)을 참조하세요.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft Cloud 서비스 내 미사용 데이터 암호화

Microsoft Cloud 서비스는 세 가지 클라우드 모델, 즉 IaaS, PaaS, SaaS에서 모두 사용됩니다. 각 모델에서 작동하는 방식에 대한 예는 다음과 같습니다.

- Software as a Server 또는 SaaS라고 하는 소프트웨어 서비스에는 Microsoft 365와 같은 클라우드 제공 애플리케이션이 있습니다.
- 고객이 자신의 애플리케이션에서 클라우드를 활용하고 스토리지, 분석 및 서비스 버스 기능과 같은 용도로 클라우드를 사용하는 플랫폼 서비스
- 고객이 클라우드에 호스팅되어 다른 클라우드 서비스를 활용할 수 있는 운영 체제 및 애플리케이션을 배포하는 인프라 서비스 또는 IaaS(Infrastructure as a Service)

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS 고객에 대한 미사용 데이터 암호화

SaaS(Software as a Service) 고객은 일반적으로 각 서비스에서 미사용 데이터 암호화를 사용하도록 설정하거나 사용할 수 있습니다. Microsoft 365에는 고객이 미사용 상태의 암호화를 확인하거나 사용할 수 있는 여러 가지 옵션이 있습니다. Microsoft 365 서비스에 대한 자세한 내용은 [ Microsoft 365의 암호화](/microsoft-365/compliance/encryption)를 참조하세요.

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS 고객에 대한 미사용 데이터 암호화

Platform as a Service(PaaS) 고객의 데이터는 일반적으로 Blob Storage와 같은 스토리지 서비스에 있지만 가상 머신과 같은 애플리케이션 실행 환경에 캐시 또는 저장될 수도 있습니다. 미사용 데이터 암호화 옵션을 사용할 수 있는지 확인하려면 [데이터 암호화 모델: 지원 서비스 테이블](encryption-models.md#supporting-services)에서 사용할 수 있는 스토리지 및 애플리케이션 플랫폼을 살펴보세요.

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS 고객에 대한 미사용 데이터 암호화

IaaS(Infrastructure as a Service) 고객은 다양한 서비스와 애플리케이션을 사용할 수 있습니다. IaaS 서비스를 사용하면 Azure Disk Encryption을 사용하여 Azure 호스팅 가상 머신 및 VHD에서 미사용 데이터 암호화를 사용할 수 있습니다.

#### <a name="encrypted-storage"></a>암호화된 스토리지

PaaS와 마찬가지로 IaaS 솔루션은 암호화된 미사용 데이터를 저장하는 다른 Azure 서비스를 활용할 수 있습니다. 이 경우 사용된 각 Azure 서비스에서 제공하는 대로 미사용 데이터 암호화 지원을 사용하도록 설정할 수 있습니다. [데이터 암호화 모델: 지원 서비스 테이블](encryption-models.md#supporting-services)에서는 주요 스토리지, 서비스 및 애플리케이션 플랫폼과 지원되는 미사용 데이터 암호화 모델을 나열합니다.

#### <a name="encrypted-compute"></a>암호화된 컴퓨팅

모든 관리 디스크, 스냅샷 및 이미지는 서비스 관리 키를 사용하여 스토리지 서비스 암호화를 통해 암호화됩니다. 미사용 암호화 솔루션을 완전하게 사용하면 데이터가 암호화되지 않은 형태로 지속되지 않아야 합니다. 가상 머신에서 데이터를 처리하는 동안 Windows 페이지 파일 또는 Linux 스왑 파일, 크래시 덤프 또는 애플리케이션 로그에 데이터를 저장할 수 있습니다. 이러한 미사용 데이터가 암호화되어 있는지 확인하려면 IaaS 애플리케이션이 Azure IaaS 가상 머신(Windows 또는 Linux) 및 가상 디스크에서 Azure Disk Encryption을 사용할 수 있습니다.

#### <a name="custom-encryption-at-rest"></a>사용자 지정 미사용 데이터 암호화

가능할 때마다 사용된 모든 Azure 서비스에서 제공하는 Azure Disk Encryption 및 미사용 데이터 암호화 옵션을 IaaS 애플리케이션에서 활용하는 것이 좋습니다. 불규칙한 암호화 요구 사항 또는 Azure 기반이 아닌 스토리지와 같은 어떤 경우에는 IaaS 애플리케이션 개발자가 자체적으로 미사용 데이터 암호화를 구현해야 할 수도 있습니다. IaaS 솔루션 개발자는 특정 Azure 구성 요소를 활용하여 Azure 관리 및 고객 기대와 더 잘 통합될 수 있습니다. 특히 개발자는 Azure Key Vault 서비스를 사용하여 안전한 키 스토리지를 제공할 뿐만 아니라 대부분의 Azure플랫폼 서비스에 있는 옵션과 함께 일관된 키 관리 옵션도 고객에게 제공해야 합니다. 또한 사용자 지정 솔루션은 Azure Managed Service Identities를 사용하여 서비스 계정에서 암호화 키에 액세스할 수 있도록 설정해야 합니다. Azure Key Vault 및 Managed Service Identities에 대한 개발자 정보는 해당 SDK를 참조하세요.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure 리소스 공급자 암호화 모델 지원

Microsoft Azure 서비스마다 하나 이상의 미사용 데이터 암호화 모델을 지원합니다. 그러나 일부 서비스의 경우 하나 이상의 암호화 모델이 적용되지 않을 수 있습니다. 고객이 관리하는 키 시나리오를 지원하는 서비스의 경우, Azure Key Vault가 주요 암호화 키를 지원하는 키 유형의 하위 집합만 지원할 수 있습니다. 또한 서비스가 서로 다른 일정으로 이러한 시나리오 및 키 형식에 대한 지원을 해제할 수 있습니다. 이 섹션에서는 이 문서를 작성할 당시의 주요 Azure 데이터 스토리지 서비스 각각에 대한 미사용 데이터 암호화에 대해 설명하고 있습니다.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure IaaS(Infrastructure as a Service) 기능을 사용하는 모든 고객은 Azure Disk Encryption을 통해 IaaS VM 및 디스크에 대한 미사용 데이터 암호화를 달성할 수 있습니다. Azure Disk Encryption에 대한 자세한 내용은 [Azure Disk Encryption 설명서](./azure-disk-encryption-vms-vmss.md)를 참조하세요.

#### <a name="azure-storage"></a>Azure Storage

모든 Azure Storage 서비스(Blob storage, Queue storage, Table storage, Azure Files)는 미사용 상태의 서버측 암호화를 지원하며, 일부 서비스는 고객 관리 키 및 클라이언트측 암호화를 추가로 지원합니다.

- 서버 쪽: 모든 Azure Storage 서비스는 기본적으로 애플리케이션에 투명한 서비스 관리 키를 사용하여 서버 쪽 암호화를 활성화합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](../../storage/common/storage-service-encryption.md)를 참조하세요. Azure Blob Storage 및 Azure Files도 Azure Key Vault의 RSA 2048비트 고객 관리 키를 지원합니다. 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](../../storage/common/customer-managed-keys-configure-key-vault.md)를 참조하세요.
- 클라이언트 쪽: Azure Blob, 테이블 및 큐에서 클라이언트 쪽 암호화를 지원합니다. 클라이언트 쪽 암호화를 사용하는 경우 고객은 데이터를 암호화하고 암호화된 Blob으로 데이터를 업로드합니다. 키 관리는 고객이 수행합니다. 자세한 내용은 [Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault](../../storage/common/storage-client-side-encryption.md)를 참조하세요.

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database는 현재 Microsoft 관리 서비스 쪽 및 클라이언트 쪽 암호화 시나리오에 대한 저장 데이터 암호화를 지원합니다.

서버 암호화 지원은 현재 투명한 데이터 암호화라는 SQL 기능을 통해 제공됩니다. Azure SQL Database 고객이 TDE 키를 사용하도록 설정하면 해당 키가 자동으로 만들어지고 관리됩니다. 데이터베이스 및 서버 수준에서 미사용 데이터 암호화를 사용하도록 설정할 수 있습니다. 2017년 6월 현재 [TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption)는 새로 만든 데이터베이스에서 기본적으로 사용하도록 설정되어 있습니다. Azure SQL Database는 Azure Key Vault의 RSA 2048비트 고객 관리 키를 지원합니다. 자세한 내용은 [Azure SQL Database 및 데이터 웨어하우스에 대한 BYOK(Bring Your Own Key) 지원을 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql)를 참조하세요.

Azure SQL Database 데이터의 클라이언트 쪽 암호화는 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 기능을 통해 지원됩니다. Always Encrypted는 클라이언트에서 만들고 저장한 키를 사용합니다. 고객은 Windows 인증서 저장소, Azure Key Vault 또는 로컬 HSM(하드웨어 보안 모듈)에 마스터 키를 저장할 수 있습니다. SQL Server Management Studio를 사용하는 경우 SQL 사용자는 열 암호화에 사용할 키를 선택합니다.

## <a name="conclusion"></a>결론

Azure 서비스에 저장된 고객 데이터의 보호는 Microsoft에 매우 중요합니다. 모든 Azure 호스팅 서비스에서 미사용 데이터 암호화 옵션을 제공하도록 노력하고 있습니다. Azure 서비스는 서비스 관리 키, 고객 관리 키 또는 클라이언트 측 암호화를 지원합니다. Azure 서비스는 미사용 데이터 암호화 가용성을 크게 향상시키고 있으며 향후 몇 개월 동안 미리 보기 및 일반 가용성에 대한 새로운 옵션이 제공될 계획입니다.

## <a name="next-steps"></a>다음 단계

- 서비스 관리 키 및 고객 관리 키에 대한 자세한 내용은 [데이터 암호화 모델](encryption-models.md)을 참조하세요.
- Azure에서 [이중 암호화](double-encryption.md)를 사용하여 데이터 암호화와 제공되는 위협을 완화하는 방법에 대해 알아보세요.
- 하드웨어 및 펌웨어 빌드, 통합, 운영화 및 파이프라인 복구 등의 호스트 [플랫폼 무결성 및 보안](platform.md)을 보장하기 위해 Microsoft가 수행하는 작업에 대해 알아보세요.

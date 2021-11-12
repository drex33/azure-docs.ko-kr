---
title: Azure SQL Database의 Intel SGX Enclave 및 증명 계획
description: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 배포를 계획합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: 18700a89b94a1fb739e279230ab6f2b172ab6671
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306513"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Azure SQL Database의 Intel SGX Enclave 및 증명 계획

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database의 [보안 Enclave를 사용한 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)는 [Intel SGX(Intel Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) Enclave를 사용하고 [Microsoft Azure Attestation](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)이 필요합니다.

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Azure SQL Database의 Intel SGX 계획

Intel SGX는 하드웨어 기반의 신뢰할 수 있는 실행 환경 기술입니다. Intel SGX는 [vCore 모델](service-tiers-sql-database-vcore.md) 및 [DC 시리즈](service-tiers-sql-database-vcore.md?#dc-series) 하드웨어 세대를 사용하는 데이터베이스에 사용할 수 있습니다. 따라서 데이터베이스에서 보안 Enclave를 사용한 Always Encrypted를 사용하려면 데이터베이스를 만들 때 DC 시리즈 하드웨어 생성을 선택하거나, 기존 데이터베이스를 업데이트하면 DC 시리즈 하드웨어 생성을 사용할 수 있습니다.

> [!NOTE]
> Intel SGX는 DC 시리즈 이외의 하드웨어 세대에서 사용할 수 없습니다. 예를 들어 Intel SGX는 Gen5 하드웨어에 사용할 수 없으며 [DTU 모델](service-tiers-dtu.md)을 사용하는 데이터베이스에 사용할 수 없습니다.

> [!IMPORTANT]
> 데이터베이스에 대한 DC 시리즈 하드웨어 세대를 구성하기 전에 DC 시리즈의 지역별 가용성을 확인하고 성능 제한 사항을 이해하고 있는지 확인합니다. 자세한 내용은 [DC 시리즈](service-tiers-sql-database-vcore.md#dc-series)를 참조하세요.

## <a name="plan-for-attestation-in-azure-sql-database"></a>Azure SQL Database의 증명 계획

[Microsoft Azure Attestation](../../attestation/overview.md)은 DC 시리즈 하드웨어 세대를 사용하는 Azure SQL Database의 Intel SGX Enclave를 포함한 TEE(Trusted Execution Environment)를 증명하기 위한 솔루션입니다.

Azure SQL Database에서 Intel SGX Enclave를 증명하는 데 Azure Attestation을 사용하려면 [증명 공급자](../../attestation/basic-concepts.md#attestation-provider)를 만들고 Microsoft에서 제공하는 증명 정책을 사용하여 구성해야 합니다. [Azure Attestation을 사용하여 Always Encrypted에 대한 증명 구성](always-encrypted-enclaves-configure-attestation.md)을 참조하세요.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>SGX Enclave 및 증명을 구성할 때의 역할 및 책임

Azure SQL Database의 Always Encrypted에 대한 Intel SGX Enclave 및 증명을 지원하도록 환경을 구성하려면 Microsoft Azure Attestation, Azure SQL Database 및 Enclave 증명을 트리거하는 애플리케이션과 같은 다양한 유형의 구성 요소를 설정해야 합니다. 각 형식의 구성 요소는 다음과 같은 고유한 역할 중 하나를 수행하는 사용자가 구성합니다.

- 증명 관리자 - Microsoft Azure Attestation에서 증명 공급자를 만들고, 증명 정책을 작성하며, Azure SQL 논리 서버에 증명 공급자에 대한 액세스 권한을 부여하고, 정책을 가리키는 증명 URL을 애플리케이션 관리자에게 공유합니다.
- Azure SQL Database 관리자 - DC 시리즈 하드웨어 세대를 선택하여 데이터베이스에서 SGX Enclave를 사용하도록 설정하고 증명 공급자에 액세스해야 하는 Azure SQL 논리 서버의 ID를 증명 관리자에게 제공합니다.
- 애플리케이션 관리자 - 증명 관리자로부터 얻은 증명 URL을 사용하여 애플리케이션을 구성합니다.

프로덕션 환경(실제 중요한 데이터 처리)에서 조직은 증명을 구성할 때 각 고유한 역할을 서로 다른 사용자가 수행하는 역할 분리를 준수해야 합니다. 특히, Azure SQL Databas 관리자가 중요한 데이터에 액세스할 수 없도록 함으로써 공격 노출 영역을 줄이기 위해 조직에 Always Encrypted를 배포하는 경우에는 Azure SQL Database 관리자가 증명 정책을 제어하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database에 대해 Intel SGX 사용](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>참고 항목

- [자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작](always-encrypted-enclaves-getting-started.md)

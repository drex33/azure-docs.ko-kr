---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 65ef6a9116afa3dea544d0fe2bdd69ab43d28e4c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674829"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB 서버에서 사용할 수 있도록 Advanced Threat Protection 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6cf7411-da9e-49e2-aec0-cba0250eaf8c) |데이터베이스에 액세스하거나 데이터베이스를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 탐지하려면 기본 계층이 아닌 Azure Database for MariaDB 서버에서 Advanced Threat Protection을 사용하도록 설정합니다. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/DeployAtpOnMariaDbServers_Deploy.json) |
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 서버는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |가상 네트워크 기반 방화벽 규칙은 Azure 경계 내에서 트래픽을 유지하면서 특정 서브넷에서 Azure Database for MariaDB로의 트래픽을 사용하도록 설정하는 데 사용됩니다. 이 정책은 Azure Database for MariaDB에서 사용 중인 가상 네트워크 서비스 엔드포인트가 있는지 감사하는 방법을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MariaDB에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |

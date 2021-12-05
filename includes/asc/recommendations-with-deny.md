---
author: memildin
ms.service: defender-for-cloud
ms.topic: include
ms.date: 12/04/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 4997b82cce6304cfd1d1a909274d0fadee95651f
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570899"
---
- [필요한 경우 사용] Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 합니다.
- [필요한 경우 사용] Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 합니다.
- [필요한 경우 사용] Cognitive Services 계정은 CMK(고객 관리형 키)를 사용하여 데이터 암호화를 사용하도록 설정해야 합니다.
- [필요한 경우 사용] 컨테이너 레지스트리는 CMK(고객 관리형 키)를 사용하여 암호화해야 합니다.
- 방화벽 및 가상 네트워크 구성을 사용하여 스토리지 계정 액세스를 제한해야 함
- 자동화 계정 변수를 암호화해야 합니다.
- Azure Cache for Redis는 가상 네트워크 내에 있어야 함
- Azure Spring Cloud는 네트워크 주입을 사용해야 함
- 컨테이너 CPU 및 메모리 한도를 적용해야 함
- 신뢰할 수 있는 레지스트리의 컨테이너 이미지만 배포해야 함
- 권한 상승을 포함하는 컨테이너를 사용하지 않아야 함
- 중요한 호스트 네임스페이스를 공유하는 컨테이너를 사용하지 않아야 함
- 컨테이너는 허용되는 포트에서만 수신 대기해야 함
- 변경 불가능한(읽기 전용) 루트 파일 시스템을 컨테이너에 적용해야 함
- Key Vault 키에는 만료 날짜가 있어야 함
- Key Vault 비밀에는 만료 날짜가 있어야 함
- 키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함
- 키 자격 증명 모음에 일시 삭제를 사용하도록 설정해야 함
- 최소 권한 Linux 기능을 컨테이너에 적용해야 함
- 컨테이너 AppArmor 프로필의 재정의 또는 비활성화를 제한해야 함
- 권한 있는 컨테이너를 피해야 함
- SSL을 통해서만 액세스를 허용 해야 Redis Cache
- 컨테이너를 루트 사용자로 실행하지 않아야 함
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- Service Fabric 클러스터의 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.
- 서비스는 허용되는 포트에서만 수신 대기해야 함
- 스토리지 계정 공용 액세스가 허용되지 않아야 함
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함
- 호스트 네트워킹 및 포트 사용을 제한해야 함
- 손상된 컨테이너에서 노드 액세스를 제한하려면 Pod HostPath 볼륨 탑재 사용을 알려진 목록으로 제한해야 함
- Azure Key Vault에 저장된 인증서의 유효 기간이 12개월을 넘어서는 안 됩니다.
- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함
- Azure Front Door Service 서비스에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함


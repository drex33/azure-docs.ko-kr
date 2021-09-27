---
title: Azure 역할 기반 액세스 제어로 마이그레이션 | Microsoft Docs
description: 자격 증명 모음 액세스 정책에서 Azure 역할로 마이그레이션하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 5f92ecca8ccecda0f4ba6e7e87dc0287c75ed41b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761622"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>자격 증명 모음 액세스 정책에서 Azure 역할 기반 액세스 제어 권한 모델로 마이그레이션

자격 증명 모음 액세스 정책 모델은 키, 비밀 및 인증서에 대한 액세스를 제공하기 위해 키 자격 증명 모음에 기본 제공되는 기존 권한 부여 시스템입니다. 키 자격 증명 모음 범위에서 보안 주체(사용자, 그룹, 서비스 주체, 관리 ID)에 개별 사용 권한을 할당하여 액세스를 제어할 수 있습니다. 

Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure 리소스의 세밀한 액세스를 관리하는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 기반의 권한 부여 시스템입니다. Azure RBAC에서는 역할 할당을 만들어 리소스에 대한 액세스를 제어합니다. 역할 할당은 보안 주체, 역할 정의(미리 정의된 사용 권한 집합) 및 범위(리소스 그룹 또는 개별 리소스)의 세 요소로 구성됩니다. 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

Azure RBAC로 마이그레이션하기 전에 이점 및 제한 사항을 이해하는 것이 중요합니다.

자격 증명 모음 액세스 정책과 비교 시 Azure RBAC의 주요 이점:
- Azure 서비스 전반에서 동일한 API를 사용하여 Azure 리소스에 대한 통합된 액세스 제어 모델을 제공합니다.
- 관리자용 중앙 집중식 액세스 관리 - 한 보기에서 모든 Azure 리소스를 관리합니다.
- 시간 기반 액세스 제어를 위해 [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)와 통합합니다.
- 거부 할당 - 특정 범위에서 보안 주체를 제외할 수 있습니다. 자세한 내용은 [Azure 거부 할당 이해](../../role-based-access-control/deny-assignments.md)를 참조하세요.

Azure RBAC 단점:
- 역할 할당 대기 시간 - 역할 할당을 적용하는 데 몇 분 정도 걸릴 수 있습니다. 자격 증명 모음 액세스 정책은 즉시 할당됩니다.
- 제한 된 수의 역할 할당-Azure RBAC는 구독 당 모든 서비스 및 1024 액세스 Key Vault 정책에 대해 2000 역할 할당만 허용 합니다.

## <a name="access-policies-to-azure-roles-mapping"></a>Azure 역할 매핑에 대한 액세스 정책

Azure RBAC는 사용자, 그룹, 서비스 주체 및 관리 ID에 할당할 수 있는 여러 가지 Azure 기본 제공 역할을 제공합니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 만들면 됩니다.

키, 인증서 및 비밀 액세스 관리를 위한 키 자격 증명 모음 기본 제공 역할:
- 키 자격 증명 모음 관리자
- 키 자격 증명 모음 읽기 권한자
- 키 자격 증명 모음 인증서 책임자
- 키 자격 증명 모음 암호화 책임자
- 키 자격 증명 모음 암호화 사용자
- 키 자격 증명 모음 암호화 서비스 암호화 사용자
- 키 자격 증명 모음 비밀 책임자
- 키 자격 증명 모음 비밀 사용자

기존 기본 제공 역할에 대한 자세한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

자격 증명 모음 액세스 정책은 개별적으로 선택된 권한 또는 미리 정의된 권한 템플릿을 사용하여 할당할 수 있습니다.

액세스 정책 미리 정의된 권한 템플릿:
- 키, 비밀, 인증서 관리
- 키 및 비밀 관리
- 비밀 및 인증서 관리
- 키 관리
- 비밀 관리
- 인증서 관리
- SQL Server 커넥터
- Azure Data Lake Storage 또는 Azure Storage
- Azure Backup
- Exchange Online 고객 키
- SharePoint Online 고객 키
- Azure Information BYOK

### <a name="access-policy-templates-to-azure-roles-mapping"></a>Azure 역할 매핑에 대한 액세스 정책 템플릿
| 액세스 정책 템플릿 | 작업 | Azure 역할 |
| --- | --- | --- |
| 키, 비밀, 인증서 관리 | 키: 모든 작업 <br>인증서: 모든 작업<br>비밀: 모든 작업 | 키 자격 증명 모음 관리자 |
| 키 및 비밀 관리 | 키: 모든 작업 <br>비밀: 모든 작업| 키 자격 증명 모음 암호화 책임자 <br> 키 자격 증명 모음 비밀 책임자 |
| 비밀 및 인증서 관리 | 인증서: 모든 작업 <br>비밀: 모든 작업| 키 자격 증명 모음 인증서 책임자 <br> 키 자격 증명 모음 비밀 책임자|
| 키 관리 | 키: 모든 작업| 키 자격 증명 모음 암호화 책임자|
| 비밀 관리 | 비밀: 모든 작업| 키 자격 증명 모음 비밀 책임자|
| 인증서 관리 | 인증서: 모든 작업 | 키 자격 증명 모음 인증서 책임자|
| SQL Server 커넥터 | 키: 가져오기, 나열, 키 래핑, 키 래핑 해제 | 키 자격 증명 모음 암호화 서비스 암호화 사용자|
| Azure Data Lake Storage 또는 Azure Storage | 키: 가져오기, 나열, 키 래핑 해제 | 해당 없음<br> 사용자 지정 역할 필요|
| Azure Backup | 키: 가져오기, 나열, 백업<br> 비밀: 가져오기, 나열, 백업 | 해당 없음<br> 사용자 지정 역할 필요|
| Exchange Online 고객 키 | 키: 가져오기, 나열, 키 래핑, 키 래핑 해제 | 키 자격 증명 모음 암호화 서비스 암호화 사용자|
| Exchange Online 고객 키 | 키: 가져오기, 나열, 키 래핑, 키 래핑 해제 | 키 자격 증명 모음 암호화 서비스 암호화 사용자|
| Azure Information BYOK | 키: 가져오기, 암호 해독, 서명 | 해당 없음<br>사용자 지정 역할 필요|


## <a name="assignment-scopes-mapping"></a>할당 범위 매핑  

키 자격 증명 모음용 Azure RBAC를 사용하면 다음 범위에서 역할을 할당할 수 있습니다.
- 관리 그룹
- 구독
- Resource group
- 키 자격 증명 모음 리소스
- 개별 키, 비밀 및 인증서

자격 증명 모음 액세스 정책 권한 모델은 키 자격 증명 모음 리소스 수준 정책 할당으로 제한됩니다.

일반적으로 애플리케이션당 하나의 키 자격 증명 모음을 사용하고 키 자격 증명 모음 수준에서 액세스를 관리하는 것이 좋습니다. 다른 범위에서 액세스를 관리하면 액세스 관리를 간소화할 수 있는 시나리오가 있습니다.

- ** 인프라, 보안 관리자 및 운영자: 자격 증명 모음 액세스 정책을 사용하여 관리 그룹, 구독 또는 리소스 그룹 수준에서 키 자격 증명 모음 그룹을 관리하려면 각 키 자격 증명 모음에 대한 정책을 유지 관리해야 합니다. Azure RBAC를 통해 관리 그룹, 구독 또는 리소스 그룹에서 하나의 역할 할당을 만들 수 있습니다. 이 할당은 동일한 범위에서 만든 모든 새 키 자격 증명 모음에 적용됩니다. 이 시나리오에서는 영구 액세스를 제공하는 것보다 Privileged Identity Management를 just-in time 액세스와 함께 사용하는 것이 좋습니다.
 
- ** 애플리케이션: 애플리케이션에서 다른 애플리케이션과 암호를 공유해야 하는 시나리오가 있습니다. 자격 증명 모음 액세스 정책을 사용하면 모든 비밀에 대한 액세스 권한을 부여하지 않도록 별도의 키 자격 증명 모음을 만들어야 했습니다. Azure RBAC를 사용하면 단일 키 자격 증명 모음을 사용하는 대신 개별 비밀의 범위를 사용하여 역할을 할당할 수 있습니다.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>자격 증명 모음 액세스 정책에서 Azure RBAC로 마이그레이션 단계
Azure RBAC와 자격 증명 모음 액세스 정책 권한 모델 간에는 많은 차이점이 있습니다. 마이그레이션 도중 중단이 발생하지 않도록 하려면 아래 단계를 수행하는 것이 좋습니다.
 
1. **역할 식별 및 할당**: 위의 매핑 테이블을 기반으로 기본 제공 역할을 식별하고 필요한 경우 사용자 지정 역할을 만듭니다. 범위 매핑 지침에 따라 범위에서 역할을 할당합니다. 키 자격 증명 모음에 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용하여 키 자격 증명 모음에 대한 액세스 제공](rbac-guide.md)을 참조하세요.
1. **역할 할당 유효성 검사**: Azure RBAC의 역할 할당은 전파하는 데 몇 분 정도 걸릴 수 있습니다. 역할 할당을 확인하는 방법에 대한 지침은 [범위의 역할 할당 나열](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)을 참조하세요.
1. **키 자격 증명 모음에 대한 모니터링 및 경고 구성**: 액세스 거부 예외에 대한 로깅을 설정하고 경고를 설정하는 것이 중요합니다. 자세한 내용은 [Azure Key Vault 모니터링 및 경고](./alert.md)를 참조하세요.
1. **키 자격 증명 모음에서 Azure 역할 기반 액세스 제어 권한 모델 설정**: Azure RBAC 권한 모델을 사용하도록 설정하면 기존 액세스 정책이 모두 무효화됩니다. 오류가 발생하는 경우 모든 기존 액세스 정책을 그대로 두고 다시 권한 모델로 전환할 수 있습니다.

> [!NOTE]
> 권한 모델을 변경하려면 [소유자](../../role-based-access-control/built-in-roles.md#owner) 및 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할의 일부인 'Microsoft.Authorization/roleAssignments/write' 권한이 있어야 합니다. '서비스 관리자' 및 '공동 관리자'와 같은 클래식 구독 관리자 역할은 지원되지 않습니다.

> [!NOTE]
> Azure RBAC 권한 모델을 사용하도록 설정하면 액세스 정책 업데이트를 시도하는 모든 스크립트가 실패합니다. Azure RBAC를 사용하도록 해당 스크립트를 업데이트하는 것이 중요합니다.

## <a name="troubleshooting"></a>문제 해결
-  역할 할당이 몇 분 후에 작동하지 않음 - 경우에 따라 역할 할당에 시간이 더 오래 걸릴 수 있습니다. 이러한 경우를 처리하기 위해 코드에서 재시도 논리를 작성하는 것이 중요합니다.
- 키 자격 증명 모음을 삭제(일시 삭제)했다가 복구하면 역할 할당이 사라짐 - 이는 현재 모든 Azure 서비스에서 일시 삭제 기능의 제한입니다. 복구 후 모든 역할 할당을 다시 만들어야 합니다.    

## <a name="learn-more"></a>자세한 정보

- [Azure RBAC 개요](../../role-based-access-control/overview.md)
- [사용자 지정 역할 자습서](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)

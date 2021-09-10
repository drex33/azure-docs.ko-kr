---
title: Azure SQL용 Azure Active Directory의 Directory Readers 역할
description: Azure SQL용 Azure AD의 디렉터리 읽기 권한자 역할에 관해 알아봅니다.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/30/2021
ms.openlocfilehash: 738338c4f3931e97965e697ca1bf316018a495c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566741"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Azure SQL용 Azure Active Directory의 Directory Readers 역할

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory(Azure AD)는 [Azure AD 그룹을 사용하여 역할 할당 관리](../../active-directory/roles/groups-concept.md) 기능을 도입했습니다. 따라서 Azure AD 역할을 그룹에 할당할 수 있습니다.

Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse Analytics에 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 사용하도록 설정하는 경우 Azure AD [  **디렉터리 읽기 권한자**](../../active-directory/roles/permissions-reference.md#directory-readers) 역할을 ID에 할당해야 [Azure AD Graph API](/graph/migrate-azure-ad-graph-planning-checklist)에 대한 읽기 권한이 허용됩니다. SQL Database 및 Azure Synapse의 관리 ID를 서버 ID라고 합니다. SQL Managed Instance의 관리 ID는 관리형 인스턴스 ID라고 하며, 인스턴스를 만들 때 자동으로 할당됩니다. SQL Database 또는 Azure Synapse에서 서버 ID를 할당하는 데 관한 자세한 내용은 [서비스 주체를 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)를 참조하세요.

다음을 수행하려면 **디렉터리 읽기 권한자** 역할이 필요합니다.

- SQL Managed Instance에 대한 Azure AD 로그인 만들기
- Azure SQL에서 Azure AD 사용자 가장
- Windows 인증을 사용하는 SQL Server 사용자를 Azure AD 인증을 사용하는 SQL Managed Instance로 마이그레이션([ALTER USER(Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) 명령 사용)
- SQL Managed Instance에 대한 Azure AD 관리자 변경
- [서비스 주체(애플리케이션)](authentication-aad-service-principal.md)가 Azure SQL에서 Azure AD 사용자를 만들 수 있도록 허용

## <a name="assigning-the-directory-readers-role"></a>디렉터리 읽기 권한자 역할 할당

[**디렉터리 읽기 권한자**](../../active-directory/roles/permissions-reference.md#directory-readers) 역할을 ID에 할당하려면 [전역 관리자](../../active-directory/roles/permissions-reference.md#global-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) 권한이 있는 사용자가 필요합니다. SQL Database, SQL Managed Instance 또는 Azure Synapse를 자주 관리하거나 배포하는 사용자는 이와 같이 권한이 높은 역할에 액세스하지 못할 수 있습니다. 이로 인해 계획되지 않은 Azure SQL 리소스를 만들거나 대규모 조직에서 종종 액세스할 수 없는 높은 권한이 있는 역할 구성원의 도움이 필요한 사용자에게 복잡한 문제가 발생할 수 있습니다.

SQL Managed Instance의 경우 [관리형 인스턴스에 대한 Azure AD 관리자를 설정](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)하려면 먼저 관리형 인스턴스 ID에 **디렉터리 읽기 권한자** 역할을 할당해야 합니다. 

논리 서버에 대한 Azure AD 관리자를 설정할 때에는 SQL Database 또는 Azure Synapse에서 서버 ID에 **디렉터리 읽기 권한자** 역할을 할당할 필요가 없습니다. 그러나 Azure AD 애플리케이션 대신 SQL Database 또는 Azure Synapse에서 Azure AD 개체 만들기를 사용하려면 **디렉터리 읽기 권한자** 역할이 필요합니다. SQL 논리 서버 ID에 역할이 할당되지 않으면 Azure SQL에서 Azure AD 사용자를 만들 수 없습니다. 자세한 내용은 [Azure SQL을 사용하는 Azure Active Directory 서비스 주체](authentication-aad-service-principal.md)를 참조하세요.

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Azure AD 그룹에 디렉터리 읽기 권한자 역할 부여

이제 [전역 관리자](../../active-directory/roles/permissions-reference.md#global-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)가 Azure AD 그룹을 생성하고 [**디렉터리 읽기**](../../active-directory/roles/permissions-reference.md#directory-readers) 권한을 그룹에 할당할 수 있습니다. 그러면 이 그룹의 구성원이 Azure AD Graph API에 액세스할 수 있습니다. 또한 이 그룹의 소유자인 Azure AD 사용자는 Azure SQL 논리 서버의 ID를 포함하여 이 그룹의 새 구성원을 할당할 수 있습니다.

이 솔루션에서는 여전히 ​​높은 권한 사용자(글로벌 관리자 또는 권한이 있는 역할 관리자)가 그룹을 만들고 사용자를 일회성 작업으로 할당해야 하지만, Azure AD 그룹 소유자는 계속 추가 구성원을 할당할 수 있습니다. 따라서 나중에 Azure AD 테넌트에서 모든 SQL Database, SQL Managed Instances 또는 Azure Synapse 서버를 구성하기 위해 높은 권한의 사용자를 포함할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure AD 그룹에 디렉터리 읽기 권한자 역할 할당 및 역할 할당 관리](authentication-aad-directory-readers-role-tutorial.md)
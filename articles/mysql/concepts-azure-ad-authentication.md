---
title: Azure Active Directory 인증 - Azure Database for MySQL
description: Azure Database for MySQL을 사용하여 인증을 위한 Azure Active Directory 개념에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: ea70b094c1b53a5ec4a7c80705e4ac795e6d8629
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598451"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>MySQL 인증에 Azure Active Directory 사용

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Microsoft Azure AD(Azure Active Directory) 인증은 Azure AD에 정의된 ID를 사용하여 Azure Database for MySQL에 연결하기 위한 방법입니다.
Azure AD 인증을 사용하면 중앙 위치에서 데이터베이스 사용자 ID 및 기타 Microsoft 서비스를 관리할 수 있으므로, 권한 관리가 간소화됩니다.

Azure AD 사용 이점은 다음과 같습니다.

- Azure 서비스 간 사용자 인증을 단일 방식으로 수행할 수 있습니다.
- 암호 정책 및 암호 순환을 단일 장소에서 관리할 수 있습니다.
- Azure Active Directory에서 여러 인증 양식이 지원되므로, 암호를 저장할 필요가 없습니다.
- 고객이 외부(Azure AD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Azure AD 인증은 MySQL 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- 애플리케이션이 Azure Database for MySQL에 연결할 수 있도록 토큰 기반 인증이 지원됩니다.

Azure Active Directory 인증을 구성하고 사용하려면 다음 프로세스를 수행합니다.

1. 필요에 따라 Azure Active Directory를 만들고 사용자 ID를 입력합니다.
2. 선택적으로 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
3. Azure Database for MySQL 서버에 대한 Azure AD 관리자를 만듭니다.
4. Azure AD ID에 매핑된 데이터베이스에서 데이터베이스 사용자를 만듭니다.
5. Azure AD ID의 토큰을 검색하고 로그인하여 데이터베이스에 연결합니다.

> [!NOTE]
> Azure AD를 만들고 입력한 후 Azure Database for MySQL에 Azure AD를 구성하려면 [Azure Database for MySQL에서 Azure AD 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조하세요.

## <a name="architecture"></a>Architecture

다음 개략적인 다이어그램은 Azure Database for MySQL에서 Azure AD 인증을 사용하여 인증을 수행하는 방법을 요약해서 보여 줍니다. 화살표는 통신 경로 나타냅니다.

![인증 흐름][1]

## <a name="administrator-structure"></a>관리자 구조

Azure AD 인증을 사용하는 경우 MySQL 서버의 관리자 계정으로는 원래 MySQL 관리자와 Azure AD 관리자의 두 가지 관리자 계정이 있습니다. Azure AD 계정을 기반으로 하는 관리자만 사용자 데이터베이스에서 최초 Azure AD 포함 데이터베이스 사용자를 만들 수 있습니다. Azure AD 관리자 로그인은 Azure AD 사용자나 Azure AD 그룹이 될 수 있습니다. 관리자가 그룹 계정일 경우 모든 그룹 구성원이 사용할 수 있으므로 해당 MySQL 서버에 대해 여러 Azure AD 관리자가 가능합니다. 그룹 계정을 관리자로 사용하면 MySQL 서버에서 사용자나 권한을 변경하지 않고도 Azure AD의 그룹 구성원을 중앙에서 추가 및 제거할 수 있으므로 관리 효율성이 향상됩니다. 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)를 구성할 수 있습니다.

![관리자 구조][2]

## <a name="permissions"></a>사용 권한

Azure AD로 인증할 수 있는 새 사용자를 만들려면 설계된 Azure AD 관리자여야 합니다. 이 사용자는 특정 Azure Database for MySQL 서버에 대해 Azure AD 관리자 계정을 구성하여 할당됩니다.

새 Azure AD 데이터베이스 사용자를 만들려면 Azure AD 관리자로 연결해야 합니다. 자세한 내용은 [Azure Database for MySQL에서 Azure AD 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조하세요.

모든 Azure AD 인증은 Azure Database for MySQL에 대해 Azure AD 관리자가 생성된 경우에만 가능합니다. Azure Active Directory 관리자가 서버에서 제거된 경우 이전에 만든 기존 Azure Active Directory 사용자는 Azure Active Directory 자격 증명을 사용하여 데이터베이스에 더 이상 연결할 수 없습니다.

## <a name="connecting-using-azure-ad-identities"></a>Azure AD ID를 사용하여 연결

Azure Active Directory 인증에서는 Azure AD ID를 사용하여 데이터베이스에 연결하는 다음 방법을 지원합니다.

- Azure Active Directory 암호
- Azure Active Directory 통합
- Azure Active Directory MFA 지원을 통한 유니버설 인증
- Active Directory 애플리케이션 인증서 또는 클라이언트 암호 사용
- [관리 ID](howto-connect-with-managed-identity.md)

Active Directory에 대해 인증을 수행한 후 토큰을 검색합니다. 이 토큰은 로그인에 사용되는 암호입니다.

새 사용자를 추가하는 등의 관리 작업은 이 시점에서 Azure AD 사용자 역할에 한해 지원됩니다.

> [!NOTE]
> Active Directory 토큰으로 연결하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에서 Azure AD 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조하세요.

## <a name="additional-considerations"></a>기타 고려 사항

- Azure Active Directory 인증은 MySQL 5.7 이상에서만 사용할 수 있습니다.
- Azure Database for MySQL 서버에서는 언제든지 Azure AD 관리자를 하나만 구성할 수 있습니다.
- MySQL의 Azure AD 관리자만 처음에 Azure Active Directory 계정을 사용해서 Azure Database for MySQL에 연결할 수 있습니다. Active Directory 관리자가 이후의 Azure AD 데이터베이스 사용자를 구성할 수 있습니다.
- Azure AD에서 사용자가 삭제된 경우 사용자가 더 이상 Azure AD에 인증할 수 없습니다. 따라서 더 이상 해당 사용자에 대해 액세스 토큰을 얻을 수 없습니다. 이 경우 일치하는 사용자가 데이터베이스에 있더라도 해당 사용자를 사용해서 서버에 연결하는 것이 불가능합니다.
> [!NOTE]
> 삭제된 Azure AD 사용자를 사용한 로그인은 토큰이 만료될 때까지(토큰 발급부터 최대 60분) 계속 수행될 수 있습니다.  Azure Database for MySQL에서도 사용자를 제거하면 이 액세스 권한이 즉시 해지됩니다.
- Azure AD 관리자가 서버에서 제거된 경우, 서버가 Azure AD 테넌트와 더 이상 연결되지 않습니다. 따라서 서버에 대해 모든 Azure AD 로그인이 비활성화됩니다. 동일한 테넌트에서 새 Azure AD 관리자를 추가하면 Azure AD 로그인이 다시 활성화됩니다.
- Azure Database for MySQL은 사용자 이름 사용과 반대로, 사용자의 고유한 Azure AD 사용자 ID를 사용해서 Azure Database for MySQL 사용자에 액세스 토큰을 일치시킵니다. 즉, Azure AD에서 Azure AD 사용자가 삭제되었고 동일한 이름으로 새 사용자가 생성되었을 때, Azure Database for MySQL은 이를 다른 사용자로 고려합니다. 따라서 Azure AD에서 사용자를 삭제하고 동일한 이름의 새 사용자가 추가된 경우, 새 사용자가 기존 사용자를 사용해서 연결할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Azure AD를 만들고 입력한 후 Azure Database for MySQL에 Azure AD를 구성하려면 [Azure Database for MySQL에서 Azure AD 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조하세요.
- Azure Database for MySQL의 로그인 및 데이터베이스 사용자 개요를 보려면 [Azure Database for MySQL에서 사용자 만들기](howto-create-users.md)를 참조하세요.

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png

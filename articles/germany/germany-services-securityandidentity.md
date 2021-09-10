---
title: Azure 독일 보안 및 ID 서비스 | Microsoft Docs
description: 이 문서에서는 Azure 독일의 보안 및 ID 서비스를 비교합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 1d66a1f9a1fd2aea17d28d52cbcaa102ec3cb6cc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122528054"
---
# <a name="azure-germany-security-and-identity-services"></a>Azure 독일 보안 및 ID 서비스

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="key-vault"></a>Key Vault
Azure Key Vault 서비스 및 사용 방법에 대한 자세한 내용은 [Azure Key Vault 글로벌 설명서](../key-vault/index.yml)를 참조하세요.

Key Vault는 일반적으로 Azure 독일에서 사용할 수 있습니다. 글로벌 Azure와 마찬가지로 확장명이 없으므로 PowerShell 및 CLI를 통해서만 Key Vault를 사용할 수 있습니다.

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory는 Microsoft Azure에서 실행되는 정보 시스템에 대한 ID 및 액세스 기능을 제공합니다. 디렉터리 서비스, 보안 그룹 및 그룹 정책을 통해 Azure Active Directory를 사용하는 머신의 액세스 및 보안 정책을 제어할 수 있습니다. Azure RBAC(역할 기반 액세스 제어)와 함께 계정 및 보안 그룹을 사용하여 정보 시스템에 대한 액세스를 관리할 수 있습니다. 

Azure Active Directory는 Azure 독일에서 일반 공급됩니다.

### <a name="variations"></a>변형

* Azure 독일의 Azure Active Directory는 글로벌 Azure의 Azure Active Directory와 완전히 분리됩니다. 
* 고객은 Microsoft 계정을 사용하여 Azure 독일에 로그인할 수 없습니다.
* Azure 독일의 로그인 접미사는 *onmicrosoft.de*(글로벌 Azure와 같이 *onmicrosoft.com* 이 아님)입니다.
* 고객이 Azure 독일에서 작업하려면 별도의 구독이 필요합니다.
* Azure 독일의 고객은 글로벌 Azure에서 구독 또는 ID가 필요한 리소스에 액세스할 수 없습니다.
* 글로벌 Azure의 고객은 Azure 독일의 구독 또는 ID가 필요한 리소스에 액세스할 수 없습니다.
* 추가 도메인은 클라우드 환경 중 하나에서만 추가/확인할 수 있습니다.
 
> [!NOTE]
> *Azure 독일 내에서 두 테넌트* 가 모두 있는 다른 테넌트의 사용자에게 권한을 할당하는 것은 아직 사용할 수 없습니다.


## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트를 보려면 [Azure 독일 블로그](/archive/blogs/azuregermany/)를 구독하세요.
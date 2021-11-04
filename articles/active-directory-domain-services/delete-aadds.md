---
title: Azure Active Directory Domain Services 삭제 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Active Directory Domain Services 관리되는 도메인 사용하지 않도록 설정 또는 삭제하는 방법 알아보기
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fa0cc8c0ca548dbf5021d7531198802729afaf2f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427820"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Active Directory Domain Services 관리되는 도메인을 사용하지 않도록 설정 또는 삭제하는 방법 알아보기

Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인이 더 이상 필요하지 않은 경우 이를 삭제할 수 있습니다. Azure AD DS 관리되는 도메인을 끄거나 일시적으로 사용하지 않도록 설정할 수 있는 옵션은 없습니다. 관리되는 도메인을 삭제해도 Azure AD 테넌트가 삭제되거나 테넌트에 기타 부정적인 영향을 주지는 않습니다.

이 문서에서는 Azure Portal을 사용하여 관리되는 도메인을 삭제하는 방법에 대해 보여줍니다.

> [!WARNING]
> **삭제는 영구적이며 되돌릴 수 없습니다.**
> 
> 관리되는 도메인을 삭제하면 다음 단계가 수행됩니다.
>   * 관리되는 도메인의 도메인 컨트롤러가 가상 네트워크에서 프로비전 해제되고 제거됩니다.
>   * 관리되는 도메인의 데이터가 영구적으로 삭제됩니다. 이 데이터에는 사용자가 만든 사용자 지정 OU, GPO, 사용자 지정 DNS 레코드, 서비스 주체, GMSA 등이 포함됩니다.
>   * 관리되는 도메인에 가입된 컴퓨터는 도메인과의 트러스트 관계를 해제하고 도메인 가입을 해제해야 합니다.
>       * 기업 AD 자격 증명을 사용하여 이러한 컴퓨터에 로그인할 수는 없습니다. 대신 컴퓨터에 대한 로컬 관리자 자격 증명을 사용해야 합니다.

## <a name="delete-the-managed-domain"></a>관리되는 도메인 삭제

관리되는 도메인을 삭제하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다.
1. 관리되는 도메인 이름(예: *aaddscontoso.com*)을 선택합니다.
1. **개요** 페이지에서 **삭제** 를 선택합니다. 삭제를 확인하려면 관리되는 도메인의 도메인 이름을 다시 입력하고 **삭제** 를 선택합니다.

관리되는 도메인을 삭제하는 데 15-20분 이상 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS에 표시하려는 기능에 대한 [피드백을 공유][feedback]해 보세요.

Azure AD DS를 다시 시작하려는 경우 [Azure Active Directory Domain Services 관리되는 도메인 만들기 및 구성][create-instance]을 참조하세요.

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789?c=5d63b5b7-ae25-ec11-b6e6-000d3a4f0789
[create-instance]: tutorial-create-instance.md

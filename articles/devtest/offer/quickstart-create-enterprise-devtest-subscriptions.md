---
title: Enterprise Azure 개발/테스트 구독 만들기
description: 팀 및 대규모 조직에 대한 Enterprise 및 조직의 Azure 개발/테스트 구독을 만듭니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 5897e60a2c3c5d46dac48973d78072364af27336
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102874"
---
# <a name="creating-enterprise-and-organization-azure-devtest-subscriptions"></a>Enterprise 및 조직 Azure 개발/테스트 구독 만들기

Enterprise 개발/테스트 구독은 대규모 조직의 팀 개발에 사용할 수 있습니다.

- 대기업에서 팀 개발  
- Enterprise 전체 청구 – 별도의 결제 없음  
- Azure Enterprise Portal을 통해 엔터프라이즈 개발/테스트 제품 구독 만들기  

## <a name="prerequisites"></a>사전 요구 사항

엔터프라이즈 Azure 개발/테스트 구독을 만들려면 등록 계정에 대한 계정 소유자 역할이 있어야 구독을 만들 수 있습니다. 이 역할에 액세스하는 방법에는 두 가지가 있습니다.  

- 등록의 Enterprise 관리자가 계정 [소유자로 만들](../../cost-management-billing/manage/grant-access-to-create-subscription.md) 수 있습니다.  
- 계정의 기존 소유자는 [액세스 권한을 부여할 수 있습니다.](../../cost-management-billing/manage/grant-access-to-create-subscription.md)  

### <a name="important-information-before-you-add-account-owners"></a>계정 소유자를 추가하기 전의 중요한 정보

EA 계정 소유자는 EA Portal 및 기타 Azure 제안에 동일한 로그인 계정을 사용할 **수 없습니다.** 계정 소유자로 EA Portal 추가되었으며 개별 Visual Studio 혜택에 사용한 것과 동일한 자격 증명을 사용했다고 가정합니다. 이 경우 해당 Visual Studio 구독이 EA 개발/테스트 제안으로 변환됩니다.  

> [!Note]  
> 계정 소유자로 EA Portal 처음 로그인하면 경고 팝업이 표시됩니다. 읽고 이해하는 것이 중요합니다. 기존 구독은 청구 가능한 EA 제안으로 변환됩니다. 계정 소유자로 추가된 Visual Studio 구독자인 경우 추가 조치를 취할 때까지 개별 월간 Azure 크레딧이 손실됩니다.

### <a name="to-recover-your-individual-visual-studio-azure-benefits"></a>개별 Visual Studio Azure 혜택을 복구하려면  

EA 계정 소유자로 역할을 인증한 후 다음 중 하나를 수행할 수 있습니다.  

- 소유한 Azure 구독 제거 또는 이동  
- EA Portal 계정 소유자 역할을 삭제합니다.  
- 개별 Visual Studio Azure 혜택에 다시 등록  
- 또는 VLSC의 Visual Studio 관리자 사이트에서 구독자를 삭제하고 다른 로그인을 사용하여 구독을 다시 할당한 다음, Azure 혜택을 새로 Visual Studio 개별 구독에 등록할 수 있습니다.  

## <a name="create-your-subscription"></a>구독 만들기

엔터프라이즈 개발/테스트 구독을 만들려면 먼저 등록 계정을 개발/테스트 계정으로 선택합니다.  

1. 시작하려면 [Ea.azure.com](https://ea.azure.com)이동합니다. Azure Enterprise Portal에서 회사의 등록을 관리할 수 있습니다.  
2. 관리 탭에서 계약 번호를 선택합니다.  

    > [!Note]
    > 등록은 기업계약 갱신 주기에 따라 변경됩니다.
    
    ![기업계약 관리 포털 페이지의 스크린샷.](media/quickstart-create-enterprise-devtest-subscriptions/ea-manage-portal.png "Azure Enterprise Portal에서 회사의 등록을 관리합니다.")
    
3. 적절한 등록 번호를 선택한 후 "계정" 탭을 선택합니다.  
4. 여기에서 계정을 편집하고 개발/테스트 플래그를 "예"로 전환할 수 있습니다.  

계정을 추가하려면 다음을 수행합니다.  

1. navigation Enterprise Portal의 왼쪽 탐색 영역에서 관리를 선택합니다.  
2. 계정 페이지에서 계정 탭을 선택하고 + 계정 추가를 선택합니다.  
3. 부서를 선택하거나 비워 둘 수 있습니다.
4. 원하는 인증 유형을 선택합니다.  
5. 보고에서 계정을 식별하는 식별 이름을 입력합니다.  
6. 새 계정과 연결할 계정 소유자 전자 메일 주소를 입력합니다.  
7. 이메일 주소를 확인하고 추가를 선택합니다.  

![+계정 추가가 선택된 Azure Enterprise Portal 관리 페이지의 스크린샷.](media/quickstart-create-enterprise-devtest-subscriptions/add-account.png "계정 추가를 클릭합니다.")

## <a name="add-your-azure-enterprise-devtest-subscription"></a>Azure Enterprise 개발/테스트 구독 추가

엔터프라이즈 Azure 개발/테스트 구독을 만들 계정을 선택한 후 적절한 구독 제안을 추가합니다. 두 가지 방법이 있습니다. 어떤 것을 선택하든 환경은 동일합니다.  

1. 각 포털의 구독 탭은 "+/추가" 단추가 됩니다. 
    1. 로그인 페이지로 이동합니다.
    1. Microsoft Azure 자격 증명으로 로그인합니다.
    1. 로그인하면 사용 가능한 제안을 나열하는 페이지로 안내됩니다. EA 포털에서 **예를** 전환하여 엔터프라이즈 제안을 확인합니다.
    
1. 엔터프라이즈 개발/테스트 제안을 선택하고 구독을 만듭니다.
    1. ID와 연결된 액세스 권한 및 권한이 있어야 합니다.
    1. 등록 포털 내에서 계정을 개발/테스트 계정으로 지정해야 합니다.

### <a name="next-steps"></a>다음 단계  

- [Azure EA Portal 관리](../../cost-management-billing/manage/ea-portal-administration.md)
- [Azure Enterprise Portal 시작](../../cost-management-billing/manage/ea-portal-get-started.md)

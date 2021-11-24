---
author: vlrodrig
ms.author: whhender
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: include
ms.date: 11/23/2021
ms.custom: ''
ms.openlocfilehash: 70b2476899c500ebf08ffde24d9b9133d510c410
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041299"
---
### <a name="enable-access-policy-enforcement-for-the-azure-storage-account"></a>Azure Storage 계정에 대 한 액세스 정책 적용 사용
Azure Storage 계정이 있는 구독에서 다음 PowerShell 명령을 실행 해야 합니다. 그러면 해당 구독의 모든 Azure Storage 계정이 포함 됩니다.

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
마지막 명령의 출력에 "RegistrationState"의 값이 "등록 됨"으로 표시 되는 경우이 기능에 대해 구독을 사용할 수 있습니다.

---
author: ePpnqeqR
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: include
ms.date: 11/23/2021
ms.custom: ''
ms.openlocfilehash: 14e817e8248b85a95c1478d2bc4ed7af94679d3b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133406051"
---
### <a name="enable-access-policy-enforcement-for-the-azure-storage-account"></a>Azure Storage 계정에 대한 액세스 정책 적용 사용
Azure Storage 계정이 있는 구독에서 다음 PowerShell 명령을 실행해야 합니다. 그러면 해당 구독의 모든 Azure Storage 계정이 설명됩니다.

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
마지막 명령의 출력에 "RegistrationState" 값이 "Registered"로 표시되면 이 기능에 대해 구독이 활성화됩니다.

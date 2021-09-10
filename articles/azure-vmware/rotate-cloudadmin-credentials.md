---
title: Azure VMware Solution용 cloudadmin 자격 증명 회전
description: Azure VMware Solution 프라이빗 클라우드용 vCenter Server 및 NSX-T Manager 자격 증명을 회전하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 884e3f09e429fff39c0dfafcb5d0c9a7b4915e03
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538835"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Azure VMware Solution용 cloudadmin 자격 증명 회전

이 문서에서는 Azure VMware Solution 프라이빗 클라우드용 cloudadmin 자격 증명(vCenter 및 NSX-T 자격 증명)을 회전합니다.  해당 계정의 암호는 만료되지 않지만 새 암호를 생성할 수 있습니다. 새 암호를 생성한 후에는 최신 자격 증명을 적용하여 VMware HCX 커넥터를 업데이트해야 합니다.

[vCenter CloudAdmin 및 NSX-T 관리자 암호를 재설정](https://youtu.be/cK1qY3knj88)하는 방법에 대한 비디오를 볼 수도 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

HCX, vRealize Orchestrator, vRealize Operations Manager 또는 VMware Horizon과 같은 연결된 서비스에 cloudadmin 자격 증명을 사용하는 경우 암호를 업데이트하면 연결이 중지됩니다.  따라서 암호 순환을 시작하기 전에 해당 서비스를 중지합니다. 그러지 않으면 서비스에서 이전 자격 증명을 사용하여 계속 호출하기 때문에 vCenter CloudAdmin 및 NSX-T 관리자 계정이 일시적으로 잠깁니다.  연결된 서비스에 대해 별도의 계정을 설정하는 방법에 대한 자세한 내용은 [액세스 및 ID 개념](./concepts-identity.md)을 참조하세요.

## <a name="reset-your-azure-vmware-solution-cloudadmin-credentials"></a>Azure VMware Solution cloudadmin 자격 증명 다시 설정

이 단계에서는 Azure VMware Solution 구성 요소에 대한 cloudadmin 자격 증명을 회전합니다. 

>[!NOTE]
>**{SubscriptionID}** , **{ResourceGroup}** 및 **{PrivateCloudName}** 을 프라이빗 클라우드 정보로 바꾸어야 합니다.

1. Azure Portal에서 Azure Cloud Shell 세션을 엽니다.

2. vCenter CloudAdmin 암호를 업데이트합니다.  

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
          
3. NSX-T 관리자 암호를 업데이트합니다. 

   ```azurecli-interactive
   az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

## <a name="update-hcx-connector-with-the-latest-cloudadmin-credentials"></a>최신 cloudadmin 자격 증명으로 HCX 커넥터 업데이트

이 단계에서는 업데이트된 자격 증명으로 HCX 커넥터를 업데이트합니다.

1. https://{HCX 커넥터 어플라이언스의 IP}:443에서 온-프레미스 HCX 커넥터로 이동한 다음, 새 자격 증명을 사용하여 로그인합니다.

   포트 443을 사용해야 합니다. 

2. VMware HCX 대시보드에서 **사이트 페어링** 을 선택합니다.
    
   :::image type="content" source="media/rotate-cloudadmin-credentials/hcx-site-pairing.png" alt-text="사이트 페어링이 강조 표시된 VMware HCX 대시보드의 스크린샷":::입니다.
 
3. Azure VMware Solution에 대한 올바른 연결을 선택한 다음, **연결 편집** 을 선택합니다.
 
4. 새 vCenter Server CloudAdmin 사용자 자격 증명을 제공하고 **편집** 을 선택하여 자격 증명을 저장합니다. 저장하면 성공이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution에 대한 vCenter Server 및 NSX-T Manager 자격 증명 다시 설정을 다루었으므로 다음 사항을 알아볼 수 있습니다.

- [Azure VMware Solution에서 Azure 네이티브 서비스 통합](integrate-azure-native-services.md)
- [VMware HCX를 사용하여 Azure VMware Solution 워크로드에 대한 재해 복구 배포](deploy-disaster-recovery-using-vmware-hcx.md)

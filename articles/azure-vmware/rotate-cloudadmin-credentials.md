---
title: Azure VMware Solution용 cloudadmin 자격 증명 회전
description: Azure VMware Solution 프라이빗 클라우드용 vCenter Server 자격 증명을 회전하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 22a88feb7e7b8656666d82cdac4b4d02d546441e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258060"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Azure VMware Solution용 cloudadmin 자격 증명 회전

>[!IMPORTANT]
>현재 NSX-T Manager *admin* 자격 증명 회전은 지원되지 않습니다.  NSX-T Manager 암호를 회전하려면 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 제출합니다. 이 프로세스는 실행 중인 HCX 서비스에 영향을 줄 수 있습니다.

이 문서에서는 Azure VMware Solution 프라이빗 클라우드용 cloudadmin 자격 증명(vCenter Server *CloudAdmin* 자격 증명)을 회전합니다.  이 계정의 암호는 만료되지 않지만 언제든지 새 암호를 생성할 수 있습니다.

>[!CAUTION]
>cloudadmin 사용자 자격 증명을 사용하여 프라이빗 클라우드의 vCenter에 서비스를 연결하는 경우 암호를 회전하면 해당 연결의 작동이 중지됩니다. 암호를 회전하기 전에 관련 서비스를 중지하지 않는 한 해당 연결로 인해 cloudadmin 계정도 잠깁니다.

## <a name="prerequisites"></a>필수 조건

암호를 회전하기 전에 *cloudadmin@vsphere.local* 로 vCenter에 연결하는 서비스를 고려하고 결정합니다. 이 서비스에는 HCX, vRealize Orchestrator, vRealize Operations Manager, VMware Horizon 또는 모니터링이나 프로비저닝에 사용되는 기타 타사 도구 등의 VMware 서비스가 포함될 수 있습니다. 

cloudadmin 사용자를 사용하여 vCenter에 인증하는 서비스를 결정하는 한 가지 방법은 프라이빗 클라우드에 대해 vSphere 클라이언트를 사용하여 vSphere 이벤트를 검사하는 것입니다. 해당 서비스를 식별한 후 암호를 회전하기 전에 이 서비스를 중지해야 합니다. 중지하지 않으면 암호를 회전한 후 서비스가 작동하지 않습니다. 또한 이 서비스는 캐시된 버전의 이전 자격 증명을 사용하여 지속적으로 인증을 시도하기 때문에 vCenter CloudAdmin 계정에서 임시 잠금이 발생합니다. 

서비스를 vCenter에 연결하는 데 cloudadmin 사용자를 사용하는 대신 각 서비스에 개별 계정을 사용하는 것이 좋습니다. 연결된 서비스에 대해 별도의 계정을 설정하는 방법에 대한 자세한 내용은 [액세스 및 ID 개념](./concepts-identity.md)을 참조하세요.

## <a name="reset-your-vcenter-credentials"></a>vCenter 자격 증명 다시 설정

1. Azure Portal에서 Azure Cloud Shell 세션을 엽니다.

2. vCenter *CloudAdmin* 자격 증명을 업데이트합니다.  **{SubscriptionID}** , **{ResourceGroup}** , **{PrivateCloudName}** 을 프라이빗 클라우드 정보로 바꾸어야 합니다. 

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
 
## <a name="update-hcx-connector"></a>HCX 커넥터 업데이트 

1. https://{HCX 커넥터 어플라이언스의 IP}:443에서 온-프레미스 HCX 커넥터로 이동한 다음, 새 자격 증명을 사용하여 로그인합니다.

   포트 **443** 을 사용해야 합니다. 

2. VMware HCX 대시보드에서 **사이트 페어링** 을 선택합니다.
    
   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="사이트 페어링이 강조 표시된 VMware HCX 대시보드의 스크린샷":::입니다.
 
3. Azure VMware Solution에 대한 올바른 연결을 선택한 다음, **연결 편집** 을 선택합니다.
 
4. 새 vCenter 사용자 자격 증명을 제공하고 **편집** 을 선택하여 자격 증명을 저장합니다. 저장하면 성공이 표시됩니다.


## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution의 vCenter 자격 증명 다시 설정을 다루었으므로 다음 사항을 알아볼 수 있습니다.

- [Azure VMware Solution에서 Azure 네이티브 서비스 통합](integrate-azure-native-services.md)
- [VMware HCX를 사용하여 Azure VMware Solution 워크로드에 대한 재해 복구 배포](deploy-disaster-recovery-using-vmware-hcx.md)

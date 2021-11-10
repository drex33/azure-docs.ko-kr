---
title: 확인된 도메인 변경에 대한 데이터 감사 문제 해결 | Microsoft Docs
description: 사용자의 확인된 도메인을 변경하는 경우 Azure Active Directory 활동 로그에 표시되는 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18c25f183d996ceb0829cad6d16633be98aa0707
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995714"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>문제 해결: 확인된 도메인 변경에 대한 데이터 감사 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>사용자에 대한 변경 사항이 많으며 그 원인을 잘 모르겠습니다.

### <a name="symptoms"></a>증상

Azure AD 감사 로그를 확인하면 Azure AD 테넌트에서 여러 사용자 업데이트가 발생하는 것으로 나타납니다. 해당 **사용자 업데이트** 이벤트에서는 **행위자** 정보를 표시하지 않으므로 사용자에 대한 대량 변경을 트리거한 항목/사용자가 불확실하게 됩니다. 

### <a name="cause"></a>원인

 대량 개체 변경의 일반적인 원인은 **ProxyCalc** 라는 비동기 백 엔드 작업입니다.  **ProxyCalc** 는 Azure AD 사용자, 그룹 또는 연락처에서 업데이트되는 적절한 **UserPrincipalName** 및 **프록시 주소** 를 결정하는 논리입니다. **ProxyCalc** 의 디자인은 언제든 Azure AD의 모든 **UserPrincipalName** 및 **프록시 주소** 가 일관되도록 합니다. **ProxyCalc** 는 확인된 도메인 변경과 같이 명시적인 변경에 의해 트리거되어야 하며 백그라운드에서 작업으로 영구 실행되지 않습니다. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>UserPrincipalName 일관성은 무엇을 의미하나요? 

클라우드 전용 사용자의 경우 일관성은 **UserPrincipalName** 이 확인된 도메인 접미사로 설정됨을 의미합니다. 일관되지 않은 **UserPrincipalName** 을 처리하는 경우 **ProxyCalc** 에서 기본 onmicrosoft.com 접미사(예: username@Contoso.onmicrosoft.com)로 변환합니다. 

동기화된 사용자의 경우 일관성은 **UserPrincipalName** 이 확인된 도메인 접미사로 설정되며 온-프레미스 **UserPrincipalName** 값(ShadowUserPrincipalName)과 일치함을 의미합니다. 일관되지 않은 **UserPrincipalName** 을 처리하는 경우 **ProxyCalc** 에서 **ShadowUserPrincipalName** 과 동일한 값으로 되돌립니다. 도메인 접미사가 테넌트에서 제거된 경우에는 기본 *.onmicrosoft.com 도메인 접미사로 변환합니다. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>프록시 주소 일관성은 무엇을 의미하나요? 

클라우드 전용 사용자의 경우 일관성은 프록시 주소가 확인된 도메인 접미사와 일치하는 것을 의미합니다. 일관되지 않은 프록시 주소를 처리하는 경우 **ProxyCalc** 에서 기본 *.onmicrosoft.com 도메인 접미사(예: SMTP:username@Contoso.onmicrosoft.com)로 변환합니다. 

동기화된 사용자의 경우 일관성은 프록시 주소가 온-프레미스 프록시 주소 값(즉, ShadowProxyAddresses)과 일치하는 것을 의미합니다. **ProxyAddresses** 는 **ShadowProxyAddresses** 와 동기화되어야 합니다. 동기화된 사용자에게 Exchange 라이선스가 할당된 경우 프록시 주소는 온-프레미스 프록시 주소 값과 일치해야 하며 확인된 도메인 접미사와도 일치해야 합니다. 이 시나리오에서 **ProxyCalc** 는 확인되지 않은 도메인 접미사를 사용하는 일관되지 않은 프록시 주소를 삭제하며 Azure AD의 개체에서 제거됩니다. 확인되지 않은 도메인을 나중에 확인하는 경우 **ProxyCalc** 는 다시 계산하여 **ShadowProxyAddresses** 의 프록시 주소를 Azure AD의 개체에 다시 추가합니다.  

> [!NOTE]
> 동기화된 개체의 경우 **ProxyCalc** 논리에서 예기치 않은 결과가 계산되지 않도록 하려면 프록시 주소를 온-프레미스 개체에 있는 Azure AD의 확인된 도메인으로 설정하는 것이 가장 좋습니다.  

  
**ProxyCalc** 를 트리거할 수 있는 관리 작업 중 하나로, 확인된 도메인 변경이 있습니다. 이 작업은 Azure AD 테넌트에서 확인된 도메인이 추가/제거될 때마다 발생하며, 내부적으로 **ProxyCalc** 를 트리거합니다.  

예를 들어 확인된 도메인 Fabrikam.com을 Contoso.onmicrosoft.com 테넌트에 추가하는 경우 이 작업은 테넌트의 모든 개체에서 ProxyCalc 작업을 트리거합니다. 이 이벤트는 Azure AD 감사 로그에 **사용자 업데이트** 이벤트로 캡처되며 앞에는 **확인된 도메인 추가** 이벤트가 옵니다. 반대로, Fabrikam.com이 Contoso.onmicrosoft.com 테넌트에서 제거된 경우에는 모든 **사용자 업데이트** 이벤트 앞에 **확인된 도메인 제거** 이벤트가 옵니다.   

#### <a name="additional-notes"></a>추가 참고 사항:

다음과 같은 특정 개체는 ProxyCalc에 의해 변경되지 않습니다. 

- 활성 Exchange 라이선스가 없는 개체 
- **MSExchRemoteRecipientType** 이 Null로 설정된 개체 
- 공유 리소스로 간주되지 않는 개체. **CloudMSExchRecipientDisplayType** 에 **MailboxUser (shared)** , **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **Group mailbox**, **Scheduling mailbox**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 값 중 하나가 포함되어 있는 경우 공유 리소스로 간주됩니다. 
  
 이러한 두 가지 이벤트 간에 추가 상관 관계를 구축할 수 있도록 Microsoft에서는 확인된 도메인 변경에 의해 트리거되는 변경 사항을 확인할 감사 로그의 **행위자** 정보를 업데이트하는 작업을 진행하고 있습니다. 이 작업은 확인된 도메인 변경 이벤트가 발생한 시기와 테넌트의 개체를 대량 업데이트하기 시작한 시기를 확인하는 데 도움이 됩니다. 

또한, 대부분의 경우 **UserPrincipalName** 및 **프록시 주소** 가 일관되어 사용자에 대한 변경 사항이 없으므로 감사 로그에는 개체에 대한 실제 변경이 발생한 업데이트만 표시하도록 작업하고 있습니다. 이 작업은 감사 로그에 노이즈가 발생하는 것을 방지하며 관리자가 위에서 설명한 대로 나머지 사용자 변경 사항을 확인된 도메인 변경 이벤트와 상호 연결하는 데 도움을 줍니다. 

## <a name="next-steps"></a>다음 단계

[Azure AD Connect 동기화 서비스 섀도 특성](../hybrid/how-to-connect-syncservice-shadow-attributes.md)

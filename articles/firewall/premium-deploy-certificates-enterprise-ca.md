---
title: Azure Firewall 프리미엄에 대한 엔터프라이즈 CA 인증서 배포 및 구성
description: Azure Firewall 프리미엄에 대한 엔터프라이즈 CA 인증서를 배포 및 구성하는 방법을 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: 067daef5782697357d08edc0a6c09bea8af085f6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441076"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall"></a>Azure Firewall에 대한 엔터프라이즈 CA 인증서 배포 및 구성


Azure Firewall 프리미엄에는 인증서 인증 체인이 필요한 TLS 검사 기능이 포함되어 있습니다. 프로덕션 배포의 경우 엔터프라이즈 PKI를 사용하여 Azure Firewall 프리미엄에서 사용하는 인증서를 생성해야 합니다. 이 문서를 사용하여 Azure Firewall 프리미엄에 대한 중간 CA 인증서를 만들고 관리합니다.

Azure Firewall 프리미엄에서 사용하는 인증서에 대한 자세한 내용은 [Azure Firewall 프리미엄 인증서](premium-certificates.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

엔터프라이즈 CA를 사용하여 Azure Firewall 프리미엄에서 사용할 인증서를 생성하려면 다음 리소스가 있어야 합니다. 

- Active Directory 포리스트 
- 웹 등록을 사용하도록 설정한 Active Directory 인증 서비스 루트 CA 
- 프리미엄 계층 방화벽 정책을 사용하는 Azure Firewall 프리미엄 
- Azure Key Vault 
- Key Vault 액세스 정책에 정의된 **인증서 및 암호** 에 대한 읽기 권한이 있는 관리 ID 

## <a name="request-and-export-a-certificate"></a>인증서 요청 및 내보내기

1. 루트 CA의 웹 등록 사이트(일반적으로 `https://<servername>/certsrv`)에 액세스하고 **인증서 요청** 을 선택합니다.
1. **고급 인증서 요청** 을 선택합니다.
1. **이 CA에 요청을 만들어 제출합니다.** 를 선택합니다.
1. 하위 인증 기관 템플릿을 사용하여 양식을 작성합니다.
1. 요청을 제출하고 인증서를 설치합니다.
1. Internet Explorer를 사용하여 Windows Server에서 이 요청을 수행한다고 가정하고 **인터넷 옵션** 을 엽니다.
1. **콘텐츠** 탭으로 이동하여 **인증서** 를 선택합니다.
1. 방금 발급한 인증서를 선택하고 **내보내기** 를 선택합니다.
1. **다음** 을 선택하여 마법사를 시작합니다. **예, 프라이빗 키를 내보냅니다.** 를 선택한 후, **다음** 을 선택합니다.
1. .pfx 파일 형식이 기본적으로 선택됩니다. **가능하면 인증 경로에 있는 인증서 모두 포함** 을 선택 취소합니다. 전체 인증서 체인을 내보내는 경우 Azure Firewall로의 가져오기 프로세스가 실패합니다.
1. 키를 보호하기 위해 암호를 할당하고 확인한 후 **다음** 을 선택합니다.
1. 파일 이름 및 내보내기 위치를 선택하고 **다음** 을 선택합니다.
1. **마침** 을 선택하고 내보낸 인증서를 안전한 위치로 이동합니다.

## <a name="add-the-certificate-to-a-firewall-policy"></a>방화벽 정책에 인증서 추가

1. Azure Portal에서 Key Vault의 인증서 페이지로 이동하고 **생성/가져오기** 를 선택합니다.
1. 만들기 방법으로 **가져오기** 를 선택하고, 인증서 이름을 지정한 후, 내보낸 .pfx 파일을 선택하고, 암호를 입력한 다음, **만들기** 를 선택합니다.
1. 방화벽 정책의 **TLS 검사** 페이지로 이동하고 관리 ID, Key Vault 및 인증서를 선택합니다. 
1. **저장** 을 선택합니다.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS 조사":::

## <a name="validate-tls-inspection"></a>TLS 검사 유효성 검사

1. 대상 URL 또는 선택한 FQDN에 대한 TLS 검사를 사용하여 애플리케이션 규칙을 만듭니다.  예: `*bing.com`
1. 규칙의 원본 범위 내에 있는 도메인 조인 머신에서 대상으로 이동하고 브라우저의 주소 표시줄 옆에 있는 잠금 기호를 선택합니다. 인증서는 퍼블릭 CA가 아닌 엔터프라이즈 CA에서 발급된 것으로 표시해야 합니다.
1. 인증서 경로를 포함하는 자세한 정보를 표시하려면 인증서를 표시합니다.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="인증서 세부 정보":::
1. Log Analytics에서 다음 KQL 쿼리를 실행하여 TLS 검사가 적용된 모든 요청을 반환합니다.
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   결과에는 검사된 트래픽의 전체 URL이 표시됩니다. :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL 쿼리":::

## <a name="next-steps"></a>다음 단계

[Azure Portal의 Azure Firewall 프리미엄](premium-portal.md)

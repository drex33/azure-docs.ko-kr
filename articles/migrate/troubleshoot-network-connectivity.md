---
title: 네트워크 연결 문제 해결 | Microsoft Docs
description: 프라이빗 엔드포인트에 Azure Migrate를 사용하는 경우에 발생하는 일반적인 오류에 대한 문제 해결 팁을 제공합니다.
author: SGSneha
ms.author: SGSneha
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 2c76b8d916c656409921428587bb276388276448
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138105"
---
# <a name="troubleshoot-network-connectivity"></a>네트워크 연결 문제 해결
이 문서는 프라이빗 엔드포인트에 Azure Migrate를 사용하는 경우에 발생하는 네트워크 연결 문제를 해결하는 데 도움이 됩니다.

## <a name="validate-private-endpoints-configuration"></a>프라이빗 엔드포인트 구성 유효성 검사

프라이빗 엔드포인트가 승인됨 상태인지 확인합니다.  

1. **Azure Migrate**: **검색 및 평가** 및 **서버 마이그레이션 속성** 페이지로 이동합니다.

2. 속성 페이지에는 Azure Migrate에서 자동으로 생성된 프라이빗 엔드포인트 및 프라이빗 링크 FQDN 목록이 포함됩니다.  

3. 진단하려는 프라이빗 엔드포인트를 선택합니다.  
   a. 연결 상태가 승인됨인지 유효성을 검사합니다.           
   b. 연결이 보류 중 상태인 경우 승인을 받아야 합니다.                         
   다. 프라이빗 엔드포인트 리소스로 이동하고 가상 네트워크가 Migrate 프로젝트 프라이빗 엔드포인트 가상 네트워크와 일치하는지 검토할 수도 있습니다.                                                        

     ![프라이빗 엔드포인트 연결 보기](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>프라이빗 엔드포인트를 통해 데이터 흐름의 유효성 검사
데이터 흐름 메트릭을 검토하여 프라이빗 엔드포인트를 통한 트래픽 흐름을 확인합니다. Azure Migrate: 서버 평가 및 서버 마이그레이션 속성 페이지에서 프라이빗 엔드포인트를 선택합니다. 이렇게 하면 Azure Private Link 센터의 프라이빗 엔드포인트 개요 섹션으로 리디렉션됩니다. 왼쪽 메뉴에서 **메트릭** 을 선택하여 _데이터 입력 바이트 수_ 및 _데이터 출력 바이트 수_ 정보를 통해 트래픽 흐름을 확인합니다.

## <a name="verify-dns-resolution"></a>DNS 확인을 확인

온-프레미스 어플라이언스(또는 복제 공급자)는 정규화된 프라이빗 링크 도메인 이름(FQDN)을 사용하여 Azure Migrate 리소스에 액세스합니다. 원본 환경에서 프라이빗 엔드포인트의 개인 IP 주소를 확인하려면 추가 DNS 설정이 필요할 수 있습니다. [이 문서를 참조](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)하여 네트워크 연결 문제를 해결하는 데 도움이 될 수 있는 DNS 구성 시나리오를 이해합니다.  

프라이빗 링크 연결의 유효성을 검사하려면 Migrate 어플라이언스를 호스트하는 온-프레미스 서버에서 Azure Migrate 리소스 엔드포인트(프라이빗 링크 리소스 FQDN)의 DNS 확인을 수행하고 개인 IP 주소로 확인되는지 확인합니다.
프라이빗 엔드포인트 세부 정보 및 프라이빗 링크 리소스 FQDN 정보는 검색 및 평가 및 서버 마이그레이션 속성 페이지에서 사용할 수 있습니다. **DNS 설정 다운로드** 를 선택하여 목록을 확인합니다.   

 ![Azure Migrate: 검색 및 평가 속성](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Azure Migrate: 서버 마이그레이션 속성](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

스토리지 계정 프라이빗 링크 FQDN의 DNS 확인을 설명하는 예제  

- 다음을 입력합니다. _nslookup ```<storage-account-name>_.blob.core.windows.net.``` ```<storage-account-name>```을 Azure Migrate에 사용되는 스토리지 계정 이름으로 바꿉니다.  

    다음과 같은 메시지가 표시됩니다.  

   ![DNS 확인 예제](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- 스토리지 계정 이름에 대해 개인 IP 주소 10.1.0.5가 반환됩니다. 이 주소는 프라이빗 엔드포인트 가상 네트워크 서브넷에 속합니다.   

비슷한 접근 방식을 사용하여 다른 Azure Migrate 아티팩트의 DNS 확인을 확인할 수 있습니다.   

DNS 확인이 잘못된 경우 다음 단계를 수행합니다.  

테스트에 **권장**: 프라이빗 링크 리소스 FQDN 및 연결된 개인 IP 주소로 온-프레미스 어플라이언스의 DNS 호스트 파일을 편집하여 원본 환경 DNS 레코드를 수동으로 업데이트할 수 있습니다.
- 사용자 지정 DNS를 사용하는 경우 사용자 지정 DNS 설정을 검토하고 DNS 구성이 올바른지 유효성을 검사합니다. 지침은 [프라이빗 엔드포인트 개요: DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)을 참조하세요.
- Azure에서 제공하는 DNS 서버를 사용하는 경우 추가 문제 해결에 관해서는 아래 섹션을 참조하세요.  

> [!Tip]
> 프라이빗 링크 리소스 FQDN 및 연결된 개인 IP 주소로 온-프레미스 어플라이언스의 DNS 호스트 파일을 편집하여 원본 환경 DNS 레코드를 수동으로 업데이트할 수 있습니다. 이 옵션은 테스트용으로만 사용하는 것이 좋습니다. <br/>  


## <a name="validate-the-private-dns-zone"></a>프라이빗 DNS 영역 유효성 검사   
이전 섹션에 설명된 대로 DNS 확인이 작동하지 않는 경우 프라이빗 DNS 영역에 문제가 있을 수 있습니다.  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>필요한 프라이빗 DNS 영역 리소스가 있는지 확인  
기본적으로 Azure Migrate는 각 리소스 종류의 ‘privatelink’ 하위 도메인에 해당하는 프라이빗 DNS 영역도 만듭니다. 프라이빗 DNS 영역은 프라이빗 엔드포인트 리소스 그룹과 동일한 Azure 리소스 그룹에 생성됩니다. Azure 리소스 그룹은 다음 형식의 프라이빗 DNS 영역 리소스를 포함해야 합니다.
- privatelink.vaultcore.azure.net - 키 자격 증명 모음
- privatelink.blob.core.windows.net - 스토리지 계정
- privatelink.siterecovery.windowsazure.com - Recovery Services 자격 증명 모음(Hyper-V 및 에이전트 기반 복제용)
- privatelink.prod.migration.windowsazure.com - Migrate 프로젝트, 평가 프로젝트 및 검색 사이트.   

Azure Migrate은 프라이빗 DNS 영역을 자동으로 만듭니다(사용자가 선택한 캐시/복제 스토리지 계정 제외). 프라이빗 엔드포인트 페이지로 이동하고 DNS 구성을 선택하여 연결된 프라이빗 DNS 영역을 찾을 수 있습니다. 여기서는 프라이빗 DNS 통합 섹션에서 프라이빗 DNS 영역을 확인할 수 있습니다.

[![DNS 구성 스크린샷](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

아래 표시된 대로 DNS 영역이 없는 경우 [새 프라이빗 DNS 영역 리소스를 만듭니다.](../dns/private-dns-getstarted-portal.md)  

[![프라이빗 DNS 영역 만들기](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>프라이빗 DNS 영역이 가상 네트워크에 연결되어 있는지 확인  
프라이빗 DNS 영역은 리소스 엔드포인트의 개인 IP 주소를 확인하기 위해 DNS 쿼리의 프라이빗 엔드포인트를 포함하는 가상 네트워크에 연결되어야 합니다. 프라이빗 DNS 영역이 올바른 가상 네트워크에 연결되지 않은 경우 해당 Virtual Network의 DNS 확인은 프라이빗 DNS 영역을 무시합니다.   

Azure Portal에서 프라이빗 DNS 영역 리소스로 이동하고 왼쪽 메뉴에서 가상 네트워크 링크를 선택합니다. 연결된 가상 네트워크가 표시됩니다.

[![가상 네트워크 링크 보기](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

이 작업은 각각 구독에 가상 네트워크 이름이 포함된 링크 목록을 표시합니다. 프라이빗 엔드포인트 리소스를 포함하는 가상 네트워크가 여기에 나열되어야 합니다. 또는 [이 문서에 따라](../dns/private-dns-getstarted-portal.md#link-the-virtual-network) 프라이빗 DNS 영역을 가상 네트워크에 연결합니다.    

프라이빗 DNS 영역이 가상 네트워크에 연결된 후 가상 네트워크에서 시작된 DNS 요청은 프라이빗 DNS 영역에서 DNS 레코드를 찾습니다. 이 레코드는 프라이빗 엔드포인트가 생성된 가상 네트워크에 대한 올바른 주소 확인을 위해 필요합니다.   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>프라이빗 DNS 영역에 올바른 A 레코드가 포함되어 있는지 확인합니다.

문제를 해결하려는 프라이빗 DNS 영역으로 이동합니다. 개요 페이지에는 해당 프라이빗 DNS 영역의 모든 DNS 레코드가 표시됩니다. 리소스에 관한 DNS A 레코드가 있는지 확인합니다. A 레코드 값(IP 주소)은 리소스의 개인 IP 주소여야 합니다. 잘못된 IP 주소가 포함된 A 레코드를 찾은 경우에는 잘못된 IP 주소를 제거하고 새 주소를 추가해야 합니다. 전체 A 레코드를 제거하고, 새 레코드를 추가한 다음, 온-프레미스 원본 어플라이언스에서 DNS 플러시를 수행하는 것이 좋습니다.   

프라이빗 DNS 영역의 스토리지 계정 DNS A 레코드를 설명하는 예제:

![DNS 레코드](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

프라이빗 DNS 영역의 Recovery Services 자격 증명 모음 마이크로 서비스 DNS A 레코드를 설명하는 예제:

[![Recovery Services 자격 증명 모음의 DNS 레코드](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> A 레코드를 제거하거나 수정하면 TTL(Time To Live) 값이 아직 만료되지 않았을 수 있기 때문에 머신이 계속 이전 IP 주소로 확인될 수 있습니다.  

### <a name="items-that-may-affect-private-link-connectivity"></a>프라이빗 링크 연결에 영향을 줄 수 있는 항목  

고급 또는 복잡한 시나리오에서 찾을 수 있는 일부 항목 목록은 다음과 같습니다.

- 방화벽 설정(가상 네트워크에 연결된 Azure Firewall 또는 어플라이언스 머신에 배포하는 사용자 지정 방화벽 솔루션)  
- 네트워크 피어링. 사용되는 DNS 서버 및 트래픽이 라우팅되는 방식에 영향을 줄 수 있습니다.  
- 사용자 지정 게이트웨이(NAT) 솔루션(DNS 쿼리의 트래픽을 비롯한 트래픽의 라우팅 방식에 영향을 줄 수 있음)

자세한 내용은 [프라이빗 엔드포인트 연결 문제의 문제 해결 가이드](../private-link/troubleshoot-private-endpoint-connectivity.md)를 참조하세요.  

## <a name="common-issues-while-using-azure-migrate-with-private-endpoints"></a>프라이빗 엔드포인트에 Azure Migrate를 사용하는 경우의 일반적인 문제
이 섹션에서는 자주 발생하는 몇 가지 문제를 나열하고 직접 수행할 수 있는 문제 해결 단계를 제안합니다.

### <a name="appliance-registration-fails-with-the-error-forbiddentoaccesskeyvault"></a>ForbiddenToAccessKeyVault 오류로 인해 어플라이언스 등록 실패
<_ErrorMessage_> 오류로 인해 <_KeyVaultName_>에 대한 Azure Key Vault 만들기 또는 업데이트 작업이 실패함
#### <a name="possible-causes"></a>가능한 원인:
어플라이언스를 등록하는 데 사용되는 Azure 계정에 필요한 권한이 없거나 Azure Migrate 어플라이언스에서 Key Vault에 액세스할 수 없는 경우 이 문제가 발생할 수 있습니다.
#### <a name="remediation"></a>수정:
**Key Vault 액세스 문제 해결 단계:**
1. 어플라이언스를 등록하는 데 사용되는 Azure 사용자 계정에는 적어도 구독에 대한 기여자 역할이 있어야 합니다.
2. 어플라이언스를 등록하려는 사용자가 Key Vault에 액세스할 수 있고 Key Vault>액세스 정책 섹션에 할당된 액세스 정책이 있는지 확인합니다. [자세한 정보](../key-vault/general/assign-access-policy-portal.md)
- 필수 Azure 역할 및 사용 권한에 대해 [자세히 알아보세요](./migrate-appliance.md#appliance---vmware).

**Key Vault에 대한 연결 문제를 해결하는 단계:** 프라이빗 엔드포인트 연결에 대해 어플라이언스를 사용하도록 설정한 경우 다음 단계를 사용하여 네트워크 연결 문제를 해결합니다.
- 어플라이언스는 동일한 가상 네트워크에서 호스트되거나 프라이빗 링크를 통해 대상 Azure 가상 네트워크(Key Vault 프라이빗 엔드포인트가 만들어진 위치)에 연결되어 있는지 확인합니다. Key Vault 프라이빗 엔드포인트는 프로젝트 생성 환경 중에 선택한 가상 네트워크에 만들어집니다. **Azure Migrate > 속성** 페이지에서 가상 네트워크 세부 정보를 확인할 수 있습니다.
![Azure Migrate 속성](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)  

- 어플라이언스가 프라이빗 링크를 통해 Key Vault에 네트워크로 연결되어 있는지 확인합니다. 프라이빗 링크 연결의 유효성을 검사하려면 어플라이언스를 호스트하는 온-프레미스 서버에서 Key Vault 리소스 엔드포인트의 DNS 확인을 수행하고 개인 IP 주소로 확인되는지 알아봅니다.
- **Azure Migrate: 검색 및 평가>속성** 으로 이동하여 키 생성 단계 중에 만들어진 Key Vault와 같은 리소스의 프라이빗 엔드포인트 세부 정보를 찾습니다.  

    ![Azure Migrate 서버 평가 속성](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
- **DNS 설정 다운로드** 를 선택하여 DNS 매핑을 다운로드합니다.

    ![DNS 설정 다운로드](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)  

- 명령줄을 열고 다음 nslookup 명령을 실행하여 DNS 설정 파일에 언급된 Key Vault URL에 대한 네트워크 연결을 확인합니다.   

    ```console
    nslookup <your-key-vault-name>.vault.azure.net
    ```

    ns lookup 명령을 실행하여 퍼블릭 엔드포인트를 통해 키 자격 증명 모음의 IP 주소를 확인하는 경우 다음과 같은 결과가 표시됩니다.

    ```console
    c:\ >nslookup <your-key-vault-name>.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  (public IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
    ```

    ns lookup 명령을 실행하여 프라이빗 엔드포인트를 통해 키 자격 증명 모음의 IP 주소를 확인하는 경우 다음과 같은 결과가 표시됩니다.

    ```console
    c:\ >nslookup your_vault_name.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.20 (private IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
              <your-key-vault-name>.privatelink.vaultcore.azure.net
    ```

    nslookup 명령은 위에서 설명한 대로 개인 IP 주소로 확인되어야 합니다. 개인 IP 주소는 DNS 설정 파일에 나열된 주소와 일치해야 합니다.

DNS 확인이 잘못된 경우 다음 단계를 수행합니다.
1. DNS 매핑 및 연결된 개인 IP 주소를 사용하여 온-프레미스 어플라이언스의 DNS 호스트 파일을 편집하여 원본 환경 DNS 레코드를 수동으로 업데이트합니다. 이 옵션은 테스트용으로 권장됩니다.

   ![DNS 호스트 파일](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

2. 사용자 지정 DNS 서버를 사용하는 경우 사용자 지정 DNS 설정을 검토하고 DNS 구성이 올바른지 유효성을 검사합니다. 지침은 [프라이빗 엔드포인트 개요: DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)을 참조하세요.
3. 문제가 계속되면 추가 문제 해결을 위해 [이 섹션을 참조하세요](#validate-the-private-dns-zone).

연결을 확인한 후 등록 프로세스를 다시 시도합니다.

### <a name="start-discovery-fails-with-the-error-agentnotconnected"></a>AgentNotConnected 오류를 나타내며 검색 시작 실패
온-프레미스 에이전트가 Azure의 Azure Migrate 서비스 엔드포인트인 <_URLname_>과(와) 통신할 수 없으므로 어플라이언스가 검색을 시작할 수 없습니다.

![에이전트가 연결되지 않음 오류](./media/how-to-use-azure-migrate-with-private-endpoints/agent-not-connected-error.png)  

#### <a name="possible-causes"></a>가능한 원인:
이 문제는 어플라이언스에서 오류 메시지에 언급된 서비스 엔드포인트에 연결할 수 없는 경우에 발생할 수 있습니다.
#### <a name="remediation"></a>수정:
어플라이언스에 직접 또는 프록시를 통해 연결되어 있고 오류 메시지에 제공된 서비스 엔드포인트를 확인할 수 있는지 확인합니다.  

프라이빗 엔드포인트 연결에 어플라이언스를 사용하도록 설정한 경우 어플라이언스가 프라이빗 링크를 통해 Azure 가상 네트워크에 연결되어 있고 오류 메시지에 제공된 서비스 엔드포인트를 확인할 수 있는지 알아봅니다.

**Azure Migrate 서비스 엔드포인트에 대한 프라이빗 링크 연결 문제를 해결하는 단계:**

프라이빗 엔드포인트 연결에 어플라이언스를 사용하도록 설정한 경우 다음 단계를 사용하여 네트워크 연결 문제를 해결합니다.

- 어플라이언스는 동일한 가상 네트워크에서 호스트되거나 프라이빗 링크를 통해 대상 Azure 가상 네트워크(프라이빗 엔드포인트가 만들어진 위치)에 연결되어 있는지 확인합니다. Azure Migrate 서비스에 대한 프라이빗 엔드포인트는 프로젝트 생성 환경 중에 선택한 가상 네트워크에서 만들어집니다. **Azure Migrate > 속성** 페이지에서 가상 네트워크 세부 정보를 확인할 수 있습니다.

![Azure Migrate 속성](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)   

- 어플라이언스가 프라이빗 링크 연결을 통해 오류 메시지에 언급된 서비스 엔드포인트 URL 및 기타 URL에 대해 네트워크로 연결되어 있는지 확인합니다. 프라이빗 링크 연결의 유효성을 검사하려면 어플라이언스를 호스트하는 온-프레미스 서버에서 URL의 DNS 확인을 수행하고 개인 IP 주소로 확인되는지 알아봅니다.
- **Azure Migrate: 검색 및 평가>속성** 으로 이동하여 키 생성 단계 중에 만들어진 서비스 엔드포인트에 대한 프라이빗 엔드포인트 세부 정보를 찾습니다.  
    ![Azure Migrate 서버 평가 속성](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
- **DNS 설정 다운로드** 를 선택하여 DNS 매핑을 다운로드합니다.

    ![DNS 설정 다운로드](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)   

|**프라이빗 엔드포인트 URL을 포함하는 DNS 매핑**  | **세부 정보** |
|--- | ---|
|*.disc.privatelink.test.migration.windowsazure.com | Azure Migrate 검색 서비스 엔드포인트
|*.asm.privatelink.test.migration.windowsazure.com  | Azure Migrate 평가 서비스 엔드포인트  
|*.hub.privatelink.test.migration.windowsazure.com  | 다른 Microsoft 또는 외부 [ISV(독립 소프트웨어 공급업체)](./migrate-services-overview.md#isv-integration)제품에서 데이터를 수신하기 위한 Azure Migrate 허브 엔드포인트
|*.vault.azure.net | Key Vault 엔드포인트
|*.blob.core.windows.net | 종속성 및 성능 데이터에 대한 스토리지 계정 엔드포인트  

위의 URL 외에도 어플라이언스는 직접 또는 프록시를 통해 인터넷에서 다음 URL에 액세스해야 합니다.

| **기타 퍼블릭 클라우드 URL<br>(퍼블릭 엔드포인트 URL)** | **세부 정보** |
|--- | ---|
|*.portal.azure.com | Azure Portal로 이동합니다.
|*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> | Azure Active Directory에서 액세스 제어와 ID 관리에 사용됩니다.
|management.azure.com | Azure Resource Manager 배포 트리거용
|*.services.visualstudio.com(선택 사항) | 내부 모니터링에 사용되는 어플라이언스 로그를 업로드합니다.
|aka.ms/*(선택 사항) | aka 링크에 대한 액세스를 허용합니다. 어플라이언스 서비스의 최신 업데이트를 다운로드 및 설치하는 데 사용됩니다.
|download.microsoft.com/download | Microsoft 다운로드 센터에서 다운로드할 수 있습니다.    

- 명령줄을 열고 다음 nslookup 명령을 실행하여 DNS 설정 파일에 나열된 URL에 대한 privatelink 연결을 확인합니다. DNS 설정 파일의 모든 URL에 대해 이 단계를 반복합니다.

    _**그림**: 검색 서비스 엔드포인트에 대한 프라이빗 링크 연결 확인_

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
    ```
    요청이 프라이빗 엔드포인트를 통해 검색 서비스 엔드포인트에 도달할 수 있는 경우 다음과 같은 결과가 표시됩니다.

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.23 (private IP address)
    Aliases:  04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
              prod.cus.discoverysrv.windowsazure.com
    ```

    nslookup 명령은 위에서 설명한 대로 개인 IP 주소로 확인되어야 합니다. 개인 IP 주소는 DNS 설정 파일에 나열된 주소와 일치해야 합니다.

DNS 확인이 잘못된 경우 다음 단계를 수행합니다.
1. DNS 매핑 및 연결된 개인 IP 주소를 사용하여 온-프레미스 어플라이언스의 DNS 호스트 파일을 편집하여 원본 환경 DNS 레코드를 수동으로 업데이트합니다. 이 옵션은 테스트용으로 권장됩니다.

   ![DNS 호스트 파일](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

2. 사용자 지정 DNS 서버를 사용하는 경우 사용자 지정 DNS 설정을 검토하고 DNS 구성이 올바른지 유효성을 검사합니다. 지침은 [프라이빗 엔드포인트 개요: DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)을 참조하세요.
3. 문제가 계속되면 추가 문제 해결을 위해 [이 섹션을 참조하세요](#validate-the-private-dns-zone).

연결을 확인한 후 검색 프로세스를 다시 시도합니다.

### <a name="importexport-request-fails-with-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>가져오기/내보내기 요청이 "403: 이 요청에는 이 작업을 수행할 권한이 없습니다." 오류를 나타내며 실패함 

프라이빗 엔드포인트 연결이 있는 프로젝트에 대한 내보내기/가져오기/다운로드 보고서 요청이 *"403: 이 요청에는 이 작업을 수행할 권한이 없습니다."* 오류를 나타내며 실패합니다.

#### <a name="possible-causes"></a>가능한 원인: 
이 오류는 내보내기/가져오기/다운로드 요청이 인증된 네트워크에서 시작되지 않은 경우에 발생할 수 있습니다. 이 문제는 프라이빗 네트워크를 통해 Azure Migrate 서비스(Azure 가상 네트워크)에 연결되지 않은 클라이언트에서 가져오기/내보내기/다운로드 요청이 시작된 경우에 발생할 수 있습니다. 

#### <a name="remediation"></a>수정
**옵션 1** *(권장)* :
  
이 오류를 해결하려면 프라이빗 링크를 통해 Azure에 연결된 가상 네트워크에 있는 클라이언트에서 가져오기/내보내기/다운로드 작업을 다시 시도합니다. 온-프레미스 네트워크 또는 어플라이언스 VM에서 Azure Portal을 열고 작업을 다시 시도할 수 있습니다. 

**옵션 2**:

가져오기/내보내기/다운로드 요청은 보고서를 업로드/다운로드하기 위해 스토리지 계정에 연결합니다. 가져오기/내보내기/다운로드 작업에 사용되는 스토리지 계정의 네트워킹 설정을 변경하고 다른 네트워크(퍼블릭 네트워크)를 통해 스토리지 계정에 대한 액세스를 허용할 수도 있습니다.  

퍼블릭 엔드포인트 연결에 대한 스토리지 계정을 설정하려면

1. **스토리지 계정 찾기**: 스토리지 계정 이름은 Azure Migrate: 검색 및 평가 속성 페이지에서 사용할 수 있습니다. 스토리지 계정 이름에는 *usa* 접미사가 있습니다. 

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png" alt-text="DNS 설정 다운로드 스냅샷"::: 

2. 스토리지 계정으로 이동하고 스토리지 계정 네트워킹 속성을 편집하여 모든/다른 네트워크의 액세스를 허용합니다. 

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall-virtual-networks.png" alt-text="스토리지 계정 네트워킹 속성의 스냅샷":::

3. 또는 선택한 네트워크로 액세스를 제한하고 Azure Portal에 액세스하려는 클라이언트의 공용 IP 주소를 추가할 수 있습니다.  

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall.png" alt-text="클라이언트의 공용 IP 주소를 추가하는 스크린샷":::

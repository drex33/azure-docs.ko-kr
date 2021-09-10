---
title: Enterprise Security Package 클러스터 생성 및 구성 -Azure
description: Azure HDInsight에서 Enterprise Security Package 클러스터를 만들고 구성하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ea27e16dc08ccaf475ccc33f08c575ff45f8124
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110692463"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Azure HDInsight에서 Enterprise Security Package 클러스터 생성 및 구성

Azure HDInsight용 ESP(Enterprise Security Package)를 사용하면 Azure에서 Apache Hadoop 클러스터에 대한 Active Directory 기반 인증, 다중 사용자 지원, 역할 기반 액세스 제어에 액세스할 수 있습니다. HDInsight ESP 클러스터를 사용하면 엄격한 회사 보안 정책을 준수하는 조직에서 중요한 데이터를 안전하게 처리할 수 있습니다.

이 가이드에서는 ESP 사용 Azure HDInsight 클러스터를 만드는 방법을 보여 줍니다. 또한 Active Directory 및 DNS(Domain Name System)를 사용할 수 있는 Windows IaaS VM을 만드는 방법도 보여 줍니다. 이 가이드를 사용하여 온-프레미스 사용자가 ESP 사용 HDInsight 클러스터에 로그인하도록 하는 데 필요한 리소스를 구성할 수 있습니다.

만든 서버는 ‘실제’ 온-프레미스 환경을 대체하는 역할을 합니다. 만든 서버는 설정 및 구성 단계에 사용합니다. 나중에 사용자 환경에서 이 단계를 반복합니다.

이 가이드는 Azure AD(Azure Active Directory)와 암호 해시 동기화를 사용하여 하이브리드 ID 환경을 만드는 데도 도움이 되며, [HDInsight에서 ESP 사용](apache-domain-joined-architecture.md)을 보완합니다.

사용자 환경에서 이 프로세스를 사용하기 전에 다음을 수행합니다.

* Active Directory와 DNS를 설정합니다.
* Azure AD를 사용합니다.
* 온-프레미스 사용자 계정을 Azure AD에 동기화합니다.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png" alt-text="Azure AD 아키텍처 다이어그램" border="false":::

## <a name="create-an-on-premises-environment"></a>온-프레미스 환경 만들기

이 섹션에서는 Azure 빠른 시작 배포 템플릿을 사용하여 새 VM을 만들고, DNS를 구성하고, 새 Active Directory 포리스트를 추가합니다.

1. [새 Active Directory 포리스트와 함께 Azure VM을 만들려면](https://azure.microsoft.com/resources/templates/active-directory-new-domain/) 빠른 시작 배포 템플릿으로 이동합니다.

1. **Azure에 배포** 를 선택합니다.
1. Azure 구독에 로그인합니다.
1. **새 AD 포리스트와 함께 Azure VM 만들기** 페이지에서 다음 정보를 제공합니다.

    |속성 | 값 |
    |---|---|
    |Subscription|리소스를 배포할 구독을 선택합니다.|
    |Resource group|**새로 만들기** 를 선택하고 이름 `OnPremADVRG`를 입력합니다.|
    |Location|위치를 선택합니다.|
    |관리자 사용자 이름|`HDIFabrikamAdmin`|
    |관리자 암호|암호를 입력합니다.|
    |도메인 이름|`HDIFabrikam.com`|
    |DNS 접두사|`hdifabrikam`|

    나머지 기본값을 그대로 둡니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png" alt-text="새 Azure AD 포리스트와 함께 Azure VM을 만들기 위한 템플릿" border="true":::

1. **사용 약관** 을 검토한 다음 **위에 명시된 사용 약관에 동의함** 을 선택합니다.
1. **구매** 를 선택하고 배포를 모니터링한 후 완료될 때까지 기다립니다. 배포를 완료하는 데 약 30분이 걸립니다.

## <a name="configure-users-and-groups-for-cluster-access"></a>클러스터 액세스를 위한 사용자 및 그룹 구성

이 섹션에서는 이 가이드의 끝날 때쯤에 HDInsight 클러스터에 액세스할 수 있는 사용자를 만듭니다.

1. 원격 데스크톱을 사용하여 도메인 컨트롤러에 연결합니다.
    1. Azure Portal에서 **리소스 그룹** > **OnPremADVRG** > **adVM** > **연결** 로 이동합니다.
    1. **IP 주소** 드롭다운 목록에서 공용 IP 주소를 선택합니다.
    1. **RDP 파일 다운로드** 를 선택한 다음 파일을 엽니다.
    1. `HDIFabrikam\HDIFabrikamAdmin`을 사용자 이름으로 사용합니다.
    1. 관리자 계정에 대해 선택한 암호를 입력합니다.
    1. **확인** 을 선택합니다.

1. 도메인 컨트롤러 **서버 관리자** 대시보드에서 **도구** > **Active Directory 사용자 및 컴퓨터** 로 이동합니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png" alt-text="서버 관리자 대시보드에서 Active Directory 관리 열기" border="true":::

1. **HDIAdmin** 과 **HDIUser** 라는 두 개의 새 사용자를 만듭니다. 이 두 사용자로 HDInsight 클러스터에 로그인할 것입니다.

    1. **Active Directory 사용자 및 컴퓨터** 페이지에서 `HDIFabrikam.com`을 마우스 오른쪽 단추로 클릭한 다음 **새로 만들기** > **사용자** 로 이동합니다.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png" alt-text="새 Active Directory 사용자 만들기" border="true":::

    1. **새 개체 - 사용자** 페이지에서 **이름** 과 **사용자 로그온 이름** 에 `HDIUser`를 입력합니다. 다른 필드는 자동으로 채워집니다. **다음** 을 선택합니다.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png" alt-text="첫 번째 관리 사용자 개체 만들기" border="true":::

    1. 표시되는 팝업 창에서 새 계정의 암호를 입력합니다. **암호 사용 기간 제한 없음** 을 선택하고 팝업 메시지에서 **확인** 을 선택합니다.
    1. **다음** 을 선택하고 **마침** 을 선택하여 새 계정을 만듭니다.
    1. 위의 단계를 반복하여 사용자 `HDIAdmin`을 만듭니다.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png" alt-text="두 번째 관리 사용자 개체 만들기" border="true":::

1. 글로벌 보안 그룹을 만듭니다.

    1. **Active Directory 사용자 및 컴퓨터** 에서 `HDIFabrikam.com`을 마우스 오른쪽 단추로 클릭한 다음 **새로 만들기** > **그룹** 으로 이동합니다.

    1. **그룹 이름** 텍스트 상자에 `HDIUserGroup`을 입력합니다.

    1. **확인** 을 선택합니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png" alt-text="새 Active Directory 그룹 만들기" border="true":::

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png" alt-text="새 개체 만들기" border="true":::

1. **HDIUserGroup** 에 구성원을 추가합니다.

    1. **HDIUser** 를 마우스 오른쪽 단추로 클릭하고 **그룹에 추가...** 를 선택합니다.
    1. **선택할 개체 이름 입력** 텍스트 상자에 `HDIUserGroup`을 입력합니다. 그런 다음 **확인** 을 선택하고 팝업 항목에서 **확인** 을 다시 선택합니다.
    1. **HDIAdmin** 계정에 대해 이전 단계를 반복합니다.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png" alt-text="구성원 HDIUser를 HDIUserGroup 그룹에 추가하기" border="true":::

이제 Active Directory 환경을 만들었습니다. HDInsight 클러스터에 액세스할 수 있는 두 명의 사용자와 사용자 그룹을 추가했습니다.

사용자는 Azure AD와 동기화됩니다.

### <a name="create-an-azure-ad-directory"></a>Azure AD 디렉터리 만들기

1. Azure Portal에 로그인합니다.
1. **리소스 만들기** 를 선택하고 `directory`를 입력합니다. **Azure Active Directory** > **만들기** 를 선택합니다.
1. **조직 이름** 에 `HDIFabrikam`을 입력합니다.
1. **초기 도메인 이름** 에 `HDIFabrikamoutlook`을 입력합니다.
1. **만들기** 를 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png" alt-text="Azure AD 디렉터리 만들기" border="true":::

### <a name="create-a-custom-domain"></a>사용자 지정 도메인 만들기

1. 새 **Azure Active Directory** 의 **관리** 에서 **사용자 지정 도메인 이름** 을 선택합니다.
1. **+사용자 지정 도메인 추가** 를 선택합니다.
1. **사용자 지정 도메인 이름** 에서 `HDIFabrikam.com`을 입력한 후 **도메인 추가** 를 선택합니다.
1. 그런 다음 [DNS 정보를 도메인 등록 기관에 추가](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)를 완료합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png" alt-text="사용자 지정 도메인 만들기" border="true":::

### <a name="create-a-group"></a>그룹 만들기

1. 새 **Azure Active Directory** 의 **관리** 에서 **그룹** 을 선택합니다.
1. **+ 새 그룹** 을 선택합니다.
1. **그룹 이름** 텍스트 상자에 `AAD DC Administrators`를 입력합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-your-azure-ad-tenant"></a>Azure AD 테넌트 구성

이제 온-프레미스 Active Directory 인스턴스에서 클라우드로 사용자 및 그룹을 동기화할 수 있도록 Azure AD 테넌트를 구성해보겠습니다.

Active Directory 테넌트 관리자를 만듭니다.

1. Azure Portal에 로그인하고 Azure AD 테넌트 **HDIFabrikam** 을 선택합니다.

1. **관리** > **사용자** > **새 사용자** 로 이동합니다.

1. 새 사용자에 다음 세부 정보를 입력합니다.

   **ID**

   |속성 |Description |
   |---|---|
   |사용자 이름|텍스트 상자에 `fabrikamazureadmin`를 입력합니다. 도메인 이름 드롭다운 목록에서 `hdifabrikam.com`을 선택합니다.|
   |속성| `fabrikamazureadmin`를 입력합니다.|

   **암호**
   1. **암호 직접 만들기** 를 선택합니다.
   1. 원하는 보안 암호를 입력합니다.

   **그룹 및 역할**
   1. **0개 그룹 선택됨** 을 선택합니다.
   1. **AAD DC 관리자** 를 선택한 다음, **선택** 을 선택합니다.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png" alt-text="Azure AD 그룹 대화 상자" border="true":::

   1. **사용자** 를 선택합니다.
   1. **전역 관리자** 를 선택한 다음, **선택** 을 선택합니다.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png" alt-text="Azure AD 역할 대화 상자" border="true":::

1. **만들기** 를 선택합니다.

1. 그런 다음 새 사용자가 Azure Portal 로그인하면 암호를 변경하라는 메시지가 표시됩니다. Microsoft Azure Active Directory Connect를 구성하기 전에 이 작업을 수행해야 합니다.

## <a name="sync-on-premises-users-to-azure-ad"></a>Azure AD에 온-프레미스 사용자 동기화

### <a name="configure-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory Connect 구성

1. 도메인 컨트롤러에서 [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)를 다운로드합니다.

1. 다운로드한 실행 파일을 열고 사용 조건에 동의합니다. **계속** 을 선택합니다.

1. **기본 설정 사용** 을 선택합니다.

1. **Azure AD에 연결** 페이지에서 Azure AD에 대한 전역 관리자의 사용자 이름과 암호를 입력합니다. Active Directory 테넌트를 구성할 때 만든 사용자 이름 `fabrikamazureadmin@hdifabrikam.com`을 사용합니다. **다음** 을 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png" alt-text="Azure AD에 연결" border="true":::

1. **Active Directory Domain Services에 연결** 페이지에서 엔터프라이즈 관리자 계정의 사용자 이름과 암호를 입력합니다. 이전에 만든 사용자 이름 `HDIFabrikam\HDIFabrikamAdmin`과 암호를 사용합니다. **다음** 을 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png" alt-text="AD DS에 연결 페이지" border="true":::

1. **Azure AD 로그인 구성** 페이지에서 **다음** 을 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png" alt-text="Azure AD 로그인 구성 페이지" border="true":::

1. **구성 준비 완료** 페이지에서 **설치** 를 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png" alt-text="구성 준비 완료 페이지" border="true":::

1. **구성 완료** 페이지에서 **종료** 를 선택합니다.
   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png" alt-text="구성 완료 페이지" border="true":::

1. 동기화가 완료되면 IaaS 디렉터리에서 만든 사용자가 Azure AD와 동기화되었는지 확인합니다.
   1. Azure Portal에 로그인합니다.
   1. **Azure Active Directory** > **HDIFabrikam** > **사용자** 를 선택합니다.

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

Azure AD DS(Azure AD Domain Services)를 구성하는 데 사용할 수 있는 사용자가 할당한 관리 ID를 만듭니다. 자세한 내용은 [Azure Portal을 사용하여 사용자가 할당한 관리 ID에 역할 만들기, 나열, 삭제 또는 할당하기](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 참조하세요.

1. Azure Portal에 로그인합니다.
1. **리소스 만들기** 를 선택하고 `managed identity`를 입력합니다. **사용자가 할당한 관리 ID** > **만들기** 를 선택합니다.
1. **리소스 이름** 에 `HDIFabrikamManagedIdentity`를 입력합니다.
1. 구독을 선택합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 `HDIFabrikam-CentralUS`를 입력합니다.
1. **위치** 에서 **미국 중부** 를 선택합니다.
1. **만들기** 를 선택합니다.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png" alt-text="새 사용자가 할당한 관리 ID 만들기" border="true":::

### <a name="enable-azure-ad-ds"></a>Azure AD DS 사용

다음 단계에 따라 Azure AD DS를 사용합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure AD DS 활성화](../../active-directory-domain-services/tutorial-create-instance.md)를 참조하세요.

1. Azure AD DS를 호스트할 가상 네트워크를 만듭니다. 다음 PowerShell 코드를 실행합니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure Portal에 로그인합니다.
1. **리소스 만들기** 를 선택하고 `Domain services`를 입력하고, **Azure AD Domain Services** > **만들기** 를 선택합니다.
1. **기본 사항** 페이지에서 다음을 수행합니다.
   1. **디렉터리 이름** 에 앞서 만든 Azure AD 디렉터리 **HDIFabrikam** 을 선택합니다.
   1. **DNS 도메인 이름** 에 *HDIFabrikam.com* 을 입력합니다.
   1. 구독을 선택합니다.
   1. 리소스 그룹 **HDIFabrikam-CentralUS** 를 지정합니다. **위치** 에 **미국 중부** 를 선택합니다.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png" alt-text="Azure AD DS 기본 세부 정보" border="true":::

1. **네트워크** 페이지에서 PowerShell 스크립트를 사용하여 만든 네트워크(**HDIFabrikam-VNET**)와 서브넷(**AADDS-subnet**)을 선택합니다. 또는 **새로 만들기** 를 선택하여 지금 가상 네트워크를 만듭니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png" alt-text="가상 네트워크 만들기 단계" border="true":::

1. **관리자 그룹** 페이지에서 이 그룹을 관리하기 위해 **AAD DC Administrators** 라는 그룹이 이미 생성되었다는 알림이 표시됩니다. 원하는 경우 이 그룹의 멤버 자격을 수정할 수 있지만, 이 경우에는 변경할 필요가 없습니다. **확인** 을 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png" alt-text="Azure AD 관리자 그룹 보기" border="true":::

1. **동기화** 페이지에서 **모두** > **확인** 을 선택하여 전체 동기화를 사용합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png" alt-text="Azure AD DS 동기화 활성화" border="true":::

1. **요약** 페이지에서 Azure AD DS에 대한 세부 정보를 확인하고 **확인** 을 선택합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png" alt-text="Azure AD Domain Services 활성화" border="true":::

Azure AD DS를 사용하면 로컬 DNS 서버가 Azure AD VM에서 실행됩니다.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크 구성

사용자 지정 DNS 서버를 사용하도록 Azure AD DS 가상 네트워크(**HDIFabrikam-AADDSVNET**)를 구성하려면 다음 단계를 사용합니다.

1. 사용자 지정 DNS 서버의 IP 주소를 찾습니다.
   1. Azure AD DS 리소스 `HDIFabrikam.com`을 선택합니다.
   1. **관리** 에서 **속성** 을 선택합니다.
   1. **가상 네트워크의 IP 주소** 에서 IP 주소를 찾습니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png" alt-text="Azure AD DS에 대한 사용자 지정 DNS IP 주소 찾기" border="true":::

1. 사용자 지정 IP 주소 10.0.0.4와 10.0.0.5를 사용하도록 **HDIFabrikam-AADDSVNET** 을 구성합니다.

   1. **설정** 에서 **DNS 서버** 를 선택합니다.
   1. **사용자 지정** 을 선택합니다.
   1. 텍스트 상자에 첫 번째 IP 주소(*10.0.0.4*)를 입력합니다.
   1. **저장** 을 선택합니다.
   1. 단계를 반복하여 다른 IP 주소(*10.0.0.5*)를 추가합니다.

이 시나리오에서는 Azure AD DS 가상 네트워크에서 동일한 IP 주소를 설정하여 IP 주소 10.0.0.4와 10.0.0.5를 사용하도록 Azure AD DS를 구성했습니다.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png" alt-text="사용자 지정 DNS 서버 페이지" border="true":::

## <a name="securing-ldap-traffic"></a>LDAP 트래픽 보안

LDAP(Lightweight Directory Access Protocol)는 Azure Active Directory에서 읽고 쓰는 데 사용됩니다. SSL(Secure Sockets Layer) 또는 TLS(전송 계층 보안) 기술을 사용하여 LDAP 트래픽을 기밀로 만들고 안전하게 보호할 수 있습니다. 올바른 형식의 인증서를 설치하여 LDAPS(SSL을 통한 LDAP)를 사용할 수 있습니다.

보안 LDAP에 대한 자세한 내용은 [Azure AD DS 관리되는 도메인에 대한 LDAPS 구성](../../active-directory-domain-services/tutorial-configure-ldaps.md)을 참조하세요.

이 섹션에서는 자체 서명된 인증서를 만들고, 인증서를 다운로드하고, **HDIFabrikam** Azure AD DS 관리되는 도메인에 대해 LDAPS를 구성합니다.

다음 스크립트는 **HDIFabrikam** 에 대한 인증서를 만듭니다. 인증서는 *LocalMachine* 경로에 저장됩니다.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> 유효한 PKCS(퍼블릭 키 암호화 표준) \#10 요청을 만드는 모든 유틸리티 또는 애플리케이션을 사용하여 TLS/SSL 인증서 요청을 구성할 수 있습니다.

인증서가 컴퓨터의 **개인** 저장소에 설치되어 있는지 확인합니다.

1. MMC(Microsoft Management Console)를 시작합니다.
1. 로컬 컴퓨터에서 인증서를 관리하는 **인증서** 스냅인을 추가합니다.
1. **인증서(로컬 컴퓨터)**  > **개인** > **인증서** 를 확장합니다. 새 인증서가 **개인** 저장소에 있어야 합니다. 이 인증서는 정규화된 호스트 이름으로 발급됩니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png" alt-text="로컬 인증서 만들기 확인" border="true":::

1. 오른쪽 창에서, 앞서 만든 인증서를 마우스 오른쪽 단추로 클릭합니다. **모든 작업** 을 가리킨 다음 **내보내기** 를 선택합니다.

1. **프라이빗 키 내보내기** 페이지에서 **예, 프라이빗 키를 내보냅니다** 를 선택합니다. 키를 가져올 컴퓨터에서 암호화된 메시지를 읽으려면 프라이빗 키가 필요합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png" alt-text="인증서 내보내기 마법사의 프라이빗 키 내보내기 페이지" border="true":::

1. **내보내기 파일 형식** 페이지에서 기본 설정을 그대로 두고 **다음** 을 선택합니다.
1. **암호** 페이지에서 프라이빗 키의 암호를 입력합니다. **암호화** 의 경우 **TripleDES-SHA1** 을 선택합니다. **다음** 을 선택합니다.
1. **내보낼 파일** 페이지에서 내보낸 인증서 파일의 경로와 이름을 입력하고 **다음** 을 선택합니다. 파일 이름에는 .pfx 확장명이 있어야 합니다. 이 파일은 Azure Portal에서 구성되어 보안 연결을 설정합니다.
1. Azure AD DS 관리되는 도메인에 대해 LDAPS를 사용합니다.
   1. Azure Portal에서 도메인 `HDIFabrikam.com`을 선택합니다.
   1. **관리** 에서 **보안 LDAP** 를 선택합니다.
   1. **보안 LDAP** 페이지의 **보안 LDAP** 에서 **사용** 을 선택합니다.
   1. 컴퓨터에서 내보낸 .pfx 인증서 파일을 찾습니다.
   1. 인증서 암호를 입력합니다.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png" alt-text="보안 LDAP 활성화" border="true":::

1. 이제 LDAPS를 사용했으므로 포트 636을 사용하여 연결할 수 있는지 확인합니다.
   1. **HDIFabrikam-CentralUS** 리소스 그룹에서 네트워크 보안 그룹 **AADDS-HDIFabrikam.com-NSG** 를 선택합니다.
   1. **설정** 에서 **인바운드 보안 규칙** > **추가** 를 선택합니다.
   1. **인바운드 보안 규칙 추가** 페이지에서 다음 속성을 입력하고 **추가** 를 선택합니다.

      | 속성 | 값 |
      |---|---|
      | 원본 | 모두 |
      | 원본 포트 범위 | * |
      | 대상 | 모두 |
      | 대상 포트 범위 | 636 |
      | 프로토콜 | 모두 |
      | 작업 | 허용 |
      | 우선 순위 | \<Desired number> |
      | Name | Port_LDAP_636 |

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png" alt-text="인바운드 보안 규칙 추가 대화 상자" border="true":::

**HDIFabrikamManagedIdentity** 는 사용자가 할당한 관리 ID입니다. HDInsight 도메인 서비스 기여자 역할은 이 ID가 도메인 서비스 작업을 읽고, 만들고, 수정하고, 삭제할 수 있게 하는 관리 ID에 대해 활성화됩니다.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png" alt-text="사용자 할당 관리 ID 만들기" border="true":::

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP 사용 HDInsight 클러스터 만들기

이 단계에는 다음 사전 요구 사항이 필요합니다.

1. **미국 서부** 위치에 새 리소스 그룹 *HDIFabrikam-WestUS* 를 만듭니다.
1. ESP 사용 HDInsight 클러스터를 호스트할 가상 네트워크를 만듭니다.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure AD DS(`HDIFabrikam-AADDSVNET`)를 호스트할 가상 네트워크와 ESP 사용 HDInsight 클러스터를 호스트할 가상 네트워크(`HDIFabrikam-HDIVNet`) 간에 피어 관계를 만듭니다. 다음 PowerShell 코드를 사용하여 두 가상 네트워크를 피어링합니다.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. **Hdigen2store** 라는 새 Azure Data Lake Storage Gen2 계정을 만듭니다. 사용자 관리 ID **HDIFabrikamManagedIdentity** 를 사용하여 계정을 구성합니다. 자세한 내용은 [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.

1. 가상 네트워크 **HDIFabrikam-AADDSVNET** 에서 사용자 지정 DNS를 설정합니다.
    1. Azure Portal > **리소스 그룹** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS 서버** 로 이동합니다.
    1. **사용자 지정** 을 선택하고 *10.0.0.4* 와 *10.0.0.5* 를 입력합니다.
    1. **저장** 을 선택합니다.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png" alt-text="가상 네트워크에 대한 사용자 지정 DNS 설정 저장" border="true":::

1. 새 ESP 사용 HDInsight Spark 클러스터를 만듭니다.
    1. **사용자 지정(크기, 설정, 앱)** 을 선택합니다.
    1. **기본 사항**(섹션 1)에 대한 세부 정보를 입력합니다. **클러스터 형식** 이 **Spark 2.3(HDI 3.6)** 인지 확인합니다. **리소스 그룹** 이 **HDIFabrikam-CentralUS** 인지 확인합니다.

    1. **보안 + 네트워킹**(섹션 2)에 대해 다음 세부 정보를 입력합니다.
        * **Enterprise Security Package** 에서 **사용** 을 선택합니다.
        * **클러스터 관리 사용자** 를 선택하고 온-프레미스 관리 사용자로 만든 **HDIAdmin** 계정을 선택합니다. **선택** 을 클릭합니다.
        * **클러스터 액세스 그룹** > **HDIUserGroup** 을 선택합니다. 향후 이 그룹에 추가하는 모든 사용자는 HDInsight 클러스터에 액세스할 수 있습니다.

            :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg" alt-text="클러스터 액세스 그룹 HDIUserGroup 선택" border="true":::

    1. 클러스터 구성의 다른 단계를 완료하고 **클러스터 요약** 에서 세부 정보를 확인합니다. **만들기** 를 선택합니다.

1. `https://CLUSTERNAME.azurehdinsight.net`에서 새로 만든 클러스터에 대한 Ambari UI에 로그인합니다. 관리 사용자 이름 `hdiadmin@hdifabrikam.com`과 암호를 사용합니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg" alt-text="Apache Ambari UI 로그인 창" border="true":::

1. 클러스터 대시보드에서 **역할** 을 선택합니다.
1. **역할** 페이지의 **역할 할당** 에서 **클러스터 관리자** 역할 옆에 그룹 *hdiusergroup* 을 입력합니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg" alt-text="에 클러스터 관리자 역할 할당" border="true":::

1. SSH(Secure Shell) 클라이언트를 열고 클러스터에 로그인합니다. 온-프레미스 Active Directory 인스턴스에서 만든 **hdiuser** 를 사용합니다.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg" alt-text="SSH 클라이언트를 사용하여 클러스터에 로그인" border="true":::

이 계정으로 로그인할 수 있는 경우 온-프레미스 Active Directory 인스턴스와 동기화하도록 ESP 클러스터를 올바르게 구성한 것입니다.

## <a name="next-steps"></a>다음 단계

[ESP를 사용하는 Apache Hadoop 보안 소개](hdinsight-security-overview.md)를 읽어보세요.

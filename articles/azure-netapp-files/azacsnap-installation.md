---
title: Azure NetApp Files를 위한 Azure 애플리케이션 일치 스냅샷 도구 설치 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구 설치에 대한 안내를 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/08/2021
ms.author: phjensen
ms.openlocfilehash: 5e2d6ab8ef0f641833771bed225a7415c5267e55
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863901"
---
# <a name="install-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅샷 도구 설치

이 문서에서는 Azure NetApp Files 또는 Azure 대규모 인스턴스에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구를 설치하는 방법에 대한 지침을 제공합니다.

> [!IMPORTANT]
> 분산 설치는 개인 네트워크에 배포되는 **Azure 대규모 인스턴스** 시스템에 사용 가능한 유일한 옵션입니다.  따라서 연결을 보장하려면 각 시스템에서 AzAcSnap 설치를 수행해야 합니다.

## <a name="introduction"></a>소개

다운로드할 수 있는 자체 설치 프로그램은 스냅샷 도구를 루트가 아닌 사용자 권한(예: azacsnap)으로 쉽게 설정하고 실행할 수 있도록 설계되었습니다. 설치 프로그램이 사용자를 설정하고 스냅샷 도구를 사용자의 `$HOME/bin`하위 디렉터리(기본값 = `/home/azacsnap/bin`)에 넣습니다.
자체 설치 프로그램은 설치를 수행하는 사용자의 구성(예: 루트)을 기반으로 하여 모든 파일에 대한 올바른 설정 및 경로를 결정하려고 합니다. 필수 구성 요소 단계(스토리지 및 SAP HANA와의 통신 사용)를 루트로 실행 중인 경우 설치 과정을 통해 백업 사용자의 위치에 프라이빗 키와 `hdbuserstore`가 복사됩니다. 하지만 스토리지 백 엔드 및 SAP HANA와의 통신을 사용하도록 설정하는 단계는 설치 후 전문 지식이 있는 관리자가 수동으로 수행할 수 있습니다.

## <a name="prerequisites-for-installation"></a>설치를 위한 사전 요구 사항

지침에 따라 스냅샷 및 재해 복구 명령을 설정하고 실행합니다. 스냅샷 도구를 설치하고 사용하기 전에 다음 단계를 루트로 완료하는 것이 좋습니다.

1. **OS 패치됨**: [Azure의 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법](../virtual-machines/workloads/sap/hana-installation.md#operating-system)에서 패치 및 SMT 설치에 대해 참조하세요.
1. **시간 동기화가 설정됩니다**. 고객은 NTP 호환 시간 서버를 제공하고 이에 따라 OS를 구성해야 합니다.
1. **HANA 설치됨** : [HANA 데이터베이스에 SAP NetWeaver 설치](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database)에서 HANA 설치 지침을 참조하세요.
1. **[스토리지와의 통신 사용(자세한](#enable-communication-with-storage)** 내용은 별도 섹션 참조): 배포에 사용 중인 스토리지 백 엔드를 선택합니다.

   # <a name="azure-netapp-files"></a>[Azure NetApp Files](#tab/azure-netapp-files)
    
   1. **Azure NetApp Files(자세한 내용은 별도의 섹션 참조)** : 고객은 서비스 주체 인증 파일을 생성해야 합니다.
      
      > [!IMPORTANT]
      > Azure NetApp Files와의 통신 유효성을 검사하는 경우 통신이 실패하거나 시간이 초과될 수 있습니다. 방화벽 규칙이 AzAcSnap을 실행하는 시스템에서 다음 주소 및 TCP/IP 포트로의 아웃바운드 트래픽을 차단하지 않는지 확인합니다.
      > - (https://)management.azure.com:443
      > - (https://)login.microsoftonline.com:443
      
   # <a name="azure-large-instance-bare-metal"></a>[Azure Large Instance(운영 체제 미설치)](#tab/azure-large-instance)
      
   1. **Azure 대규모 인스턴스(자세한 내용은 별도의 섹션 참조)** : 고객은 프라이빗/공개 키 쌍으로 SSH를 설정하고 스토리지 백 엔드에서 설정을 위해 실행할 예정인 스냅샷 도구가 있는 각 노드의 공개 키를 Microsoft Operations에 제공해야 합니다.

      SSH를 사용하여 노드 중 하나에 연결하여 이를 테스트합니다(예: `ssh -l <Storage UserName> <Storage IP Address>`).
      스토리지 프롬프트의 로그아웃에 `exit`를 입력합니다.

      Microsoft Operations는 프로비전 시 스토리지 사용자 및 스토리지 IP를 제공합니다.
      
      ---

1. **[스토리지와의 통신 사용(자세한](#enable-communication-with-storage)** 내용은 별도 섹션 참조): 배포에 사용 중인 스토리지 백 엔드를 선택합니다.

1. **[데이터베이스와의 통신 사용(자세한](#enable-communication-with-database)** 내용은 별도 섹션 참조): 
   
   # <a name="sap-hana"></a>[SAP HANA](#tab/sap-hana)
   
   고객은 스냅샷을 수행하는 데 필요한 권한으로 적절한 SAP HANA 사용자를 설정해야 합니다.

   1. 이 설정은 `grey` 텍스트를 사용하여 다음과 같이 명령줄에서 테스트할 수 있습니다.
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - 위의 예제는 SAP HANA에 대한 비SSL 통신 예제입니다.
      
   ---


## <a name="enable-communication-with-storage"></a>스토리지와의 통신 사용

이 섹션에서는 스토리지와의 통신을 사용하는 방법에 대해 설명합니다. 사용 중인 스토리지 백 엔드가 올바르게 선택되었는지 확인합니다.

# <a name="azure-netapp-files-with-virtual-machine"></a>[Azure NetApp Files(Virtual Machine 포함)](#tab/azure-netapp-files)

RBAC 서비스 주체 만들기

1. Azure Cloud Shell 세션 내에서 기본적으로 서비스 주체와 연결하려는 구독에 로그온했는지 확인합니다.

    ```azurecli-interactive
    az account show
    ```

1. 구독이 올바르지 않으면 다음을 사용합니다.

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. 다음 예제에 따라 Azure CLI를 사용하여 서비스 주체를 만듭니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --role Contributor --sdk-auth
    ```

    1. 그러면 다음 예제와 유사한 출력이 생성됩니다.

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > 이 명령은 구독 수준에서 서비스 주체에 RBAC 참가자 역할을 자동으로 할당하므로 테스트에서 리소스를 만들 특정 리소스 그룹으로 범위를 좁힐 수 있습니다.

1. 출력 콘텐츠를 잘라내어 `azacsnap` 명령과 동일한 시스템에 저장된 `azureauth.json`이라는 파일에 붙여 넣고 적합한 시스템 권한으로 파일을 보호합니다.

# <a name="azure-large-instance-bare-metal"></a>[Azure Large Instance(운영 체제 미설치)](#tab/azure-large-instance)

스토리지 백 엔드와의 통신은 암호화된 SSH 채널을 통해 실행됩니다. 다음 예제 단계는 이 통신에 대해 SSH를 설정하는 방법에 대한 지침을 제공하는 것입니다.

1. `/etc/ssh/ssh_config` 파일 수정

    `MACs hmac-sha1` 줄이 추가된 다음 출력을 참조하세요.

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. 공개/프라이빗 키 쌍 만들기

    다음 예제 명령을 사용하여 키 쌍을 생성하는 경우 키를 생성할 때 암호를 입력하지 마세요.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Microsoft Operations에 공개 키 보내기

    스토리지 하위 시스템과의 통신을 위해 스냅샷 도구를 설정하려면 `cat /root/.ssh/id_rsa.pub` 명령의 출력(아래 예제)을 Microsoft Operations에 보냅니다.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

---

## <a name="enable-communication-with-database"></a>데이터베이스와의 통신 사용

이 섹션에서는 스토리지와의 통신을 사용하는 방법에 대해 설명합니다. 사용 중인 스토리지 백 엔드가 올바르게 선택되었는지 확인합니다.

# <a name="sap-hana"></a>[SAP HANA](#tab/sap-hana)

스냅샷 도구는 SAP HANA와 통신하며 데이터베이스 저장 지점을 시작 및 릴리스할 수 있는 적합한 권한이 있는 사용자가 필요합니다. 다음 예제에서는 SAP HANA 데이터베이스와의 통신을 위한 SAP HANA v2 사용자 및 `hdbuserstore` 설정을 보여줍니다.

다음 예제 명령은 SAP HANA 2의 SYSTEMDB에서 사용자(AZACSNAP)를 설정합니다.
필요에 따라 데이터베이스, IP 주소 변경, 사용자 및 암호:

1. SYSTEMDB에 연결하여 사용자 만들기

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. 사용자 만들기

    이 예제에서는 SYSTEMDB에 AZACSNAP 사용자를 만듭니다.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. 사용자 권한 부여

    이 예제에서는 AZACSNAP 사용자에 대한 권한을 설정하여 데이터베이스 일치 스토리지 스냅샷을 수행할 수 있도록 합니다.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *선택 사항* - 사용자 암호가 만료되지 않도록 방지

    > [!NOTE]
    > 이렇게 변경하려면 회사 정책을 확인하세요.

   이 예제에서는 AZACSNAP 사용자에 대한 암호 만료를 사용하지 않도록 설정합니다. 이 변경 없이 사용자의 암호는 스냅샷이 올바르게 수행되지 않도록 만료됩니다.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. SAP HANA 보안 사용자 저장소 설정(암호 변경)  이 예제에서는 Linux shell에서 `hdbuserstore` 명령을 사용하여 SAP HANA 보안 사용자 저장소를 설정합니다.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. SAP HANA 보안 사용자 저장소 확인 보안 사용자 저장소가 올바르게 설정되었는지 확인하려면 `hdbuserstore` 명령을 사용하여 다음 예제와 유사한 출력을 나열합니다. `hdbuserstore` 사용에 대한 자세한 내용은 SAP 웹 사이트에서 사용할 수 있습니다.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>SAP HANA와의 통신을 위한 SSL 사용

`azacsnap` 도구는 SAP HANA의 `hdbsql` 명령을 SAP HANA와의 통신에 활용합니다. 여기에는 SAP HANA와의 통신을 암호화할 때 SSL 옵션 사용이 포함됩니다. `azacsnap`은 다음과 같이 `hdbsql` 명령의 SSL 옵션을 사용합니다.

다음은 `azacsnap --ssl` 옵션을 사용할 때마다 항상 사용됩니다.

- `-e` - TLS 암호화 TLS/SSL 암호화를 사용하도록 설정합니다. 서버에서 사용할 수 있는 가장 높은 수준을 선택합니다.
- `-ssltrustcert` - 서버 인증서의 유효성을 검사할지 지정합니다.
- `-sslhostnameincert "*"` - 서버 ID를 확인하는 데 사용되는 호스트 이름을 지정합니다. 호스트 이름으로 `"*"`을 지정하면 서버 호스트 이름이 유효성 검사되지 않습니다.

SSL 통신에는 키 저장소 및 신뢰 저장소 파일도 필요합니다.  이러한 파일은 Linux 설치의 기본 위치에 저장될 수 있지만 다양한 SAP HANA 시스템(즉, 각 SAP HANA 시스템에 서로 다른 키 저장소 및 신뢰 저장소 파일이 사용되는 경우)에 올바른 키 자료가 사용되고 있는지 확인하기 위해 `azacsnap`은 키 저장소 및 신뢰 저장소 파일이 `azacsnap` 구성 파일에 지정된 `securityPath` 위치에 저장될 것으로 예상합니다.

#### <a name="key-store-files"></a>키 저장소 파일

- 동일한 키 자료로 여러 SID를 사용하는 경우 `azacsnap` 구성 파일에 정의된 대로 securityPath 위치에 링크 만들기가 더 쉽습니다.  다음 값이 SSL을 사용하는 모든 SID에 존재하는지 확인합니다.
  - openssl의 경우:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - commoncrypto의 경우:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- SID마다 다른 키 자료로 여러 SID를 사용하는 경우 SID `azacsnap` 구성 파일에 정의된 대로 securityPath 위치에 파일을 복사(또는 이동 및 이름 변경)합니다.
  - openssl의 경우:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - commoncrypto의 경우:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

`azacsnap`이 `hdbsql`을 호출하면 `-sslkeystore=<securityPath>/<SID>_keystore`가 명령줄에 추가됩니다.

#### <a name="trust-store-files"></a>신뢰 저장소 파일

- 동일한 키 자료로 여러 SID를 사용하는 경우 `azacsnap` 구성 파일에 정의된 대로 securityPath 위치에 하드 링크를 만듭니다.  다음 값이 SSL을 사용하는 모든 SID에 존재하는지 확인합니다.
  - openssl의 경우:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - commoncrypto의 경우:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- SID마다 다른 키 자료로 여러 SID를 사용하는 경우 SID `azacsnap` 구성 파일에 정의된 대로 securityPath 위치에 파일을 복사(또는 이동 및 이름 변경)합니다.
  - openssl의 경우:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - commoncrypto의 경우:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> 파일 이름의 `<SID>` 구성 요소는 모두 대문자로 되어 있는 SAP HANA 시스템 식별자여야 합니다(예: `H80`, `PR1` 등).

`azacsnap`이 `hdbsql`을 호출하면 `-ssltruststore=<securityPath>/<SID>_truststore`가 명령줄에 추가됩니다.

따라서 구성 파일에서 `SID`가 `H80`인 `azacsnap -c test --test hana --ssl openssl`을 실행하면 다음과 같이 `hdbsql`연결을 실행합니다.

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\` 문자는 명령줄에서 전달되는 여러 매개 변수의 명확성을 높이기 위한 명령줄 줄 바꿈입니다.

---

## <a name="installing-the-snapshot-tools"></a>스냅샷 도구 설치

다운로드할 수 있는 자체 설치 프로그램은 스냅샷 도구를 루트가 아닌 사용자 권한(예: azacsnap)으로 쉽게 설정하고 실행할 수 있도록 설계되었습니다. 설치 프로그램이 사용자를 설정하고 스냅샷 도구를 사용자의 `$HOME/bin`하위 디렉터리(기본값 = `/home/azacsnap/bin`)에 넣습니다.

자체 설치 프로그램은 설치를 수행하는 사용자의 구성(예: 루트)을 기반으로 하여 모든 파일에 대한 올바른 설정 및 경로를 결정하려고 합니다. 이전 설정 단계(스토리지 및 SAP HANA와의 통신 사용)를 루트로 실행한 경우 설치 과정을 통해 백업 사용자의 위치에 프라이빗 키와 `hdbuserstore`가 복사됩니다. 하지만 스토리지 백 엔드 및 SAP HANA와의 통신을 사용하도록 설정하는 단계는 설치 후 전문 지식이 있는 관리자가 수동으로 수행할 수 있습니다.

> [!NOTE]
> Azure 대량 인스턴스 설치에서 이전 SAP HANA의 경우 사전 설치된 스냅샷 도구의 디렉터리는 `/hana/shared/<SID>/exe/linuxx86_64/hdb`였습니다.

[필수 구성 요소 단계](#prerequisites-for-installation)가 완료되면 이제 다음과 같이 자체 설치 프로그램을 사용하여 스냅샷 도구를 설치할 수 있습니다.

1. 다운로드한 자체 설치 프로그램을 대상 시스템에 복사합니다.
1. 자체 설치 프로그램을 `root` 사용자로 실행합니다. 다음 예제를 참조하세요. 필요한 경우 `chmod +x *.run` 명령을 사용하여 파일 실행 파일을 만듭니다.

인수 없이 자체 설치 프로그램 명령을 실행하면 다음과 같이 스냅샷 도구 설치에 설치 프로그램 사용하기에 대한 도움말이 표시됩니다.

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> 자체 설치 프로그램에는 사용자 만들기 및 설정을 수행하지 않고 번들에서 스냅샷 도구를 추출(-X)하는 옵션이 있습니다. 이를 통해 숙련된 관리자가 설정 단계를 수동으로 완료하거나 기존 설치를 업그레이드하는 명령을 복사할 수 있습니다.

### <a name="easy-installation-of-snapshot-tools-default"></a>간편한 스냅샷 도구 설치(기본값)

설치 프로그램은 Azure에서 SAP HANA에 대한 스냅샷 도구를 신속하게 설치할 수 있도록 설계되었습니다. 기본적으로 설치 프로그램이 -I 옵션만 사용하여 실행되는 경우 다음 단계를 수행합니다.

1. 스냅샷 사용자 'azacsnap', 홈 디렉터리를 만들고 그룹 멤버 자격을 설정합니다.
1. azacsnap 사용자의 로그인 `~/.profile`을 구성합니다.
1. azacsnap의 `$PATH`에 추가할 디렉터리에 대한 파일 시스템을 검색합니다. 이는 일반적으로 `hdbsql` 및 `hdbuserstore`와 같은 SAP HANA 도구에 대한 경로입니다.
1. azacsnap의 `$LD_LIBRARY_PATH`에 추가할 디렉터리에 대한 파일 시스템을 검색합니다. 많은 명령의 경우 올바르게 실행하기 위해 라이브러리 경로를 설정해야 합니다. 이렇게 하면 설치된 사용자에 맞게 구성됩니다.
1. "루트" 사용자(설치를 실행하는 사용자)에서 azacsnap의 백 엔드 스토리지에 대한 SSH 키를 복사합니다. 여기서는 "루트" 사용자가 스토리지에 대한 연결을 이미 구성했다고 가정합니다.
    - "[스토리지와의 통신 사용](#enable-communication-with-storage)" 섹션을 참조하세요.
1. 대상 사용자 azacsnap에 대한 SAP HANA 연결 보안 사용자 저장소를 복사합니다. 여기서는 "루트" 사용자가 보안 사용자 저장소를 이미 구성했다고 가정합니다. "SAP HANA와의 통신 사용" 섹션을 참조하세요.
1. 스냅샷 도구는 `/home/azacsnap/bin/`으로 추출됩니다.
1. `/home/azacsnap/bin/`의 명령에는 해당 권한 집합(소유권 및 실행 파일 비트 등)이 있습니다.

다음 예제에서는 기본 설치 옵션을 사용하여 실행하는 경우 설치 프로그램의 올바른 출력을 보여 줍니다.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>스냅샷 도구 제거

기본 설정을 사용하여 스냅샷 도구가 설치된 경우에는 명령이 설치된 사용자만 제거하면 됩니다(기본값 = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>스냅샷 도구 수동 설치

도구를 수동으로 설치해야 하는 경우도 있지만 이 프로세스를 용이하게 하려면 설치 프로그램의 기본 옵션을 사용하는 것이 좋습니다.

`#` 문자로 시작하는 각 줄은 문자 다음에 나오는 예제 명령을 루트 사용자가 실행하는 방법을 보여 줍니다. 줄 맨 끝에 있는 `\`은 shell 명령의 표준 줄 연속 문자입니다.

루트 수퍼 사용자는 다음과 같이 수동 설치를 수행할 수 있습니다.

1. "sapsys" 그룹 ID를 가져옵니다. 이 경우 그룹 ID는 1010입니다.

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. 1단계의 그룹 ID를 사용하여 스냅샷 사용자 'azacsnap', 홈 디렉터리를 만들고 그룹 멤버 자격을 설정합니다.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. 사용자 azacsnap의 로그인 `.profile`이 있는지 확인합니다.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. azacsnap의 $PATH에 추가할 디렉터리에 대한 파일 시스템을 검색합니다. 이는 일반적으로 `hdbsql` 및 `hdbuserstore`와 같은 SAP HANA 도구에 대한 경로입니다.

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 업데이트된 경로를 사용자의 프로필에 추가

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. azacsnap의 $LD_LIBRARY_PATH에 추가할 디렉터리에 대한 파일 시스템을 검색합니다.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 사용자 프로필에 업데이트된 라이브러리 경로에 추가합니다.

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```
    
1. 저장소 백 엔드에 따라 수행할 작업:

    # <a name="azure-netapp-files-with-vm"></a>[Azure NetApp Files (VM 포함)](#tab/azure-netapp-files)

    1. Azure NetApp Files의 경우
        1. .NET Core 단일 파일 추출 지침에 따라 사용자의 `DOTNET_BUNDLE_EXTRACT_BASE_DIR` 경로를 구성합니다.
            1. SUSE Linux

                ```bash
                echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
                echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
                ```

            1. RHEL

                ```bash
                echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
                echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
                ```

    # <a name="azure-large-instance-bare-metal"></a>[Azure Large Instance(운영 체제 미설치)](#tab/azure-large-instance)

    1. Azure 대규모 인스턴스의 경우
        1. "루트" 사용자(설치를 실행하는 사용자)에서 azacsnap의 백 엔드 스토리지에 대한 SSH 키를 복사합니다. 여기서는 "루트" 사용자가 스토리지에 대한 연결을 이미 구성했다고 가정합니다.
           > "[스토리지와의 통신 사용](#enable-communication-with-storage)" 섹션을 참조하세요.

            ```bash
            cp -pr ~/.ssh /home/azacsnap/.
            ```

        1. SSH 파일에 대해 사용자 권한을 올바르게 설정합니다.

            ```bash
            chown -R azacsnap.sapsys /home/azacsnap/.ssh
            ```

    ---

1. 대상 사용자 azacsnap에 대한 SAP HANA 연결 보안 사용자 저장소를 복사합니다. 여기서는 "루트" 사용자가 보안 사용자 저장소를 이미 구성했다고 가정합니다.
    > "[데이터베이스와 통신 사용](#enable-communication-with-database)" 섹션을 참조 하세요.

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. `hdbuserstore` 파일에 대한 사용자 권한을 올바르게 설정합니다.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. /home/azacsnap/bin/으로 스냅샷 도구를 추출합니다.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. 명령 실행 파일을 만듭니다.

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. 사용자의 홈 디렉터리에 올바른 소유권 권한이 설정되어 있는지 확인합니다.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>스냅샷 도구 설정 완료

설치 프로그램은 스냅샷 도구 설치가 완료된 후 완료해야 하는 단계를 제공합니다.
스냅샷 도구를 구성하고 테스트하려면 다음 단계를 수행합니다.  성공적으로 테스트한 후에는 첫 번째 데이터베이스 일치 스토리지 스냅샷을 수행합니다.

다음 출력에서는 기본 설치 옵션을 사용하여 설치 프로그램을 실행한 후 완료해야 하는 단계를 보여 줍니다.

1. 스냅샷 사용자 계정으로 변경
    1. `su - azacsnap`
1. HANA 보안 사용자 저장소 설정
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. 명령 위치로 변경
   1. `cd /home/azacsnap/bin/`
1. 고객 세부 정보 파일 구성
   1. `azacsnap -c configure –-configuration new`
1. 스토리지에 대한 연결 테스트.....
   1. `azacsnap -c test –-test storage`
1. HANA에 대한 연결 테스트.....
    1. SSL을 사용하지 않는 경우
       1. `azacsnap -c test –-test hana`
    1. SSL을 사용하는 경우 올바른 SSL 옵션을 선택해야 합니다.
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. 첫 번째 스냅샷 백업 실행
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

설치 하기 전에 "[데이터베이스와 통신 사용](#enable-communication-with-database)"을 수행 하지 않은 경우 2 단계를 수행 해야 합니다.

> [!NOTE]
> 테스트 명령이 올바르게 실행되어야 합니다. 그렇지 않으면 명령이 실패할 수 있습니다.

## <a name="configuring-the-database"></a>데이터베이스 구성

이 섹션에서는 데이터베이스를 구성하는 방법을 설명합니다.

# <a name="sap-hana"></a>[SAP HANA](#tab/sap-hana)

### <a name="sap-hana-configuration"></a>SAP HANA 구성

로그 백업 및 카탈로그에 대한 보호를 보장하기 위해 SAP HANA에 적용되는 몇 가지 권장 변경 사항이 있습니다. 기본적으로 `basepath_logbackup` 및 `basepath_catalogbackup`이 해당 파일을 `$(DIR_INSTANCE)/backup/log` 디렉터리로 출력하며 이 경로가 `azacsnap`이 이러한 파일을 스냅샷하도록 구성된 볼륨에 있는 것 같지 않습니다. 이러한 파일은 스토리지 스냅샷으로 보호되지 않습니다.

다음 `hdbsql` 명령 예제는 로그 및 카탈로그 경로를 `azacsnap`으로 스냅샷할 수 있는 스토리지 볼륨에 있는 위치에 설정하는 방법을 시연하기 위한 것입니다. 명령줄에서 로컬 SAP HANA 구성과 일치하는 값을 확인해야 합니다.

### <a name="configure-log-backup-location"></a>로그 백업 위치 구성

이 예제에서는 `basepath_logbackup` 매개 변수를 변경합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>카탈로그 백업 위치 구성

이 예제에서는 `basepath_catalogbackup` 매개 변수를 변경합니다. 먼저 디렉터리와 동일한 소유권이 있는 경로를 만들지 않는 경우 파일 시스템에 `basepath_catalogbackup` 경로가 있는지 확인합니다.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

경로를 만들어야 하는 경우 다음 예제에서는 경로를 만들고 올바른 소유권 및 권한을 설정합니다. 이러한 명령은 루트로 실행해야 합니다.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

다음 예제에서는 SAP HANA 설정을 변경합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>로그 및 카탈로그 백업 위치 확인

위에서 변경한 후 다음 명령을 사용하여 설정이 올바른지 확인합니다.
이 예제에서 위의 지침에 따른 설정은 SYSTEM 설정으로 표시됩니다.

> 또한 이 쿼리는 비교를 위한 기본 설정을 반환합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>로그 백업 제한 시간 구성

로그 백업을 수행하는 SAP HANA에 대한 기본 설정은 900초(15분)입니다. 이 값을 300초(5분)로 줄이는 것이 좋습니다.  그런 다음 구성 파일의 다른 볼륨 섹션에 log_backups 볼륨을 추가하여 정기적인 백업(예: 10분마다)을 실행할 수 있습니다.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>로그 백업 제한 시간 확인

로그 백업 제한 시간을 변경한 후 다음과 같이 설정되었는지 확인합니다.
이 예제에서는 설정된 설정이 SYSTEM 설정으로 표시되지만 이 쿼리는 비교를 위한 DEFAULT 설정도 반환합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

---

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅샷 도구 구성](azacsnap-cmd-ref-configure.md)

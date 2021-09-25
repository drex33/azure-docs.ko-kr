---
title: Azure Database for MariaDB에 대한 인증서 회전
description: Azure Database for MariaDB에 영향을 주는 루트 인증서 변경의 예정된 변경 내용에 대해 알아봅니다
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 8a8a7fec5b5c5f62fb44f6c0ccf6859b1141295b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646514"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대한 루트 CA 변경의 변경 내용 이해

Azure Database for MariaDB는 표준 유지 관리 및 보안 모범 사례의 일환으로 **2021년 2월 15일(2021/02/15)** 에 루트 인증서를 성공적으로 변경했습니다. 이 문서에서는 변경 내용, 영향을 받는 리소스 및 응용 프로그램이 데이터베이스 서버에 대한 연결을 유지 관리하는 데 필요한 단계에 대한 자세한 정보를 제공합니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
>

## <a name="why-root-certificate-update-is-required"></a>루트 인증서 업데이트가 필요한 이유는 무엇인가요?

Azure Database for MariaDB 사용자는 미리 정의된 인증서를 사용하여 [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)에 있는 Azure Database for MariaDB 서버에 연결할 수 있습니다. 그러나 [CA(인증 기관) 브라우저 포럼](https://cabforum.org/) 에서는 최근에 CA 공급업체에서 비준수로 발급한 여러 인증서에 대한 보고서를 게시했습니다.

업계의 규정 준수 요구 사항에 따라 CA 공급업체는 비준수 CA에 대한 CA 인증서를 해지하여 준수 CA에서 발급한 인증서를 서버에서 사용하도록 요구하기 시작했고, 해당 준수 CA의 CA 인증서로 서명했습니다. Azure Database for MariaDB는 이러한 비준수 인증서 중 하나를 사용했으므로 인증서를 준수 버전으로 회전하여 MySQL 서버에 대한 잠재적 위협을 최소화해야 했습니다.

새 인증서는 2021년 2월 15일(2021/02/15)부터 롤아웃되어 적용되고 있습니다. 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>2021년 2월 15일(2021/02/15)에 수행된 변경 내용은 무엇인가요?

기존 고객이 아무것도 변경할 필요가 없도록 하고 서버 연결에 영향을 주지 않기 위해, 2021년 2월 15일에 [BaltimoreCyberTrustRoot 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)가 동일한 [BaltimoreCyberTrustRoot 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)의 **준수 버전** 으로 대체되었습니다. 이러한 변경에서 [BaltimoreCyberTrustRoot 루트 인증서](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)는 [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)로 **대체되지 않았으며**, 고객이 변경할 수 있는 시간을 더 허용하기 위해 해당 변경이 지연되고 있습니다.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>연결을 유지하기 위해 내 클라이언트를 변경해야 하나요?

클라이언트 쪽에서는 변경할 필요가 없습니다. 아래의 이전 권장 사항을 따른 경우에도 **BaltimoreCyberTrustRoot 루트 인증서가 결합된 CA 인증서에서 제거된 경우를 제외** 하고는 계속 연결할 수 있습니다. **연결을 유지하기 위해 추가 공지가 있을 때까지 결합된 CA 인증서에서 BaltimoreCyberTrustRoot를 제거하지 않는 것이 좋습니다.**

### <a name="previous-recommendation"></a>이전 권장 사항

- 아래 링크에서 **BaltimoreCyberTrustRoot** & **DigiCertGlobalRootG2** CA를 다운로드합니다.

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- **BaltimoreCyberTrustRoot** 및 **DigiCertGlobalRootG2** 인증서를 모두 포함하는 결합된 CA 인증서 저장소를 생성합니다.

  - Java(MariaDB 커넥터/J) 사용자의 경우 다음을 실행합니다.

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    그런 다음, 원래 키 저장소 파일을 새로 생성된 파일로 바꿉니다.

    - `System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");`
    - `System.setProperty("javax.net.ssl.trustStorePassword","password");`

  - .NET(MariaDB 커넥터/NET, MariaDBConnector) 사용자의 경우 **BaltimoreCyberTrustRoot** 및 **DigiCertGlobalRootG2** 가 모두 신뢰할 수 있는 루트 인증 기관인 Windows 인증서 저장소에 있는지 확인합니다. 인증서가 없는 경우, 누락된 인증서를 가져옵니다.

    [![Azure Database for MariaDB .net cert](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - SSL_CERT_DIR을 사용하는 Linux의 .NET 사용자의 경우 **BaltimoreCyberTrustRoot** 및 **DigiCertGlobalRootG2** 가 모두 SSL_CERT_DIR로 표시된 디렉터리에 있는지 확인합니다. 인증서가 없는 경우 누락된 인증서 파일을 생성합니다.

  - 기타(MariaDB Client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 사용자의 경우, 아래와 같은 두 가지 CA 인증서 파일을 병합할 수 있습니다

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- 원래 루트 CA pem 파일을 결합된 루트 CA 파일로 바꾸고, 응용 프로그램/클라이언트를 다시 시작합니다.
- 나중에 새 인증서가 서버 쪽에 배포되면 CA pem 파일을 DigiCertGlobalRootG2.crt.pem으로 변경할 수 있습니다.

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>2021년 2월 15일 이러한 변경에서 BaltimoreCyberTrustRoot 인증서가 DigiCertGlobalRootG2로 대체되지 않는 이유는 무엇인가요?

이러한 변경에 대한 고객의 준비 상태를 평가한 결과, 많은 고객이 이러한 변경을 관리하기 위한 추가 리드 타임이 필요하다는 것을 알게 되었습니다. 준비를 위해 고객에게 더 많은 리드 타임을 제공할 수 있도록 DigiCertGlobalRootG2에 대한 인증서 변경을 1년 이상 연기하여 고객과 최종 사용자에게 충분한 리드 타임을 제공하기로 결정했습니다. 

사용자가 앞에서 설명한 단계를 사용하여 결합된 인증서를 만들어 서버에 연결하는 한편, 제거하도록 알리는 통신문을 받을 때까지 BaltimoreCyberTrustRoot 인증서를 제거하지 않는 것이 좋습니다. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>BaltimoreCyberTrustRoot 인증서를 제거하면 어떻게 되나요?

Azure Database for MariaDB 서버에 연결하는 동안 연결 오류가 시작됩니다. 연결을 유지하려면 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 인증서를 사용하여 [SSL을 구성](howto-configure-ssl.md)해야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. SSL/TLS를 사용하지 않는 경우에도 루트 CA를 업데이트해야 하나요?

SSL/TLS를 사용하지 않는 경우 아무 작업도 수행할 필요가 없습니다.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. SSL/TLS를 사용하는 경우 루트 CA를 업데이트하려면 데이터베이스 서버를 다시 시작해야 하나요?

아니요, 새 인증서 사용을 시작하기 위해 데이터베이스 서버를 다시 시작할 필요가 없습니다. 인증서 업데이트는 클라이언트 쪽 변경이며, 들어오는 클라이언트 연결에서 새 인증서를 사용하여 데이터베이스 서버에 연결할 수 있는지 확인해야 합니다.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. 루트 인증서 확인에서 SSL/TLS를 사용하는지 확인하려면 어떻게 할까요?

연결 문자열을 검토하여 연결에서 루트 인증서를 확인하는지 여부를 식별할 수 있습니다.

- 연결 문자열에 `sslmode=verify-ca` 또는 `sslmode=verify-identity`가 포함되는 경우 인증서를 업데이트해야 합니다.
- 연결 문자열에 `sslmode=disable`, `sslmode=allow`, `sslmode=prefer`, `sslmode=require`가 포함되는 경우 인증서를 업데이트할 필요가 없습니다.
- 연결 문자열에서 sslmode를 지정하지 않는 경우 인증서를 업데이트할 필요가 없습니다.

연결 문자열을 추상화하는 클라이언트를 사용하는 경우 클라이언트 설명서를 검토하여 인증서를 확인하는지 여부를 파악합니다.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. Azure Database for MariaDB에서 App Service를 사용하는 경우 어떤 영향이 있나요?

Azure Database for MariaDB에 연결하는 Azure App Service의 경우 응용 프로그램에서 SSL을 사용하는 방법에 따라 두 가지 가능한 시나리오가 있습니다.

- 이 새 인증서는 플랫폼 수준에서 App Service에 추가되었습니다. 응용 프로그램의 App Service 플랫폼에 포함된 SSL 인증서를 사용하는 경우에는 아무 작업도 필요하지 않습니다. 이는 가장 일반적인 시나리오입니다. 
- 코드에 SSL 인증서 파일의 경로를 명시적으로 포함하는 경우, 새 인증서를 다운로드하고 새 인증서를 사용하도록 코드를 업데이트해야 합니다. 이 시나리오의 좋은 예는 [App Service 설명서](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)에서 공유된 App Service의 사용자 지정 컨테이너를 사용하는 것입니다. 이는 드문 시나리오이지만 이를 사용하는 일부 사용자를 확인했습니다.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. Azure Database for MariaDB에서 AKS(Azure Kubernetes Services)를 사용하는 경우 어떤 영향이 있나요?

AKS(Azure Kubernetes Services)를 사용하여 Azure Database for MariaDB에 연결하려는 경우 전용 고객 호스트 환경에서 액세스하는 것과 비슷합니다. [여기](../aks/ingress-own-tls.md)의 단계를 참조하세요.

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. Azure Data Factory를 사용하여 Azure Database for MariaDB에 연결하는 경우 어떤 영향이 있나요?

Azure Integration Runtime을 사용하는 커넥터의 경우 이 커넥터는 Azure 호스팅 환경의 Windows 인증서 저장소에 있는 인증서를 활용합니다. 이러한 인증서는 이미 새로 적용된 인증서와 호환되므로 아무 작업도 필요하지 않습니다.

SSL 인증서 파일의 경로를 연결 문자열에 명시적으로 포함하는 자체 호스팅 통합 런타임을 사용하는 커넥터의 경우 [새 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)를 다운로드하고 이를 사용하도록 연결 문자열을 업데이트해야 합니다.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. 이러한 변경에 대한 데이터베이스 서버 유지 관리 가동 중지 시간을 계획해야 하나요?

아니요. 여기서 변경된 내용은 데이터베이스 서버에 연결할 클라이언트 쪽에서만 변경되므로 데이터베이스 서버에 대한 유지 관리 가동 중지 시간이 필요하지 않습니다.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. 2021년 2월 15일(2021/02/15) 이후에 새 서버를 만드는 경우 영향을 받게 되나요?

2021년 2월 15일(2021/02/15) 이후에 생성한 서버의 경우 SSL을 사용하여 연결하는 응용 프로그램에 대해 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)를 계속 사용합니다.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Microsoft에서 인증서를 업데이트하는 빈도는 어떻게 되나요? 또는 만료 정책은 무엇인가요?

Azure Database for MariaDB에서 사용하는 이러한 인증서는 신뢰할 수 있는 CA(인증 기관)에서 제공합니다. 따라서 이러한 인증서를 지원하는 것은 CA에서 이러한 인증서를 지원하는 것과 관련이 있습니다. [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 인증서는 2025년에 만료될 예정이므로 Microsoft는 만료되기 전에 인증서를 변경해야 합니다. 또한 이러한 미리 정의된 인증서에 예기치 않은 버그가 있는 경우 서비스가 항상 안전하고 규정을 준수하도록 보장하기 위해 Microsoft에서 2021년 2월 15일에 수행된 변경과 비슷한 방식으로 인증서 회전을 빠르게 수행해야 합니다.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. 읽기 복제본을 사용하는 경우 이 업데이트를 원본 서버 또는 읽기 복제본에서만 수행해야 하나요?

이 업데이트는 클라이언트 쪽 변경이므로 클라이언트가 복제본 서버에서 데이터를 읽는 데 사용된 경우 해당 클라이언트에 대한 변경 내용도 적용해야 합니다.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. 입력 데이터 복제를 사용하는 경우 작업을 수행해야 하나요?

- 데이터 복제가 가상 머신(온-프레미스 또는 Azure 가상 머신)에서 Azure Database for MySQL로 수행되는 경우 SSL을 사용하여 복제본을 만드는지 확인해야 합니다. **SHOW SLAVE STATUS** 를 실행하고, 다음 설정을 확인합니다.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

[입력 데이터 복제](concepts-data-in-replication.md)를 사용하여 Azure Database for MySQL에 연결하는 경우 다음 두 가지 사항을 고려해야 합니다.

- 데이터 복제가 가상 머신(온-프레미스 또는 Azure 가상 머신)에서 Azure Database for MySQL로 수행되는 경우 SSL을 사용하여 복제본을 만드는지 확인해야 합니다. **SHOW SLAVE STATUS** 를 실행하고, 다음 설정을 확인합니다. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  CA_file, SSL_Cert 및 SSL_Key에 대한 인증서가 제공된 것으로 확인되면 [새 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)를 추가하여 파일을 업데이트하고 결합된 인증서 파일을 만들어야 합니다.

- 데이터 복제가 두 Azure Database for MySQL 사이에 있는 경우 **CALL mysql.az_replication_change_master** 를 실행하여 복제본을 다시 설정하고 새 이중 루트 인증서를 마지막 [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) 매개 변수로 제공해야 합니다.

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. SSL이 사용되고 있는지 확인하는 서버 쪽 쿼리가 있나요?

SSL 연결을 사용하여 서버에 연결하는지 확인하려면 [SSL 확인](howto-configure-ssl.md#verify-the-ssl-connection)을 참조하세요.

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. 내 인증서 파일에 DigiCertGlobalRootG2가 이미 있는 경우 필요한 작업이 있나요?

아니요. 인증서 파일에 **DigiCertGlobalRootG2** 가 이미 있는 경우 아무 작업도 필요하지 않습니다.

### <a name="14-what-if-i-have-further-questions"></a>14. 추가 질문이 있으면 어떻게 해야 하나요?

질문이 있는 경우, [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com)에서 커뮤니티 전문가의 답변을 받으세요. 지원 계획이 있고 기술 지원이 필요한 경우 [Microsoft에 문의](mailto:AzureDatabaseformariadb@service.microsoft.com)하세요.

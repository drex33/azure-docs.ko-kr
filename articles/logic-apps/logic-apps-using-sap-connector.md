---
title: SAP 시스템에 연결
description: Azure Logic Apps에서 워크플로를 자동화하여 SAP 리소스에 액세스하고 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, azla
ms.topic: how-to
ms.date: 10/21/2021
tags: connectors
ms.openlocfilehash: 3951ae0b6ea1d3764c60723c476a989712fce6e5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218001"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps에서 SAP 시스템에 연결

이 문서에서는 [SAP 커넥터](/connectors/sap/)를 사용하여 Azure Logic Apps에서 SAP 리소스에 액세스하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* SAP 리소스에 액세스하려는 논리 앱 워크플로. Azure Logic Apps를 처음 사용하는 경우 [Azure Logic Apps 개요](logic-apps-overview.md) 및 [Azure Portal에서 첫 번째 논리 앱 워크플로를 만들기 위한 빠른 시작](quickstart-create-first-logic-app-workflow.md)을 검토하세요.

  * 더 이상 사용되지 않는 이전 버전의 SAP 커넥터를 사용한 경우에는 SAP 서버에 연결하려면 먼저 [현재 커넥터로 마이그레이션](#migrate-to-current-connector)해야 합니다.

  * 다중 테넌트 Azure에서 논리 앱 워크플로를 실행하는 경우 [다중 테넌트 필수 구성 요소](#multi-tenant-azure-prerequisites)를 검토하세요.

  * 프리미엄 수준 [ISE(통합 서비스 환경)](connect-virtual-network-vnet-isolated-environment-overview.md)에서 논리 앱 워크플로를 실행하는 경우 [ISE 필수 구성 요소](#ise-prerequisites)를 검토하세요.

* Azure Logic Apps에서 액세스하려는 [SAP 애플리케이션 서버](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 또는 [SAP 메시지 서버](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm). 이 커넥터를 지원하는 SAP 서버에 대한 자세한 내용은 [SAP 호환성](#sap-compatibility)을 검토하세요.

  > [!IMPORTANT]
  > RFC 사용을 허용하도록 SAP 서버 및 사용자 계정을 설정했는지 확인합니다. 자세한 내용은 지원되는 사용자 계정 유형 및 각 작업 유형(RFC, BAPI, IDOC)에 대한 최소 필수 권한 부여를 포함하며, [460089 - 외부 RFC 프로그램에 대한 최소 권한 부여 프로필](https://launchpad.support.sap.com/#/notes/460089) SAP 참고 사항을 검토하세요. 
  >
  > * RFC 작업의 경우 사용자 계정은 기능 모듈 `RFC_GROUP_SEARCH` 및 `DD_LANGU_TO_ISOLA`에 대한 액세스 권한이 추가로 필요합니다.
  > * BAPI 작업의 경우 사용자 계정은 `BAPI_TRANSACTION_COMMIT`, `BAPI_TRANSACTION_ROLLBACK`, `RPY_BOR_TREE_INIT`, `SWO_QUERY_METHODS` 및 `SWO_QUERY_API_METHODS` 함수 모듈에도 액세스해야 합니다.
  > * IDOC 작업의 경우 사용자 계정은 `IDOCTYPES_LIST_WITH_MESSAGES`, `IDOCTYPES_FOR_MESTYPE_READ`, `INBOUND_IDOCS_FOR_TID`, `OUTBOUND_IDOCS_FOR_TID`, `GET_STATUS_FROM_IDOCNR` 및 `IDOC_RECORD_READ` 기능 모듈에도 액세스해야 합니다.
  > * **테이블 읽기** 작업의 경우 사용자 계정은 `RFC BBP_RFC_READ_TABLE` 또는 `RFC_READ_TABLE` 함수 모듈 *둘 중 하나* 에 액세스해야 합니다.

* SAP 서버로 전송할 메시지 콘텐츠(예: 샘플 IDoc 파일). 이 콘텐츠는 XML 형식이어야 하며 사용하려는 [SAP 작업](#actions)의 네임스페이스를 포함해야 합니다. [IDoc 파일을 XML 봉투에 래핑하여 플랫 파일 스키마를 통해 전송](#send-flat-file-idocs)할 수 있습니다.

* **SAP에서 메시지가 수신될 때** 트리거를 사용하려면 다음 작업도 수행해야 합니다.

  * SAP 게이트웨이 보안 권한 또는 ACL(액세스 제어 목록)을 설정합니다. 게이트웨이 모니터 대화 상자인 T-Code SMGW에 표시되는 **secinfo** 및 **reginfo** 파일에서 **이동 > 전문가 기능 > 외부 보안 > ACL 파일 유지 관리** 를 따릅니다. 다음 권한 설정이 필요합니다.

    `P TP=LOGICAPP HOST=<on-premises-gateway-server-IP-address> ACCESS=*`

    이 줄의 형식은 다음과 같습니다.

    `P TP=<trading-partner-identifier-(program-name)-or-*-for-all-partners> HOST=<comma-separated-list-with-external-host-IP-or-network-names-that-can-register-the-program> ACCESS=<*-for-all-permissions-or-a-comma-separated-list-of-permissions>`

    SAP 게이트웨이 보안 권한을 구성하지 않으면 다음 오류가 수신될 수 있습니다.

    `Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed`

    자세한 내용은 [SAP Note 1850230 - GW: "tp &lt;프로그램 ID&gt; 등록이 허용되지 않음"](https://userapps.support.sap.com/sap/support/knowledge/en/1850230)을 검토하세요.

  * ACL(액세스 제어 목록) 문제를 찾는 데 도움이 되도록 SAP 게이트웨이 보안 로깅을 설정합니다. 자세한 내용은 [게이트웨이 로깅 설정에 대한 SAP 도움말 항목](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)을 검토하세요.

  * **RFC 연결 구성**(T-Code SM59) 대화 상자에서 **TCP/IP** 유형의 RFC 연결을 만듭니다. **활성화 유형** 은 **등록된 서버 프로그램** 이어야 합니다. RFC 연결의 **대상 시스템이 있는 통신 유형** 값을 **유니코드** 로 설정합니다.

  * [플랫 파일 디코딩 작업](logic-apps-enterprise-integration-flatfile.md)과 함께 **FlatFile** 로 설정된 **IDOC 형식** 매개 변수와 함께 이 SAP 트리거를 사용하는 경우 이 값을 `true`로 설정하여 플랫 파일 스키마에서 `early_terminate_optional_fields` 속성을 사용해야 합니다.

    이 요구 사항은 tRFC 호출 `IDOC_INBOUND_ASYNCHRONOUS`에서 SAP가 보낸 플랫 파일 IDoc 데이터 레코드가 전체 SDATA 필드 길이로 채워지지 않기 때문에 필요합니다. Azure Logic Apps는 SAP에서 받은 대로 패딩 없이 플랫 파일 IDoc 원본 데이터를 제공합니다. 또한 이 SAP 트리거를 플랫 파일 디코딩 작업과 결합할 때 작업에 제공된 스키마가 일치해야 합니다.

  > [!NOTE]
  > 이 SAP 트리거는 동일한 URI 위치를 사용하여 webhook 구독을 갱신하고 구독 취소합니다. 갱신 작업에는 HTTP `PATCH` 메서드가 사용되고, 구독 취소 작업에는 HTTP `DELETE` 메서드가 사용됩니다. 이 동작으로 인해 갱신 작업이 트리거 기록에 구독 취소 작업으로 표시될 수 있지만, 트리거는 `DELETE`가 아닌 `PATCH`를 HTTP 메서드로 사용하므로 작업은 여전히 갱신 작업입니다.

### <a name="sap-compatibility"></a>SAP 호환성

SAP 커넥터는 다음과 같은 유형의 SAP 시스템과 호환됩니다.

* S/4 HANA 같은 온-프레미스 및 클라우드 기반 HANA 기반 SAP 시스템

* R/3 및 ECC 같은 클래식 온-프레미스 SAP 시스템

SAP 커넥터는 다음과 같은 SAP NetWeaver 기반 시스템의 메시지 및 데이터 통합 유형을 지원합니다.

* IDoc(중간 문서)

* BAPI(비즈니스 애플리케이션 프로그래밍 인터페이스)

* RFC(원격 함수 호출) 및 tRFC(트랜잭션 RFC)

SAP 커넥터는 [SAP .NET Connector(NCo) 라이브러리](https://support.sap.com/en/product/connectors/msnet.html)를 사용합니다.

사용 가능한 [SAP 트리거](#triggers) 및 [SAP 작업](#actions)을 사용하려면 먼저 연결을 인증해야 합니다. 사용자 이름과 암호로 연결을 인증할 수 있습니다. SAP 커넥터는 인증을 위해 [SAP 보안 네트워크 통신(SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)도 지원합니다. SNC는 SAP NetWeaver SSO(Single Sign-On) 또는 외부 제품의 추가 보안 기능에 사용할 수 있습니다. SNC를 사용하는 경우 [SNC 전제조건](#snc-prerequisites) 및 [ISE 커넥터에 대한 SNC 전제조건](#snc-prerequisites-ise)을 검토하세요.

### <a name="migrate-to-current-connector"></a>현재 커넥터로 마이그레이션

이전 SAP 애플리케이션 서버 및 SAP 메시지 서버 커넥터는 2020년 2월 29일부터 더 이상 사용되지 않습니다. 현재 SAP 커넥터로 마이그레이션하려면 다음 단계를 수행합니다.

1. [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127)를 현재 버전으로 업데이트합니다. 자세한 내용은 [Azure Logic Apps용 온-프레미스 데이터 게이트웨이 설치](logic-apps-gateway-install.md)를 검토하세요.

1. 사용 중단된 SAP 커넥터를 사용하는 논리 앱 워크플로에서 **SAP로 보내기** 작업을 삭제합니다.

1. 현재 SAP 커넥터에서 **SAP로 메시지 보내기** 작업을 추가합니다.

1. 새 작업에서 SAP 시스템에 다시 연결합니다.

1. 논리 앱 워크플로 저장 디자이너 도구 모음에서 **저장** 을 선택합니다.

## <a name="multi-tenant-azure-prerequisites"></a>다중 테넌트 Azure 필수 구성 요소

이러한 필수 구성 요소는 논리 앱 워크플로가 다중 테넌트 Azure에서 실행되는 경우에 적용됩니다. 관리형 SAP 커넥터는 기본적으로 [ISE](connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행되지 않습니다.

> [!TIP]
> 프리미엄 수준 ISE를 사용하는 경우 관리형 SAP 커넥터 대신 SAP ISE 커넥터를 사용할 수 있습니다. 자세한 내용은 [ISE 필수 구성 요소](#ise-prerequisites)를 검토하세요.

관리형 SAP 커넥터는 [온-프레미스 데이터 게이트웨이](logic-apps-gateway-connection.md)를 통해 SAP 시스템과 통합됩니다. 예를 들어 메시지 보내기 시나리오에서 논리 앱 워크플로가 SAP 시스템으로 메시지를 보내면 데이터 게이트웨이는 논리 앱 워크플로에서 받은 요청을 SAP에 전달하는 RFC 클라이언트 역할을 합니다. 마찬가지로 메시지 받기 시나리오에서 데이터 게이트웨이는 SAP에서 요청을 수신하여 논리 앱 워크플로에 전달하는 RFC 서버 역할을 합니다.

1. 연결하는 SAP 시스템과 동일한 가상 네트워크에 있는 호스트 컴퓨터 또는 가상 머신에 [온-프레미스 데이터 게이트웨이를 다운로드하여 설치](logic-apps-gateway-install.md)합니다.

1. Azure Portal에서 온-프레미스 데이터 게이트웨이에 대한 [Azure 게이트웨이 리소스를 만듭니다](logic-apps-gateway-connection.md#create-azure-gateway-resource). 이 게이트웨이는 온-프레미스 데이터 및 리소스에 안전하게 액세스하는 데 도움이 됩니다. 지원되는 버전의 게이트웨이를 사용해야 합니다.

  > [!TIP]
  > 게이트웨이에 문제가 발생하면 [최신 버전으로 업그레이드](https://aka.ms/on-premises-data-gateway-installer)합니다. 문제를 해결하는 업데이트가 포함되어 있을 수 있습니다.

1. 온-프레미스 데이터 게이트웨이와 동일한 로컬 컴퓨터에서 [최신 SAP 클라이언트 라이브러리를 다운로드하여 설치](#sap-client-library-prerequisites)합니다.

1. 온-프레미스 데이터 게이트웨이를 설치한 호스트 시스템에 대한 네트워크 호스트 이름 및 서비스 이름 확인을 구성합니다.

  Azure Logic Apps에서 연결하기 위해 호스트 이름 또는 서비스 이름을 사용하려는 경우 이름 확인을 위해 서비스와 함께 각 SAP 애플리케이션, 메시지 및 게이트웨이 서버를 설정해야 합니다. 네트워크 호스트 이름 확인은 `%windir%\System32\drivers\etc\hosts` 파일 또는 온-프레미스 데이터 게이트웨이 호스트 시스템에서 사용할 수 있는 DNS 서버에서 구성됩니다. 서비스 이름 확인은 `%windir%\System32\drivers\etc\services`에서 구성됩니다. 연결에 네트워크 호스트 이름이나 서비스 이름을 사용하지 않으려면 호스트 IP 주소와 서비스 포트 번호를 대신 사용할 수 있습니다.

   SAP 시스템에 대한 DNS 항목이 없는 경우 다음 예는 호스트 파일에 대한 샘플 항목을 보여 줍니다.

   ```text
   10.0.1.9           sapserver                   # SAP single-instance system host IP by simple computer name
   10.0.1.9           sapserver.contoso.com       # SAP single-instance system host IP by fully qualified DNS name
   ```

   서비스 파일에 대한 샘플 항목 집합은 다음과 같습니다.

   ```text
   sapdp00            3200/tcp              # SAP system instance 00 dialog (application) service port
   sapgw00            3300/tcp              # SAP system instance 00 gateway service port
   sapmsDV6           3601/tcp              # SAP system ID DV6 message service port
   ```

## <a name="ise-prerequisites"></a>ISE 필수 구성 요소

ISE는 Azure 가상 네트워크로 보호되는 리소스에 대한 액세스를 제공하며, 논리 앱 워크플로가 온-프레미스 데이터 게이트웨이를 사용하지 않고 온-프레미스 리소스에 직접 액세스할 수 있게 해주는 다른 ISE 네이티브 커넥터를 제공합니다.

1. Blob 컨테이너가 포함된 Azure Storage 계정이 아직 없는 경우 [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) 또는 [Azure Storage Explorer](../storage/blobs/quickstart-storage-explorer.md)를 사용하여 컨테이너를 만듭니다.

1. 로컬 컴퓨터에 [최신 SAP 클라이언트 라이브러리를 다운로드하여 설치](#sap-client-library-prerequisites)합니다. 이제 다음과 같은 어셈블리 파일이 생겼습니다.

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. 이러한 어셈블리 파일이 포함된 .zip 파일을 만듭니다. Azure Storage의 Blob 컨테이너에 패키지를 업로드합니다.

1. Azure Portal 또는 Azure Storage Explorer에서 .zip 파일을 업로드한 컨테이너 위치를 찾습니다.

1. 컨테이너 위치의 URL을 복사합니다. SAS 토큰에 권한이 부여되도록 SAS(공유 액세스 서명) 토큰을 포함해야 합니다. 그렇지 않으면 SAP ISE 커넥터 배포가 실패합니다.

1. ISE에서 SAP 커넥터를 설치하고 배포합니다. 자세한 내용은 [ISE 커넥터 추가](add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)를 검토하세요.

   1. [Azure Portal](https://portal.azure.com)에서 ISE를 찾아서 엽니다.

   1. ISE 메뉴에서 **관리형 커넥터** &gt; **추가** 를 선택합니다. 커넥터 목록에서 **SAP** 를 찾아 선택합니다.

   1. **새 관리형 커넥터 추가** 창의 **SAP 패키지** 상자에 SAP 어셈블리가 들어 있는 .zip 파일의 URL을 붙여넣습니다. 마찬가지로 SAS 토큰을 포함해야 합니다.

   1. **만들기** 를 선택하여 ISE 커넥터 만들기를 완료합니다.

1. SAP 인스턴스와 ISE가 서로 다른 가상 네트워크에 있는 경우 서로 연결되도록 [해당 네트워크를 피어링](../virtual-network/tutorial-connect-virtual-networks-portal.md)해야 합니다. 또한 [ISE 커넥터에 대한 SNC 필수 구성 요소](#snc-prerequisites-ise)를 검토하세요.

1. 논리 앱 워크플로에서 연결에 사용할 SAP 애플리케이션, 메시지 및 게이트웨이 서버의 IP 주소를 가져옵니다. ISE의 SAP 연결에는 네트워크 이름 확인을 사용할 수 없습니다.

1. 논리 앱과의 연결에 사용할 SAP 애플리케이션, 메시지 및 게이트웨이 서비스의 포트 번호를 가져옵니다. ISE의 SAP 커넥터에는 서비스 이름 확인을 사용할 수 없습니다.

### <a name="sap-client-library-prerequisites"></a>SAP 클라이언트 라이브러리 필수 구성 요소

다음 목록은 커넥터와 함께 사용 중인 SAP 클라이언트 라이브러리에 대한 필수 구성 요소를 설명합니다.

* 최신 버전의 [.NET Framework 4.0  - Windows 64비트(x64)를 사용하여 컴파일된 Microsoft .NET 3.0.24.0용 SAP Connector(NCo 3.0)](https://support.sap.com/en/product/connectors/msnet.html)를 설치해야 합니다. 이전 버전의 SAP NCo에서는 다음과 같은 문제가 발생할 수 있습니다.

  * 둘 이상의 IDoc 메시지가 동시에 전송되는 경우 이 조건은 SAP 대상으로 전송되는 이후의 모든 메시지를 차단하여 메시지가 시간 초과되도록 합니다.

  * 누출된 세션으로 인해 세션 활성화가 실패할 수 있습니다. 이 조건은 SAP에서 논리 앱 워크플로 트리거로 보낸 호출을 차단할 수 있습니다.

  * 온-프레미스 데이터 게이트웨이(2021년 6월 릴리스)는 리소스를 확보하기 위해 SAP NCo의 `SAP.Middleware.Connector.RfcConfigParameters.Dispose()` 메서드에 따라 다릅니다.

* 데이터 게이트웨이는 64비트 시스템에서만 실행되므로 64비트 버전의 SAP 클라이언트 라이브러리가 설치되어 있어야 합니다. 지원되지 않는 32비트 버전을 설치하면 "잘못된 이미지" 오류가 발생합니다.

* 다음과 같이 시나리오에 따라 기본 설치 폴더의 어셈블리 파일을 다른 위치로 복사합니다.

  * ISE에서 실행되는 논리 앱 워크플로의 경우 대신 [ISE 필수 구성 요소](#ise-prerequisites)를 따르세요.

  * 다중 테넌트 Azure에서 실행되고 온-프레미스 데이터 게이트웨이를 사용하는 논리 앱 워크플로인 경우에는 어셈블리 파일을 데이터 게이트웨이 설치 폴더에 복사합니다. 

    > [!NOTE]
    > **계정 정보 및/또는 권한을 확인하고 다시 시도하세요** 오류 메시지와 함께 SAP 연결에 실패하면 어셈블리 파일을 데이터 게이트웨이 설치 폴더에 복사했는지 확인합니다.
    > 
    > [.NET 어셈블리 바인딩 로그 뷰어](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)를 사용하여 추가 문제를 해결할 수 있습니다. 
    > 이 도구를 사용하여 어셈블리 파일이 올바른 위치에 있는지 확인할 수 있습니다. 
  
  * 필요에 따라 SAP 클라이언트 라이브러리를 설치할 때 **전역 어셈블리 캐시 등록** 옵션을 선택합니다.

SAP 클라이언트 라이브러리, .NET Framework, .NET 런타임 및 게이트웨이 간의 다음 관계를 확인합니다.

* Microsoft SAP 어댑터와 게이트웨이 호스트 서비스는 둘 다 .NET Framework 4.7.2를 사용합니다.

* SAP NCo for .NET Framework 4.0은 .NET 런타임 4.0~4.8을 사용하는 프로세스에서 작동합니다.

* SAP NCo for .NET Framework 2.0은 .NET 런타임 2.0~3.5를 사용하는 프로세스에서 작동하지만, 최신 게이트웨이에서는 더 이상 작동하지 않습니다.

### <a name="snc-prerequisites"></a>SNC 필수 구성 요소

다중 테넌트 Azure에서만 지원되는 온-프레미스 데이터 게이트웨이를 선택적 SNC와 함께 사용하는 경우 다음과 같은 추가 설정을 구성해야 합니다. ISE를 사용하는 경우 [ISE 커넥터에 대한 SNC 필수 구성 요소](#snc-prerequisites-ise)를 검토하세요.

SSO와 함께 SNC를 사용하는 경우 데이터 게이트웨이 서비스를 SAP 사용자에 대해 매핑되는 사용자로 실행해야 합니다. 기본 계정을 변경하려면 **계정 변경** 을 선택하고 사용자 자격 증명을 입력합니다.

![온-프레미스 데이터 게이트웨이 설정이 있는 Azure Portal 및 게이트웨이 서비스 계정 변경 단추가 선택된 서비스 설정 페이지를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/gateway-account.png)

외부 보안 제품을 통해 SNC를 사용하도록 설정하는 경우 데이터 게이트웨이가 설치된 컴퓨터에 SNC 라이브러리 또는 파일을 복사합니다. SNC 제품으로는 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos 및 NTLM이 있습니다. 데이터 게이트웨이에 SNC를 사용하도록 설정하는 방법에 대한 자세한 내용은 [SNC(보안 네트워크 통신) 사용](#enable-secure-network-communications)을 검토하세요.

> [!TIP]
> SNC 라이브러리 버전 및 해당 종속성은 SAP 환경과 호환되어야 합니다.
>
> * 특히 SAPGENPSE 유틸리티로 `sapgenpse.exe`를 사용해야 합니다.
> * 온-프레미스 데이터 게이트웨이를 사용하는 경우 동일한 이진 파일도 설치 폴더에 복사합니다.
> * 연결에 PSE가 제공되면 온-프레미스 데이터 게이트웨이에 대해 PSE 및 SECUDIR을 복사하고 설정할 필요가 없습니다.
> * 온-프레미스 데이터 게이트웨이를 사용하여 라이브러리 호환성 문제를 해결할 수도 있습니다.

#### <a name="snc-prerequisites-ise"></a>SNC 필수 구성 요소(ISE)

SAP 커넥터의 ISE 버전은 SNC X.509를 지원합니다. 다음 단계에 따라 SAP ISE 연결에 SNC를 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> SNC를 사용하기 위해 기존 SAP 커넥터를 재배포하기 전에 이전 커넥터에 대한 모든 연결을 삭제해야 합니다. 여러 논리 앱 워크플로에서 SAP에 대한 동일한 연결을 사용할 수 있습니다. 따라서 ISE의 모든 논리 앱 워크플로에서 모든 SAP 연결을 삭제해야 합니다. 그런 다음 이전 커넥터를 삭제해야 합니다.
>
> 이전 커넥터를 삭제해도 이 커넥터를 사용하는 논리 앱 워크플로를 계속 유지할 수 있습니다. 커넥터를 다시 배포한 후 이러한 논리 앱 워크플로의 SAP 트리거 및 작업에서 새 연결을 인증할 수 있습니다.

먼저 SNC 또는 SAPGENPSE 라이브러리 없이 SAP 커넥터를 이미 배포한 경우 모든 연결과 커넥터를 삭제합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 논리 앱 워크플로에서 SAP 커넥터에 대한 모든 연결을 삭제합니다.

   1. Azure Portal에서 논리 앱 리소스를 엽니다.

   1. 논리 앱 메뉴의 **개발 도구** 아래에서 **API 연결** 을 선택합니다.

   1. **API 연결** 페이지에서 SAP 연결을 선택합니다.

   1. 연결 페이지 메뉴에서 **삭제** 를 선택합니다.

   1. 연결을 삭제하려면 확인 메시지를 수락합니다.

   1. 연결이 삭제되었다는 포털 알림을 기다립니다.

1. 또는 ISE의 API 연결에서 SAP 커넥터에 대 한 연결을 삭제 합니다.

   1. Azure Portal에서 ISE 리소스를 엽니다.

   1. ISE 메뉴의 **설정** 에서 **API 연결** 을 선택합니다.

   1. **API 연결** 페이지에서 SAP 연결을 선택합니다.

   1. 연결 페이지 메뉴에서 **삭제** 를 선택합니다.

   1. 연결을 삭제하려면 확인 메시지를 수락합니다.

   1. 연결이 삭제되었다는 포털 알림을 기다립니다.

그런 다음 ISE에서 SAP 커넥터를 삭제합니다. 커넥터를 삭제하려면 먼저 모든 논리 앱에서 이 커넥터에 대한 모든 연결을 삭제해야 합니다. 모든 연결을 아직 삭제하지 않은 경우 이전 단계 집합을 검토합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 ISE 리소스를 다시 엽니다.

1. ISE 메뉴의 **설정** 에서 **관리형 커넥터** 를 선택합니다.

1. **관리형 커넥터** 페이지에서 SAP 커넥터의 확인란을 선택합니다.

1. 도구 모음에서 **삭제** 를 선택합니다.

1. 커넥터를 삭제하려면 확인 메시지를 수락합니다.

1. 커넥터가 삭제되었다는 포털 알림을 기다립니다.

다음으로 ISE에서 SAP 커넥터를 배포하거나 다시 배포합니다.

1. SAP 커넥터 배포에 사용할 새 zip 보관 파일을 준비합니다. SNC 라이브러리와 SAPGENPSE 유틸리티를 포함해야 합니다.

   1. 모든 SNC, SAPGENPSE 및 NCo 라이브러리를 zip 보관의 루트 폴더에 복사합니다. 이 이진을 하위 폴더에 넣지 마세요.

   1. 64비트 SNC 라이브러리를 사용해야 합니다. 32비트는 지원하지 않습니다.

   1. SNC 라이브러리 및 해당 종속성은 SAP 환경과 호환되어야 합니다. 호환성을 확인하는 방법은 [ISE 필수 구성 요소](#ise-prerequisites)를 참조하세요.

1. 새 zip 보관으로 [ISE 필수 구성 요소](#ise-prerequisites)의 배포 단계를 따릅니다.

마지막으로 SAP 커넥터를 사용하는 모든 논리 앱에서 SNC를 사용하는 새 연결을 만듭니다. 각 연결에 대해 다음 단계를 따르세요.

1. 워크플로 디자이너에서 워크플로를 다시 엽니다.

1. SAP 커넥터를 사용하는 단계를 작성하거나 편집합니다.

1. SAP 연결에 대한 필수 정보를 입력합니다.

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings.png" alt-text="SAP 연결 설정을 보여 주는 워크플로 디자이너의 스크린샷." lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings.png":::

   > [!NOTE]
   > **SAP 사용자 이름** 및 **SAP 암호** 필드는 선택 사항입니다. 사용자 이름과 암호를 제공하지 않으면 커넥터는 이후 단계에서 인증을 위해 제공된 클라이언트 인증서를 사용합니다.

1. SNC를 사용하도록 설정합니다.

   1. **SNC 사용** 의 확인란을 선택합니다.

   1. **SNC 라이브러리** 에 SNC 라이브러리의 이름을 입력합니다. 예: `sapcrypto.dll`.

   1. **SNC 파트너 이름** 에 백엔드의 SNC 이름을 입력합니다. 예: `p:CN=DV3, OU=LA, O=MS, C=US`.

   1. **SNC 인증서** 에 SNC 클라이언트의 공개 인증서를 base64로 인코딩된 형식으로 입력합니다. PEM 머리글이나 바닥글을 포함하지 마세요.

   1. 필요에 따라 **SNC 내 이름**, **SNC 보호 품질** 에 대한 SNC 설정을 입력합니다(선택 사항).

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png" alt-text="새 SAP 연결을 위한 워크플로 디자이너 및 SNC 구성 설정을 보여 주는 스크린샷." lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png":::

1. PSE 설정을 구성합니다. **PSE** 의 경우 SNC PSE를 base64로 인코딩된 이진으로 입력합니다.

   * PSE에는 이전 단계에서 제공한 공개 클라이언트 인증서와 지문이 일치하는 프라이빗 클라이언트 인증서가 포함되어야 합니다.

   * PSE에는 추가 클라이언트 인증서가 포함될 수 있습니다.

   * PSE에는 PIN이 없어야 합니다. 필요한 경우 SAPGENPSE 유틸리티를 사용하여 PIN을 비어 있도록 설정합니다.

   인증서 교체의 경우 다음 단계를 따르세요.

   1. ISE에서 SAP ISE X.509를 사용하는 모든 연결에 대해 base64로 인코딩된 이진 PSE를 업데이트합니다.

   1. 새 인증서를 PSE 복사본으로 가져옵니다.

   1. PSE 파일을 base64로 인코딩된 이진으로 인코딩합니다.

   1. SAP 커넥터에 대한 API 연결을 편집하고 여기에 새 PSE 파일을 저장합니다.

   커넥터는 PSE 변경을 감지하고 다음 연결 요청 중에 자체 복사본을 업데이트합니다.

   > [!NOTE]
   > ISE에 대해 둘 이상의 SNC 클라이언트 인증서를 사용하는 경우 모든 연결에 대해 동일한 PSE를 제공해야 합니다. 클라이언트 공개 인증서 매개 변수를 ISE에서 사용되는 각 연결에 대한 특정 인증서로 설정할 수 있습니다.

1. **만들기** 를 선택하여 연결을 만듭니다. 매개 변수가 정확하면 연결이 만들어집니다. 매개 변수에 문제가 있는 경우 연결 만들기 대화 상자에 오류 메시지가 표시됩니다.

   > [!TIP]
   > 연결 매개 변수 문제를 해결하기 위해 온-프레미스 데이터 게이트웨이와 게이트웨이의 로컬 로그를 사용할 수 있습니다.

1. 워크플로 디자이너 도구 모음에서 **저장** 을 선택하여 변경 내용을 저장합니다.

## <a name="send-idoc-messages-to-sap-server"></a>SAP 서버에 IDoc 메시지 보내기

다음 예를 따라 하여 SAP 서버에 IDoc 메시지를 보내고 응답을 반환하는 논리 앱 워크플로를 만들어 보세요.

1. [HTTP 요청에 의해 트리거되는 논리 앱 워크플로를 만듭니다.](#add-http-request-trigger)

1. [워크플로에서 SAP로 메시지를 보내는 작업을 만듭니다.](#create-sap-action-to-send-message)

1. [워크플로에서 HTTP 응답 작업을 만듭니다.](#create-http-response-action)

1. [RFC를 사용하여 SAP ABAP에서 회신을 받는 경우 RFC(원격 함수 호출) 요청-응답 패턴을 만듭니다.](#create-rfc-request-response)

1. [논리 앱을 테스트합니다.](#test-your-logic-app-workflow)

<a name="add-http-request-trigger"></a>

### <a name="add-an-http-request-trigger"></a>HTTP 요청 트리거 추가

논리 앱 워크플로가 XML HTTP를 통해 SAP에서 IDoc를 수신하도록 하려면 [요청 트리거](../connectors/connectors-native-reqres.md)를 사용할 수 있습니다.

> [!TIP]
> CPIC(일반 프로그래밍 인터페이스 통신)를 통해 IDoc를 일반 XML 또는 플랫 파일로 수신하려면 [SAP에서 메시지 수신](#receive-message-sap) 섹션을 검토하세요.

이 섹션은 요청 트리거로 계속되므로 먼저 Azure에서 엔드포인트가 있는 논리 앱 워크플로를 만들어 워크플로에 *HTTP POST* 요청을 보냅니다. 논리 앱 워크플로가 이러한 HTTP 요청을 받으면 트리거가 실행되고 워크플로의 다음 단계를 실행합니다.

1. [Azure Portal](https://portal.azure.com)에서 빈 논리 앱을 만들어 워크플로 디자이너를 엽니다.

1. 검색 상자에서 필터로 `http request`을 입력합니다. **트리거** 목록에서 **HTTP 요청을 수신하는 경우** 트리거를 선택합니다.

   ![논리 앱 워크플로에 새 요청 트리거가 추가되는 워크플로 디자이너를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 요청을 받을 수 있는 엔드포인트 URL을 생성하는 논리 앱 워크플로를 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다. 이제 엔드포인트 URL이 트리거에 표시됩니다.

   ![생성된 POST URL이 복사되는 요청 트리거가 있는 워크플로 디자이너를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>메시지를 보내는 SAP 작업 만들기

다음으로 [요청 트리거](#add-http-request-trigger)가 발생할 때 IDoc 메시지를 SAP로 보내는 작업을 만듭니다. 기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행하여 잘못된 값을 확인하는 데 사용됩니다. 이 동작은 이전에 발생한 문제를 검색하는 데 도움이 될 수 있습니다. **안전한 형식 지정** 옵션은 이전 버전과의 호환성을 위해 제공되며 문자열 길이만 확인합니다. [안전한 형식 지정 옵션](#safe-typing)에 대해 자세히 알아보세요.

1. 워크플로 디자이너의 트리거 아래에서 **새 단계** 를 선택합니다.

   ![새 단계를 추가하기 위해 편집 중인 워크플로가 있는 워크플로 디자이너를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 검색 상자에서 필터로 `send message sap`을 입력합니다. **작업** 목록에서 **SAP로 메시지 보내기** 를 선택합니다.

   !["SAP에 메시지 보내기" 작업이 선택된 워크플로 디자이너를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-sap-send-action.png)

   또는 **Enterprise** 탭을 선택하고 SAP 작업을 선택할 수도 있습니다.

   ![엔터프라이즈 탭에서 "SAP에 메시지 보내기" 작업이 선택된 워크플로 디자이너를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 이미 연결이 있는 경우 다음 단계를 진행합니다. 새 연결을 만들라는 메시지가 표시되면 다음 정보를 입력하여 온-프레미스 SAP 서버에 연결합니다.

   1. 연결 이름을 제공합니다.

   1. 데이터 게이트웨이를 사용하는 경우 다음 단계를 수행합니다.

      1. **데이터 게이트웨이** 섹션의 **구독** 에서, 데이터 게이트웨이를 설치하기 위해 Azure Portal에서 만든 데이터 게이트웨이 리소스의 Azure 구독을 선택합니다.

      1. **연결 게이트웨이** 에서 Azure의 데이터 게이트웨이 리소스를 선택합니다.

   1. **로그온 유형** 속성의 경우 속성이 **애플리케이션 서버** 로 설정되었는지 아니면 **그룹** 으로 설정되었는지 여부에 따라 단계를 수행합니다.

      * **애플리케이션 서버** 로 설정된 경우 이 속성은(보통은 선택 사항) 필수입니다.

        ![SAP 애플리케이션 서버 연결을 만드는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹** 으로 설정된 경우 이 속성은(보통은 선택 사항) 필수입니다.

        ![SAP 메시지 서버 연결을 만드는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        SAP에서 로그온 그룹은 **CCMS: 로그온 그룹 유지**(T-Code SMLG) 대화 상자를 열어 유지 관리됩니다. 자세한 내용은 [SAP Note 26317 - 자동 부하 분산을 위한 로그온 그룹 설정](https://service.sap.com/sap/support/notes/26317)을 검토하세요.

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행하여 잘못된 값을 확인하는 데 사용됩니다. 이 동작은 이전에 발생한 문제를 검색하는 데 도움이 될 수 있습니다. **안전한 형식 지정** 옵션은 이전 버전과의 호환성을 위해 제공되며 문자열 길이만 확인합니다. [안전한 형식 지정 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 작업이 완료되면 **만들기** 를 선택합니다.

      Azure Logic Apps는 연결을 설정하고 테스트하여 연결이 제대로 작동하는지 확인합니다.

      > [!NOTE]
      > 다음 오류가 표시되면 SAP NCo 클라이언트 라이브러리 설치에 문제가 있는 것입니다. 
      >
      > **연결 테스트에 실패했습니다. 오류 '요청을 처리하지 못했습니다. 오류 세부 정보: '파일 또는 어셈블리 'sapnco, Version=3.0.0.42, Culture=neutral, PublicKeyToken 50436dca5c7f7d23' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템에서 지정된 파일을 찾을 수 없습니다.'.'**
      >
      > [필요한 SAP NCo 클라이언트 라이브러리 버전을 설치하고 다른 모든 필수 구성 요소를 충족](#sap-client-library-prerequisites)하는지 확인합니다.

1. 이제 SAP 서버에서 작업을 찾아 선택합니다.

   1. **SAP 작업** 상자에서 폴더 아이콘을 선택합니다. 파일 목록에서 사용하려는 SAP 메시지를 찾아서 선택합니다. 목록을 탐색하려면 화살표를 사용합니다.

      이 예제에서는 **Orders** 유형의 IDoc를 선택합니다.

      ![IDoc 작업을 찾고 선택하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      원하는 작업을 찾을 수 없는 경우 경로를 수동으로 입력할 수 있습니다. 예를 들면 다음과 같습니다.

      ![IDoc 작업에 대한 경로를 수동으로 제공을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 식 편집기를 통해 **SAP 작업** 의 값을 입력합니다. 이와 같은 방식으로 여러 메시지 유형에 동일한 작업을 사용할 수 있습니다.

      IDoc 작업에 대한 자세한 내용은 [IDoc 작업에 대한 메시지 스키마](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 검토하세요.

   1. **입력 메시지** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 해당 목록의 **HTTP 요청을 수신하는 경우** 아래에서 **본문** 필드를 선택합니다.

      이 단계에서는 요청 트리거의 본문 콘텐츠를 포함시키고 해당 출력을 SAP 서버로 전송합니다.

      ![요청 트리거에서 "본문" 속성 선택을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      여기까지 마쳤으면 SAP 작업이 다음 예제와 비슷하게 표시됩니다.

      ![SAP 작업 완료를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 논리 앱 워크플로 저장 디자이너 도구 모음에서 **저장** 을 선택합니다.

### <a name="send-flat-file-idocs"></a>플랫 파일 IDoc 보내기

IDoc를 XML 봉투에 래핑하는 경우 플랫 파일 스키마에 IDoc를 사용할 수 있습니다. 플랫 파일 IDoc를 보내려면 [IDoc 메시지를 보내는 SAP 작업 만들기](#create-sap-action-to-send-message) 일반 지침을 따르고 다음과 같이 변경합니다.

1. **SAP로 메시지 보내기** 작업의 경우 SAP 작업 URI `http://Microsoft.LobServices.Sap/2007/03/Idoc/SendIdoc`를 사용합니다.

1. XML 봉투를 사용하여 입력 메시지의 서식을 지정합니다.

예를 들어 다음 예 XML 페이로드를 검토합니다.

```xml
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
<idocData>EDI_DC 3000000001017945375750 30INVOIC011BTSVLINV30KUABCABCFPPC LDCA X004010810 4 SAPMSX LSEDI ABCABCFPPC 000d3ae4-723e-1edb-9ca4-cc017365c9fd 20210217054521INVOICINVOIC01ZINVOIC2RE 20210217054520
E2EDK010013000000001017945375000001E2EDK01001000000010 ABCABC1.00000 0060 INVO9988298128 298.000 298.000 LB Z4LR EN 0005065828 L
E2EDKA1 3000000001017945375000002E2EDKA1 000000020 RS ABCABCFPPC 0005065828 ABCABCABC ABCABC Inc. Limited Risk Distributor ABCABC 1950 ABCABCABCA Blvd ABCABAABCAB L5N8L9 CA ABCABC E ON V-ABCABC LDCA
E2EDKA1 3000000001017945375000003E2EDKA1 000000020 AG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000004E2EDKA1 000000020 RE 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000005E2EDKA1 000000020 RG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000006E2EDKA1 000000020 WE 0005001847 41 ABCABC ABCABC INC (ABCABC) DC A. ABCABCAB 88 ABCABC CRESCENT ABCABAABCAB L5R 4A2 CA ABCABC 111-111-1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000007E2EDKA1 000000020 Z3 0005533050 ABCABCABC ABCABC Inc. ABCA Bank Swift Code -ABCABCABCAB Sort Code - 1950 ABCABCABCA Blvd. Acc No -1111111111 ABCABAABCAB L5N8L9 CA ABCABC E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000008E2EDKA1 000000020 BK 1075 ABCABCABC ABCABC Inc 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDKA1 3000000001017945375000009E2EDKA1 000000020 CR 1075 CONTACT ABCABCABC 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDK02 3000000001017945375000010E2EDK02 000000020 0099988298128 20210217
E2EDK02 3000000001017945375000011E2EDK02 000000020 00140-N6260-S 20210205
E2EDK02 3000000001017945375000012E2EDK02 000000020 0026336270425 20210217
E2EDK02 3000000001017945375000013E2EDK02 000000020 0128026580537 20210224
E2EDK02 3000000001017945375000014E2EDK02 000000020 01740-N6260-S
E2EDK02 3000000001017945375000015E2EDK02 000000020 900IAC
E2EDK02 3000000001017945375000016E2EDK02 000000020 901ZSH
E2EDK02 3000000001017945375000017E2EDK02 000000020 9078026580537 20210217
E2EDK03 3000000001017945375000018E2EDK03 000000020 02620210217
E2EDK03 3000000001017945375000019E2EDK03 000000020 00120210224
E2EDK03 3000000001017945375000020E2EDK03 000000020 02220210205
E2EDK03 3000000001017945375000021E2EDK03 000000020 01220210217
E2EDK03 3000000001017945375000022E2EDK03 000000020 01120210217
E2EDK03 3000000001017945375000023E2EDK03 000000020 02420210217
E2EDK03 3000000001017945375000024E2EDK03 000000020 02820210418
E2EDK03 3000000001017945375000025E2EDK03 000000020 04820210217
E2EDK17 3000000001017945375000026E2EDK17 000000020 001DDPDelivered Duty Paid
E2EDK17 3000000001017945375000027E2EDK17 000000020 002DDPdestination
E2EDK18 3000000001017945375000028E2EDK18 000000020 00160 0 Up to 04/18/2021 without deduction
E2EDK28 3000000001017945375000029E2EDK28 000000020 CA BOFACATT Bank of ABCABAB ABCABC ABCABAB 50127217 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000030E2EDK28 000000020 CA 026000082 ABCAbank ABCABC ABCABAB 201456700OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000031E2EDK28 000000020 GB ABCAGB2L ABCAbank N.A ABCABA E14, 5LB GB63ABCA18500803115593 ABCABCABC ABCABC Inc. GB63ABCA18500803115593
E2EDK28 3000000001017945375000032E2EDK28 000000020 CA 020012328 ABCABANK ABCABC ABCABAB ON M5J 2M3 2014567007 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000033E2EDK28 000000020 CA 03722010 ABCABABC ABCABABC Bank of Commerce ABCABAABCAB 64-04812 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000034E2EDK28 000000020 IE IHCC In-House Cash Center IHCC1075 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000035E2EDK28 000000020 CA 000300002 ABCAB Bank of ABCABC ABCABAB 0021520584OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000036E2EDK28 000000020 US USCC US Cash Center (IHC) city USCC1075 ABCABCABC ABCABC Inc.
E2EDK29 3000000001017945375000037E2EDK29 000000020 0064848944US A CAD CA ABCABC CA United States US CA A Air Air
E2EDKT1 3000000001017945375000038E2EDKT1 000000020 ZJ32E EN
E2EDKT2 3000000001017945375000039E2EDKT2 000038030 GST/HST877845941RT0001 *
E2EDKT2 3000000001017945375000040E2EDKT2 000038030 QST1021036966TQ0001 *
E2EDKT1 3000000001017945375000041E2EDKT1 000000020 Z4VL
E2EDKT2 3000000001017945375000042E2EDKT2 000041030 0.000 *
E2EDKT1 3000000001017945375000043E2EDKT1 000000020 Z4VH
E2EDKT2 3000000001017945375000044E2EDKT2 000043030 *
E2EDK14 3000000001017945375000045E2EDK14 000000020 008LDCA
E2EDK14 3000000001017945375000046E2EDK14 000000020 00710
E2EDK14 3000000001017945375000047E2EDK14 000000020 00610
E2EDK14 3000000001017945375000048E2EDK14 000000020 015Z4F2
E2EDK14 3000000001017945375000049E2EDK14 000000020 0031075
E2EDK14 3000000001017945375000050E2EDK14 000000020 021M
E2EDK14 3000000001017945375000051E2EDK14 000000020 0161075
E2EDK14 3000000001017945375000052E2EDK14 000000020 962M
E2EDP010013000000001017945375000053E2EDP01001000000020 000011 2980.000 EA 298.000 LB MOUSE 298.000 Z4TN 4260
E2EDP02 3000000001017945375000054E2EDP02 000053030 00140-N6260-S 00000120210205 DFUE
E2EDP02 3000000001017945375000055E2EDP02 000053030 0026336270425 00001120210217
E2EDP02 3000000001017945375000056E2EDP02 000053030 0168026580537 00001020210224
E2EDP02 3000000001017945375000057E2EDP02 000053030 9100000 00000120210205 DFUE
E2EDP02 3000000001017945375000058E2EDP02 000053030 911A 00000120210205 DFUE
E2EDP02 3000000001017945375000059E2EDP02 000053030 912PP 00000120210205 DFUE
E2EDP02 3000000001017945375000060E2EDP02 000053030 91300 00000120210205 DFUE
E2EDP02 3000000001017945375000061E2EDP02 000053030 914CONTACT ABCABCABC 00000120210205 DFUE
E2EDP02 3000000001017945375000062E2EDP02 000053030 963 00000120210205 DFUE
E2EDP02 3000000001017945375000063E2EDP02 000053030 965 00000120210205 DFUE
E2EDP02 3000000001017945375000064E2EDP02 000053030 9666336270425 00000120210205 DFUE
E2EDP02 3000000001017945375000065E2EDP02 000053030 9078026580537 00001020210205 DFUE
E2EDP03 3000000001017945375000066E2EDP03 000053030 02920210217
E2EDP03 3000000001017945375000067E2EDP03 000053030 00120210224
E2EDP03 3000000001017945375000068E2EDP03 000053030 01120210217
E2EDP03 3000000001017945375000069E2EDP03 000053030 02520210217
E2EDP03 3000000001017945375000070E2EDP03 000053030 02720210217
E2EDP03 3000000001017945375000071E2EDP03 000053030 02320210217
E2EDP03 3000000001017945375000072E2EDP03 000053030 02220210205
E2EDP19 3000000001017945375000073E2EDP19 000053030 001418VVZ
E2EDP19 3000000001017945375000074E2EDP19 000053030 002RJR-00001 AB ABCABCABC Mouse FORBUS BLUETOOTH
E2EDP19 3000000001017945375000075E2EDP19 000053030 0078471609000
E2EDP19 3000000001017945375000076E2EDP19 000053030 003889842532685
E2EDP19 3000000001017945375000077E2EDP19 000053030 011CN
E2EDP26 3000000001017945375000078E2EDP26 000053030 00459064.20
E2EDP26 3000000001017945375000079E2EDP26 000053030 00352269.20
E2EDP26 3000000001017945375000080E2EDP26 000053030 01052269.20
E2EDP26 3000000001017945375000081E2EDP26 000053030 01152269.20
E2EDP26 3000000001017945375000082E2EDP26 000053030 0126795.00
E2EDP26 3000000001017945375000083E2EDP26 000053030 01552269.20
E2EDP26 3000000001017945375000084E2EDP26 000053030 00117.54
E2EDP26 3000000001017945375000085E2EDP26 000053030 00252269.20
E2EDP26 3000000001017945375000086E2EDP26 000053030 940 2980.000
E2EDP26 3000000001017945375000087E2EDP26 000053030 939 2980.000
E2EDP05 3000000001017945375000088E2EDP05 000053030 + Z400MS List Price 52269.20 17.54 1 EA CAD 2980
E2EDP05 3000000001017945375000089E2EDP05 000053030 + XR1 Tax Jur Code Level 6795.00 13.000 52269.20
E2EDP05 3000000001017945375000090E2EDP05 000053030 + Tax Subtotal1 6795.00 2.28 1 EA CAD 2980
E2EDP05 3000000001017945375000091E2EDP05 000053030 + Taxable Amount + TaxSubtotal1 59064.20 19.82 1 EA CAD 2980
E2EDP04 3000000001017945375000092E2EDP04 000053030 CX 13.000 6795.00 7000000000
E2EDP04 3000000001017945375000093E2EDP04 000053030 CX 0 0 7001500000
E2EDP04 3000000001017945375000094E2EDP04 000053030 CX 0 0 7001505690
E2EDP28 3000000001017945375000095E2EDP28 000053030 00648489440000108471609000 CN CN ABCAB ZZ 298.000 298.000 LB US 400 United Stat KY
E2EDPT1 3000000001017945375000096E2EDPT1 000053030 0001E EN
E2EDPT2 3000000001017945375000097E2EDPT2 000096040 AB ABCABCABC Mouse forBus Bluetooth EN/XC/XD/XX Hdwr Black For Bsnss *
E2EDS01 3000000001017945375000098E2EDS01 000000020 0011
E2EDS01 3000000001017945375000099E2EDS01 000000020 01259064.20 CAD
E2EDS01 3000000001017945375000100E2EDS01 000000020 0056795.00 CAD
E2EDS01 3000000001017945375000101E2EDS01 000000020 01159064.20 CAD
E2EDS01 3000000001017945375000102E2EDS01 000000020 01052269.20 CAD
E2EDS01 3000000001017945375000103E2EDS01 000000020 94200000 CAD
E2EDS01 3000000001017945375000104E2EDS01 000000020 9440.00 CAD
E2EDS01 3000000001017945375000105E2EDS01 000000020 9450.00 CAD
E2EDS01 3000000001017945375000106E2EDS01 000000020 94659064.20 CAD
E2EDS01 3000000001017945375000107E2EDS01 000000020 94752269.20 CAD
E2EDS01 3000000001017945375000108E2EDS01 000000020 EXT
Z2XSK010003000000001017945375000109Z2XSK01000000108030 Z400 52269.20
Z2XSK010003000000001017945375000110Z2XSK01000000108030 XR1 13.000 6795.00 CX
</idocData>
</SendIdoc>
```

### <a name="create-http-response-action"></a>HTTP 응답 작업 만들기

이제 논리 앱의 워크플로에 응답 작업을 추가하고 SAP 작업의 출력을 포함합니다. 이렇게 하면 논리 앱 워크플로가 SAP 서버의 결과를 원래 요청자에게 반환합니다.

1. 워크플로 디자이너의 SAP 작업에서 **새 단계** 를 선택합니다.

1. 검색 상자에서 필터로 `response`을 입력합니다. **작업** 목록에서 **응답** 을 선택합니다.

1. **본문** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **SAP로 메시지 보내기** 에서 **본문** 필드를 선택합니다.

   ![SAP 작업 완료를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 논리 앱 워크플로 저장

### <a name="create-rfc-request-response"></a>RFC 요청-응답 만들기

Azure Logic Apps에 대한 RFC(원격 함수 호출)를 사용하여 SAP ABAP에서 응답을 수신해야 하는 경우 요청-응답 패턴을 만들어야 합니다. 논리 앱 워크플로에서 IDoc를 수신하려면 워크플로의 첫 번째 작업을 상태 코드가 `200 OK`이고 콘텐츠가 없는 [응답 작업](../connectors/connectors-native-reqres.md#add-a-response-action)으로 만들어야 합니다. 이 권장 단계에서는 tRFC를 통해 즉시 SAP LUW(Logical Unit of Work) 비동기 전송을 완료합니다. 그러면 SAP CPIC 대화를 다시 사용할 수 있습니다. 그런 다음, 더 이상 전송을 차단하지 않고 수신된 IDoc를 처리하도록 논리 앱 워크플로에 작업을 추가할 수 있습니다.

> [!NOTE]
> SAP 트리거는 응답 매개 변수가 없는 tRFC를 통해 IDoc를 수신합니다.

요청-응답 패턴을 구현하려면 먼저 [`generate schema` 명령](#generate-schemas-for-artifacts-in-sap)을 사용하여 RFC 스키마를 찾아야 합니다. 생성된 스키마에는 두 개의 루트 노드가 있을 수 있습니다. 

* 하나는 SAP에서 수신하는 호출인 요청 노드입니다.
* 다른 하나는 SAP에 대한 회신인 응답 노드입니다.

다음은 `STFC_CONNECTION` RFC 모듈에서 요청-응답 패턴이 생성되는 예제입니다. 요청 XML을 구문 분석하여 SAP가 `<ECHOTEXT>`를 요청하는 노드 값을 추출합니다. 응답은 현재 타임스탬프를 동적 값으로 삽입합니다. 논리 앱 워크플로에서 SAP로 `STFC_CONNECTION` RFC를 보낼 때 비슷한 응답을 받게 됩니다.

```xml
<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>
```

### <a name="test-your-logic-app-workflow"></a>논리 앱 워크플로 테스트

1. 논리 앱을 사용할 수 없는 경우 논리 앱 메뉴에서 **개요** 를 선택합니다. 도구 모음에서 **사용** 을 선택합니다.

1. 디자이너 도구 모음에서 **실행** 을 선택합니다. 이 단계에서는 논리 앱 워크플로를 수동으로 시작합니다.

1. 요청 트리거의 URL에 HTTP POST 요청을 전송하여 논리 앱 워크플로를 트리거합니다. 요청에 메시지 콘텐츠를 포함합니다. 요청을 보내려면 [Postman](https://www.getpostman.com/apps)과 같은 도구를 사용할 수 있습니다.

   이 문서에서는 요청이 XML 형식으로, 사용 중인 SAP 작업에 대한 네임스페이스를 포함해야 하는 IDoc 파일을 보냅니다. 예를 들면 다음과 같습니다.

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
   <idocData>
   <...>
   </idocData>
   </Send>
   ```

1. HTTP 요청을 보낸 후 논리 앱 워크플로의 응답을 기다립니다.

   > [!NOTE]
   > 응답에 필요한 모든 단계가 [요청 시간 제한](logic-apps-limits-and-config.md) 내에 완료되지 않을 경우 논리 앱 워크플로가 시간 초과될 수 있습니다. 이 조건이 발생하면 요청이 차단될 수 있습니다. 문제를 진단하려면 [논리 앱을 확인하고 모니터링](monitor-logic-apps.md)하는 방법을 알아보세요.

이제 SAP 서버와 통신할 수 있는 논리 앱 워크플로를 만들었습니다. 논리 앱 워크플로에 대한 SAP 연결을 설정했으므로, BAPI 및 RFC와 같은 다른 사용 가능한 SAP 작업을 살펴볼 수 있습니다.

<a name="receive-message-sap"></a>

## <a name="receive-message-from-sap"></a>SAP에서 메시지 수신

이 예에서는 앱이 SAP 시스템에서 메시지를 받을 때 트리거되는 논리 앱 워크플로를 사용합니다.

### <a name="add-an-sap-trigger"></a>SAP 트리거 추가

1. Azure Portal에서 빈 논리 앱을 만들어 워크플로 디자이너를 엽니다.

1. 검색 상자에서 필터로 `when message received sap`을 입력합니다. **트리거** 목록에서 **SAP에서 메시지를 수신하는 경우** 를 선택합니다.

   ![SAP 트리거 추가를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   또는 **Enterprise** 탭을 선택하고 트리거를 선택할 수도 있습니다.

   ![엔터프라이즈 탭에서 SAP 트리거를 추가하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 이미 연결된 경우 SAP 트리거를 설정할 수 있도록 다음 단계를 계속 진행합니다. 그러나 연결 정보를 입력하라는 메시지가 표시되면 온-프레미스 SAP 서버에 연결할 수 있도록 정보를 입력합니다.

   1. 연결 이름을 제공합니다.

   1. 데이터 게이트웨이를 사용하는 경우 다음 단계를 수행합니다.

      1. **데이터 게이트웨이** 섹션의 **구독** 에서, 데이터 게이트웨이를 설치하기 위해 Azure Portal에서 만든 데이터 게이트웨이 리소스의 Azure 구독을 선택합니다.

      1. **연결 게이트웨이** 에서 Azure의 데이터 게이트웨이 리소스를 선택합니다.

   1. 연결 정보를 계속 입력합니다. **로그온 유형** 속성의 경우 속성이 **애플리케이션 서버** 로 설정되었는지 아니면 **그룹** 으로 설정되었는지 여부에 따라 단계를 수행합니다.

      * **애플리케이션 서버** 로 설정된 경우 이 속성은(보통은 선택 사항) 필수입니다.

        ![SAP 애플리케이션 서버에 대한 연결 만들기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹** 으로 설정된 경우 이 속성은(보통은 선택 사항) 필수입니다.

        ![SAP 메시지 서버에 대한 연결 만들기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행하여 잘못된 값을 확인하는 데 사용됩니다. 이 동작은 이전에 발생한 문제를 검색하는 데 도움이 될 수 있습니다. **안전한 형식 지정** 옵션은 이전 버전과의 호환성을 위해 제공되며 문자열 길이만 확인합니다. [안전한 형식 지정 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 작업이 완료되면 **만들기** 를 선택합니다.

      Azure Logic Apps는 연결을 설정하고 테스트하여 연결이 제대로 작동하는지 확인합니다.

1. SAP 시스템 구성에 따라 [필수 매개 변수](#parameters)를 제공하고 이 트리거에 사용할 수 있는 다른 매개 변수를 추가합니다. 예를 들면 다음과 같습니다.

   * IDoc를 일반 XML로 수신하려면 SAP 일반 XML 형식을 지원하는 **SAP에서 메시지를 수신할 때** 트리거에서 **IDOC 형식** 매개 변수를 추가하고 **SapPlainXml** 로 설정합니다.

   * 동일한 SAP 트리거를 사용하여 IDoc를 플랫 파일로 수신하려면 **IDOC 형식** 매개 변수를 추가하고 **FlatFile** 로 설정합니다. [플랫 파일 디코딩 작업](logic-apps-enterprise-integration-flatfile.md)도 사용하는 경우 플랫 파일 스키마에서 `early_terminate_optional_fields` 속성을 사용하고 값을 `true`로 설정해야 합니다.

     이 요구 사항은 tRFC 호출 `IDOC_INBOUND_ASYNCHRONOUS`에서 SAP가 보낸 플랫 파일 IDoc 데이터 레코드가 전체 SDATA 필드 길이로 채워지지 않기 때문에 필요합니다. Azure Logic Apps는 SAP에서 받은 대로 패딩 없이 플랫 파일 IDoc 원본 데이터를 제공합니다. 또한 이 SAP 트리거를 플랫 파일 디코딩 작업과 결합할 때 작업에 제공된 스키마가 일치해야 합니다.

   * [SAP 서버에서 받은 메시지를 필터링하려면 SAP 작업 목록을 지정합니다](#filter-with-sap-actions).

     예를 들어 파일 선택기에서 SAP 작업을 선택합니다.

     ![논리 앱 워크플로에 SAP 작업을 추가하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

     또는 작업을 수동으로 지정할 수 있습니다.

     ![사용하려는 SAP 작업을 수동으로 입력하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

     다음은 두 개 이상의 메시지를 수신하도록 트리거를 설정할 때 작업이 어떻게 나타나는지 보여주는 예제입니다.

     ![여러 메시지를 수신하는 트리거 예를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/example-trigger.png)

     SAP 작업에 대한 자세한 내용은 [IDoc 작업에 대한 메시지 스키마](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 검토하세요.

1. 이제 SAP 시스템에서 메시지 수신을 시작할 수 있도록 논리 앱 워크플로를 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

   이제 논리 앱 워크플로가 SAP 시스템에서 메시지를 수신할 준비가 완료되었습니다.

   > [!NOTE]
   > 이 단계의 SAP 트리거는 폴링 트리거가 아닌 웹후크 기반 트리거입니다. 데이터 게이트웨이를 사용하는 경우 메시지가 존재하는 경우에만 데이터 게이트웨이에서 트리거가 호출되므로 폴링이 필요하지 않습니다.

1. 논리 앱 워크플로의 트리거 기록에서 트리거 등록이 성공했는지 확인합니다.

**서비스 'sapgw00' 알 수 없음** 과 유사한 메시지와 함께 **500 잘못된 게이트웨이** 또는 **400 잘못된 요청** 오류가 수신되는 경우 포트 번호에 대한 네트워크 서비스 이름 확인은 예를 들어 다음과 같습니다.

```json
{
   "body": {
      "error": {
         "code": 500,
         "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
         "clientRequestId": "00000000-0000-0000-0000-000000000000",
         "message": "BadGateway",
         "innerError": {
            "error": {
               "code": "UnhandledException",
               "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
            }
         }
      }
   }
}
```

* **옵션 1:** API 연결 및 트리거 구성에서 게이트웨이 서비스 이름을 해당 포트 번호로 바꿉니다. 오류 예에서는 `sapgw00`을 실제 포트 번호(예: `3300`)로 바꿔야 합니다. 이것은 ISE에 사용할 수 있는 유일한 옵션입니다.

* **옵션 2:** 온-프레미스 데이터 게이트웨이를 사용하는 경우 게이트웨이 서비스 이름을 `%windir%\System32\drivers\etc\services`의 포트 매핑에 추가한 다음 온-프레미스 데이터 게이트웨이 서비스를 다시 시작할 수 있습니다. 예를 들면 다음과 같습니다.

  ```text
  sapgw00  3300/tcp
  ```

SAP 애플리케이션 서버 또는 메시지 서버 이름이 IP 주소로 확인되는 경우 유사한 오류가 발생할 수 있습니다. ISE의 경우 SAP 애플리케이션 서버 또는 메시지 서버의 IP 주소를 지정해야 합니다. 온-프레미스 데이터 게이트웨이의 경우 대신 `%windir%\System32\drivers\etc\hosts`의 IP 주소 매핑에 이름을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

```text
10.0.1.9 SAPDBSERVER01 # SAP System Server VPN IP by computer name
10.0.1.9 SAPDBSERVER01.someguid.xx.xxxxxxx.cloudapp.net # SAP System Server VPN IP by fully qualified computer name
```

### <a name="parameters"></a>매개 변수

SAP 커넥터는 간단한 문자열 및 숫자 입력과 함께 다음 테이블 매개 변수(`Type=ITAB` 입력)를 허용합니다.

* 이전 SAP 릴리스에 대한 테이블 방향 매개 변수(입력 및 출력 모두)

* 새 SAP 릴리스의 테이블 방향 매개 변수를 대체하는 변경 매개 변수

* 계층적 테이블 매개 변수

### <a name="filter-with-sap-actions"></a>SAP 작업으로 필터링

필요에 따라 단일 또는 여러 SAP 작업을 사용하여 목록 또는 배열을 제공하면 논리 앱 워크플로가 SAP 서버에서 수신하는 메시지를 필터링할 수 있습니다. 기본적으로 이 배열은 비어 있습니다. 즉, 논리 앱이 필터링 없이 SAP 서버의 모든 메시지를 수신합니다.

배열 필터를 설정하면 트리거는 지정된 SAP 작업 유형의 메시지만 수신하고 SAP 서버에서 보내는 그 외의 모든 메시지를 거부합니다. 그러나 이 필터는 수신한 페이로드의 형식 지정이 강한지 아니면 약한지에는 영향을 주지 않습니다.

SAP 작업 필터링은 온-프레미스 데이터 게이트웨이의 SAP 어댑터 수준에서 발생합니다. 자세한 내용은 [SAP에서 Azure Logic Apps로 테스트 IDoc를 보내는 방법](#test-sending-idocs-from-sap)을 참조하세요.

SAP에서 논리 앱 워크플로의 트리거로 IDoc 패킷을 보낼 수 없는 경우 SAP tRFC(T-코드 SM58) 대화 상자에서 tRFC(트랜잭션 RFC) 호출 거부 메시지를 검토하세요. SAP 인터페이스에 다음과 같은 오류 메시지가 나타날 수 있습니다. 이 오류 메시지는 **상태 텍스트** 필드의 부분 문자열 제한으로 인해 잘립니다.

#### <a name="the-requestcontext-on-the-ireplychannel-was-closed-without-a-reply-being-sent"></a>IReplyChannel의 RequestContext가 회신 전송 없이 닫혔습니다.

이 오류 메시지는 채널에 대한 모든 catch-all 처리기가 오류로 인해 종료되면 예기치 않은 오류가 발생하며 다른 메시지를 처리하기 위해 채널을 다시 만든다는 것을 의미합니다.

논리 앱 워크플로가 IDoc를 수신했음을 확인하려면 상태 코드 `200 OK`를 반환하는 [응답 작업을 추가](../connectors/connectors-native-reqres.md#add-a-response-action)합니다. 본문을 비워 두고, 헤더를 변경하거나 추가하지 않습니다. IDoc는 응답 페이로드를 허용하지 않는 tRFC를 통해 전송됩니다.

IDoc를 거부하려면 `200 OK` 이외의 HTTP 상태 코드로 응답합니다. 그러면 SAP 어댑터는 사용자를 대신하여 SAP에 다시 예외를 반환합니다. 애플리케이션에서 처리할 수 없는 잘못 라우팅된 IDoc 같은 IDoc만 거부하여 전송 오류를 SAP에 다시 알려야 합니다. IDoc에 포함된 데이터와 관련된 문제처럼 애플리케이션 수준 오류에 대한 IDoc를 거부해서는 안 됩니다. 애플리케이션 수준 유효성 검사를 위한 전송 수락을 지연하는 경우 연결이 다른 IDoc를 전송하지 못하도록 차단하기 때문에 성능이 저하될 수 있습니다.

이 오류 메시지가 수신되고 Azure Logic Apps 호출과 관련된 시스템 오류가 발생하는 경우 해당 환경의 온-프레미스 데이터 게이트웨이 서비스에 대한 네트워크 설정을 구성했는지 확인합니다. 예를 들어 네트워크 환경에서 Azure 엔드포인트를 호출하려면 프록시를 사용해야 하는 경우에는 프록시를 사용하도록 온-프레미스 데이터 게이트웨이 서비스를 구성해야 합니다. 자세한 내용은 [프록시 구성](/dotnet/framework/network-programming/proxy-configuration)을 검토하세요.

이 오류 메시지가 수신되고 Azure Logic Apps를 호출하는 동안 간헐적 오류가 발생하는 경우 다시 시도 횟수 또는 다시 시도 간격을 늘려야 할 수도 있습니다.

1. 온-프레미스 데이터 게이트웨이 서비스 구성 파일 `Microsoft.PowerBI.EnterpriseGateway.exe.config`에서 SAP 설정을 확인합니다.

   다시 시도 횟수 설정은 `WebhookRetryMaximumCount="2"` 형식입니다. 다시 시도 간격 설정은 `WebhookRetryDefaultDelay="00:00:00.10"` 형식이며 시간 간격은 `HH:mm:ss.ff` 형식입니다.

1. 변경 내용을 저장합니다. 온-프레미스 데이터 게이트웨이 다시 시작

#### <a name="the-segment-or-group-definition-e2edk36001-was-not-found-in-the-idoc-meta"></a>IDoc 메타에서 세그먼트 또는 그룹 정의 E2EDK36001을 찾을 수 없습니다.

이 오류 메시지는 예상한 오류가 다른 오류와 함께 발생했다는 뜻입니다. 예를 들어 해당 세그먼트를 SAP에서 해제하지 않았기 때문에 IDoc XML 페이로드를 생성할 수 없습니다. 따라서 변환에 필요한 세그먼트 형식 메타데이터가 없습니다.

SAP에서 이러한 세그먼트를 해제하게 하려면 SAP 시스템의 ABAP 엔지니어에게 문의하세요.

### <a name="asynchronous-request-reply-for-triggers"></a>트리거에 대한 비동기 요청-회신

SAP 커넥터는 Azure Logic Apps 트리거에 Azure의 [비동기 요청-회신 패턴](/azure/architecture/patterns/async-request-reply)을 지원합니다. 이 패턴을 사용하여 성공적인 요청을 만들 수 있으며, 사용하지 않으면 기본 동기 요청-회신 패턴으로 인해 요청이 실패합니다.

> [!TIP]
> 응답 작업이 여러 개 있는 논리 앱 워크플로에서 모든 응답 작업에서 동일한 요청-회신 패턴을 사용해야 합니다. 예를 들어 여러 응답 작업을 사용할 수 있는 스위치 컨트롤을 논리 앱 워크플로에서 사용하는 경우 동일한 요청-응답 패턴(동기 또는 비동기)을 사용하도록 모든 응답 작업을 구성해야 합니다.

응답 작업에 대해 비동기 응답을 사용하도록 설정하면 논리 앱 워크플로가 처리 요청을 수락한 후 `202 Accepted` 회신으로 응답할 수 있습니다. 회신에는 요청의 최종 상태를 검색하는 데 사용할 수 있는 위치 헤더가 포함됩니다.

SAP 커넥터를 사용하여 논리 앱 워크플로에 대한 비동기 요청-회신 패턴을 구성하려면 다음 단계를 따르세요.

1. 워크플로 디자이너에서 논리 앱을 엽니다.

1. SAP 커넥터가 논리 앱 워크플로의 트리거인지 확인합니다.

1. 논리 앱 워크플로의 **응답** 작업을 엽니다. 작업의 제목 표시줄에서 메뉴( **...** ) &gt; **설정** 을 선택합니다.

1. 응답 작업에 대한 **설정** 의 **비동기 응답** 에서 토글을 켭니다. [완료]를 선택합니다.

1. 논리 앱 워크플로 변경 내용을 저장합니다.

## <a name="find-extended-error-logs"></a>확장 오류 로그 찾기

전체 오류 메시지를 보려면 SAP 어댑터의 확장 로그를 확인합니다. [SAP 커넥터에 확장 로그 파일을 사용하도록 설정](#extended-sap-logging-in-on-premises-data-gateway)할 수도 있습니다.

* 2020년 4월 이전에 출시된 온-프레미스 데이터 게이트웨이 릴리스에서는 기본적으로 로그가 사용되지 않습니다.

* 2020년 6월 이후에 출시된 온-프레미스 데이터 게이트웨이 릴리스의 경우 [앱 설정에서 게이트웨이 로그를 사용하도록 설정](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)할 수 있습니다.

  * 기본 로깅 수준은 **경고** 입니다.

  * 온-프레미스 데이터 게이트웨이 앱의 **진단** 설정에서 **추가 로깅** 을 사용하도록 설정하면 로깅 수준이 **정보** 로 상향됩니다.

  * 로깅 수준을 **자세히** 로 상향하려면 구성 파일에서 다음 설정을 업데이트합니다. 일반적으로 구성 파일은 `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config`에 있습니다.

    ```xml
    <setting name="SapTraceLevel" serializeAs="String">
       <value>Verbose</value>
    </setting>
    ```

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이의 확장된 SAP 로깅

[Azure Logic Apps에 온-프레미스 데이터 게이트웨이](logic-apps-gateway-install.md)를 사용하는 경우 SAP 커넥터에 대한 확장 로그 파일을 구성할 수 있습니다. 온-프레미스 데이터 게이트웨이를 사용하여 ETW(Windows용 이벤트 추적) 이벤트를 게이트웨이의 로깅 .zip 파일에 포함된 순환 로그 파일로 리디렉션할 수 있습니다.

게이트웨이 앱의 설정에서 [게이트웨이의 모든 구성 및 서비스 로그를 .zip 파일로 내보낼 수 있습니다](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

> [!NOTE]
> 확장 로깅을 항상 사용하면 논리 앱 워크플로의 성능에 영향을 미칠 수 있습니다. 가장 좋은 방법은 문제를 분석하고 해결한 후에는 확장 로그 파일을 사용하지 않는 것입니다.

#### <a name="capture-etw-events"></a>ETW 이벤트 캡처

필요에 따라 고급 사용자는 ETW 이벤트를 직접 캡처할 수 있습니다. 그런 다음, [Event Hubs의 Azure Diagnostics에서 데이터를 사용](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)하거나 [Azure Monitor 로그에 데이터를 수집](../azure-monitor/agents/diagnostics-extension-logs.md)할 수 있습니다. 자세한 내용은 [데이터 수집 및 저장 모범 사례](/azure/architecture/best-practices/monitoring#collecting-and-storing-data)를 검토하세요. [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md)를 사용하여 결과 ETL 파일을 작업할 수도 있고 프로그램을 직접 작성할 수도 있습니다. 이 연습에서는 PerfView를 사용합니다.

1. PerfView 메뉴에서 **수집** &gt; **수집** 을 선택하여 이벤트를 캡처합니다.

1. **추가 공급자** 필드에 `*Microsoft-LobAdapter`를 입력하여 SAP 어댑터 이벤트를 캡처할 SAP 공급자를 지정합니다. 이 정보를 지정하지 않으면 추적에 일반 ETW 이벤트만 포함됩니다.

1. 다른 기본 설정은 유지합니다. 원한다면 **데이터 파일** 필드에서 파일 이름이나 위치를 변경할 수 있습니다.

1. **수집 시작** 을 선택하여 추적을 시작합니다.

1. 문제를 재현하거나 분석 데이터를 충분히 수집한 다음, **수집 중지** 를 선택합니다.

1. Azure 지원 엔지니어와 같은 다른 사람과 데이터를 공유하려면 ETL 파일을 압축합니다.

1. 추적의 내용을 보려면 다음을 수행합니다.

   1. PerfView에서 **파일** &gt; **열기** 를 선택하고 방금 생성한 ETL 파일을 선택합니다.

   1. PerfView 사이드바의 ETL 파일 아래에서 **이벤트** 섹션을 선택합니다.

   1. **필터** 에서 `Microsoft-LobAdapter`로 필터링하여 관련 이벤트 및 게이트웨이 프로세스만 표시합니다.

### <a name="test-your-workflow"></a>워크플로 테스트

1. 논리 앱 워크플로를 트리거하기 위해, SAP 시스템에서 메시지를 보냅니다.

1. 논리 앱 메뉴에서 **개요** 를 선택합니다. **실행 기록** 을 검토하여 논리 앱 워크플로에 대한 모든 실행을 확인합니다.

1. 가장 최근 실행을 엽니다. 그러면 SAP 시스템에서 보낸 메시지가 트리거 출력 섹션에 표시됩니다.

### <a name="test-sending-idocs-from-sap"></a>SAP에서 IDoc 보내기 테스트

SAP에서 논리 앱 워크플로로 IDoc를 보내려면 다음과 같은 최소 구성이 필요합니다.

> [!IMPORTANT]
> 논리 앱 워크플로에서 SAP 구성을 테스트하는 경우에만 다음 단계를 수행합니다. 프로덕션 환경은 추가 구성이 필요합니다.

1. [SAP에서 RFC 대상 구성](#create-rfc-destination)

1. [RFC 대상에 대한 ABAP 연결 만들기](#create-abap-connection)

1. [수신 포트 만들기](#create-receiver-port)

1. [송신 포트 만들기](#create-sender-port)

1. [논리 시스템 파트너 만들기](#create-logical-system-partner)

1. [파트너 프로필 만들기](#create-partner-profiles)

1. [메시지 보내기 테스트](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC 대상 만들기

1. SAP 인터페이스에서 **RFC 연결 구성** 설정을 열려면 **/n** 접두사와 함께 **sm59** 트랜잭션 코드(T-코드)를 사용합니다.

1. **TCP/IP 연결** > **만들기** 를 선택합니다.

1. 다음 설정을 사용하여 새 RFC 대상을 만듭니다.

    1. **RFC 대상** 에 이름을 입력합니다.

    1. **기술 설정** 탭의 **정품 인증 유형** 에서 **등록된 서버 프로그램** 을 선택합니다.

    1. **프로그램 ID** 에 값을 입력합니다. SAP에서 논리 앱 워크플로의 트리거는 이 식별자를 사용하여 등록됩니다.

       > [!IMPORTANT]
       > SAP **프로그램 ID** 는 대/소문자를 구분합니다. 논리 앱 워크플로와 SAP 서버를 구성할 때 **프로그램 ID** 에 동일한 대/소문자 형식을 일관되게 사용해야 합니다. 그렇지 않으면 SAP에 IDoc를 보내려고 할 때 tRFC 모니터(T-코드 SM58)에서 다음과 같은 오류가 발생할 수 있습니다.
       >
       > * **함수 IDOC_INBOUND_ASYNCHRONOUS 찾을 수 없음**
       > * **ABAP RFC가 아닌 클라이언트(파트너 유형)는 지원되지 않음**
       >
       > SAP에 대한 자세한 내용은 다음 참고 사항을 검토하세요(로그인 필요).
       >
       > * [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329)
       > * [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)

    1. **유니코드** 탭에서 **대상 시스템과 통신 형식** 으로 **유니코드** 를 선택합니다.

       > [!NOTE]
       > SAP .NET 클라이언트 라이브러리는 유니코드 문자 인코딩만 지원합니다. SAP에서 Azure Logic Apps로 IDoc을 보낼 때 `Non-ABAP RFC client (partner type ) not supported` 오류가 발생하면 **대상 시스템이 있는 통신 유형** 값이 **유니코드** 로 설정되어 있는지 확인하세요.

1. 변경 내용을 저장합니다.

1. **SAP에서 메시지가 수신될 때** 라는 SAP 트리거로 시작하는 논리 앱 워크플로를 만들어 Azure Logic Apps에 새 **프로그램 ID** 를 등록합니다.

   이렇게 하면 워크플로를 저장할 때 Azure Logic Apps가 SAP 게이트웨이에 **프로그램 ID** 를 등록합니다.

1. 워크플로의 트리거 기록, 온-프레미스 데이터 게이트웨이 SAP 어댑터 로그 및 SAP 게이트웨이 추적 로그에서 등록 상태를 확인합니다. SAP 게이트웨이 모니터 대화 상자(T-Code SMGW)의 **로그온 클라이언트** 아래에 새 등록이 **등록된 서버** 로 표시되어야 합니다.

1. 연결을 테스트하려면 SAP 인터페이스의 새 **RFC 대상** 에서 **연결 테스트** 를 선택합니다.

#### <a name="create-abap-connection"></a>ABAP 연결 만들기

1. SAP 인터페이스에서 **RFC 연결 구성** 설정을 열려면 _ */n** 접두사와 함께 **sm59** 트랜잭션 코드(T-코드)를 사용합니다.

1. **ABAP 연결** > **만들기** 를 선택합니다.

1. **RFC 대상** 에는 [테스트 SAP 시스템](#create-rfc-destination)의 식별자를 입력합니다.

1. 변경 내용을 저장합니다.

1. 연결을 테스트하려면 **연결 테스트** 를 선택합니다.

#### <a name="create-receiver-port"></a>수신 포트 만들기

1. **IDOC 처리 포트** 설정을 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we21** 트랜잭션 코드(T-코드)를 사용합니다.

1. **포트** > **트랜잭션 RFC** > **만들기** 를 선택합니다.

1. 열리는 설정 상자에서 **자체 포트 이름** 을 선택합니다. 테스트 포트의 **이름** 을 입력합니다. 변경 내용을 저장합니다.

1. 새 수신 포트의 설정에서 [테스트 RFC 대상](#create-rfc-destination)의 식별자를 **RFC 대상** 으로 입력합니다.

1. 변경 내용을 저장합니다.

#### <a name="create-sender-port"></a>송신 포트 만들기

1. **IDOC 처리 포트** 설정을 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we21** 트랜잭션 코드(T-코드)를 사용합니다.

1. **포트** > **트랜잭션 RFC** > **만들기** 를 선택합니다.

1. 열리는 설정 상자에서 **자체 포트 이름** 을 선택합니다. **SAP** 로 시작하는 테스트 포트의 **이름** 입력합니다. 모든 송신 포트 이름은 **SAP** 로 시작해야 합니다(예: **SAPTEST**). 변경 내용을 저장합니다.

1. 새 수신 포트의 설정에서 [ABAP 연결](#create-abap-connection)의 식별자를 **RFC 대상** 으로 입력합니다.

1. 변경 내용을 저장합니다.

#### <a name="create-logical-system-partner"></a>논리 시스템 파트너 만들기

1. **변경 보기 "논리 시스템": 개요** 설정을 열려면 SAP 인터페이스에서 **bd54** 트랜잭션 코드(T-코드)를 사용합니다.

1. 표시되는 **주의: 테이블이 교차 클라이언트입니다** 경고 메시지를 수락합니다.

1. 기존 논리 시스템을 표시하는 목록 위에서 **새 항목** 을 선택합니다.

1. 새 논리 시스템에 대해 **Log.System** 식별자와 짧은 **이름** 설명을 입력합니다. 변경 내용을 저장합니다.

1. **워크벤치 프롬프트** 가 표시되면 설명을 입력하여 새 요청을 만들거나, 요청을 이미 만든 경우 이 단계를 건너뜁니다.

1. 워크벤치 요청을 만든 후에는 해당 요청을 테이블 업데이트 요청에 연결합니다. 테이블이 업데이트되었는지 확인하려면 변경 내용을 저장합니다.

#### <a name="create-partner-profiles"></a>파트너 프로필 만들기

프로덕션 환경에서는 두 개의 파트너 프로필을 만들어야 합니다. 첫 번째 프로필은 발신자(조직 및 SAP 시스템)용입니다. 두 번째 프로필은 수신자(논리 앱)용입니다.

1. **파트너 프로필** 설정을 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we20** 트랜잭션 코드(T_코드)를 사용합니다.

1. **파트너 프로필** 에서 **파트너 유형 LS** > **만들기** 를 선택합니다.

1. 다음 설정을 사용하여 새 파트너 프로필을 만듭니다.

    * **파트너 번호** 에 [논리 시스템 파트너의 식별자](#create-logical-system-partner)를 입력합니다.

    * **파트너 유형** 에는 **LS** 를 입력합니다.

    * **에이전트** 에는 Azure Logic Apps 또는 다른 비 SAP 시스템의 프로그램 식별자를 등록할 때 사용할 SAP 사용자 계정의 식별자를 입력합니다.

1. 변경 내용을 저장합니다. [논리 시스템 파트너를 만들지](#create-logical-system-partner) 않은 경우 **올바른 파트너 번호를 입력하세요** 오류가 발생합니다.

1. 파트너 프로필 설정의 **아웃바운드 매개 변수** 에서 **아웃바운드 매개 변수 만들기** 를 선택합니다.

1. 다음 설정을 사용하여 새 아웃바운드 매개 변수를 만듭니다.

    * **메시지 유형** 을 입력합니다(예: **CREMAS**).

    * [수신 포트의 식별자](#create-receiver-port)를 입력합니다.

    * **패키지 크기** 에 대한 IDoc 크기를 입력합니다. 또는 [SAP에서 한 번에 하나씩 IDoc를 보내려면](#receive-idoc-packets-from-sap) **즉시 IDoc 전달** 을 선택합니다.

1. 변경 내용을 저장합니다.

#### <a name="test-sending-messages"></a>메시지 보내기 테스트

1. **IDoc 처리 테스트 도구** 설정을 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we19** 트랜잭션 코드(T-코드)를 사용합니다.

1. **테스트용 템플릿** 에서 **Via message type(메시지 유형을 통해)** 을 선택하고 메시지 유형을 입력합니다(예: **CREMAS**). **만들기** 를 선택합니다.

1. **계속** 을 선택하여 **Which IDoc type?(어떤 IDoc 유형입니까?)** 메시지를 확인합니다.

1. **EDIDC** 노드를 선택합니다. 수신 포트 및 송신 포트의 적절한 값을 입력합니다. **계속** 을 선택합니다.

1. **표준 아웃바운드 처리** 를 선택합니다.

1. 아웃바운드 IDoc 처리를 시작하려면 **계속** 을 선택합니다. 처리가 완료되면 **IDoc sent to SAP system or external program(IDoc를 SAP 시스템 또는 외부 프로그램으로 보냈습니다)** 메시지가 표시됩니다.

1. 처리 오류를 확인하려면 **/n** 접두사와 함께 **sm58** 트랜잭션 코드(T-코드)를 사용합니다.

## <a name="receive-idoc-packets-from-sap"></a>SAP에서 IDoc 패킷 수신

IDoc 일괄 처리 또는 그룹인 [패킷으로 IDoc를 보내도록](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html) SAP를 설정할 수 있습니다. 추가 구성이 없어도 IDoc 패킷, SAP 커넥터, 그리고 특히 트리거를 수신할 수 있습니다. 그러나 트리거가 패킷을 받은 후 IDoc 패킷의 각 항목을 처리하려면 패킷을 개별 IDoc로 분할하기 위한 몇 가지 추가 단계가 필요합니다.

다음은 [`xpath()` 함수](./workflow-definition-language-functions-reference.md#xpath)를 사용하여 패킷에서 개별 IDoc를 추출하는 방법을 보여주는 예제입니다.

1. 시작하려면 SAP 트리거를 사용하는 논리 앱 워크플로가 필요합니다. 논리 앱 워크플로에 이 트리거가 아직 없는 경우 이 항목의 이전 단계를 수행하여 [SAP 트리거를 사용하여 논리 앱 워크플로를 설정](#receive-message-from-sap)합니다.

   > [!IMPORTANT]
   > SAP **프로그램 ID** 는 대/소문자를 구분합니다. 논리 앱 워크플로와 SAP 서버를 구성할 때 **프로그램 ID** 에 동일한 대/소문자 형식을 일관되게 사용해야 합니다. 그렇지 않으면 SAP에 IDoc를 보내려고 할 때 tRFC 모니터(T-코드 SM58)에서 다음과 같은 오류가 발생할 수 있습니다.
   >
   > * **함수 IDOC_INBOUND_ASYNCHRONOUS 찾을 수 없음**
   > * **ABAP RFC가 아닌 클라이언트(파트너 유형)는 지원되지 않음**
   >
   > SAP에 대한 자세한 내용은 다음 참고 사항(로그인 필요)을 검토하세요([https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329) 및 [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)).

   예를 들면 다음과 같습니다.

   ![논리 앱 워크플로에 SAP 트리거를 추가하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. [논리 앱 워크플로에 응답 작업을 추가](../connectors/connectors-native-reqres.md#add-a-response-action)하여 SAP 요청 상태를 즉시 회신합니다. SAP 서버와의 통신 채널을 해제하도록 트리거 바로 뒤에 이 작업을 추가하는 것이 가장 좋습니다. 다음 상태 코드(`statusCode`) 중에서 응답 작업에 사용할 코드를 하나 선택합니다.

    * **202 Accepted**: 요청 처리가 수락되었지만 처리가 아직 완료되지 않았다는 뜻입니다.

    * **204 No Content**: 서버에서 요청을 성공적으로 수행했으며 응답 페이로드 본문에 보낼 추가 콘텐츠가 없다는 뜻입니다. 

    * **200 OK**. 서버에서 길이가 0인 페이로드 본문을 생성하는 경우에도 이 상태 코드는 항상 페이로드를 포함합니다. 

1. 논리 앱 워크플로가 SAP에서 수신하는 XML IDoc의 루트 네임스페이스를 가져옵니다. XML 문서에서 이 네임스페이스를 추출하려면 `xpath()` 식을 사용하여 로컬 문자열 변수를 만들고 해당 네임스페이스를 저장하는 단계를 추가합니다.

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![IDoc에서 루트 네임스페이스를 가져오는 것을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 개별 IDoc를 추출하려면 또 다른 `xpath()` 식을 사용하여 배열 변수를 만들고 IDoc 컬렉션을 저장하는 단계를 추가합니다.

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![항목 배열 가져오기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/get-array.png)

   배열 변수를 사용하면 컬렉션을 열거하여 논리 앱 워크플로에 제공되는 각 IDoc에서 개별적으로 처리할 수 있습니다. 이 예의 논리 앱 워크플로는 루프를 사용하여 각 IDoc를 SFTP 서버로 전송합니다.

   ![IDoc를 SFTP 서버로 보내기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/loop-batch.png)

   각 IDoc는 루트 네임스페이스를 포함해야 합니다. 이러한 이유로 IDoc를 다운스트림 앱 또는 이 예제처럼 SFTP 서버로 보내기 전에 파일 콘텐츠를 루트 네임스페이스와 함께 `<Receive></Receive>` 요소 내에 래핑하는 것입니다.

새 논리 앱 워크플로를 만들 때 워크플로 디자이너에서 이 템플릿을 선택하여 이 패턴의 빠른 시작 템플릿을 사용할 수 있습니다.

![일괄 논리 앱 템플릿 선택을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP에서 아티팩트에 대한 스키마 생성

이 예에서는 HTTP 요청으로 트리거할 수 있는 논리 앱 워크플로를 사용합니다. 지정된 IDoc 및 BAPI에 대한 스키마를 생성하기 위해 SAP 작업 **스키마 생성** 에서는 SAP 시스템에 요청을 보냅니다.

이 SAP 작업은 XML 문서 자체의 콘텐츠나 데이터가 아닌 [XML 스키마](#sample-xml-schemas)를 반환합니다. 응답에 반환된 스키마는 Azure Resource Manager 커넥터를 사용하여 통합 계정에 업로드됩니다. 스키마에는 다음과 같은 부분이 포함됩니다.

* 요청 메시지의 구조. 이 정보를 사용하여 BAPI `get` 목록을 구성합니다.

* 응답 메시지의 구조. 이 정보를 사용하여 응답을 구문 분석합니다.

요청 메시지를 보내려면 일반 SAP 작업 **SAP에 메시지 보내기** 또는 대상화된 **\[BAPI] SAP에서 메서드 호출** 작업을 사용합니다.

### <a name="sample-xml-schemas"></a>샘플 XML 스키마

샘플 문서를 만드는 데 사용할 XML 스키마를 생성하는 방법을 알아보려면 다음 샘플을 검토하세요. 아래 예제에서는 다음을 포함하여 여러 가지 유형의 페이로드를 사용하는 방법을 보여줍니다.

* [RFC 요청](#xml-samples-for-rfc-requests)

* [BAPI 요청](#xml-samples-for-bapi-requests)

* [IDoc 요청](#xml-samples-for-idoc-requests)

* 단순 또는 복합 XML 스키마 데이터 형식

* 테이블 매개 변수

* 선택적 XML 동작

선택적 XML 프롤로그를 사용하여 XML 스키마를 시작할 수 있습니다. SAP 커넥터는 XML 프롤로그와 함께 또는 XML 프롤로그 없이 작동합니다.

```xml
<?xml version="1.0" encoding="utf-8">
```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 요청에 대한 XML 샘플

다음 예제는 기본적인 RFC 호출입니다. RFC 이름은 `STFC_CONNECTION`입니다. 이 요청에서는 기본 네임스페이스 `xmlns=`를 사용하지만, `xmmlns:exampleAlias=`와 같은 네임스페이스 별칭을 할당하고 사용할 수 있습니다. 네임스페이스 값은 Microsoft 서비스용 SAP의 모든 RFC에 대한 네임스페이스입니다. `<REQUTEXT>` 요청에는 단순 입력 매개 변수가 있습니다.

```xml
<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>
```

다음 예제는 테이블 매개 변수가 있는 RFC 호출입니다. 이 예제 호출과 해당 테스트 RFC 그룹은 모든 SAP 시스템의 일부로 사용할 수 있습니다. 테이블 매개 변수의 이름은 `TCPICDAT`입니다. 테이블 선 유형은 `ABAPTEXT`이며, 이 요소는 테이블의 행마다 반복됩니다. 이 예제에는 `LINE`이라는 단일 줄이 포함되어 있습니다. 테이블 매개 변수가 있는 요청은 필드를 임의의 개수만큼 포함할 수 있으며, 여기서 숫자는 양의 정수(*n*)입니다. 

```xml
<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <RESTART_QNAME>exampleQName</RESTART_QNAME>
   <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
   </TCPICDAT>
</STFC_WRITE_TO_TCPIC>
```

> [!NOTE]
> SAP Logon의 데이터 브라우저(T-Code SE16)를 사용하여 RFC **STFC_WRITE_TO_TCPIC** 의 결과를 확인합니다. 테이블 이름 **TCPIC** 를 사용합니다.

다음 예제는 익명 필드가 있는 테이블 매개 변수가 포함된 RFC 호출입니다. 익명 필드는 이름이 할당되지 않은 필드입니다. 복합 형식은 별도의 네임스페이스에 선언되며, 이 선언을 통해 현재 노드와 모든 자식 요소의 새로운 기본값이 설정됩니다. 이 예제에서는 */* 기호의 이스케이프 문자로 16진수 코드 `x002F`를 사용합니다. 이 기호는 SAP 필드 이름에 예약되어 있기 때문입니다.

```xml
<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <IM_XML_TABLE>
      <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
      </RFC_XMLCNT>
   </IM_XML_TABLE>
</RFC_XML_TEST_1>
```

다음 예제는 네임스페이스의 접두사를 포함하고 있습니다. 모든 접두사를 한꺼번에 선언할 수도 있고, 원하는 수의 접두사를 노드 특성으로 선언할 수도 있습니다. RFC 네임스페이스 별칭 `ns0`은 기본 형식의 루트 및 매개 변수로 사용됩니다.

> [!NOTE]
> 복합 형식은 별칭 `ns0`을 사용하는 일반 RFC 네임스페이스 대신 별칭 `ns3`을 사용하는 RFC 형식의 다른 네임스페이스 아래에 선언됩니다.

```xml
<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
   <ns0:DELIMITER>0</ns0:DELIMITER>
   <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
   <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
   <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
   <ns0:FIELDS>
      <ns3:RFC_DB_FLD>
         <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
      </ns3:RFC_DB_FLD>
   </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>
```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 요청에 대한 XML 샘플

다음 XML 샘플은 [BAPI 메서드를 호출](#actions)하는 요청 예제입니다.

> [!NOTE]
> SAP는 외부 시스템에서도 비즈니스 개체를 사용할 수 있도록 Azure Logic Apps가 입력 필터 없이 실행하는 RFC `RPY_BOR_TREE_INIT`에 대한 응답으로 비즈니스 개체를 설명합니다. Logic Apps는 출력 테이블 `BOR_TREE`를 검사합니다. `SHORT_TEXT` 필드는 비즈니스 개체의 이름에 사용됩니다. SAP가 출력 테이블에 반환하지 않은 비즈니스 개체는 Azure Logic Apps에서 액세스할 수 없습니다.
> 
> 사용자 지정 비즈니스 개체를 사용하는 경우 SAP에서 이러한 비즈니스 개체를 게시하고 릴리스해야 합니다. 그렇지 않으면 SAP는 사용자 지정 비즈니스 개체를 출력 테이블 `BOR_TREE`에 나열하지 않습니다. SAP에서 비즈니스 개체를 노출할 때까지 Logic Apps의 사용자 지정 비즈니스 개체에 액세스할 수 없습니다. 

다음 예제에서는 BAPI 메서드 `GETLIST`를 사용하여 은행 목록을 가져옵니다. 이 샘플에는 은행 `BUS1011`의 비즈니스 개체가 포함되어 있습니다.

```xml
<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
   <BANK_CTRY>US</BANK_CTRY>
   <MAX_ROWS>10</MAX_ROWS>
</GETLIST>
```

다음 예제에서는 `CREATE` 메서드를 사용하여 은행 개체를 만듭니다. 이 예제에서는 이전 예제와 동일한 비즈니스 개체 `BUS1011`을 사용합니다. `CREATE` 메서드를 사용하여 은행을 만드는 경우 이 메서드는 기본적으로 커밋되지 않으므로 변경 내용을 커밋해야 합니다.

> [!TIP]
> XML 문서가 SAP 시스템에 구성된 유효성 검사 규칙을 따르는지 확인합니다. 예를 들어 이 샘플 문서의 은행 키(`<BANK_KEY>`)는 미국에서 ABA 번호라고도 하는 은행 라우팅 번호여야 합니다.

```xml
<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>
```

다음 예제에서는 은행 라우팅 번호(`<BANK_KEY>` 값)를 사용하여 은행 세부 정보를 가져옵니다. 

```xml
<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>
```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 요청에 대한 XML 샘플

일반 SAP IDoc XML 스키마를 생성하려면 **SAP 로그온** 애플리케이션과 T-코드 `WE-60`을 사용합니다. GUI를 통해 SAP 설명서에 액세스하고 IDoc 유형 및 확장명에 대한 XML 스키마를 XSD 형식으로 생성합니다. 일반 SAP 형식 및 페이로드와 기본 제공 대화 상자에 대한 설명은 [SAP 설명서](https://help.sap.com/viewer/index)를 검토하세요.

이 예제에서는 루트 노드 및 네임스페이스를 선언합니다. 샘플 코드의 URI `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send`는 다음 구성을 선언합니다.

* `/IDoc`는 모든 IDoc의 루트 노드입니다.

* `/3`은 공통 세그먼트 정의의 레코드 형식 버전입니다.

* `/ORDERS05`는 IDoc 유형입니다.

* `//`는 IDoc 확장이 없기 때문에 빈 세그먼트입니다.

* `/700`은 SAP 버전입니다.

* `/Send`는 SAP에 정보를 보내는 작업입니다.

```xml
<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
   <ns0:idocData>
```

`idocData` 노드를 반복하여 호출 한 번으로 IDoc 일괄 처리를 보낼 수 있습니다. 아래 예제에는 하나의 컨트롤 레코드 `EDI_DC40` 및 여러 개의 데이터 레코드가 있습니다.

```xml
<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
      <...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
    <...>
  </ns0:idocData>
```

다음 예제는 접두사 `EDI_DC`를 사용하는 샘플 IDoc 컨트롤 레코드입니다. SAP 설치 및 IDoc 유형과 일치하도록 값을 업데이트해야 합니다. 예를 들어 IDoc 클라이언트 코드는 `800`이 아닐 수 있습니다. SAP 팀에 문의하여 SAP 설치에 올바른 값을 사용하고 있는지 확인하세요.

```xml
<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>
```

다음 예제는 일반 세그먼트가 있는 샘플 데이터 레코드입니다. 이 예제에서는 SAP 날짜 형식을 사용합니다. 강력한 형식의 문서는 `2020-12-31 23:59:59` 같은 네이티브 XML 날짜 형식을 사용할 수 있습니다.

```xml
<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>
```

다음 예제는 세그먼트가 그룹화된 데이터 레코드입니다. 이 레코드에는 그룹 부모 노드 `E2EDKT1002GRP`와 `E2EDKT1002` 및 `E2EDKT2001`을 포함한 여러 자식 노드가 포함되어 있습니다. 

```xml
<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>
```

tRFC와 함께 사용할 IDoc 식별자를 만드는 것이 좋습니다. SAP 커넥터 API에서 [IDoc 보내기 작업](/connectors/sap/#send-idoc)을 사용하여 이 트랜잭션 식별자`tid`를 설정할 수 있습니다.

다음 예제는 트랜잭션 식별자 `tid`를 설정하는 다른 방법입니다. 이 예제에서는 마지막 데이터 레코드 세그먼트 노드와 IDoc 데이터 노드가 닫혀 있습니다. 그리고 GUID `guid`를 tRFC 식별자로 사용하여 중복 항목을 탐지합니다.

```xml
     </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>
```

### <a name="add-the-request-trigger"></a>요청 트리거 추가

1. Azure Portal에서 빈 논리 앱을 만들어 워크플로 디자이너를 엽니다.

1. 검색 상자에서 필터로 `http request`을 입력합니다. **트리거** 목록에서 **HTTP 요청을 수신하는 경우** 트리거를 선택합니다.

   ![요청 트리거 추가를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 이제 논리 앱 워크플로를 위한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 만들기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>스키마를 생성하는 SAP 작업 추가

1. 워크플로 디자이너의 트리거 아래에서 **새 단계** 를 선택합니다.

   ![논리 앱 워크플로에 새 단계를 추가하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 검색 상자에서 필터로 `generate schemas sap`을 입력합니다. **작업** 목록에서 **스키마 생성** 을 선택합니다.
  
   ![워크플로에 "스키마 만들기" 작업을 추가하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   또는 **Enterprise** 탭을 선택하고 SAP 작업을 선택할 수도 있습니다.

   ![엔터프라이즈 탭에서 "스키마 만들기" 작업 선택을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 이미 연결된 경우 SAP 작업을 설정할 수 있도록 다음 단계를 계속 진행합니다. 그러나 연결 정보를 입력하라는 메시지가 표시되면 온-프레미스 SAP 서버에 연결할 수 있도록 정보를 입력합니다.

   1. 연결 이름을 제공합니다.

   1. **데이터 게이트웨이** 섹션의 **구독** 에서, 데이터 게이트웨이를 설치하기 위해 Azure Portal에서 만든 데이터 게이트웨이 리소스의 Azure 구독을 선택합니다. 

   1. **연결 게이트웨이** 에서 Azure의 데이터 게이트웨이 리소스를 선택합니다.

   1. 연결 정보를 계속 입력합니다. **로그온 유형** 속성의 경우 속성이 **애플리케이션 서버** 로 설정되었는지 아니면 **그룹** 으로 설정되었는지 여부에 따라 단계를 수행합니다.

      * **애플리케이션 서버** 로 설정된 경우 이 속성은(보통은 선택 사항) 필수입니다.

        ![SAP 애플리케이션 서버에 대한 연결 만들기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹** 으로 설정된 경우 이 속성은(보통은 선택 사항) 필수입니다.

        ![SAP 메시지 서버에 대한 연결 만들기를 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   1. 작업이 완료되면 **만들기** 를 선택합니다.

      Azure Logic Apps는 연결을 설정하고 테스트하여 연결이 제대로 작동하는지 확인합니다.

1. 스키마를 생성할 아티팩트의 경로를 입력합니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![SAP 작업 선택을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   또는 작업을 수동으로 입력할 수 있습니다.

   ![SAP 작업을 수동으로 입력하는 방법을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   두 개 이상의 아티팩트에 대한 스키마를 생성하려면 다음과 같이 각 아티팩트에 대한 SAP 작업 세부 정보를 제공합니다.

   !["새 항목 추가" 선택을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![두 항목을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/schema-generator-example.png)

   SAP 작업에 대한 자세한 내용은 [IDoc 작업에 대한 메시지 스키마](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 검토하세요.

1. 논리 앱 워크플로 저장 디자이너 도구 모음에서 **저장** 을 선택합니다.

기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행하여 잘못된 값을 확인하는 데 사용됩니다. 이 동작은 이전에 발생한 문제를 검색하는 데 도움이 될 수 있습니다. **안전한 형식 지정** 옵션은 이전 버전과의 호환성을 위해 제공되며 문자열 길이만 확인합니다. [안전한 형식 지정 옵션](#safe-typing)에 대해 자세히 알아보세요.

### <a name="test-your-workflow"></a>워크플로 테스트

1. 디자이너 도구 모음에서 **실행** 을 선택하여 논리 앱 워크플로에 대한 실행을 트리거합니다.

1. 실행을 열고, **스키마 생성** 작업의 출력을 확인합니다.

   출력은 지정된 메시지 목록에 대해 생성된 스키마를 보여줍니다.

### <a name="upload-schemas-to-an-integration-account"></a>통합 계정에 스키마 업로드

필요에 따라 생성된 스키마를 Blob, 스토리지, 통합 계정 등의 리포지토리에 다운로드하거나 저장할 수 있습니다. 통합 계정은 다른 XML 작업을 통해 최고의 환경을 제공하므로, 이 예에서는 Azure Resource Manager 커넥터를 사용하여 동일한 논리 앱 워크플로의 통합 계정에 스키마를 업로드하는 방법을 보여줍니다.

1. 워크플로 디자이너의 트리거 아래에서 **새 단계** 를 선택합니다.

1. 검색 상자에서 필터로 `resource manager`을 입력합니다. **리소스 만들기 또는 업데이트** 를 선택합니다.

   ![Azure Resource Manager 작업 선택을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Azure 구독, Azure 리소스 그룹, 통합 계정을 포함한 작업 세부 정보를 입력합니다. 필드에 SAP 토큰을 추가하려면 해당 필드의 상자 내부를 클릭하고, 나타나는 동적 콘텐츠 목록에서 선택합니다.

   1. **새 매개 변수 추가** 목록을 열고 **위치** 및 **속성** 필드를 선택합니다.

   1. 이 예제에서 보여주는 것처럼 새 필드의 세부 정보를 입력합니다.

      ![Azure Resource Manager 작업에 대한 세부 정보 입력을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP **스키마 생성** 작업은 스키마를 컬렉션으로 생성하므로, 디자이너가 자동으로 **For each** 루프를 작업에 추가합니다. 다음은 이 작업이 어떻게 나타나는지 보여주는 예제입니다.

   !["for each" 루프가 있는 Azure Resource Manager 작업을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 스키마에는 base64 인코딩 형식이 사용됩니다. 통합 계정에 스키마를 업로드하려면 `base64ToString()` 함수를 사용하여 디코딩해야 합니다. 다음은 `"properties"` 요소의 코드를 보여주는 예제입니다.
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 논리 앱 워크플로 저장 디자이너 도구 모음에서 **저장** 을 선택합니다.

### <a name="test-your-workflow"></a>워크플로 테스트

1. 디자이너 도구 모음에서 **실행** 을 선택하여 논리 앱 워크플로를 수동으로 트리거합니다.

1. 실행이 성공하면 통합 계정으로 이동하여 생성된 스키마가 있는지 확인합니다.

<a name="enable-secure-network-communications"></a>

## <a name="enable-secure-network-communications-snc"></a>SNC(보안 네트워크 통신) 사용

시작하기 전에, 앞에서 나열한 [필수 구성 요소](#prerequisites)를 충족하는지 확인합니다. 이러한 필수 구성 요소는 데이터 게이트웨이를 사용하고 논리 앱 워크플로가 다중 테넌트 Azure에서 실행되는 경우에만 적용됩니다.

* SAP 시스템과 동일한 네트워크에 있는 컴퓨터에 온-프레미스 데이터 게이트웨이가 설치되어 있는지 확인합니다.

* SSO의 경우 데이터 게이트웨이는 SAP 사용자에 매핑되는 사용자로 실행됩니다.

* 추가 보안 기능을 제공하는 SNC 라이브러리는 데이터 게이트웨이와 동일한 머신에 설치됩니다. [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos 및 NTLM을 예로 들 수 있습니다.

   SAP 시스템과 주고 받는 요청에 SNC를 사용하려면 SAP 연결에서 **SNC 사용** 확인란을 선택하고 다음 속성을 제공합니다.

   ![SAP 연결에서 SNC 설정을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 속성 | Description |
   |----------| ------------|
   | **SNC 라이브러리 경로** | NCo 설치 위치나 절대 경로를 기준으로 하는 SNC 라이브러리 이름 또는 경로입니다. 예: `sapsnc.dll` 또는 `.\security\sapsnc.dll` 또는 `c:\security\sapsnc.dll`. |
   | **SNC SSO** | SNC를 통해 연결하는 경우 SNC ID는 일반적으로 호출자를 인증하는 데 사용됩니다. 또 다른 옵션은 재정의입니다. 이 방법을 사용하면 호출자를 인증하는 데 사용자 및 암호 정보를 사용할 수 있지만 줄은 여전히 암호화됩니다. |
   | **SNC 내 이름** | 대부분의 경우 이 속성을 생략할 수 있습니다. 설치된 SNC 솔루션은 일반적으로 자체 SNC 이름을 알고 있습니다. 여러 ID를 지원하는 솔루션에서만 이 특정 대상 또는 서버에 사용할 ID를 지정해야 할 수도 있습니다. |
   | **SNC 파트너 이름** | 백 엔드 SNC의 이름입니다. |
   | **SNC 보호 품질** | 이 특정 대상 또는 서버의 SNC 통신에 사용되는 서비스 품질입니다. 기본값은 백 엔드 시스템에서 정의합니다. 최댓값은 SNC에 사용되는 보안 제품에서 정의합니다. |
   |||

   > [!NOTE]
   > 데이터 게이트웨이와 SNC 라이브러리가 있는 머신에서는 환경 변수 SNC_LIB 및 SNC_LIB_64를 설정하지 마세요. 설정하면 커넥터를 통해 전달되는 SNC 라이브러리 값보다 우선적으로 적용됩니다.

## <a name="safe-typing"></a>안전한 형식 지정

기본적으로 SAP 연결을 만들 때 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행하여 잘못된 값을 확인하는 데 사용됩니다. 이 동작은 이전에 발생한 문제를 검색하는 데 도움이 될 수 있습니다. **안전한 형식 지정** 옵션은 이전 버전과의 호환성을 위해 제공되며 문자열 길이만 확인합니다. **안전한 형식 지정** 을 선택하면 SAP의 DATS 형식 및 TIMS 형식은 해당하는 XML이 아닌 문자열 `xs:date` 및 `xs:time`로 처리되며 `xmlns:xs="http://www.w3.org/2001/XMLSchema"`입니다. 안전한 형식 지정은 모든 스키마 생성 동작, "전송" 페이로드 및 "수신" 응답에 대한 전송 메시지 그리고 트리거에 영향을 줍니다.

강력한 형식 지정을 사용하는 경우(**안전한 형식 지정** 은 사용되지 않음) 스키마는 DATS 및 TIMS 형식을 보다 간단한 XML 형식으로 매핑합니다.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

강력한 형식 지정을 사용하여 메시지를 보내면 DATS 및 TIMS 응답은 일치하는 XML 형식 포맷을 준수합니다.

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

**안전한 형식 지정** 을 사용하면 스키마는 DATS 및 TIMS 형식을 길이가 제한된 XML 문자열 필드에만 매핑합니다. 예를 들면 다음과 같습니다.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

**안전한 형식 지정** 을 사용하도록 설정된 메시지를 보내면 DATS 및 TIMS 응답은 다음 예제와 같습니다.

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="send-sap-telemetry-foron-premises-data-gateway-to-azure-application-insights"></a>온-프레미스 데이터 게이트웨이에 대한 SAP 원격 분석을 Azure Application Insights로 보내기

온-프레미스 데이터 게이트웨이에 대한 2021년 8월 업데이트에서는 SAP 커넥터 작업이 SAP 클라이언트 라이브러리의 원격 분석 데이터와 Microsoft SAP 어댑터의 추적을 Azure Monitor의 한 기능인 [Application Insights](../azure-monitor/app/app-insights-overview.md)로 전송할 수 있습니다. 이 원격 분석에는 주로 다음 데이터가 포함됩니다.

* SAP NCo 메트릭 및 모니터를 기반으로 하는 메트릭 및 추적

* Microsoft SAP 어댑터의 추적

### <a name="metrics-and-traces-from-sap-client-library"></a>SAP 클라이언트 라이브러리의 메트릭 및 추적

메트릭은 온-프레미스 데이터 게이트웨이의 리소스 사용 현황 및 가용성에 따라 경시적으로 변동할 수 있는 숫자 값입니다. 이러한 메트릭을 사용하여 시스템 상태를 보다 잘 이해하고 다음과 같은 작업에 대한 경고를 만들 수 있습니다.

* 시스템 상태가 저하되는지 여부

* 비정상적인 이벤트

* 높은 시스템 부하

이 정보는 `customMetrics`라는 Application Insights 테이블로 전송됩니다. 기본적으로 메트릭은 30초 간격으로 전송됩니다.

SAP NCo 메트릭 및 추적은 SAP NCo 메트릭, 특히 다음과 같은 NCo 클래스를 기반으로 합니다.

* RfcDestinationMonitor.

* RfcConnectionMonitor.

* RfcServerMonitor.

* RfcRepositoryMonitor.

각 클래스에서 제공하는 메트릭에 대한 자세한 내용은 [SAP NCo 설명서(로그인 필요)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546)를 참조하세요.

추적에는 메트릭에 사용되는 텍스트 정보가 포함됩니다. 이 정보는 `traces`라는 Application Insights 테이블로 전송됩니다. 기본적으로 추적은 10분 간격으로 전송됩니다.

### <a name="set-up-sap-telemetry-for-application-insights"></a>Application Insights에 대한 SAP 원격 분석​ 설정

게이트웨이 설치에 대한 SAP 원격 분석을 Application Insights에 보내려면 먼저 Application Insights 리소스를 만들고 설정해야 합니다. 자세한 내용은 다음 설명서를 검토하세요.

* [Application Insights 리소스 만들기(클래식)](../azure-monitor/app/create-new-resource.md)

* [작업 영역 기반 Application Insights 리소스](../azure-monitor/app/create-workspace-resource.md)

SAP 원격 분석을 Application insights로 보내도록 설정하려면 다음 단계를 수행합니다.

1. 다음 위치에서 **Microsoft.ApplicationInsights.EventSourceListener.dll** 의 NuGet 패키지를 다운로드합니다. [https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0).

1. 온-프레미스 데이터 게이트웨이 설치 디렉터리에 다운로드한 파일을 추가합니다.

1. 온-프레미스 데이터 게이트웨이 설치 디렉터리에서 **Microsoft.ApplicationInsights.dll** 파일의 버전 번호가 추가한 **Microsoft.ApplicationInsights.EventSourceListener.dll** 파일과 동일한지 확인합니다. 게이트웨이는 현재 2.14.0 버전을 사용합니다.

1. **ApplicationInsights.config** 파일에서 `<InstrumentationKey></Instrumentation>` 요소를 사용하여 줄을 주석 처리하여 [Application Insights 계측 키](../azure-monitor/app/app-insights-overview.md#how-does-application-insights-work)를 추가합니다. 자리 표시자 *your-Application-Insights-instrumentation-key* 를 사용자의 키로 바꿉니다. 예를 들면 다음과 같습니다.

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <ApplicationInsights schemaVersion="2014-05-30" xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
         <!-- Uncomment this element and insert your Application Insights key to receive ETW telemetry about your gateway <InstrumentationKey>*your-instrumentation-key-placeholder*</InstrumentationKey> -->
         <TelemetryModules>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
               <IsHeartbeatEnabled>false</IsHeartbeatEnabled>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
               <Sources>
                  <Add Name="Microsoft-LobAdapter" Level="Verbose" />
               </Sources>
            </Add>
         </TelemetryModules>
      </ApplicationInsights>
      ```

1. **ApplicationInsights.config** 파일에서 요구 사항에 따라 SAP 커넥터 작업에 필요한 추적 `Level` 값을 변경할 수 있습니다. 예를 들면 다음과 같습니다.

   ```xml
   <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
         <Add Name="Microsoft-LobAdapter" Level="Verbose" />
      </Sources>
   </Add>
   ```

   자세한 내용은 다음 설명서를 검토하세요.

   * `Level` 값: [EventLevel 열거형](/dotnet/api/system.diagnostics.tracing.eventlevel)

   * [EventSource 추적](../azure-monitor/app/configuration-with-applicationinsights-config.md#eventsource-tracking)

   * [EventSource 이벤트](../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)

1. 변경 내용을 적용한 후 온-프레미스 데이터 게이트웨이 서비스를 다시 시작합니다.

### <a name="review-metrics-in-application-insights"></a>Application Insights에서 메트릭 검토

논리 앱 워크플로에서 SAP 작업을 실행한 후 Application Insights로 전송된 원격 분석을 검토할 수 있습니다.

1. Azure Portal에서 Application Insights 리소스를 엽니다.

1. 리소스 메뉴의 **모니터링** 에서 **로그** 를 선택합니다.

   다음 스크린샷은 Application Insights가 **로그** 창에 열려 있는 Azure Portal을 보여 줍니다.

   [![Application Insights가 쿼리를 만들기 위한 ‘로그’ 창으로 열린 Azure Portal을 보여 주는 스크린샷](./media/logic-apps-using-sap-connector/application-insights-query-panel.png)](./media/logic-apps-using-sap-connector/application-insights-query-panel.png#lightbox)

1. **로그** 창에서 특정 요구 사항에 따라 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/concepts/)을 사용하여 [쿼리](/azure/data-explorer/kusto/query/)를 만들 수 있습니다.

   다음 예제 쿼리와 유사한 쿼리 패턴을 사용할 수 있습니다.

   ```Kusto
   customMetrics
   | extend DestinationName = tostring(customDimensions["DestinationName"])
   | extend MetricType = tostring(customDimensions["MetricType"])
   | where customDimensions contains "RfcDestinationMonitor"
   | where name contains "MaxUsedCount"
   ```

1. 쿼리를 실행한 후 결과를 검토합니다.

   다음 스크린샷은 예제 쿼리의 메트릭 결과 테이블을 보여 줍니다.

   [![Application Insights의 메트릭 결과 테이블을 보여 주는 스크린샷](./media/logic-apps-using-sap-connector/application-insights-metrics.png)](./media/logic-apps-using-sap-connector/application-insights-metrics.png#lightbox)

   * **MaxUsedCount** 는 ‘모니터링되는 대상에서 동시에 사용되는 최대 클라이언트 연결 수’입니다. [SAP NCo 설명서(로그인 필요)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546)에 설명된 것과 같습니다. 이 값을 사용하여 동시에 열린 연결 수를 파악할 수 있습니다.

   * **valueCount** 열에는 각 읽기에 대해 **2** 가 표시됩니다. 메트릭은 30초 간격으로 생성되고 Application Insights는 이러한 메트릭을 분 단위로 집계하기 때문입니다.

   * **DestinationName** 열에는 Microsoft SAP 어댑터 내부 이름인 문자열이 포함됩니다.

     이 RFC(원격 함수 호출) 대상을 보다 잘 이해하려면 이 값을 `traces`와 함께 사용합니다. 예를 들면 다음과 같습니다.

     ```Kusto
     customMetrics
     | extend DestinationName = tostring(customDimensions["DestinationName"])
     | join kind=inner (traces
        | extend DestinationName = tostring(customDimensions["DestinationName"]),
        AppServerHost = tostring(customDimensions["AppServerHost"]),
        SncMode = tostring(customDimensions["SncMode"]),
        SapClient = tostring(customDimensions["Client"])
        | where customDimensions contains "RfcDestinationMonitor"
        )
        on DestinationName , $left.DestinationName == $right.DestinationName
     | where customDimensions contains "RfcDestinationMonitor"
     | where name contains "MaxUsedCount"
     | project AppServerHost, SncMode, SapClient, name, valueCount, valueSum, valueMin, valueMax
     ```

Application Insights에서 이러한 기능을 사용하여 메트릭 차트 또는 경고를 만들 수도 있습니다. 예를 들면 다음과 같습니다.

[![Application Insights의 차트 형식 결과를 보여 주는 스크린샷](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png)](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png#lightbox)

### <a name="traces-from-microsoft-sap-adapter"></a>Microsoft SAP 어댑터의 추적

Microsoft SAP 어댑터에서 전송된 추적을 사용하여 문제 사후 분석을 수행하고 SAP 커넥터 작업에서 노출될 수도 있고 노출되지 않을 수도 있는 기존 내부 시스템 오류를 찾을 수 있습니다. 이러한 추적은 Application Insights 이전의 이벤트 소스 프레임워크에서 발생하므로 `message`가 `"n\a"`로 설정됩니다. 예를 들면 다음과 같습니다.

```Kusto
traces
| where message == "n/a"
| where severityLevel > 0
| extend ActivityId = tostring(customDimensions["ActivityId"])
| extend fullMessage = tostring(customDimensions["fullMessage"])
| extend shortMessage = tostring(customDimensions["shortMessage"])
| where ActivityId contains "8ad5952b-371e-4d80-b355-34e28df9b5d1"
```

다음 스크린샷은 예제 쿼리의 추적 결과 테이블을 보여 줍니다.

[![Application Insights의 추적 결과 테이블을 보여 주는 스크린샷](./media/logic-apps-using-sap-connector/application-insights-traces.png)](./media/logic-apps-using-sap-connector/application-insights-traces.png#lightbox)

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="change-language-headers"></a>언어 헤더 변경

Logic Apps에서 SAP에 연결하면 연결의 기본 언어는 영어입니다. 인바운드 요청에 [표준 HTTP 헤더 `Accept-Language`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4)를 사용하여 연결의 언어를 설정할 수 있습니다.

> [!TIP]
> 대부분의 웹 브라우저는 사용자의 설정에 따라 `Accept-Language` 헤더를 추가합니다. 웹 브라우저는 워크플로 디자이너에서 새 SAP 연결을 만들 때 이 헤더를 적용합니다. 웹 브라우저 설정을 업데이트하여 원하는 언어를 사용하거나 워크플로 디자이너 대신 Azure Resource Manager를 사용하여 SAP 연결을 만듭니다.

예를 들어 **요청** 트리거를 사용하여 `Accept-Language` 헤더가 포함된 요청을 논리 앱 워크플로에 보낼 수 있습니다. 논리 앱 워크플로의 모든 작업은 헤더를 수신합니다. 그런 다음, SAP는 BAPI 오류 메시지처럼 시스템 메시지에 지정된 언어를 사용합니다.

논리 앱 워크플로의 SAP 연결 매개 변수에는 언어 속성이 없습니다. 따라서 `Accept-Language` 헤더를 사용하는 경우 **계정 정보 및/또는 권한을 확인하고 다시 시도하세요** 오류가 발생할 수 있습니다. 이 경우 SAP 구성 요소의 오류 로그를 확인합니다. 이 오류는 헤더를 사용하는 SAP 구성 요소에서 실제로 발생하므로 다음 오류 메시지 중 하나가 표시될 수 있습니다.

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`

* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>명시적으로 트랜잭션 확인

Logic Apps에서 SAP로 트랜잭션을 보낼 때 SAP 문서 [트랜잭션 RFC 서버 프로그램](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)에 설명된 대로 이 교환이 두 단계로 이루어집니다. 기본적으로 **SAP로 보내기** 작업은 한 번의 호출로 함수 전송 및 트랜잭션 확인 단계를 모두 처리합니다. SAP 커넥터는 이러한 단계를 분리하는 옵션을 제공합니다. 자동으로 트랜잭션을 확인하는 대신 IDoc를 보낼 수 있으며, 명시적 **\[IDOC] 트랜잭션 ID 확인** 작업을 사용할 수 있습니다.

트랜잭션 ID 확인을 분리하는 이 기능은 네트워크 문제와 같은 원인으로 인해 오류가 발생할 수 있는 시나리오처럼 SAP에서 트랜잭션이 중복되는 것을 원하지 않는 경우에 유용합니다. 트랜잭션 ID를 별도로 확인하면 트랜잭션이 SAP 시스템에서 한 번만 완료됩니다.

다음은 이 패턴을 보여주는 예제입니다.

1. 빈 논리 앱을 만들고 요청 트리거를 추가합니다.

1. SAP 커넥터에서 **\[IDOC] SAP에 문서 보내기** 작업을 추가합니다. SAP 시스템에 보내는 IDoc의 세부 정보를 제공합니다.

1. 별도의 단계에서 트랜잭션 ID를 명시적으로 확인하려면 **TID 확인** 필드에서 **아니요** 를 선택합니다. 선택적 **트랜잭션 ID GUID** 필드의 경우 값을 수동으로 지정할 수도 있고 커넥터가 **\[IDOC] SAP에 문서 보내기** 작업에 대한 응답으로 이 GUID를 자동으로 생성하여 반환하게 할 수도 있습니다.

   !["[IDOC] SAP에 문서 보내기" 작업 속성을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 트랜잭션 ID를 명시적으로 확인하려면 **\[IDOC] 트랜잭션 ID 확인** 작업을 추가하여 [중복된 IDoc를 SAP로 보내지 않도록](#avoid-sending-duplicate-idocs) 합니다. **트랜잭션 ID** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 해당 목록에서 **\[IDOC] SAP에 문서 보내기** 작업에서 반환된 **트랜잭션 ID** 값을 선택합니다.

   !["트랜잭션 ID 확인" 작업을 보여 주는 스크린샷.](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   이 단계가 실행된 후에는 SAP 커넥터 쪽과 SAP 시스템 쪽에서 현재 트랜잭션이 완료된 것으로 표시됩니다.

#### <a name="avoid-sending-duplicate-idocs"></a>중복된 IDoc 보내지 않기

논리 앱 워크플로에서 SAP로 중복된 IDoc가 전송되는 문제가 발생하는 경우 다음 단계에 따라 IDoc 트랜잭션 식별자로 사용할 문자열 변수를 만듭니다. 이 트랜잭션 식별자를 만들면 일시적인 중단, 네트워크 문제 또는 승인 손실 등의 문제가 발생할 때 중복 네트워크 전송을 방지하는 데 도움이 됩니다.

> [!NOTE]
> SAP 시스템은 지정된 시간이 지난 후 또는 기본값인 24시간 후 트랜잭션 식별자를 잊어버립니다. 따라서 SAP는 ID 또는 GUID를 알 수 없을 때 틀림없이 트랜잭션 식별자를 확인합니다.
> 트랜잭션 식별자 확인이 실패한다는 것은 SAP가 확인을 승인하기 전에 SAP 시스템에서 오류가 발생했다는 뜻입니다.

1. 워크플로 디자이너에서 논리 앱 워크플로에 **변수 초기화** 작업을 추가합니다.

1. **변수 초기화** 작업의 편집기에서 다음 설정을 구성합니다. 그런 다음, 변경 사항을 저장합니다.

   1. **이름** 에 변수 이름을 입력합니다. 예들 들어 `IDOCtransferID`입니다.

   1. **형식** 에서 **문자열** 을 변수 형식으로 선택합니다.

   1. **값** 에서 **초기 값 입력** 텍스트 상자를 선택하여 동적 콘텐츠 메뉴를 엽니다.

   1. **언어 식** 탭을 선택합니다. 함수 목록에서 `guid()` 함수를 입력합니다.

   1. **확인** 을 선택하여 변경 내용을 저장합니다. 이제 **값** 필드가 GUID를 생성하는 `guid()` 함수로 설정되었습니다.

1. **변수 초기화** 작업 후 **\[IDOC] SAP에 문서 보내기** 작업을 추가합니다.

1. **\[IDOC] SAP에 문서 보내기** 작업에 대한 편집기에서 다음 설정을 구성합니다. 그런 다음, 변경 사항을 저장합니다.

   1. **IDOC 유형** 에서 메시지 유형을 선택하고, **입력 IDOC 메시지** 에서 메시지를 지정합니다.

   1. **SAP 릴리스 버전** 에서 SAP 구성 값을 선택합니다.

   1. **레코드 유형 버전** 에서 SAP 구성 값을 선택합니다.

   1. **TID 확인** 에서 **아니요** 를 선택합니다.

   1. **새 매개 변수 목록 추가** > **트랜잭션 ID GUID** 를 선택합니다.

   1. 텍스트 상자를 선택하여 동적 콘텐츠 메뉴를 엽니다. **변수** 탭에서 이전에 만든 변수의 이름을 선택합니다(예: `IDOCtransferID`).

1. **\[IDOC] SAP에 문서 보내기** 작업의 제목 표시줄에서 **...**  > **설정** 을 선택합니다.

   **재시도 정책** 에서는 **기본** &gt; **완료** 를 선택하는 것이 좋습니다. 그러나 요구 사항에 맞게 사용자 지정 정책을 구성할 수도 있습니다. 사용자 지정 정책의 경우 일시적인 네트워크 중단을 극복할 수 있도록 재시도를 하나 이상 구성하는 것이 좋습니다.

1. **\[IDOC] SAP에 문서 보내기** 작업 후에 **\[IDOC] 트랜잭션 ID 확인** 작업을 추가합니다.

1. **\[IDOC] 트랜잭션 ID 확인** 작업의 편집기에서 다음 설정을 구성합니다. 그런 다음, 변경 사항을 저장합니다.

1. **트랜잭션 ID** 에 변수 이름을 다시 입력합니다. 예들 들어 `IDOCtransferID`입니다.

1. 필요에 따라 테스트 환경에서 중복 제거의 유효성을 검사합니다.

    1. 이전 단계에서 사용한 것과 동일한 **트랜잭션 ID** GUID를 사용하여 **\[IDOC] SAP에 문서 보내기** 작업을 반복합니다.
    
    1. **\[ IDOC] SAP로 문서 보내기** 작업에 대 한 각 호출 후에 할당 된 idoc 번호를 확인 하려면 동일한 **트랜잭션 ID** 와 **수신** 방향을 사용 하 여 **\[ 트랜잭션 작업에 대해 idoc] Get idoc 목록을** 사용 합니다.
    
       두 호출에 대해 동일한 단일 IDoc 번호가 반환 되는 경우 IDoc는 중복 제거 되었습니다.

   동일한 IDoc를 두 번 보내면 SAP에서 tRFC 호출의 중복을 식별할 수 있는지 확인하고 단일 인바운드 IDoc 메시지에 대한 두 번의 호출을 확인할 수 있습니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음은 관리형(비 ISE) SAP 커넥터에 대해 현재 알려진 문제와 제한 사항입니다.

* 일반적으로 SAP 트리거는 데이터 게이트웨이 클러스터를 지원하지 않습니다. 일부 장애 조치(failover)에서는 SAP 시스템과 통신하는 데이터 게이트웨이 노드가 활성 노드와 달라서 예기치 않은 동작이 발생할 수 있습니다.

  * 보내기 시나리오의 경우 장애 조치(failover) 모드의 데이터 게이트웨이 클러스터가 지원됩니다.

  * 상태 저장 [SAP 작업](#actions)에서는 부하 분산 모드의 데이터 게이트웨이 클러스터가 지원되지 않습니다. 이러한 작업에는 **\[BAPI - RFC] 상태 저장 세션 만들기**, **\[BAPI] 트랜잭션 커밋**, **\[BAPI] 트랜잭션 롤백**, **\[BAPI - RFC] 상태 저장 세션 닫기** 및 **세션 ID** 값을 지정하는 모든 작업이 포함됩니다. 상태 저장 통신은 동일한 데이터 게이트웨이 클러스터 노드에 유지되어야 합니다.

  * 상태 저장 SAP 작업의 경우 비 클러스터 모드에서 또는 장애 조치(failover)용으로만 설정된 클러스터에서 데이터 게이트웨이를 사용합니다.

* 현재 SAP 커넥터는 SAP 라우터 문자열을 지원하지 않습니다. 온-프레미스 데이터 게이트웨이가 연결하려는 SAP 시스템과 동일한 LAN에 있어야 합니다.

* SAP 작업 **\[ 의 Bapi] Call 메서드에서** , 동작에서 반환 된 **callbapiresponse** 개체에 하나 이상의 경고가 있으면 자동 커밋 기능이 bapi 변경 내용을 커밋하지 않습니다. 경고에도 불구 하 고 BAPI 변경 내용을 커밋하려면 **\[ bapi-RFC] 상태 저장 세션 만들기** 작업을 사용 하 여 명시적으로 세션을 만들고, SAP 작업 **\[ 에서 bapi] 호출 메서드에서** 자동 커밋 기능을 사용 하지 않도록 설정 하 고, **\[ bapi] 트랜잭션** 작업을 대신 호출 합니다.

* [ISE의 논리 앱에서는](connect-virtual-network-vnet-isolated-environment-overview.md) 이 커넥터의 ISE 레이블 지정 버전이 [ISE 메시지 한도](logic-apps-limits-and-config.md#message-size-limits)를 대신 사용합니다.

## <a name="connector-reference"></a>커넥터 참조

SAP 커넥터에 대한 자세한 내용은 [커넥터 참조](/connectors/sap/)를 검토하세요. SAP 커넥터, 트리거 및 작업에 대한 제한, 매개 변수 및 반환에 대한 세부 정보를 찾을 수 있습니다.

### <a name="triggers"></a>트리거

:::row:::
    :::column span="1":::
        [**SAP에서 메시지가 수신될 때**](/connectors/sap/#when-a-message-is-received)
    :::column-end:::
    :::column span="3":::
        SAP에서 메시지가 수신될 때 수행합니다.
    :::column-end:::
:::row-end:::

### <a name="actions"></a>동작

:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] 상태 저장 세션 닫기**](/connectors/sap/#[bapi---rfc]-close-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        SAP 시스템에 대한 기존 상태 저장 연결 세션을 닫습니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] 상태 저장 세션 만들기**](/connectors/sap/#[bapi---rfc]-create-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        SAP 시스템에 대한 상태 저장 연결 세션을 만듭니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] SAP에서 메서드 호출**](/connectors/sap/#[bapi]-call-method-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        SAP 시스템에서 BAPI 메서드를 호출합니다.
        \
        \
        호출에서     \ 매개 변수를 사용해야 합니다.
        \
        **비즈니스 개체**(`businessObject`): 검색 가능한 드롭다운 메뉴입니다.
        \
        \
        **메서드**(`method`): **비즈니스 개체** 가 선택된 후 사용 가능한 메서드를 채웁니다. 사용 가능한 메서드는 선택한 **비즈니스 개체** 에 따라 달라집니다.
        \
        \
        **입력 BAPI 매개 변수**(`body`): 여기서는 호출에 대한 BAPI 메서드 입력 매개 변수 값이 포함된 XML 문서를 호출하거나 BAPI 매개 변수가 포함된 스토리지 Blob의 URI를 호출합니다.
        \
        \
        **[BAPI] SAP에서 메서드 호출** 작업을 사용하는 방법에 대한 자세한 예는 [BAPI 요청의 XML 샘플](#xml-samples-for-bapi-requests)을 검토하세요.
        \
        워크플로 디자이너를 사용하여 BAPI 요청을 편집하는 경우 다음 검색 함수를 사용할 수 있습니다.     \
        \
        디자이너에서 개체를 선택하여 사용 가능한 메서드의 드롭다운 메뉴를 봅니다.
        \
        \
        BAPI API 호출에서 제공하는 검색 가능 목록을 사용하여 키워드로 비즈니스 개체 형식을 필터링합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] 트랜잭션 커밋**](/connectors/sap/#[bapi]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        세션에 대한 BAPI 트랜잭션을 커밋합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] 롤백 트랜잭션**](/connectors/sap/#[bapi]-roll-back-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        세션에 대한 BAPI 트랜잭션을 롤백합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC - RFC] 트랜잭션 ID 확인**](/connectors/sap/#[idoc---rfc]-confirm-transaction-id-(preview))
    :::column-end:::
    :::column span="3":::
        SAP에 트랜잭션 식별자 확인을 보냅니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] 트랜잭션에 대한 IDOC 목록 가져오기**](/connectors/sap/#[idoc]-get-idoc-list-for-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        세션 식별자 또는 트랜잭션 식별자를 기준으로 트랜잭션에 대한 IDoc 목록을 가져옵니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] IDOC 상태 가져오기**](/connectors/sap/#[idoc]-get-idoc-status-(preview))
    :::column-end:::
    :::column span="3":::
        IDoc의 상태를 가져옵니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] SAP에 문서 보내기**](/connectors/sap/#[idoc]-send-document-to-sap-(preview))
    :::column-end:::
    :::column span="3":::
        IDoc 메시지를 SAP 서버에 보냅니다.
        \
        \
        호출에서     \ 매개 변수를 사용해야 합니다.
        \
        **선택적 확장이 포함된 IDOC 유형**(`idocType`): 검색 가능한 드롭다운 메뉴입니다.
        \
        \
        **입력 IDOC 메시지**(`body`): 여기서 IDoc 페이로드가 포함된 XML 문서 또는 IDoc XML 문서가 포함된 스토리지 Blob의 URI를 호출합니다. 이 문서는 WE60 IDoc 설명서에 따른 SAP IDoc XML 스키마 또는 일치하는 SAP IDoc 작업 URI에 대해 생성된 스키마를 준수해야 합니다.
        \
        \
        선택적 매개 변수 **SAP 릴리스 버전**(`releaseVersion`)은 사용자가 IDoc 유형을 선택한 후 값을 채우며, 사용자가 선택한 IDoc 유형에 따라 달라집니다.
        \
        \
        IDoc 보내기 작업을 사용하는 방법에 대한 자세한 예는 [SAP 서버에 IDoc 메시지를 보내는 연습](#send-idoc-messages-to-sap-server)을 검토하세요.
        \
        \
        선택적 매개 변수 **TID 확인**(`confirmTid`)을 사용하는 방법은 [트랜잭션을 명시적으로 확인하는 연습](#confirm-transaction-explicitly)을 검토하세요.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] 트랜잭션에 RFC 추가**](/connectors/sap/#[rfc]-add-rfc-to-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        RFC 호출을 트랜잭션에 추가합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] SAP에서 함수 호출**](/connectors/sap/#[rfc]-call-function-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        SAP 시스템에서 RFC 작업(sRFC, tRFC 또는 qRFC)을 호출합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] 트랜잭션 커밋**](/connectors/sap/#[rfc]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        세션 및/또는 큐에 대한 RFC 트랜잭션을 커밋합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] 트랜잭션 만들기**](/connectors/sap/#[rfc]-create-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        식별자 및/또는 큐 이름을 기준으로 새 트랜잭션을 만듭니다. 트랜잭션이 있는 경우 세부 정보를 가져옵니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] 트랜잭션 가져오기**](/connectors/sap/#[rfc]-get-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        식별자 및/또는 큐 이름을 기준으로 트랜잭션의 세부 정보를 가져옵니다. 존재하는 항목이 없는 경우 새 트랜잭션을 만듭니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**스키마 생성**](/connectors/sap/#generate-schemas)
    :::column-end:::
    :::column span="3":::
        IDoc, BAPI 또는 RFC에 대한 SAP 아티팩트의 스키마를 생성합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**SAP 테이블 읽기**](/connectors/sap/#read-sap-table-(preview))
    :::column-end:::
    :::column span="3":::
        SAP 테이블을 읽습니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**SAP에 메시지 보내기**](/connectors/sap/#send-message-to-sap)
    :::column-end:::
    :::column span="3":::
        SAP에 메시지 유형(RFC, BAPI, IDoc)을 보냅니다.
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

* Azure Logic Apps에서 [온-프레미스 시스템에 연결](logic-apps-gateway-connection.md)
* [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md)을 사용하여 다른 메시지 작업의 유효성을 검사하고, 변환하고, 사용하는 방법 알아보기
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

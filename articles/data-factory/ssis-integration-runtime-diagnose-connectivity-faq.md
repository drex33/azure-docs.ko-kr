---
title: SSIS Integration Runtime에서 연결 진단 기능 사용
description: 연결 진단 기능을 사용하여 SSIS Integration Runtime에서 연결 이슈를 해결합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/21/2021
ms.openlocfilehash: 1d501a461caab23fb45441473fda60e13c7e8334
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535946"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>SSIS Integration Runtime에서 연결 진단 기능 사용

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

SSIS Integration Runtime에서 SSIS(SQL Server Integration Services) 패키지를 실행하는 동안 연결 문제가 발생할 수 있습니다. 해당 문제는 SSIS Integration Runtime이 Azure Virtual Network에 참가하는 경우에 자주 발생합니다.

*연결 진단* 기능을 사용하여 연결을 테스트하면 연결 문제를 해결할 수 있습니다. 해당 기능은 Azure Data Factory 포털의 SSIS Integration Runtime 모니터링 페이지에 있습니다.

 ![모니터 페이지 - 연결 진단](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![모니터 페이지 - 연결 테스트](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

다음 섹션에서는 연결을 테스트할 때 발생하는 일반적인 오류에 대해 알아봅니다. 각 섹션에서 다음에 대해 설명합니다.

- 오류 코드
- 오류 메시지
- 오류의 잠재적 원인
- 권장 솔루션

## <a name="error-code-invalidinput"></a>오류 코드: InvalidInput

- **오류 메시지**: ‘입력이 올바른지 확인하세요.’
- **잠재적 원인**: 입력이 잘못되었습니다.
- **권장 사항**: 입력을 확인합니다.

## <a name="error-code-firewallornetworkissue"></a>오류 코드: FirewallOrNetworkIssue

- **오류 메시지**: ‘방화벽/서버/NSG에서 해당 포트가 열려 있으며 네트워크가 안정적인지 확인하세요.’
- **가능한 원인:**
  - 서버에서 포트를 열지 않습니다.
  - 네트워크 보안 그룹의 아웃 바운드 트래픽이 포트에서 거부되었습니다.
  - NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 열지 않습니다.
- **권장 사항:**
  - 서버에서 포트를 엽니다.
  - 포트에서 네트워크 보안 그룹의 아웃바운드 트래픽을 허용하도록 업데이트합니다.
  - NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 엽니다.

## <a name="error-code-misconfigureddnssettings"></a>오류 코드: MisconfiguredDnsSettings

- **오류 메시지**: ‘Azure-SSIS IR를 통해 참가하는 VNet에서 자체 DNS 서버를 사용하는 경우 호스트 이름을 확인할 수 있는지 점검합니다.’
- **가능한 원인:**
  -  사용자 지정 DNS에 문제가 있습니다.
  -  개인 호스트 이름에 FQDN(정규화된 도메인 이름)을 사용하지 않습니다.
- **권장 사항:**
  -  사용자 지정 DNS 문제를 해결하여 호스트 이름을 확인할 수 있는지 봅니다.
  -  FQDN을 사용해야 합니다. Azure-SSIS IR은 자체 DNS 접미사를 자동으로 추가하지 않습니다. 예를 들어, **<your_private_server>** 대신 **<your_private_server> contoso.com** 을 사용합니다.

## <a name="error-code-servernotallowremoteconnection"></a>오류 코드: ServerNotAllowRemoteConnection

- **오류 메시지**: ‘서버에서 이 포트를 통해 원격 TCP 연결을 허용하는지 확인하세요.’
- **가능한 원인:**
  -  서버 방화벽은 원격 TCP 연결을 허용하지 않습니다.
  -  서버가 온라인 상태가 아닙니다.
- **권장 사항:**
  -  서버 방화벽에서 원격 TCP 연결을 허용합니다.
  -  서버를 시작합니다.
   
## <a name="error-code-misconfigurednsgsettings"></a>오류 코드: MisconfiguredNsgSettings

- **오류 메시지**: "VNet의 NSG가 이 포트를 통해 아웃바운드 트래픽을 허용하는지 확인하세요. Azure ExpressRoute 또는 UDR을 사용하는 경우, 이 포트가 방화벽/서버에서 열려 있는지 확인하세요."
- **가능한 원인:**
  -  네트워크 보안 그룹의 아웃 바운드 트래픽이 포트에서 거부되었습니다.
  -  NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 열지 않습니다.
- **권장 사항:**
  -  포트에서 네트워크 보안 그룹의 아웃바운드 트래픽을 허용하도록 업데이트합니다.
  -  NVA/Azure 방화벽/온-프레미스 방화벽에서 포트를 엽니다.

## <a name="error-code-genericissues"></a>오류 코드: GenericIssues

- **오류 메시지**: ‘포괄적 이슈로 인해 연결 테스트에 실패했습니다.’
- **잠재적 원인**: 연결 테스트에 일시적인 문제가 발생했습니다.
- **권장 사항**: 나중에 연결 테스트를 다시 시도합니다. 다시 시도해도 문제가 해결되지 않을 경우, Azure Data Factory 지원 팀에 문의하세요.

## <a name="error-code-pspingexecutiontimeout"></a>오류 코드: PSPingExecutionTimeout

- **오류 메시지**: ‘연결 테스트 시간 초과. 나중에 다시 시도하세요.’
- **잠재적 원인**: 연결 테스트 시간이 초과되었습니다.
- **권장 사항**: 나중에 연결 테스트를 다시 시도합니다. 다시 시도해도 문제가 해결되지 않을 경우, Azure Data Factory 지원 팀에 문의하세요.

## <a name="error-code-networkinstable"></a>오류 코드: NetworkInstable

- **오류 메시지**: ‘네트워크가 불안정하여 연결 테스트가 불규칙하게 진행되었습니다.’
- **잠재적 원인**: 일시적인 네트워크 문제입니다.
- **권장 사항**: 서버 또는 방화벽 네트워크가 안정적인지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [SSMS를 사용하여 SSIS 작업 마이그레이션](how-to-migrate-ssis-job-ssms.md)
- [SSDT를 사용하여 Azure에서 SSIS 패키지 실행](how-to-invoke-ssis-package-ssdt.md)
- [Azure에서 SSIS 패키지 예약](how-to-schedule-azure-ssis-integration-runtime.md)

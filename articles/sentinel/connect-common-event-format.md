---
title: Azure Sentinel에 CEF 데이터 연결 | Microsoft Docs
description: Linux 컴퓨터를 로그 전달자로 사용하여 CEF(Common Event Format) 메시지를 Azure Sentinel로 전송하는 외부 솔루션을 연결합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2021
ms.author: yelevin
ms.openlocfilehash: ccf05bb51c64a94c07a0277ed901912f9e4dda3e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529020"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Common Event Format을 사용하여 외부 솔루션 연결

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

CEF 메시지를 전송하는 외부 솔루션을 연결하는 경우 Azure Sentinel에 연결하는 세 단계는 다음과 같습니다.

1단계: [Syslog/CEF 전달자를 배포하여 CEF 연결](connect-cef-agent.md) 2단계: [솔루션별 단계 수행](connect-cef-solution-config.md) 3단계: [연결 확인](connect-cef-verify.md)

이 문서에서는 연결이 작동하는 방식을 설명하고, 필수 구성 요소를 나열하고, 보안 솔루션이 Syslog 위에 CEF(Common Event Format) 메시지를 전송하는 메커니즘을 배포하는 단계를 보여 줍니다. 

> [!NOTE] 
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

이 연결을 만들려면 어플라이언스와 Azure Sentinel 간의 통신을 지원하기 위해 Syslog 전달자 서버를 배포해야 합니다.  이 서버는 Linux용 Log Analytics 에이전트가 설치된 전용 Linux 컴퓨터(VM 또는 온-프레미스)로 구성됩니다. 

다음 다이어그램에서는 Azure에 있는 Linux VM의 설정을 설명합니다.

 ![Azure의 CEF](./media/connect-cef/cef-syslog-azure.png)

또는 다른 클라우드 또는 온-프레미스 컴퓨터에서 VM을 사용하는 경우 다음과 같은 설정이 존재합니다. 

 ![온-프레미스의 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>보안 고려 사항

머신 보안은 조직의 보안 정책에 따라 구성해야 합니다. 예를 들어 네트워크가 회사 네트워크 보안 정책에 맞게 구성되고 디먼의 포트 및 프로토콜이 사용자의 요구 사항에 맞게 변경될 수 있습니다. [Azure에서 VM 보안](../virtual-machines/security-policy.md), [네트워크 보안 모범 사례](../security/fundamentals/network-best-practices.md) 지침을 사용하여 컴퓨터 보안 구성을 향상시킬 수 있습니다.

Syslog 원본과 Syslog Forwarder 간에 TLS 통신을 사용하려면 TLS로 통신하도록 Syslog 디먼을 구성해야 합니다(rsyslog 또는 syslog-ng). [TLS를 사용하는 Syslog 트래픽 암호화 -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) 및 [TLS를 사용하는 로그 메시지 암호화 –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)를 참조하세요.
 
## <a name="prerequisites"></a>필수 구성 요소

Log Analytics로 CEF 데이터를 수집하려면 Azure Sentinel 작업 영역이 필요합니다.

로그 전달자로 사용할 Linux 머신이 다음 운영 체제 중 하나를 실행하고 있는지 확인합니다.

- 64비트
  - CentOS 7 및 8, 부 버전 포함(6 아님)
  - Amazon Linux 2017.09
  - Oracle Linux 7
  - RHEL(Red Hat Enterprise Linux) Server 7 및 8, 부 버전 포함(6 아님)
  - Debian GNU/Linux 8 및 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS, 18.04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32비트
  - CentOS 7 및 8, 부 버전 포함(6 아님)
  - Oracle Linux 7
  - RHEL(Red Hat Enterprise Linux) Server 7 및 8, 부 버전 포함(6 아님)
  - Debian GNU/Linux 8 및 9
  - Ubuntu Linux 14.04 LTS 및 16.04 LTS
 
- 디먼 버전
  - Syslog-ng: 2.1 - 3.22.1
  - Rsyslog: v8
  
- Syslog RFC 지원
  - Syslog RFC 3164
  - Syslog RFC 5424
 
머신에서 다음 요구 사항을 충족하는지 확인합니다. 

- 용량
  - 컴퓨터에는 최소 **4개의 CPU 코어와 8GB RAM** 이 있어야 합니다.

    > [!NOTE]
    > - **rsyslog** 디먼을 사용하는 단일 로그 전달자 컴퓨터에는 수집된 **EPS(초당 이벤트)가 최대 8,500개까지** 지원되는 용량이 있습니다.

- 사용 권한
  - 머신에 상승된 권한(sudo)이 있어야 합니다. 

- 소프트웨어 요구 사항
  - 머신에서 Python 2.7 또는 3이 실행되고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel이 보안 솔루션 및 어플라이언스에서 CEF 로그를 수집하는 방법을 알아봤습니다. Azure Sentinel에 솔루션을 연결하는 방법을 알아보려면 다음 문서를 참조하세요.

- 1단계: [Syslog/CEF 전달자를 배포하여 CEF 연결](connect-cef-agent.md)
- 2단계: [솔루션별 단계 수행](connect-cef-solution-config.md)
- 3단계: [연결 확인](connect-cef-verify.md)

Azure Sentinel에서 수집한 데이터로 수행할 작업에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [CEF 및 CommonSecurityLog 필드 매핑](cef-name-mapping.md)에 대해 알아봅니다.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
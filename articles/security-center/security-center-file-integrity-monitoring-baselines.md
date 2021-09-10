---
title: Azure Security Center에서 파일 무결성 모니터링
description: Azure Security Center에서 기준을 파일 무결성 모니터링과 비교하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 1b5f4314afa17f245c36417916bc9af59aa7493a
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237536"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>FIM(파일 무결성 모니터링)을 사용하여 기준 비교

FIM(파일 무결성 모니터링)은 리소스의 중요한 영역이 변경될 때 알리므로 권한 없는 작업을 조사하고 해결할 수 있습니다. FIM은 Windows 파일, Windows 레지스트리 및 Linux 파일을 모니터링합니다.

이 항목에서는 파일 및 레지스트리에서 FIM을 사용하도록 설정하는 방법을 설명합니다. FIM에 대한 자세한 내용은 [Azure Security Center에서 파일 무결성 모니터링](security-center-file-integrity-monitoring.md)을 참조하세요.

## <a name="why-use-fim"></a>FIM을 사용하는 이유

운영 체제, 애플리케이션, 관련 구성은 리소스의 동작과 보안 상태를 제어합니다. 따라서 공격자는 리소스의 운영 체제를 급습하거나 탐지되지 않고 작업을 실행하기 위해 리소스를 제어하는 파일을 노립니다.

실제로 PCI DSS 및 ISO 17799 등의 많은 규정 준수 표준에서는 FIM 컨트롤 구현을 요구합니다.  

## <a name="enable-built-in-recursive-registry-checks"></a>기본 제공 재귀 레지스트리 검사 사용

FIM 레지스트리 하이브 기본값은 일반적인 보안 영역 내에서 재귀적 변경 내용을 모니터링하는 편리한 방법을 제공합니다.  예를 들어 악의적 사용자는 시작 또는 종료 시 실행을 구성하여 LOCAL_SYSTEM 컨텍스트에서 실행되도록 스크립트를 구성할 수 있습니다.  이 유형의 변경 내용을 모니터링하려면 기본 제공 검사를 사용하도록 설정하세요.  

![레지스트리.](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 재귀 검사는 권장 보안 하이브에만 적용되고 사용자 지정 레지스트리 경로에는 적용되지 않습니다.  

## <a name="add-a-custom-registry-check"></a>사용자 지정 레지스트리 검사 추가

FIM 기준은 운영 체제 및 지원 애플리케이션의 알려진 정상 상태의 특징을 식별하는 것으로 시작합니다.  이 예에서는 Windows Server 2008 이상의 암호 정책 구성을 중점적으로 다룹니다.


|정책 이름                 | 레지스트리 설정|
|---------------------------------------|-------------|
|도메인 컨트롤러: 컴퓨터 계정 암호 변경 거부| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|도메인 구성원: 보안 채널 데이터를 디지털 암호화 또는 서명(항상)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|도메인 구성원: 보안 채널 데이터를 디지털 암호화(가능한 경우)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|도메인 구성원: 보안 채널 데이터 디지털 서명(가능한 경우)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|도메인 구성원: 컴퓨터 계정 암호 변경 사항 사용 안 함|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|도메인 구성원: 컴퓨터 계정 암호의 최대 사용 기간|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|도메인 구성원: 고급 세션 키 요청(Windows 2000 이상)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|네트워크 보안: NTLM 제한: 이 도메인에서 NTLM 인증|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|네트워크 보안: NTLM 제한: 이 도메인에서 서버 예외 추가|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|네트워크 보안: NTLM 제한: 이 도메인에서 NTLM 인증 감사|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 다양한 운영 체제 버전에서 지원되는 레지스트리 설정에 대한 자세한 내용은 [그룹 정책 설정 참조 스프레드시트](https://www.microsoft.com/download/confirmation.aspx?id=25250)를 참조하세요.

레지스트리 기준을 모니터링하도록 FIM을 구성하려면:

1. **변경 내용 추적을 위해 Windows 레지스트리 추가** 창에서 **Windows 레지스트리 키** 텍스트 상자에 다음 레지스트리 키를 입력합니다.

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="레지스트리에서 FIM 사용.":::

## <a name="track-changes-to-windows-files"></a>Windows 파일 변경 내용 추적

1. **변경 내용 추적을 위해 Windows 파일 추가** 창의 **경로 입력** 텍스트 상자에 추적하려는 파일이 있는 폴더를 입력합니다. 다음 그림의 예에서 **Contoso 웹앱** 은 **ContosWebApp** 폴더 구조 내의 D:\ 드라이브에 있습니다.  
1. 설정 클래스의 이름을 제공하고, 재귀를 사용하도록 설정하고, 와일드 카드(*) 접미사로 최상위 폴더를 지정하여 사용자 지정 Windows 파일 항목을 만듭니다.

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="파일에서 FIM 사용.":::

## <a name="retrieve-change-data"></a>변경 데이터 검색

파일 무결성 모니터링 데이터는 Azure Log Analytics/ConfigurationChange 테이블 집합 내에 있습니다.  

 1. 리소스별로 변경 내용의 요약을 검색할 시간 범위를 설정합니다.
다음 예제에서는 레지스트리 및 파일 범주에서 지난 14일간의 모든 변경 내용을 검색합니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 레지스트리 변경의 세부 정보를 보려면:

    1. **where** 절에서 **Files** 를 제거합니다. 
    1. 요약 줄을 제거하고 순서 절로 바꿉니다.

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

보고서는 보관을 위해 CSV로 내보내거나 Power BI 보고서로 보낼 수 있습니다.  

![FIM 데이터.](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)
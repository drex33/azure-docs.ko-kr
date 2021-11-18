---
title: Microsoft 센티널 SAP 솔루션 자세한 SAP 요구 사항 | Microsoft Docs
description: Microsoft 센티널 SAP 솔루션에 대 한 자세한 SAP 시스템 요구 사항을 알아봅니다.
author: batamig
ms.author: bagold
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 2c1383f2917b7f26aaf623b7ddd9666ff2909fc6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711852"
---
# <a name="microsoft-sentinel-sap-solution-detailed-sap-requirements-public-preview"></a>Microsoft 센티널 SAP 솔루션 자세한 SAP 요구 사항 (공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Microsoft 센티널 sap 솔루션을 배포 하는 기본 절차](sap-deploy-solution.md) 에는 필요한 sap 변경 요청 및 sap 메모가 포함 되어 있으며, 모든 필수 사용 권한이 있는 기본 제공 역할을 제공 합니다.

이 문서에서는 필요한 SAP 변경 요청, 노트, 권한을 자세히 나열합니다.

관리자이거나 [SAP 솔루션을 수동으로 배포](sap-solution-deploy-alternate.md)하는 경우 이 문서를 참조하세요. 이 문서는 고급 SAP 사용자를 위한 것입니다.


> [!IMPORTANT]
> Microsoft 센티널 SAP 솔루션은 현재 미리 보기로 제공 됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

> [!NOTE]
> 보안 SNC 연결을 사용하여 SAP 데이터 커넥터를 배포하는 경우의 추가 요구 사항이 나열되어 있습니다. 자세한 내용은 [SNC를 사용 하 여 Microsoft 센티널 SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)를 참조 하세요.
>
## <a name="recommended-virtual-machine-sizing"></a>권장되는 가상 머신 크기

다음 표에서는 의도한 사용량에 따라 가상 머신의 크기를 조정하는 권장 방법을 설명합니다.

|사용  |크기 조정 권장 방법  |
|---------|---------|
|랩 환경과 같은 **최소 사양**     |   *Standard_B2s* VM      |
|**표준 커넥터**(기본값)     |   다음을 갖춘 *DS2_v2* VM <br>- 코어 2개<br>- 8GB 메모리      |
|**커넥터 여러 개**     |다음을 갖춘 *Standard_B4ms* VM <br>- 코어 4개<br>- 16GB 메모리         |
|     |         |

## <a name="required-sap-log-change-requests"></a>필수 SAP 로그 변경 요청

SAP Basis 버전에 따라 SAP 솔루션에는 다음 SAP 로그 변경 요청이 필요합니다.

- **SAP Basis 버전 7.50 이상**, NPLK900144 설치
- **하위 버전**, NPLK900146 설치
- **필요한 권한 부여를 사용 하 여 sap 역할을 만들려면** 지원 되는 sap 기반 버전에 대해 NPLK900163를 설치 합니다. 자세한 내용은 [SAP 시스템 구성](sap-deploy-solution.md#configure-your-sap-system) 및 [필요한 ABAP 권한](#required-abap-authorizations)을 참조하세요.

> [!NOTE]
> 필요한 SAP 로그 변경 요청은 커넥터에 필요한 사용자 지정 RFC FM을 노출하며 표준 또는 사용자 지정 개체를 변경하지 않습니다.
>

## <a name="required-sap-notes"></a>필요한 SAP 노트

SAP Basis 버전이 7.50 이하인 경우 다음 SAP 노트를 설치합니다.

|SAP BASIS 버전  |필요한 노트 |
|---------|---------|
|- 750 SP01~SP12<br>- 751 SP01~SP06<br>- 752 SP01~SP03     |  2641084: 보안 감사 로그 데이터에 대한 표준화된 읽기 권한       |
|- 700~702<br>- 710~711, 730, 731, 740, 750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
|- 700~702<br>- 710~711, 730, 731, 740<br>- 750~752     | 2502336: CD(변경 문서): RSSCD100 - 데이터베이스가 아닌 보관 계층에서 읽기 전용        |
|     |         |

[SAP 지원 Launchpad 사이트](https://support.sap.com/en/index.html)에서 SAP 노트에 액세스합니다.
## <a name="requires-sap-ports-access"></a>SAP 포트 액세스 필요:
다음 TCP 포트를 통해 SAP 환경 호스트, 32xx, 5xx13 및 33xx, 여기서 xx는 SAP 인스턴스 번호입니다.

## <a name="required-abap-authorizations"></a>필요한 ABAP 권한

다음 표에는 백 엔드 sap 사용자가 Microsoft 센티널을 sap 로그에 연결 하는 데 필요한 권한 부여 ABAP 나열 되어 있습니다. 자세한 내용은 [SAP 시스템 구성](sap-deploy-solution.md#configure-your-sap-system)을 참조하세요.

필요한 권한이 로그 유형별로 나열됩니다. Microsoft 센티널로 수집 하려는 로그 형식에 대해 나열 된 권한 집합만 필요 합니다.

> [!TIP]
> 필요한 모든 권한 부여를 사용 하 여 역할을 만들려면 sap change request NPLK900163를 SAP 시스템에 배포 합니다. 이 변경 요청은 **/msftsen/SENTINEL_CONNECTOR** 역할을 만들며, 일반적으로 SAP 기반 또는 역할 소유자는 Azure 센티널에 연결 하는 ABAP 사용자에 게 역할을 할당 해야 합니다.
>

| 권한 개체 | 필드 | 값 |
| -------------------- | ----- | ----- |
| **모든 RFC 로그** | | |
| S_RFC | FUGR | /OSP/SYSTEM_TIMEZONE |
| S_RFC | FUGR | ARFC |
| S_RFC | FUGR | STFC |
| S_RFC | FUGR | RFC1 |
| S_RFC | FUGR | SDIFRUNTIME  |
| S_RFC | FUGR | SMOI |
| S_RFC | FUGR | SYST |
| S_RFC | FUGR/FUNC | SRFC/RFC_SYSTEM_INFO |
| S_RFC | FUGR/FUNC | THFB/TH_SERVER_LIST |
| S_TCODE | TCD | SM51 |
| **ABAP 애플리케이션 로그**  | | |
| S_APPL_LOG | ACTVT | 표시 |
| S_APPL_LOG | ALG_OBJECT | * |
| S_APPL_LOG | ALG_SUBOBJ | * |
| S_RFC | FUGR | SXBP_EXT |
| S_RFC | FUGR | /MSFTSEN/_APPLOG |
| **ABAP 변경 문서 로그** | | |
| S_RFC | FUGR | /MSFTSEN/_CHANGE_DOCS |
| **ABAP CR 로그** | | |
| S_RFC | FUGR | CTS_API |
| S_RFC | FUGR | /MSFTSEN/_CR |
| S_TRANSPRT | ACTVT | 표시 |
| S_TRANSPRT | TTYPE | * |
| **ABAP DB 테이블 데이터 로그** | | |
| S_RFC | FUGR | /MSFTSEN/_TD |
| S_TABU_DIS | ACTVT | 표시 |
| S_TABU_DIS | DICBERCLS | &NC& |
| S_TABU_DIS | DICBERCLS | + 로깅에 필요한 모든 개체 |
| S_TABU_NAM | ACTVT | 표시 |
| S_TABU_NAM | TABLE | + 로깅에 필요한 모든 개체 |
| S_TABU_NAM | TABLE | DBTABLOG |
| **ABAP 작업 로그** | | |
| S_RFC | FUGR | SXBP |
| S_RFC | FUGR | /MSFTSEN/_JOBLOG |
| **ABAP 작업 로그, ABAP 애플리케이션 로그** | | |
| S_XMI_PRD | 인터페이스 | XBP |
| **ABAP 보안 감사 로그 - XAL** | | |
| All RFC | S_RFC | FUGR |
| S_ADMI_FCD | S_ADMI_FCD | AUDD |
| S_RFC | FUGR | SALX |
| S_USER_GRP | ACTVT | 표시 |
| S_USER_GRP | CLASS | * |
| S_XMI_PRD | 인터페이스 | XAL |
| **ABAP 보안 감사 로그 - XAL, ABAP 작업 로그, ABAP 애플리케이션 로그** | | |
| S_RFC | FUGR | SXMI |
| S_XMI_PRD | EXTCOMPANY | Microsoft |
| S_XMI_PRD | EXTPRODUCT | Microsoft Sentinel |
| **ABAP 보안 감사 로그 - SAL** | | |
| S_RFC | FUGR | RSAU_LOG |
| S_RFC | FUGR | /MSFTSEN/_AUDITLOG |
| **ABAP 스풀 로그, ABAP 스풀 출력 로그** | | |
| S_RFC | FUGR | /MSFTSEN/_SPOOL |
| **ABAP 워크플로 로그** | | |
| S_RFC | FUGR | SWRR |
| S_RFC | FUGR | /MSFTSEN/_WF |
| **사용자 데이터** | | |
| S_RFC | FUNC | RFC_READ_TABLE |
| | |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [SAP용 Microsoft Sentinel 솔루션 배포](sap-deploy-solution.md)
- [SNC를 사용 하 여 Microsoft 센티널 SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)
- [전문가 구성 옵션, 온-프레미스 배포, SAPControl 로그 원본](sap-solution-deploy-alternate.md)
- [Microsoft 센티널 SAP 솔루션 로그 참조](sap-solution-log-reference.md)
- [Microsoft 센티널 SAP 솔루션: 사용 가능한 보안 콘텐츠](sap-solution-security-content.md)
- [Microsoft 센티널 SAP 솔루션 배포 문제 해결](sap-deploy-troubleshoot.md)

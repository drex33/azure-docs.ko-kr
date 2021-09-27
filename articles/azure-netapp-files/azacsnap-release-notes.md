---
title: Azure NetApp Files용 Azure Application Consistent Snapshot 도구에 대한 릴리스 정보 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구에 대한 릴리스 정보를 제공합니다.
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
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: phjensen
ms.openlocfilehash: a580a135d2158234c84f3378b024997679ad6017
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778327"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅숏 도구의 릴리스 정보

이 페이지에는 새 기능을 제공하거나 결함을 해결하기 위해 AzAcSnap에서 변경된 주요 내용이 나와 있습니다.

## <a name="aug-2021"></a>8 월-2021

### <a name="azacsnap-v502-build_2021082719086---patch-update-to-v501"></a>AzAcSnap v 5.0.2 (Build_20210827 .19086)-v 5.0.1 용에 업데이트 패치

AzAcSnap v 5.0.2 (Build_20210827 .19086)는 다음 수정 및 개선 사항을 포함 하 여 v 5.0 분기에 대 한 패치 업데이트로 제공 됩니다.

- `ssh`255 종료 코드를 무시 합니다.  경우에 따라 `ssh` Azure Large Instance의 저장소와 통신 하는 데 사용 되는 명령을 실행 하는 동안 오류 또는 실행 실패가 발생 하지 않은 경우 종료 코드 255가 발생 `man ssh` 합니다 ("종료 상태" 참조). 이후 AzAcSnap는이를 실패로 트래핑 하 고 중단 합니다.  이 업데이트를 사용 하면 올바른 실행의 유효성을 검사 하기 위해 추가 확인이 수행 됩니다. 여기에는 `ssh` 기존 종료 코드 검사 외에도 STDOUT 및 STDERR의 오류 구문 분석이 포함 됩니다.
- 설치 관리자 hdbuserstore 원본 경로 확인을 수정 합니다.  설치 관리자가 설치를 실행 하는 사용자에 대 한 hdbuserstore에 대 한 잘못 된 원본 디렉터리가 있는지 확인 합니다 .이 문제는 확인 하기 위해 수정 되었습니다 `~/.hdb` .  이는를 `root` 설치 하기 전에 사용자에 대해 hdbuserstore가 미리 구성 된 시스템 (예: Azure Large Instance)에 적용 됩니다 `azacsnap` .
- 설치 관리자는 이제 설치/추출 되는 버전을 표시 합니다 (설치 관리자가 인수 없이 실행 되는 경우).

설치 관리자의 [최신 릴리스](https://aka.ms/azacsnapinstaller)를 다운로드하고 [시작](azacsnap-get-started.md) 방법을 검토하세요.

## <a name="may-2021"></a>2021년 5월

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v 5.0.1(빌드: 20210524.14837) - v5.0에 대한 패치 업데이트

AzAcSnap v5.0.1(빌드: 20210524.14837)은 다음 수정 사항 및 개선 사항이 포함된 v5.0 분기에 대한 패치 업데이트로 제공됩니다.

- 종료 코드 처리가 개선되었습니다.  경우에 따라 0이 아니어야 하는 실행 실패가 있는 경우에도 종료 코드 0(영)이 내보내집니다.  이제 종료 코드는 `azacsnap`을 실행하여 완료할 때만 0이 되어야 하고 실패할 경우에는 0이 아니어야 합니다.  또한 AzAcSnap의 내부 오류 처리가 확장되어 AzAcSnap이 실행하는 외부 명령(예: hdbsql, ssh)의 종료 코드(예: hdbsql, ssh)를 캡처하고 내보냅니다(실패의 원인인 경우).

## <a name="april-2021"></a>2021년 4월

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0(빌드: 20210421.6349) - GA 릴리스(2021년 4월 21일)

AzAcSnap v5.0(빌드: 20210421.6349)이 일반 공급되었으며 이 빌드에 대해 다음과 같은 수정 사항 및 개선 사항이 있습니다.

- 경합 상태를 방지하기 위해 hdbsql 다시 시도 시간 제한(SAP HANA의 응답을 기다리기 위함)은 "savePointAbortWaitSeconds"의 절반으로 자동 설정됩니다.  "savePointAbortWaitSeconds" 설정은 JSON 구성 파일에서 직접 수정할 수 있으며 최소 600초여야 합니다.

## <a name="march-2021"></a>2021년 3월

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview(빌드:20210318.30771)

AzAcSnap v5.0 Preview(빌드:20210318.30771)는 다음과 같은 수정 사항 및 개선 사항을 포함하여 릴리스되었습니다.

- SAP HANA 테 넌 트 Db에 AZACSNAP 사용자를 추가 하지 않아도 됨, [데이터베이스와 통신 사용](azacsnap-installation.md#enable-communication-with-database) 섹션을 참조 하세요.
- 수동 QOS로 구성된 볼륨으로 [복원](azacsnap-cmd-ref-restore.md)을 허용하도록 수정합니다.
- Azure 대규모 인스턴스에 대한 SSH 연결을 제한하는 뮤텍스 컨트롤이 추가되었습니다.
- 공백 및 기타 관련 이슈가 있는 경로 이름을 처리하기 위해 설치 관리자를 수정했습니다.
- 다른 데이터베이스 서버를 지원하기 위해 선택적 매개 변수 '--hanasid'를 '--dbsid'로 변경했습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅샷 도구 시작](azacsnap-get-started.md)

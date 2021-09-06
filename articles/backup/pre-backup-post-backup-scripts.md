---
title: 백업 전 및 백업 후 스크립트 사용
description: 이 문서에는 백업 전 및 백업 후 스크립트를 지정하는 절차가 포함되어 있습니다. Azure Backup Server(MABS)
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 48d65679f4ff1b6486513067058c1f385e4f1434
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568609"
---
# <a name="using-pre-backup-and-post-backup-scripts"></a>백업 전 및 백업 후 스크립트 사용

적용 대상: MABS(Microsoft Azure Backup Server)

_백업 전 스크립트_ 는 보호된 컴퓨터에 있는 스크립트이고, 각 MABS 백업 작업 전에 실행되고, 보호된 데이터 원본의 백업을 준비합니다.

_백업 후 스크립트_ 는 MABS 백업 작업에서 백업 후 처리(가상 머신을 다시 온라인으로 설정)를 수행한 후 실행되는 스크립트입니다.

컴퓨터에 보호 에이전트를 설치하는 경우 ScriptingConfig.xml 파일이 보호된 컴퓨터의 _설치 경로_ \Microsoft Data Protection Manager\DPM\Scripting 폴더에 추가됩니다. 컴퓨터의 각 보호된 데이터 원본에 대해 ScriptingConfig.xml에서 백업 전 스크립트를 지정하고 백업 후 스크립트를 지정할 수 있습니다.

>[!Note]
>백업 전 및 백업 후 스크립트는 VBScript가 될 수 없습니다. 대신 **cscript myscript.vbs** 를 포함하는 스크립트에 래퍼 명령을 사용해야 합니다.

MABS에서 보호 작업을 실행할 경우 보호된 컴퓨터의 ScriptingConfig.xml이 검사됩니다. 백업 전 스크립트를 지정한 경우 MABS에서 스크립트를 실행한 다음, 작업을 완료합니다. 백업 후 스크립트를 지정한 경우 MABS에서 작업을 완료한 다음, 스크립트를 실행합니다.

>[!Note]
>보호 작업에는 복제본 만들기, 빠른 전체 백업, 동기화 및 일관성 확인이 포함됩니다.

MABS는 로컬 시스템 계정을 사용하여 백업 전 및 백업 후 스크립트를 실행합니다. 모범 사례로, 스크립트가 관리자 및 로컬 시스템 계정에 대해서만 읽기 및 실행 권한을 보유하는지 확인해야 합니다. 이 수준의 권한은 권한이 없는 사용자가 스크립트를 수정할 수 없도록 합니다.

**ScriptingConfig.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
xmlns="http://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
   <DatasourceScriptConfig DataSourceName="Data source">
     <PreBackupScript>”Path\Script Parameters” </PreBackupScript>
     <PostBackupScript>"Path\Script Parameters” </PostBackupScript>
     <TimeOut>30</TimeOut>
   </DatasourceScriptConfig>
</ScriptConfiguration>
```

백업 전 및 백업 후 스크립트를 지정하려면

1. 보호된 컴퓨터에서 ScriptingConfig.xml 파일을 XML 또는 텍스트 편집기로 엽니다.

   >[!Note]
   >DataSourceName 특성은 **Drive:** 로 제공되어야 합니다(예: 데이터 원본이 D 드라이브인 경우 D:).

1. 각 데이터 원본에 대해, DatasourceScriptConfig 요소를 다음과 같이 완료합니다.


   1. DataSourceName 특성의 경우 데이터 원본 볼륨(파일 데이터 원본의 경우) 또는 이름(다른 모든 데이터 원본의 경우)을 입력합니다. 응용 프로그램 데이터의 데이터 원본 이름은 SQL의 경우 _Instance\Database_ 이고, Exchange의 경우 _저장소 그룹 이름_ 이고, Virtual Server의 경우 _논리 경로\구성 요소 이름_ 이고, Windows SharePoint Services의 경우 _SharePoint Farm\SQL Server 이름\SQL 인스턴스 이름\SharePoint Config DB_ 입니다.
   1. _PreBackupScript_ 태그에서 경로 및 스크립트 이름을 입력합니다.
   1. _PreBackupCommandLine_ 태그에서 스크립트로 전달되는 명령줄 매개 변수를 공백으로 구분하여 입력합니다.
   1. _PostBackupScript_ 태그에서 경로 및 스크립트 이름을 입력합니다.
   1. _PostBackupCommandLine_ 태그에서 스크립트로 전달되는 명령줄 매개 변수를 공백으로 구분하여 입력합니다.
   1. _TimeOut_ 태그에서 호출 후 시간 제한이 발생하여 스크립트가 실패한 것으로 표시되기 전까지 MABS가 대기해야 하는 시간(분)을 입력합니다.

1. ScriptingConfig.xml 파일을 저장합니다.

>[!Note]
>MABS에서 백업 후 스크립트 명령에 추가 부울(참/거짓) 매개 변수를 접미사로 지정하여 MABS 백업 작업의 상태를 표시합니다.

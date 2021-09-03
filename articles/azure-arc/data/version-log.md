---
title: Azure Arc 지원 데이터 서비스 - 릴리스 버전
description: Azure Arc 지원 데이터 서비스의 릴리스 날짜별 버전 로그
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/06/2021
ms.topic: conceptual
ms.openlocfilehash: 3e636fe5ff6477f78f09d940b70099b8cbf41fb9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537347"
---
# <a name="version-log"></a>버전 로그

다음 표에서는 시간이 지남에 따라 변경되는 다양한 버전에 대해 설명합니다.

|날짜|릴리스 이름|컨테이너 이미지 태그|CRD 접두사 및 버전|ARM API 버전|`arcdata` Azure CLI 확장 버전|Arc 지원 Kubernetes Helm 차트 확장 버전|Azure Data Studio용 Arc Data 확장|
|---|---|---|---|---|---|---|---|
|2021년 7월 30일|Arc 지원 SQL Managed Instance 범용 및 Arc 지원 SQL Server 일반 공급|v1.0.0_2021-07-30|`datacontrollers`: v1beta1, v1 <br/>`exporttasks.tasks`: v1beta1, v1 <br/>`monitors`: v1beta1, v1 <br/>`sqlmanagedinstances.sql`: v1beta1, v1 <br/>`postgresqls`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`: v1beta1 <br/>`dags.sql`: v1beta1 <br/>|2021-08-01(안정)|1.0|1.0.16701001, 릴리스 학습: 안정|0.9.5|
|2021년 8월 3일|7월 30일 일반 공급에 맞춰 Azure Data Studio용 Azure Arc 확장 업데이트|변경 내용 없음|변경 내용 없음|변경 내용 없음|변경 내용 없음|변경 내용 없음|0.9.6|

전체 CRD의 경우 접두사에 `.arcdata.microsoft.com`을 추가합니다. 

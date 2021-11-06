---
title: Azure Arc 지원 데이터 서비스 - 릴리스 버전
description: Azure Arc 지원 데이터 서비스의 릴리스 날짜별 버전 로그
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/04/2021
ms.topic: conceptual
ms.openlocfilehash: ca1b31abcfb9985dadda1ece8fced0a9f70c3b04
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893921"
---
# <a name="version-log"></a>버전 로그

이 문서에서는 Azure Arc 지원 데이터 서비스의 각 릴리스와 함께 구성 요소 버전을 식별합니다.

## <a name="november-2-2021"></a>2021년 11월 2일

다음 표에서는 이 릴리스의 구성 요소에 대해 설명합니다.

|구성 요소  |값  |
|--------------------------------------------------------|---------|
|컨테이너 이미지 태그                                    | v1.1.0_2021-11-02 |
|CRD 이름 및 버전                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v2beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2 |
|ARM API 버전                                         | 2021-11-01 |
|`arcdata` Azure CLI 확장 버전                   | 1.1.0,(11월 3일),</br>1.1.1(Nov4) |
|Arc 지원 Kubernetes Helm 차트 확장 버전     | 1.0.17551005 - GA에서 업그레이드하는 경우 필수 <br/><br/> 1.1.17561007 - GA+1/Nov 릴리스 차트 |
|Azure Data Studio용 Arc Data 확장                | 0.9.7 |

## <a name="august-3-2021"></a>2021년 8월 3일

이 릴리스는 일반 공급 7월 30일과 일치하도록 Azure Data Studio 대한 Azure Arc 확장을 업데이트합니다. 다음 표에서는 이 릴리스에서 업데이트하는 구성 요소에 대해 설명합니다. 

|구성 요소  |값  |
|--------------------------------------------------------|---------|
|Azure Data Studio용 Arc Data 확장                | 0.9.6 |

다른 모든 구성 요소는 이전에 릴리스된 구성 요소와 동일합니다.

## <a name="july-30-2021"></a>2021년 7월 30일

이 릴리스에서는 Azure Arc 사용 SQL Managed Instance 범용 및 Azure Arc 지원 SQL Server 대한 일반 공급이 도입되었습니다. 다음 표에서는 이 릴리스의 구성 요소에 대해 설명합니다.

|구성 요소  |값  |
|--------------------------------------------------------|---------|
|컨테이너 이미지 태그                                    | v1.0.0_2021-07-30 |
|CRD 이름 및 버전                                  |`datacontrollers.arcdata.microsoft.com`: v1beta1, v1 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1 <br/> |
|ARM API 버전                                         | 2021-08-01(안정) |
|`arcdata` Azure CLI 확장 버전                   | 1.0 |
|Arc 지원 Kubernetes Helm 차트 확장 버전     | 1.0.16701001, 릴리스 학습: 안정 |
|Azure Data Studio용 Arc Data 확장                | 0.9.5 |

---
title: 테이블 크기 결정 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 하이퍼스케일(Citus) 서버 그룹에서 분산 테이블의 실제 크기를 찾는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 10426baece6b14984dc10a6eadc1d5fc19db382c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631896"
---
# <a name="determine-table-and-relation-size"></a>테이블 및 관계 크기 결정

PostgreSQL에서 테이블 크기를 찾는 일반적인 방법인 `pg_total_relation_size`는 하이퍼스케일(Citus) 분산 테이블의 크기를 매우 작게 보고합니다.
하이퍼스케일(Citus) 서버 그룹에서 이 기능의 역할은 코디네이터 노드의 테이블 크기를 표시하는 것이 전부입니다.  실제로 분산 테이블의 데이터는 코디네이터가 아닌 작업자 노드(분할된 상태로)에 있습니다. 분산 테이블 크기의 실제 측정값은 분할된 값의 합계로 얻을 수 있습니다. 하이퍼스케일(Citus)은 이 정보를 쿼리하는 도우미 기능을 제공합니다.

<table>
<colgroup>
<col width="40%" />
<col width="59%" />
</colgroup>
<thead>
<tr class="header">
<th>함수</th>
<th>반환</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(relation_name)</td>
<td><ul>
<li>테이블의 실제 데이터 크기("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">메인 포크</a>").</li>
<li>관계는 테이블 또는 인덱스의 이름일 수 있습니다.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(relation_name)</td>
<td><ul>
<li><p>citus_relation_size 더하기:</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">여유 공간 맵</a>의 크기</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">표시 유형 맵</a>의 크기</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(relation_name)</td>
<td><ul>
<li><p>citus_table_size 더하기:</p>
<blockquote>
<ul>
<li>인덱스의 크기</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

이러한 함수는 노드에 연결할 수 없는 경우 오류가 발생한다는 점을 제외하고 세 가지의 표준 PostgreSQL [개체 크기 함수](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)와 유사합니다.

## <a name="example"></a>예제

모든 분산 테이블의 크기를 나열하는 방법은 다음과 같습니다.

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

출력:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>다음 단계

* 더 많은 데이터를 보유하도록 [서버 그룹을 확장](howto-hyperscale-scale-grow.md)하는 방법을 알아봅니다.
* 하이퍼스케일(Citus) 서버 그룹에서 [테이블 유형](concepts-hyperscale-nodes.md)을 구별합니다.

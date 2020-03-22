---
title: DB 데이터 변경(UPDATE, DELETE)시 사용하는 보험들
date: "2020-03-22T10:00:00.169Z"
template: "post"
draft: false
slug: "about-schema"
category: "backend"
tags:
  - "think"
  - "backend"
  # - "load balancing"
# description: "스키마가 무엇인지 고민해보"
socialImage: "/media/42-line-bible.jpg"
---
# 데이터베이스의 데이터를 변경할 때 사용하는 보험들

근래 들어, 일하면서 특정 조건에 해당하는 고객데이터를 수정할 일이 많았다. DB 데이터를 변경하게 되는 UPDATE/DELETE 쿼리 문은 사용할 때 항상 조심스럽다. 트랜잭션을 마친 데이터는 DB에서의 복구가 불가능하며, 백업본으로 복구하는 것 밖에는 방법이 없기 때문이다. (백업본마저 없다면 애도를 표한다 ...) UPDATE 쿼리시에는 항상 보험을 들어놓고 시도하는 것이 안전하다. 아래는 안전한 UPDATE/DELETE를 사용할 때 안전하게 사용할 수 있도록 하는 보편적인 방법들이다.

1. DB 백업
2. 트랜잭션

나는 백업과 트랜잭션을 조합해서 다음과 같이 사용한다.

1. (테스트 DB에서) **트랜잭션**을 이용해, UPDATE/DELETE 쿼리문을 실행하고 롤백해보면서 개발한다.
2. (라이브 DB 반영시) 라이브 DB 스냅샷 떠서 백업본을 만들고, 트랜잭션을 거쳐 업데이트를 실행한다.
    - 업데이트 결과가 적절한지 확인한다.
        - affected rows를 체크
        - SELECT 문을 실행하여 해당 데이터들이 의도한데로 수정된지 확인 후 commit
3. 라이브

DB를 백업과 트랜잭션을 사용하는 자세한 내용은 다음 글에서 다룰 예정이다. 
# 📚 RDBMS Practice

**MySQL과 Oracle SQL을 학습하며 RDBMS의 핵심 개념과 쿼리 실습 진행**

SELECT, WHERE 등 다양한 SQL 함수와 조건을 활용하여 팀원들과 문제를 출제하고, 서로의 문제를 풀며 SQL 이해도를 높였습니다.

---

## 📌 학습 개요

- DBMS: **MySQL**, **Oracle**
- 학습 내용:
    - SELECT, WHERE, BETWEEN, UPPER/LOWER 등 기초~중급 SQL 문법
    - 날짜 및 문자열 함수 활용
    - 팀 프로젝트 형식으로 문제 출제 및 풀이 진행

---

## 🧠 연습 문제 예시

### ✅ 문제 1

**1981년에 입사한 사원들 중, 이름에 'A'가 포함되지 않은 사원의 이름과 입사일을, 입사일 기준 내림차순으로 검색**

### ✅ 문제 2

**입사 월이 2월이고 부서번호가 30인 사람들의 사번, 사원명, 급여 검색**

### ✅ 문제 3

**사원명에 'A'가 포함된 사람들 중, 입사 연도가 1981년 또는 1987년인 사람의 이름과 입사일 검색**

### ✅ 문제 4

**12월에 입사한 사람들 중, 급여가 2000 이상인 사람의 이름, 입사일, 급여 검색**

---

## 🧾 정답 예시 (Oracle 기준)

<details>
<summary>문제 1 - 이름에 'A' 미포함 + 1981년 입사자</summary>

```sql
SELECT ename, hiredate
FROM emp
WHERE hiredate BETWEEN TO_DATE('1981-01-01', 'YYYY-MM-DD')
  AND TO_DATE('1981-12-31', 'YYYY-MM-DD')
  AND ename NOT LIKE '%A%'
ORDER BY hiredate DESC;

SELECT ename, hiredate
FROM emp
WHERE ename NOT LIKE '%A%'
  AND TO_CHAR(hiredate, 'YYYY') = '1981'
ORDER BY hiredate DESC;

```

</details> <details> <summary>문제 2 - 2월 입사 + 부서번호 30</summary>

```sql
SELECT empno, ename, sal
FROM emp
WHERE EXTRACT(MONTH FROM hiredate) = 2 AND deptno = 30;

SELECT empno, ename, sal
FROM emp
WHERE TO_CHAR(hiredate, 'MM') = '02' AND deptno = 30;

```

</details> <details> <summary>문제 3 - 이름에 'A' 포함 + 1981년 또는 1987년 입사</summary>

```sql
SELECT ename, hiredate
FROM emp
WHERE ename LIKE '%A%' AND TO_CHAR(hiredate, 'YYYY') IN ('1981', '1987');

SELECT ename, hiredate
FROM emp
WHERE ename LIKE '%A%'
  AND (
    hiredate BETWEEN TO_DATE('1981-01-01', 'YYYY-MM-DD') AND TO_DATE('1981-12-31', 'YYYY-MM-DD')
    OR
    hiredate BETWEEN TO_DATE('1987-01-01', 'YYYY-MM-DD') AND TO_DATE('1987-12-31', 'YYYY-MM-DD')
  );

```

</details> <details> <summary>문제 4 - 12월 입사 + 급여 2000 이상</summary>

```sql
SELECT ename, hiredate, sal
FROM emp
WHERE EXTRACT(MONTH FROM hiredate) = 12 AND sal >= 2000;

SELECT ename, hiredate, sal
FROM emp
WHERE TO_CHAR(hiredate, 'MM') = '12' AND sal >= 2000;

```

</details>

---

## ⚠️ 트러블 슈팅 & 인사이트

**"날짜의 연/월 비교 시 `EXTRACT`와 `TO_CHAR` 중 어떤 방식이 성능이 더 좋은가?"**

---

## 💬 결론

- 작은 데이터셋(수천~수만 건)에서는 성능 차이가 거의 없음
- 대규모 데이터셋(수백만 건 이상)에서는 다음과 같은 차이가 발생

| 방식 | 설명 | 인덱스 사용 | 성능 |
| --- | --- | --- | --- |
| `TO_CHAR(hiredate, 'MM')` | 문자열 변환 | ❌ | 느림 |
| `EXTRACT(MONTH FROM hiredate)` | 숫자 비교 | ❌ | 보통 |
| `hiredate BETWEEN ...` | 범위 조건 | ✅ 가능 | 빠름 |

---

## ✅ 정리

- 작은 테이블 → 아무거나 써도 됨
- 습관적으로는 `BETWEEN` 또는 `EXTRACT` 추천 (숫자 비교 우선)
- 인덱스를 활용하고 싶다면 `BETWEEN` 방식이 가장 유리함
- 단순히 정답만 쓰기보다, **여러 접근법을 비교하며 최적화 방향을 고민함**

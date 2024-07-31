---
title: "MySQL, Java : 프로시저와 JDBC"
date: 2024-07-31 17:07:00 +0900
last_modified_at: 
categories: ["MySQL", "Java"]
tags: ["MySQL", "Java"]
---
# MySQL 저장 프로시저 및 Java 호출 예제

## 저장 프로시저 생성 및 호출

### 테이블 생성 및 데이터 복사
```sql
CREATE TABLE dept_clone AS SELECT * FROM dept;
```

### 데이터 삭제 저장 프로시저
```sql
DELIMITER //
CREATE PROCEDURE sp_deleteDept()
BEGIN
    DELETE FROM dept_clone;
END
//
DELIMITER ;

CALL sp_deleteDept();
```

### 데이터 삽입 저장 프로시저
```sql
DELIMITER $$
CREATE PROCEDURE sp_insertDept(
    IN v_deptno tinyint,
    IN v_dname varchar(14),
    IN v_loc varchar(13)
)
BEGIN
    INSERT INTO dept_clone(deptno, dname, loc)
    VALUES(v_deptno, v_dname, v_loc);
END
$$
DELIMITER ;
```

### 사원 정보 조회 저장 프로시저
```sql
DELIMITER $$
CREATE PROCEDURE sp_selectEmp(
    IN v_empno smallint,
    OUT v_dname varchar(14),
    OUT v_loc varchar(13)
)
BEGIN
    SELECT dname, loc INTO v_dname, v_loc
    FROM emp JOIN dept ON emp.deptno = dept.deptno
    WHERE empno = v_empno;
END
$$
DELIMITER ;
```

### 부서 이름으로 위치 조회 저장 프로시저
```sql
DELIMITER $$
CREATE PROCEDURE sp_selectDname(
    INOUT v_name varchar(14)
)
BEGIN
    DECLARE v_str varchar(14);
    SELECT loc INTO v_str
    FROM dept
    WHERE dname = v_name;
    SET v_name = v_str;
END
$$
DELIMITER ;
```

### 부서 번호로 사원 정보 조회 저장 프로시저
```sql
DELIMITER $$
CREATE PROCEDURE sp_select_emp_dept(
    IN v_deptno tinyint
)
BEGIN
    SELECT empno, ename, dname, loc
    FROM emp
    NATURAL JOIN dept
    WHERE deptno = v_deptno;
END
$$
DELIMITER ;
```

## Java 코드에서 저장 프로시저 호출 예제

### 데이터 삭제 저장 프로시저 호출
```java
String sql = "{call sp_deleteDept()}";
CallableStatement cstmt = conn.prepareCall(sql);
cstmt.execute();
```

### 데이터 삽입 저장 프로시저 호출
```java
String sql = "{call sp_insertDept(?,?,?)}";
CallableStatement cstmt = conn.prepareCall(sql);
cstmt.setInt(1, deptNo);
cstmt.setString(2, dname);
cstmt.setString(3, loc);
cstmt.execute();
```

### 사원 정보 조회 저장 프로시저 호출
```java
String sql = "{call sp_selectEmp(?,?,?)}";
CallableStatement cstmt = conn.prepareCall(sql);
cstmt.setInt(1, empno);
cstmt.registerOutParameter(2, Types.VARCHAR);
cstmt.registerOutParameter(3, Types.VARCHAR);
cstmt.execute();
System.out.println(cstmt.getString(2));
System.out.println(cstmt.getString(3));
```

### 부서 이름으로 위치 조회 저장 프로시저 호출
```java
String sql = "{call sp_selectDname(?)}";
CallableStatement cstmt = conn.prepareCall(sql);
cstmt.setString(1, dname);
cstmt.registerOutParameter(1, java.sql.Types.VARCHAR);
cstmt.execute();
System.out.println(cstmt.getString(1));
```

### 부서 번호로 사원 정보 조회 저장 프로시저 호출
```java
String sql = "{call sp_select_emp_dept(?)}";
CallableStatement cstmt = conn.prepareCall(sql);
cstmt.setInt(1, deptno);
ResultSet rs = cstmt.executeQuery();
while (rs.next()) {
    System.out.printf("%d	%s	%s	%s%n",
            rs.getInt("empno"), rs.getString("ename"), rs.getString("dname"),
            rs.getString("loc"));
}
```

### 요약
- **저장 프로시저**: MySQL에서 저장 프로시저를 작성하고, 이를 통해 다양한 데이터베이스 작업(데이터 삽입, 삭제, 조회 등)을 수행함.
- **Java 코드**: Java의 `CallableStatement`를 사용하여 저장 프로시저를 호출하고, 입력 및 출력 파라미터를 처리하는 방법을 다룸.

이를 통해 Java 애플리케이션에서 MySQL 데이터베이스와의 상호작용을 효율적으로 수행할 수 있음.

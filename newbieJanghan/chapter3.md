# 오라클 데이터베이스
오라클 사에서 개발한 ORDBMS 제품
- ORDBMS: 객체-관계형 모델을 따르는 데이터베이스를 관리하는 어플리케이션 시스템.

## 개념
### 사용자
- user
- 시스템 설치시 자동으로 관리자 user 가 만들어짐.

### 오브젝트
- 논리적인 데이터 구조. 사용자에 종속될 수 있다. 오브젝트의 논리적 집합으 스키마라고 한다.
- 사용자에 종속된 오브젝트를 스키마 오브젝트, 반대를 비 스키마 오브젝트라고 한다.
  - 비 스키마 오브젝드: User, Role, Directory
- 데이터 저장 여부에 따라 세그먼트 오브젝트, 비 세그먼트 오브젝트로 구분
  - 비 세그먼트 오브젝트: View, Sequence, Synonym, Object Type, User, Role, Directory

### 테이블
### 데이터 타입
### 데이터 무결성 Data Integrity
#### 개체 무결성
- 인스턴스가 속성이나 속성의 조합으로 식별되어야 한다.
- PK 제약조건, UNIQUE 제약 조건, NOT NULL 제약 조건으로 보장
#### 참조 무결성
- 외래 식별자가 부모의 기본 식별자에 존재해야 한다.
- FK 제약 조건, 트리거로 보장
#### 범위 무결성
- 속성값이 지정한 범위에 유효해야 함.
- 데이터 타입, 기본 값, CHECK 제약 조건으로 보장
#### 사용자 정의 무결성
- 위 세 무결성에 해당하지 않는 무결성
- 트리거로 보장한다.

### 트랜잭션
- 원자성 Atomicity: 모두 수행되거나 수행되지 않거나
- 일관성 Consistency: 트랜잭션 완료 시 데이터 무결성이 일관되게 보장되어야 함
- 독립성 Isolation: 다른 트랜잭션으로부터 독립적으로 수행되어야 함
- 지속성 Durability: 트랜잭션완료 시 장애와 무관하게 변경 내용이 지속되어야 함

### 정적 데이터 딕셔너리 뷰 & 동적 성능 뷰
- 이후 챕터에서

## 구조
> 해당 챕터는 교재 여러번 읽어보자. 정리의 의미가 없음.

### 데이터베이스와 인스턴스
- 여기서 데이터베이스는 데이터를 저장하는 파일의 모음, 인스턴스는 SGA(메모리)와 백그라운드 프로세스로 구성
- 하나의 데이터베이스에 하나 이상의 인스턴스로 구성됨
- 싱글 서버: 하나의 인스턴스만
- RAC(Real Application Clusters): 고가용성, 성능 향상을 위한 2개 이상의 인스턴스로 구성

### 프로세스 
- 백그라운드 프로레스: 인스턴스 시작 시 생성
- 서버 프로세스: 사용자가 데이터베이스 서버 접속 시 생성. 클라이언트 프로세스와 통신하는 역할

### 메모리 구조
#### SGA System Global Area
- 백그라운드, 서버 프로세스의 공유 메모리 영역
#### PGA
- 서버 프로세스 독점 메모리 영역

### 저장 구조
#### 물리 저장 구조
- OS에서 확인 가능한 파일로 저장되는 것들
#### 논리 저장 구조
- 오라클 데이터베이스 내부에서 관리.
- 상세
  - data block: 데이터를 저장하는 가장 작은 논리적 단위. 행은 블록에 저장되며, 하나의 행을 조회하더라도 행이 저장된 블록 전체를 읽어야 함.
  - extent: 논리적으로 연속된 data block 의 집합
  - segment: 오브젝트에 할당된 extent 의 집합
  - tablespace: segment를 포함하는 데이터베이스 저장 단위

### 네트워크 구조
1. 클라이언트 프로세스가 데이터베이스 서버에 접속 요청
2. 데이터베이스 서버에서 동작 중인 리스너가 해당 요청을 수락하고 서버 프로세스 생성
3. 리스너가 서버 프로세스와 요청한 클라이언트 프로세스를 연결하고 다른 접속 요청을 대기

### 어플리케이션 구조
1. local: 데이터베이스 서버 내에서 직접 접속
2. client-server: 클라이언트에서 접속 (2-tier)
3. multitier: 애플리케이션 서버를 통해서 접속 (3-tier). 일장 수 이상의 커넥션을 유지할 수 있도록 커넥션 풀을 사용한다.   
    이는 서버 프로세스 생성과 PGA 할당에 대한 부하를 경감. 왜? 미리 만들어두었으니까?

#### 커넥션과 세션
- 커넥션은 클라이언트 프로세스와 데이터베이스 인스턴스 사이의 물리적 통신 경로
- 세션은 데이터베이스에 로그인한 사용자의 상태를 나타내는 논리적 객체.
- 커넥션을 통해 세션이 생성된다.
- 커넥션을 유지한 상태에서 로그아웃 명령(`DISC`)을 실행하면 세션이 사라진다. 
- 다시 로그인 명령(`CONN`)을 실행하면 세션이 생성된다.
- 접속을 종료하면(`EXIT`) 커넥션도 사라진다.
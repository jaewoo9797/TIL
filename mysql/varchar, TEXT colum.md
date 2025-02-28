# TEXT , VARCHAR 컬럼
**MYSQL** 의 `TEXT` 와 `VARCHAR` 컬럼은 문자열 데이터 타입을 저장할 수 있는 컬럼의 속성입니다. 이번에는 두 속성의 차이점을 살펴보겠습니다. 

## ✅ 문자열 데이터 유형
|속성|`TEXT`|`VARCHAR`|
|---|---|---|
|최대 길이| 최대 4GB(`LONGTEXT`)|최대 65,535 바이트(테이블의 `row size` 제한 포함)|
|인덱싱|기본적으로 인덱스를 만들 수 없음(`FULLTEXTINDEX`는 가능) | 인덱스를 만들 수 있음|
|저장 위치| 별도의 저장영역, 디스크(`BLOB`처럼 테이블 외부 저장 가능) | 테이블 내부의 행(Row)에 저장됨|
|메모리 처리 방식|디스크 I/O를 통해 처리됨|메모리에 올려서 빠르게 처리 가능|
|정렬 및 비교|직접 정렬 불가능|문자열 비교 및 정렬 가능|


## ✅ 컬럼 타입 선정 규칙
문자열 데이터의 두 유형에 대해서 간단하게 살펴보았습니다. 두 타입을 선택하는 기준에 대해서 정리해보면 다음과 같습니다.

**VARCHAR**
- 최대 길이가 (상대적으로) 크지 않은 경우
- 테이블 데이터를 읽을 때 항상 해당 컬럼이 필요한 경우 (인덱싱 해서 빠르게 읽을 수 있음)
- DBMS 서버의 메모리가 (상대적으로) 충분한 경우

**TEXT**
- 최대 길이가 (상대적으로) 큰 경우
- 테이블에 길이가 긴 문자열 타입 컬럼이 많이 필요한 경우
- 테이블 데이터를 읽을 때 해당 컬럼이 자주 필요치 않은 경우

## 🔥 정리
- 게시글 내용처럼 길이가 가변적이고 큰 데이터를 저장할 때 `TEXT` 가 적절합니다.
- 검색이 많거나, 정렬/인덱싱이 중요한 경우는 `VARCHAR`가 더 적합합니다.

### 참조
[잘 정리된 블로그](https://medium.com/daangn/varchar-vs-text-230a718a22a1)

## 추가 학습 - VARCHAR 속성
`VARCHAT(n)` 이처럼 컬럼의 속성을 지정할 때 특정 숫자를 부여할 수 있습니다. 이는 `VARCHAR` 의 **문자 개수**를 의미하며, 바이트가 아닙니다. 즉, `VARCHAR(45)` 라면 최대 45개의 문자를 저장할 수 있는 것입니다. 앞서 살펴본 `VARCHAR`는 65,535 바이트 크기를 저장할 수 있습니다. 그렇다면 글자 하나의 크기의  바이트에 따라서 최대 몇 글자를 저장할 수 있는지 계산할 수 있습니다.

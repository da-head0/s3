# s3
* s3같은 object storage 📥 만들기

## object storage란
- 데이터 블록보다는 오브젝트(개별 유닛) 관리 (데이터, 메타데이터, 고유한 식별자)
- API 또는 http/https 를 통해 즉시 액세스 할 수 있음 (데이터 보호)
- 필요한 경우 여러 데이터센터에 복제 가능

### 구현 방법
- 디바이스 수준
- 시스템(요소간의 관계) 수준
- 인터페이스(컴퓨터와 사용자간의 통신이 가능하도록 하는 장치/프로그램) 수준


### 참조
* [S3-like strage system](https://blog.bytebytego.com/p/design-a-s3-like-storage-system)

---
#### 고려사항 
```angular2html
- 버킷 이름은 unique 해야 함
- 데이터를 업로드하기 전에 버킷을 생성해야 함
- 버킷에 저장되는 데이터는 개별 데이터 조각인 object(payload 라고도 불림), metadata
  * payload는 HTTP 요청을 보낼 때 전달되는 데이터
  * 최대 사이즈는 API마다 다른듯.. (페이지네이션으로 설정하기 나름?)
- object data는 우리가 저장하기 원하는 any sequence of bytes 일 수 있음
- metadata는 object를 설명하는 name-value 의 pairs
  * metadata는 mutable
  * object data는 immutable 
```

#### 설계
```angular2html
- metadata는 NoSQL 저장소에(버킷, object의 metadata가 있음)
- object data를 효율적으로 저장하는 방법을 고려
- payload의 최대 사이즈 결정, 최대 사이즈 넘을 경우는?
```

#### TODO
```angular2html
1. object를 저장할 저장소 고려
2. HTTP PUT/POST/DELETE 등으로 api 작동
3. 파일을 찾는 알고리즘은 어떤 게 좋을까?
  - UUID 사용
  - 이것도 하나의 데이터베이스 같군... 
  - 시간복잡도, 공간복잡도는 어떻게 될까?

(나중)
- CLI로 api 사용할 수 있게 하기 
- 웹 페이지로 api 사용할 수 있게 하기
```


#### API
```angular2html
1. 사용자, 권한 식별(S3은 IAM 사용)
  - 생성 권한이 있는가?
  - 업로드, 수정, 삭제 권한이 있는가?
  => IAM은 일단은 저장소에 정보를 넣자...

2. 버킷 생성
  - metadata가 생겨야 함

3. object 업로드
  - /bucket/object/path/file 식으로 path 제공
  - metadata가 생겨야 함 (object UUID, bucket_id, object_name, etc)
  - 업로드 성공하면 success message, UUID가 리턴되어야 함
  - 데이터 전송 전에 압축하기!
  - 데이터 보내고 압축을 풀어줘야 하나?

* http 응답의 최대 요청 시간은 얼마인가?

```


#### 저장소
```angular2html
- RAID6?
- Storage Node는 3개 이상이 이상적
  * 데이터는 이중화 되어야 함
- 데이터의 일관성을 유지해야 하므로 동기화가 필요..
  * trigger 방식?

```
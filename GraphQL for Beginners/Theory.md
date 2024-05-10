api(Application Programming Interface)

Interface

- 텔레비전을 조작하는 버튼
  - 이 버튼이 텔레비전과 상호작용을 할 수 있도록 해주는 interface
- https://youtu.be/iyFHfzCRHA8[API 기초 개념 노마드코더]

RESTFUL API

- 통신은 URL로 이뤄진다.
  - URL에는 가급적 동사 사용 X
  - 검색이나 필터를 처리할 때는 query parameter 이용

```
kamja.co/api/movies
kamja.co/api/movies/1
kamja.co/api/search?rating=9 (query parameter)
```

- JSON 형식으로 반환됨
- HTTP METHOD

  - GET
    - 데이터 받기(read only)
  - POST
    - 리소스 생성
  - PUT
    - 리소스 업데이트
  - DELETE
    - 리소스 삭제
  - PATCH
    - 리소스 부분 수정

- RESTFUL API 문제점

1. Over Fetching

- 내가 데이터를 쓰던 말던 너무 많은 data를 받는다.
- 즉, 내가 사용하고자 하는 데이터만 받을 수 없다.

2. Under Fetching

- 내가 요청한 데이터보다 적게 받는다.
  - 영화 API를 요청할 때 내가 원하는 영화가 로맨스 영화 인지, 액션 영화인지 확인하기 위해 2개의 URL에 API를 요청해야 한다.
    - 하나의 URL이 주는 것 보다 더 많은 data가 필요하여 여러개의 URL을 요청해야 한다.

GraphQL

- RESTFUL API의 Over Fetching 문제 해결
  - API에 GraphQL 쿼리를 보내고 필요한 데이터만 얻을 수 있다.
  - 즉, GraphQL은 항상 예측 가능한 결과를 반환한다.
- RESTFUL API의 Under Fetching 문제 해결
  - 단 하나의 request로 원하는 모든 데이터를 얻을 수 있다.
  - 일반적인 RESTFUL API는 여러 URL에서 로딩해야 하지만, GraphQL API는 앱에 필요한 모든 데이터를 단일 request로 가져온다.

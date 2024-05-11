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

# 4.1 Query Type

- 모든 GraphQL 서버는 스키마를 사용하여 클라이언트가 쿼리할 수 있는 데이터 구조를 정의한다..
- 타입을 정의할 때 Query는 default이다.
  - 즉, Query의 타입은 항상 정의해야한다.
    - Query type 안에 있는 것들은 GET url과 동일하다.

```
const typeDefs = gql`
  type Query {
    text: String
    hello: String
  }
`
```

- 즉, 사용자가 request하게 하도록 하려면 tyep Query안에 정의해야 한다.

# 4.2 Scalar and Root Types

Scalar type

- GraphQL에 내장되어 있는 build in type
  - GraphQL 객체 타입에는 이름과 필드가 있지만, 이 필드는 더욱 구체적인 데이터로 해석되어야 한다.
  - 이 때 스칼라 타입을 사용한다.

```
gql`
  type User{
      id: ID
      username: String
  }
  type Tweet {
    id: ID
    text: String
    author: User => Tweet이 하나의 author를 갖는다.
  }
  type Query {
    allTweets: [Tweet] => allTweets는 여러개의 Tweet을 준다.
  }
  `
```

# 4.3 Mutation Type

- RESTFUL API의 POST와 동일
- user가 보낸 data로 mutate하는 동작들을 모두 Mutation Type안에 정의한다.
  - 즉, 서버 측 데이터를 수정하는 모든 작업은 mutation을 통해 보내야 한다.

```
  type Mutation {
    postTweet(text: String, userID: ID): Tweet
    deleteTweet(id: ID): Boolean
  }
```

# 4.4 Non Nullable Fields

- !를 붙이지 않으면 Nullable 타입이다.
  - 즉, null이 올 수 있다.
  - 즉, !이 붙으면 required라고 생각
    - NonNull로 명시하면 서버는 항상 이 필드에 대해 null이 아닌 값을 반환할 것으로 예상한다.
      - 즉, null값이 들어오면 에러가 발생한다.

```
tweet(id: ID): String => tweet의 타입은 String이거나 NULL이라고 판단(Nullable)
tweet(id: ID!): String! => tweet의 타입은 String이라고 확신 즉, Null이 아니라고 확인(Non Nullable)
```

# 4.6 Query Resolvers

- resolver 함수는 DB에 엑세스한 후 다음 데이터를 반환한다.

```
const tweets = [
  {
    id: "1",
    text: "First",
  },
  {
    id: "2",
    text: "Second",
  },
];
const typeDefs = gql`
  type User {
    id: ID!
    username: String!
    firstName: String!
    lastName: String!
  }
  type Tweet {
    id: ID!
    text: String!
    author: User
  }
  type Query {
    allTweets: [Tweet!]!
    tweet(id: ID!): Tweet
    ping: String!
  }
  type Mutation {
    postTweet(text: String!, userID: ID!): Tweet!
    deleteTweet(id: ID!): Boolean!
  }
`;

const resolvers = {
  Query: {
    allTweets() {
      return tweets;
    },
  },
};

const server = new ApolloServer({ typeDefs, resolvers });
```

- Query Result

```
{
  allTweets {
    id
    text
  }
}

{
  "data": {
    "allTweets": [
      {
        "id": "1",
        "text": "First"
      },
      {
        "id": "2",
        "text": "Second"
      }
    ]
  }
}
```

- Argument

```
tweet(root, args) {
      // First Argument = root argument, Second Argument = 내가 원하는 args
      console.log(args);
      return null;
    },
```

- resolver에는 2개의 argument가 있음
  - 1번째는 root argument
  - 2번째는 우리가 원하는 데이터가 있는 arguemnt이다.
- 위의 코드 실행결과로 user가 id를 1로 해서 보내면 {id:"1"}이 콘솔에 찍힌다.

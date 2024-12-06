---
title: "Instagram Database Cloning | 인스타그램 데이터베이스 클로닝"
author: 'jinjehr'
date: 2024-11-20 12:34:00 +0900
last_modified_at: 2024-11-28 17:40:00 +0900
categories: [MySQL, Programming]
tags: [MySQL, Programming]
description: Instagram Database Cloning
toc: true 
math: true
pin: false
published: true
image:
  path: /assets/post_img/2024-11-20/instagram banner.webp
  alt: Instagram Database Cloning
---

> 인스타그램 데이터베이스가 정보를 저장하는 방법에 대해 간단히 알아보고 앞으로 확장하는데 더 유리한 설계나, 다양한 설계들의 성능까지 알아보도록 하겠습니다. 
{: .prompt-info }

---

인스타그램 데이터베이스는 실제는 수십 개의 테이블로 구성되어 있습니다. 그러나 그것들을 모두 구현하기보다는 7~8개 정도에 집중할 것입니다. 따라서 광고나 유저 데이터 등 너무 깊게 들어가기보다는 인스타그램, 페이스북 등과 같은 현재 많이 쓰이는 SNS에서 중요하고 많이 쓰이는 사진, 댓글, 유저와 같은 요소에 집중하겠습니다. 즉, 게시물 페이지를 말하는 것입니다. SNS 유저들의 대다수는 IOS나 ANDROID 앱을 사용하지만, 이번엔 웹을 이용할 것입니다. 물론 엔티티나 데이터는 같습니다. 필자의 쿼리 언어는 MySQL이며, MySQL Workbench와 Terminal을 이용하여 진행합니다.

<br>

## **Introduction**
- 눈에 가장 먼저 띄는건 `유저`입니다. 만들 테이블 중 가장 중요합니다. 다른 테이블들과 많은 관계를 이루고 있으므로 유저 테이블이 반드시 필요하겠죠?
- 그다음은 `사진`입니다. 사진을 업로드하는 방식이나 자료형의 형태는 일단 고려하지는 않겠습니다. 일단 URL로 대체할 것입니다.
- 사진이나 게시물에 대한 `좋아요` 수도 보여줘야 합니다. 하지만 하트 버튼을 10번 누른다고 좋아요가 10번 늘어나면 안 되기 때문에 이를 해결해야 할 것입니다. ~~아마 데이터베이스 자체에서 처리하면 되지 않을까요??~~
- 그리고 `해시태그`가 존재합니다. 해시태그는 인스타그램뿐만 아니라 블로그나 트위터같이 SNS 세계에서는 가장 일반적인 요소입니다. 재밌는 부분이므로 해시태그 부분에서 자세히 알아보겠습니다. 약 3가지 방법과 그 장단점에 대해 알아볼 것입니다.
- 게시물에 대한 `댓글` 또한 저장해야 합니다.
- `팔로워`와 `팔로우`도 저장할 것입니다. 그렇다면 어떤 관계로 저장해야 할까요? 우린 가장 일방적인 관계로 저장할 것입니다. 인스타그램은 누군가를 팔로우 한다고 해서 상대도 자신을 팔로우하지 않아도 됩니다.

---

## **Instagram Schema**
유저의 정보를 저장할 `Users` 테이블, 게시물의 사진을 저장할 `Photos` 테이블, 댓글을 저장할 `Comments` 테이블, 좋아요 관련 `Likes` 테이블, 팔로우와 팔로잉을 저장할 `Follows` 테이블, 해시태그와 관련된 `tags`와 `photo_tags` 테이블로 구성되어 있습니다.

<br>

### **<u>Users Table</u>**

```sql
CREATE TABLE users ( 
    id INTEGER AUTO_INCREMENT PRIMARY KEY, 
    username VARCHAR(255) UNIQUE NOT NULL, 
    created_at TIMESTAMP DEFAULT NOW() 
);
```

- 유저는 모든 것의 `중심`입니다. 따라서 이 테이블을 `사진`이나 `댓글` 등에 연결될 것입니다.
- 추가적으로 이메일, 위치정보, 비밀번호 등등 많은 것을 저장할 수 있지만 일단 간단히 만들었습니다.
- `DATETIME`보다 더 용량이 작고 저장에 용이한 `TIMESTAMP`를 사용했습니다.
- 같은 유저 이름으로 회원가입을 하면 안 되므로 `UNIQUE`를 사용했습니다.
- 유저이름이 아닌 ID를 `기본 키`로 하는 이유는 유저 이름이 255까지 저장되므로 외래 키에서 유저 이름을 참조하려고 할 때, **이름이 길다면 검색이 귀찮고 느려지기 때문입니다.**
- 유저 이름 없이 회원가입 하면 안 되므로 `NOT NULL`을 추가했습니다.

<br>

### **<u>Photos Table</u>**

```sql
CREATE TABLE photos (
    id INTEGER AUTO_INCREMENT PRIMARY KEY,
    image_url VARCHAR(255) NOT NULL,
    user_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (user_id) REFERENCES users (id)
);
```

- 어떤 유저가 사진을 생성했는지 관계를 유지해야 합니다.
- 이미지는 URL로 저장, VARCHAR를 이용해서 설명을 부여할 수 있지만 일단은 URL만 추가했습니다. 그리고 사진 테이블인데 사진이 없으면 안되므로 `NOT NULL`을 추가했습니다.
- user_id는 users.id를 가리키는 `외래 키`입니다. 비어있으면 안 되므로 `NOT NULL`을 추가했습니다.

<br>

### **<u>Comments Table</u>**

```sql
CREATE TABLE comments (
    id INTEGER AUTO_INCREMENT PRIMARY KEY,
    comment_text VARCHAR(255) NOT NULL,
    photo_id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (photo_id) REFERENCES photos (id),
    FOREIGN KEY (user_id) REFERENCES users (id)
);
```

- 댓글은 누군가가 작성한 것입니다. 따라서 일정한 관계가 존재합니다. 댓글은 유저와 연결되고 그 게시물 사진에도 연결됩니다. 그저 아무 곳에 존재하는 게 아니라 그 사진, 게시물에 존재하는 댓글입니다. 이 때문에 외래 키를 `2개`로 설정해야 합니다.
- 부모 사진이 없는 자식 댓글이 있으면 안 되므로 `NOT NULL`을 추가했습니다.

<br>

### **<u>Likes Table</u>**

```sql
CREATE TABLE likes (
    user_id INTEGER NOT NULL,
    photo_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (user_id) REFERENCES users (id),
    FOREIGN KEY (photo_id) REFERENCES photos (id),
    PRIMARY KEY (user_id , photo_id)
);
```

- 누군가 특정 사진에 좋아요를 누르게 된다면, 특정 사진과 연계되어 관계가 만들어질 것입니다.
- 웹인지, 안드로이드인지 IOS인지 구분하는 요소를 추가할 수 있겠지만, 일단은 단순하게 만들어보았습니다.
- user_id, photo_id는 `외래 키`이므로 비어있으면 안 되므로 `NOT NULL`을 추가했습니다.
- 좋아요는 ID가 없는데, 그 이유는 그냥 필요 없기 때문입니다. 즉, 다른 곳에서 좋아요를 참조할 이유가 없다는 뜻입니다. ~~물론 참조할 일이 있으면 만들어야겠지만...~~ 어떻게 유저가 좋아요를 한 번만 누르게 하는 것이 설정해야 할 가장 중요한 것입니다.
- 한 번만 좋아요를 누르게 하는 것을 데이터베이스에서 처리하려면 user_id와 photo_id의 조합이 `유일`하게 존재하면 됩니다. 또, 같은 조합의 인스턴스가 하나만 존재해야 하므로 이를 처리하기 위해 `기본 키`로 설정해줍니다. 이렇게 하면 user_id와 photo_id가 같은 이미 존재하는 좋아요는 삽입할 수 없게 됩니다.

<br>

### **<u>Follows Table</u>**

```sql
CREATE TABLE follows (
    follower_id INTEGER NOT NULL,
    followee_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (follower_id) REFERENCES users (id),
    FOREIGN KEY (followee_id) REFERENCES users (id),
    PRIMARY KEY (follower_id , followee_id)
);
```

- 특정 유저를 팔로우하고 해당 유저는 나를 팔로우하건 하지 않건 자유인 관계의 데이터를 저장해야 하므로 `일방적 관계`로 만들어 저장할 것입니다.
- 팔로우를 참조하게 하지는 않을 것이므로 ID는 필요가 없습니다. ~~물론 Like Table처럼 나중에 참조할 일이 있으면 만들어야겠지만...~~
- follower_id, followee_id는  `외래 키`로 비어있으면 안 되므로 `NOT NULL`을 추가했습니다.
- 중복으로 팔로우하는 것을 막기 위해 Likes Table처럼 `기본 키`를 설정하여 중복을 막아주었습니다.

<br>

### **<u>Hashtag Tables</u>**

```sql
CREATE TABLE tags (
    id INTEGER AUTO_INCREMENT PRIMARY KEY,
    tag_name VARCHAR(255) UNIQUE,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE photo_tags (
    photo_id INTEGER NOT NULL,
    tag_id INTEGER NOT NULL,
    FOREIGN KEY (photo_id) REFERENCES photos (id),
    FOREIGN KEY (tag_id) REFERENCES tags (id),
    PRIMARY KEY (photo_id , tag_id)
);
```

- 해시태그를 구현하는 방법은 아래 `3가지 방법`이 존재합니다. 저는 3번 방법을 사용했습니다.
- tags 테이블의 tag_name은 중복되지 않아야 하므로 `UNIQUE`를 사용했습니다.
- photo_tags 테이블의 photo_id, tag_id는 `외래 키`로 비어있으면 안 되므로 `NOT NULL`을 추가했습니다.
- 하나의 사진에 같은 해시태그를 못 달게 photo_id, tag_id를 `기본 키`로 설정하여 중복을 막아주었습니다.

<br>

#### **Hashtag Solution 1 : Create tags column in photos table**
- Photos 테이블에 VARCHAR 문자열로 태그를 저장하고 `#` 으로 태그를 구분해 저장하는 방법입니다.
- **장점** : 
  - 별도의 테이블을 생성안해도 되므로 **구현이 쉽습니다.**
- **단점** : 
  - **태그 개수에 제한이 존재한다.** 열이 VARCHAR라고 하면 설정한 VARCHAR의 최대 길이가 한계입니다. 
  - **추가 정보를 저장할 수 없다.** 태그로 사람의 정보나 생성된 시간 등등을 저장할 수가 없다. 그로인해 저장된 태그의 시간대를 이용해 최근 트렌드 등을 파악하기 어려울 것입니다. 
  - **검색시에 주의해야합니다.** 검색시 원하는 검색이 안될 수 있으므로 ~~like로 검색을 하면되지만 귀찮..~~ 태그에 공백을 추가해서 저장해 이를 구분해야한다는 단점도 존재합니다.

<br>

#### **Hashtag Solution 2 : Use 2 tables** 
- Photos 테이블과 Tags 테이블을 이용하여 각 tag에 별도의 `photo_id`를 달아주는 것입니다.
- **장점** :
  - 별도로 태그를 각각 저장하기 때문에 **태그 수에 제한이 없습니다.**
- **단점** :
  - 태그 이름을 사진에 쓰인 개수만큼 **반복해서 저장해주어야 합니다.** 
  - 삽입, 업데이트, 삭제 등의 작업 시에 **1번의 솔루션보다 느리다는 단점이 존재합니다.**

<br>

#### **Hashtag Solution 3 : Use 3 tables** 
- photos, tags(id, tag_name), photo_tags(photo_id, tag_id) 3개의 테이블을 사용합니다.
- photo_tags 테이블이 다른 두개의 테이블의 정보를 `외래 키`로 받아 저장하는 방법입니다.
- **장점** :
  - 2번 솔루션처럼 **태그 숫자에 제한이 없으며** 사용된 시간이나 장소에 대한 정보 등과 같은 **추가 정보도 저장이 가능합니다.** 또, **태그 중복이 줄어든다**는 장점도 있습니다.
- **단점** : 
  - **처리할것들이 몇개 늘어난다**는 점이 존재합니다. ~~예로 처음에 해시태그를 삽입할 때 데이터베이스가 없으므로 생성 후 photo_tags 테이블의 사진과 관계시켜야하는 정도..~~ 또, **부모관계를 고려해야한다**는 점도 존재합니다. 특히, **태그를 삭제시에 연관된 테이블이 하나 더 있으므로 주의해야 합니다.**

<br>

#### **Best Hashtag Solution**
- [TagSystem](https://archive.md/7P4uT)에서 분석한 내용을 확인해보면 데이터가 많은 대형 앱은 `1번`과 `3번`을 같이 사용하는 편이 좋습니다. 각자 다른 상황에서 최고의 성능을 보여주기 때문입니다. 또, 자주 사용되는 태그로 작업시에는 3번 방법이 빠르지만 자주 사용되지 않는 태그로 작업시에는 3번 방법이 가장 느리고 1번 방법이 가장 빠릅니다. 즉, 위와 같은 이유로 `혼합사용`을 권장한다는 결론에 도달할 수 있습니다.

<br>

### **<u>Examples Using Databases</u>**

> 본 포스트에 사용된 데이터베이스는 `ig-cloning` 이라는 배포된 인스타그램 데이터베이스 클로닝용 데이터베이스를 사용하였습니다. 필자의 개발환경은 앞서 말한 것과 같이 MySQL Workbench와 Terminal을 사용하였습니다.
{: .prompt-warning }

- 애플리케이션에 봇이 얼마나 있는지, 비활성 유저가 얼마나 있는지, 사진을 단 하나도 게시하지 않은 유저가 있는지 등등을 대규모 데이터베이스를 통해 알아볼 것입니다.
- 인스타그램에 비교하면 비교할 수 없을 정도로 작은 데이터 세트입니다. 아마 인스타그램에서 1분도 안 되는 시간에 활용되는 양이라고 생각합니다. ~~그래도 사진 개수 등을 고려하면 몇 초 정도 되지 않을까요?~~
- 입력한 데이터 세트를 일단 출력해 보면 100명의 유저가 있습니다. 여기서 100명이라는 숫자는 크지 않다는 걸 알려 드리고 싶습니다. 유저 수에 따라 댓글, 게시물, 좋아요, 태그 숫자가 지수적으로 증가므로 데이터 세트는 적당한 숫자인 100명으로 설정했습니다. 유저가 100명만 되어도 다른 엔티티의 레코드 개수가 20,000개 가까이 됩니다. 200이나 1,000명도 시도해 봤는데 숫자가 너무 커지고 쿼리가 오래 걸렸습니다. 따라서 이번 활동에는 부적절하다는 것을 느꼈습니다. 그러면 다양한 상황을 부여하여 알맞은 쿼리의 예시를 알아봅시다.

<br>

#### **Example 1 : 가장 초기에 가입한 유저 5명 찾기**
- 처음에는 그냥 간단하게 초기 가입자 5명을 찾고 싶은 상황이라고 합시다. 아래는 **가장 초기에 가입한 유저 5명을 찾는 쿼리**입니다.

```sql
SELECT * FROM users 
ORDER BY created_at 
LIMIT 5;
```

![Example 1](assets/post_img/2024-11-20/Example 1.png){: width="800"}
_가장 초기에 가입한 유저 5명 찾기_

<br>

#### **Example 2 : 가장 가입을 많이 한 요일 찾기**
- 만약 우리가 캠페인을 시행할 예정이라고 가정해보겠습니다. 애플리케이션에 새 유저가 회원 가입하도록 유도하는 것이 목적입니다. 예를 들어 페이스북에 광고한다고 가정하겠습니다. 우리는 돈이 없어 한 주 동안 광고를 게시할 자금이 없다고 생각하고 하루만 게시할 겁니다. 단순한 가정이지만 상황이 이렇다고 생각하고 예산이나 제약 조건은 다양하게 설정할 수 있습니다. 이때 일주일 중 어느 요일에 광고를 게시하는 것이 좋은지 알아내야 합니다. 아래는 **가장 가입을 많이 한 요일을 찾는 쿼리**입니다.

```sql
SELECT 
    DAYNAME(created_at) AS day, COUNT(*) AS most_registered_day
FROM
    users
GROUP BY 1
ORDER BY 2 DESC;
```

![Example 2](assets/post_img/2024-11-20/Example 2.png){: width="800"}
_가장 가입을 많이 한 요일 찾기_


<br>

#### **Example 3 : 게시물을 올리지 않은 비활성 유저 찾기**
- 이번에는 게시물을 게시한 적이 없는 유저를 식별할 겁니다. 회원 가입은 했지만, 아무것도 게시한 적이 없는 겁니다. 우리로서는 많은 유저가 활동하는 것이 좋습니다. 앱에서 적극적으로 활동하면 잠재적으로 앱이 인정받는 가치가 올라가며 광고 매출이 증가하거나 더 많은 투자를 받을 수 있습니다. 따라서 그들에게 이메일을 전송할 계획입니다. 이메일을 전송하는 건 문제 없지만 사진을 게시한 적이 없는 사람들을 찾아내야 합니다. 아래는 **게시물을 올리지 않은 비활성 유저를 찾는 쿼리**입니다.

```sql
SELECT 
    U.id AS number, U.username AS USER_WITH_NO_PHOTOS
FROM
    users AS U
        LEFT JOIN
    photos AS P ON P.user_id = U.id
WHERE
    P.image_url IS NULL;
```

![Example 3](assets/post_img/2024-11-20/Example 3.png){: width="800"}
_게시물을 올리지 않은 비활성 유저 찾기_

<br>

#### **Example 4 : 좋아요가 많은 10개의 게시물과 함께 그 정보 찾기**
- 데이터베이스 내 가장 좋아요 수가 많은 사진을 찾으려고 합니다. ~~인스타그램에서 이런 걸 한 적이 있는지는 모르겠네요.~~ 그래서 이것과 관련된 특정 대회를 연다고 가정하겠습니다. 그렇지 않더라도 인스타그램과 같은 기업 내부에서는 누구의 사진이 좋아요가 가장 많은지 또는 좋아요가 가장 많은 10개의 사진이 무엇인지 알 수 있다면 좋을 거라고 생각합니다. 아래는 **좋아요가 가장 많은 게시물과 함께 그 정보 찾는 쿼리**입니다.

 ```sql
SELECT 
    U.id AS number,
    U.username,
    P.id AS photo_id,
    P.image_url,
    COUNT(*) AS total_likes
FROM
    photos AS P
        INNER JOIN
    likes AS L ON L.photo_id = P.id
        INNER JOIN
    users AS U ON P.user_id = U.id
GROUP BY P.id
ORDER BY 5 DESC
LIMIT 10;
```

![Example 4](assets/post_img/2024-11-20/Example 4.png){: width="800"}
_좋아요가 많은 10개의 게시물과 함께 그 정보 찾기_

<br>

#### **Example 5 : 모든 유저에 대한 평균 데이터 구하기**
- 평균적으로 유저가 앱을 얼마나 사용하는지 알고 싶습니다. 투자자들이 사용자가 얼마나 적극적으로 활동하는지 알아보기 위해 평균적인 데이터가 얼마나 활성화되는지 파악하려고 합니다. 전반적인 수치를 알아내려는 겁니다. 아래를 **다양한 평균 데이터를 구하는 쿼리**입니다.

```sql
SELECT (SELECT COUNT(*) FROM photos) / (SELECT COUNT(*) FROM users) AS avg_of_all_photos; 

SELECT (SELECT COUNT(*) FROM likes) / (SELECT COUNT(*) FROM users) AS avg_of_all_likes; 

SELECT (SELECT COUNT(*) FROM comments) / (SELECT COUNT(*) FROM users) AS avg_of_all_comments;
```

![Example 5](assets/post_img/2024-11-20/Example 5.png){: width="800"}
_모든 유저에 대한 평균 데이터 구하기_

<br>

#### **Example 6 : 상위 검색된 10개의 해시태그 찾기**
- 이번에는 인스타그램 클론 애플리케이션 직원의 입장이 되어보겠습니다. 광고주들이 앱에 콘텐츠를 게시하는 것으로 스폰서를 받아 수익을 창출한다고 했을 때 가장 인기 있는 해시태그가 무엇인지 알아내려는 겁니다. 따라서 가장 많이 사용되는 해시태그 10개를 찾아 추세에 따라 이를 사용하라고 광고주에게 알리려고 합니다. 아래는 **가장 많이 검색된 10개의 해시태그를 찾는 쿼리**입니다.

```sql
SELECT 
    T.tag_name, COUNT(*) AS the_most_famous_tags
FROM
    photo_tags AS PT
        JOIN
    tags AS T ON PT.tag_id = T.id
GROUP BY T.id
ORDER BY the_most_famous_tags DESC
LIMIT 10;
```

![Example 6](assets/post_img/2024-11-20/Example 6.png){: width="800"}
_상위 검색된 10개의 해시태그 찾기_

<br>

#### **Example 7 : 게시물은 없지만 좋아요와 댓글은 존재하는 계정 찾기**
- 앞에서 말씀드린 대로 실제로 인스타그램에서 봇이 문제가 되고 있습니다. 대부분의 봇 계정은 좋아요를 많이 누르고 댓글을 많이 달지만 스스로 콘텐츠를 게시하지는 않습니다. 실제 사람이 아니며 같은 댓글을 반복해서 달기도 하고 누군가가 사진을 게시하면 1초도 되지 않아 좋아요가 10개씩 눌리곤 합니다. 실제 사람이 10명이나 즉시 좋아요를 누르는 것이 아닙니다. 새로운 게시물에 즉시 좋아요를 누르는 프로그램이 설정된 것이죠. 우리가 설정한 가상 인스타그램 클론에도 같은 문제가 있습니다. 봇이라고 생각되는 유저를 전부 찾아낼 겁니다. 사진이 257개 있으면 전부 좋아요를 누르고 800개가 있어도 전부 좋아요를 누르는 봇들이 존재합니다. 아래는 이러한 **봇 유저들을 찾아내는 쿼리**입니다.

```sql
SELECT 
    username, COUNT(*) AS num_likes
FROM
    users AS U
        INNER JOIN
    likes AS L ON U.id = L.user_id
GROUP BY L.user_id
HAVING num_likes = (SELECT 
        COUNT(*)
    FROM
        photos);
```

![Example 7](assets/post_img/2024-11-20/Example 7.png){: width="800"}
_게시물은 없지만 좋아요와 댓글은 존재하는 계정 찾기_

--- 

## Reference
- [Tagsystems: performance tests](https://archive.md/7P4uT) <sup>site</sup>
- [MySQL Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/) <sup>site</sup>

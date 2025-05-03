---
title: Challenge - 아티클 읽기_2025-05
author: 한춘식
date: 2025-05-03
category: challenge
layout: post
toc:
    enabled: true
mermaid: true
---  


# Keep
- [AI 세계에서 DevOps의 미래](https://lablabs.io/blog/future-of-devops-in-the-world-of-ai)  
- [7개월간의 자바/스프링 독학 회고](https://velog.io/@backfox/7%EA%B0%9C%EC%9B%94%EA%B0%84%EC%9D%98-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8F%85%ED%95%99-%ED%9A%8C%EA%B3%A0-2021.11-2022.07)  
- [엔지니어링 리더십의 다양한 스타일](https://blog.practicalengineering.management/different-styles-of-engineering-leadership-8f376ee6a406)
- [‘kubectl create pod’를 실행하면 발생하는 일](https://medium.com/daangn/kubectl-create-pod%EB%A5%BC-%EC%8B%A4%ED%96%89%ED%95%98%EB%A9%B4-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94-%EC%9D%BC-kube-apiserver-%EA%B0%90%EC%82%AC-%EB%A1%9C%EA%B7%B8-audig-log-%EB%A1%9C-%EC%97%BF%EB%B3%B4%EA%B8%B0-6f01487abdda)


---

<br>
<br>
<br>

# 2025.05.03
## 좋은 리팩토링 vs 나쁜 리팩토링(Good Refactoring vs Bad Refactoring)
> - [링크](https://www.builder.io/blog/good-vs-bad-refactoring)
> - 출처: 웹사이트
> - 작성자/출판사: Steve Sewell

### 핵심 내용 요약    
잘못된 리팩터링은 더 좋게 만들려고 노력하다가 오히려 나쁘게 만드는 함정에 빠지기 쉽다.  
  
나쁜 리팩터링의 예시는 다음과 같다.   
1. 코딩 스타일의 대폭 변경: 기존 코드와 완전 다른 스타일이나 새로운 라이브러리 도입은 익숙하지 않은 팀원들이 이해하기 어려운 코드가 된다.  
2. 불필요한 추상화: 과도한 추상화 계층 추가로 코드가 더 복잡해지고 이해하기 어려워진다.  
3. 일관성 부족: 특정 부분만 다른 방식으로 변경하여 전체적인 일관성을 훼손한다.  
4. 코드 이해 부족: 충분히 이해하지 못한 상태에서 리팩토링하여 중요한 기능을 제거한다.  
5. 비즈니스 맥락 무시: 기술적 측면만 고려하고 비즈니스 요구사항을 무시한다.  
6. 과도한 통합: 다양한 설정이 필요한 코드를 무분별하게 통합하여 유연성을 감소시킨다. 

올바른 리팩터링을 위한 팁은 다음과 같다. 
1. 점진적 변경: 대규모 변경보다 작고 관리하기 쉬운 단위로 단계적으로 변경한다.  
2. 코드 이해: 리팩터링 전 코드의 원래 의도와 기능을 깊게 파악한다.  
3. 기존 스타일 유지: 팀의 일관성을 위해 기존 코드 스타일을 존중한다.  
4. 과도한 추상화 지양: 복잡성이 정말 필요한 경우가 아니면 단순하게 유지한다. 
5. 테스트 작성: 리팩터링 전/후 기능이 그대로인지 확인한다. 

올바른 리팩터링을 위해 ESLint, Prettier와 같은 린팅 도구들을 활용하여 일관된 코드 스타일을 유지하고, 동료 간 리뷰를 통해 문제를 조기에 발견하거나, 충분한 기능 검증을 위한 테스트를 활용하면 좋다.  


### 주요 개념/용어 및 추가 정리
**나쁜 리팩터링 예시**  
**1. 코딩 스타일의 대폭 변경**

```python
# 기존: 일반적인 반복문
def process_users(users):
    result = []
    for user in users:
        if user['age'] >= 18:
            formatted_user = {
                'name': user['name'].upper(),
                'age': user['age'],
                'is_adult': True
            }
            result.append(formatted_user)
    return result

# 나쁜 리팩터링: 팀이 익숙하지 않은 함수형 라이브러리 도입
import toolz.curried as T

def process_users(users):
    return T.pipe(
        users,
        T.filter(lambda u: u['age'] >= 18),
        T.map(lambda u: {'name': u['name'].upper(), 'age': u['age'], 'is_adult': True}),
        list
    )
```

**2. 불필요한 추상화**

```python
# 기존: 단순한 함수
def process_users(users):
    result = []
    for user in users:
        if user['age'] >= 18:
            # 처리 로직
            formatted_user = {
                'name': user['name'].upper(),
                'age': user['age'],
                'is_adult': True
            }
            result.append(formatted_user)
    return result

# 나쁜 리팩토링: 과도한 클래스 구조
class UserProcessor:
    def __init__(self, users):
        self.users = users
    
    def process(self):
        return self.filter_adults().format_users()
    
    def filter_adults(self):
        self.users = [u for u in self.users if u['age'] >= 18]
        return self
    
    def format_users(self):
        return [self._format_user(u) for u in self.users]
    
    def _format_user(self, user):
        return {
            'name': self._format_name(user['name']),
            'age': user['age'],
            'is_adult': True
        }
    
    def _format_name(self, name):
        return name.upper()

def process_users(users):
    return UserProcessor(users).process()
```

**3. 일관성 부족**
```python
# 기존: 전체 애플리케이션에서 SQLAlchemy 사용
def get_user(user_id):
    return db.session.query(User).filter(User.id == user_id).first()

# 나쁜 리팩토링: 하나의 모델만 raw SQL로 변경
def get_post(post_id):
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM posts WHERE id = %s", (post_id,))
    return cursor.fetchone()
```

**4. 코드 이해 부족**
```python
# 기존: 중요한 캐싱 로직 포함
def fetch_user_data(user_id):
    # 캐시 확인
    cached_data = cache.get(f"user_{user_id}")
    if cached_data:
        return cached_data
    
    # API 호출 및 캐싱
    user_data = api.fetch_user(user_id)
    cache.set(f"user_{user_id}", user_data, timeout=3600)
    return user_data

# 나쁜 리팩토링: 캐싱 로직 제거
def fetch_user_data(user_id):
    return api.fetch_user(user_id)  # 캐싱 제거됨
```

**5. 비즈니스 맥락 무시**
```python
# 기존: SEO를 위한 서버 렌더링 Flask 앱
@app.route('/product/<product_id>')
def product_page(product_id):
    product = get_product(product_id)
    return render_template('product.html', product=product)

# 나쁜 리팩토링: SEO에 취약한 API 엔드포인트로 변경
@app.route('/api/product/<product_id>')
def product_api(product_id):
    product = get_product(product_id)
    return jsonify(product)  # JSON만 반환, SEO 손실
```

**6. 과도한 통합**
```python
# 기존: 다양한 설정을 가진 작업
@celery.task(time_limit=60, soft_time_limit=50)
def quick_task():
    # 빠른 작업 로직
    pass

@celery.task(time_limit=3600, soft_time_limit=3500)
def long_running_task():
    # 긴 작업 로직
    pass

# 나쁜 리팩토링: 모든 설정 통합하여 유연성 제거
def create_task(func):
    return celery.task(time_limit=300, soft_time_limit=250)(func)

@create_task
def quick_task():
    # 빠른 작업에 과도한 시간 제한 적용
    pass

@create_task
def long_running_task():
    # 긴 작업에 부족한 시간 제한 적용
    pass
```

### 중요 포인트 및 고찰
"it's surprisingly easy to fall into the trap of making things worse while trying to make them better."  
  
코드 리팩토링은 언제나 필요하다고 생각해왔지만, 그것이 오히려 부정적인 영향을 줄 수 있다는 사실을 새삼 깨달았다. 잘못된 리팩토링 사례들을 보면, 겉보기에는 객체지향적이고 구조적으로 더 깔끔해 보일 수 있다. 그러나 기존 코드에 대한 깊은 이해 없이, 팀의 충분한 합의 없이 이루어진 변화는 오히려 복잡성과 혼란을 초래하는 함정이 될 수 있음을 명심해야겠다.

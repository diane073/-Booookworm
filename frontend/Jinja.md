Jinja는 현대적이고 디자이너 친화적인 파이썬 템플릿 언어이다.
Django 템플릿 이후에 만들어졌다.
빠르고, 범용성있으며 선택적인 샌드박스 템플릿 실행 환경에 의한 안전성을 제공한다.

Jinja의 철학은 애플리케이션 로직이 가능하다면 python 에 속하게 하는 것,
기능을 너무 많이 제한하여 디자이너 작업을 어렵게 만들지 않는 것.

특징:

- 템플릿 상속 및 포함.

- 템플릿 내에서 매크로를 정의하고 가져옵니다.

- HTML 템플릿은 자동 이스케이프 기능을 사용하여 XSS가 신뢰할 수 없는 사용자 입력을 방지할 수 있습니다.

- 샌드박스 환경에서는 신뢰할 수 없는 템플릿을 안전하게 렌더링할 수 있습니다.

- 추가 구문 없이 동기화 및 비동기 기능을 자동으로 처리하는 템플릿 생성을 위한 비동기 지원.

- Babel을 통한 I18N 지원.

- 템플릿은 적시에 최적화된 Python 코드로 컴파일되고 캐시되거나 미리 컴파일될 수 있습니다.

- 예외는 디버깅을 더 쉽게 만들기 위해 템플릿에서 올바른 줄을 가리킵니다.

- 확장 가능한 필터, 테스트, 함수, 구문까지 지원합니다.

```
<title>{% block title %}{% endblock %}</title>
<ul>
{% for user in users %}
  <li><a href="{{ user.url }}">{{ user.username }}</a></li>
{% endfor %}
</ul>
```

---

설치방법

\$ pip install Jinja2

종속성
: MarkupSafe는 주입 공격을 피하기 위해 템플릿을 렌더링할 때 신뢰할 수 없는 입력을 방지합니다.

선택적 종속성
: Babel은 템플릿에서 번역 지원을 제공합니다.

# 위는 jinja 2.x.x 버전

# Jinja2 3.x.x 버전

이 문서는 템플릿 언어가 아닌 Jinja API를 묘사하고있다.

-> 템플릿 언어를 위해서는 template designer 문서 참고하기(https://jinja.palletsprojects.com/en/3.0.x/templates/)

이것은 Jinja 템플릿으로 만들어지지 않은 어플리케이션의 템플릿 인터페이스의 레퍼런스를 구현하는데에 유용하게 사용될 것이다.

## 기본

Jinja는 템플릿 환경이라 불리는 중심 오브젝트를 사용한다.
이 클래스들은 조합들과 전역객체를 저장하는 대신, 파일 시스템이나 다른 위치에서의 템플릿들을 불러오는데 사용된다.
Template 클래스 생성자를 사용하여 문자열로 템플릿을 만들더라도, 공유환경이기는 하지만 Template가 자동으로 생성해줄것이다.

대부분의 어플리케이션은 어플리케이션 초기화 시 하나의 Environment 개체를 생성하고, 이를 사용하는 템플릿을 로드한다. 몇몇 경우에, 병렬 멀티 환경에서 다른 구성의 템플릿을 유용하게 사용할 수 있다.

Package Loader를 사용해 Jinja를 구성하여 어플리케이션의 템플릿으로 로드하는 것이 가장 간단한 방법이다.

```
from jinja2 import Environment, PackageLoader, select_autoescape
env = Environment(
    loader=PackageLoader("yourapp"),
    autoescape=select_autoescape()
)
```

이 명령어가 yourapp에 있는 templates 폴더내의 템플릿들을 찾아 로더와 함께 템플릿환경을 생성해줄 것이다.
HTML파일에 대한 자동변환도 가능하다.
autoescaping : 자동변환
이 loader는 yourapp으 불러오는데에 요구되며, 절대경로를 사용한다.

다른 loaders는 다른 방식, 다른 위치에서의 템플릿을 로드하는데 이용가능하며, Loader섹션에서 더 확인 가능하다. 너의 프로젝트에 더 전문화된 소스로 작성하는 것 또한 가능하다.

템플릿을 로드하기 위해 Template를 return해주는 get_template()메서드를 사용해라

```
template = env.get_template("mytemplate.html")
```

몇몇 변수를 불러오기 위해 render()메서드를 사용할 수 있다.

```
print(template.render(the="variables", go="here"))
```

문자열을 사용하는 것보다 Template나 Environment.from_string()을 통해 template loader를 사용하는 것이 여러 장점이 있다. 템플릿 상속도 가능하다.

## High level API

Jinja template을 불러오고 어플리케이션에 로드하는데에 사용하게 될 API
Low level API는 더 깊이있게 Jinja를 활용하거나 develop extension(개발 확장)을 할때 유용할 것이다.

class jinja2.Environment([options])

첫 template이 로드되고 나서, 효과, 정해지지않은 동작을 로드할 것이다.

string 블록 시작 : {%
string 블록 끝  :  %}
variable 블록 시작 {{
variable 블록 끝  }}
comment 문자열 시작 {#
comment 문자열 끝 #}

line_statement_prefix #을 사용해서 for문을 이용할 수 있음! 아래 두 예시는 동등한 결과를 가져온다.
가독성을 위해 for, if, elif문 뒤에 콜론을 붙일 수 있다.

```
<ul>
# for item in seq
    <li>{{ item }}</li>
# endfor
</ul>

<ul>
{% for item in seq %}
    <li>{{ item }}</li>
{% endfor %}
</ul>
```

여는 괄호, 중괄호 또는 대괄호가 있는 경우 Line 문은 여러 줄에 걸쳐 있을 수 있다.

```
<ul>
# for href, caption in [('index.html', 'Index'),
                        ('about.html', 'About')]:
    <li><a href="{{ href }}">{{ caption }}</a></li>
# endfor
</ul>
```

\#\# 은 ignore되므로 줄바꾸기 기호를 제외하고 줄 끝까지의 모든 내용을 무시한다.

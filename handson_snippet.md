# ハンズオンコピーペースト用スニペット

GitPitchスライドからのコピペがうまくいかないために作成  
（Atomでは改行が保持されない事象が発生中）

## クラスベースビューを使ってみよう

### `blog/urls.py`

```python
    # 変更前
    path('post/new/', views.post_new, name='post_new'),
    # 変更後
    path('post/new/', views.PostNew.as_view(), name='post_new'),
```

### `blog/views.py`

1

```python
from django.views import View  # 追加

class PostNew(View):
    pass
```

2

```python
class PostNew(View):
    # passとしていた部分を書き換える
    form_class = PostForm
    template_name = 'blog/post_edit.html'
```

3

```python
class PostNew(View):
    form_class = PostForm
    template_name = 'blog/post_edit.html'

    # 以下を追加（form_class, template_nameと始まりの位置を揃えてください）
    def get(self, request, *args, **kwargs):
        form = self.form_class()
        return render(request, self.template_name, {'form': form})
```

4

```python
class PostNew(View):
    form_class = PostForm
    template_name = 'blog/post_edit.html'

    # getは変更がないので省略

    # 以下を追加（form_class, template_name, getと始まりの位置を揃えてください）
    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)
        # このあと追加します
        return render(request, self.template_name, {'form': form})
```

5

```python
    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)
        # 追加部分始まり（コピー&ペースト推奨）
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.published_date = timezone.now()
            post.save()
            return redirect('post_list')
        # 追加部分終わり
        return render(request, self.template_name, {'form': form})
```

6

```python
from django.contrib.auth.mixins import LoginRequiredMixin  # 追加

class PostNew(LoginRequiredMixin, View):
    # この下に変更はありません
```

## ジェネリックビューを使ってみよう

1

### blog/views.py

```python
# from django.views import View  # 使わないので削除
from django.urls import reverse_lazy  # 追加
from django.views.generic import CreateView  # 追加

class PostNew(LoginRequiredMixin, CreateView):
    form_class = PostForm
    template_name = 'blog/post_edit.html'
    success_url = reverse_lazy('post_list')
```

2

```python
class PostNew(LoginRequiredMixin, CreateView):
    # プロパティの設定は省略（変更なし）

    # フォームの入力内容に問題がない場合の処理をカスタマイズ
    def form_valid(self, form):
        form.instance.author = self.request.user
        form.instance.published_date = timezone.now()
        return super().form_valid(form)
```

## ユーザ登録機能を作ってみよう

### mysite/settings.py

```python
INSTALLED_APPS = [
    'blog',
    'accounts',  # 追加
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

### mysite/urls.py

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('accounts/login/', views.LoginView.as_view(), name='login'),
    path('accounts/logout/', views.LogoutView.as_view(next_page='post_list'), name='logout'),
    path('accounts/', include('accounts.urls')),  # 追加
    path('', include('blog.urls')),
]
```

### accounts/urls.py（新規作成）

```python
from django.urls import path
from . import views

urlpatterns = [
    path('register/', views.Register.as_view(), name='register'),
]
```

### accounts/views.py

```python
from django.contrib.auth.forms import UserCreationForm
from django.urls import reverse_lazy
from django.views.generic import CreateView

class Register(CreateView):
    template_name = 'accounts/register.html'
    form_class = UserCreationForm
    success_url = reverse_lazy('post_list')
```

### accounts/templates/accounts/register.html

```html
{% extends 'blog/base.html' %}

{% block content %}
    <h2>Reader Registeration</h2>
    <form method="POST">{% csrf_token %}
        <table>
            {% for field in form %}
                <tr>
                    <td>{{ field.label_tag }}</td>
                    <td>{{ field }}</td>
                    <td>{{ field.errors }}</td>
                </tr>
            {% endfor %}
        </table>
        <input type="submit" value="register">
    </form>
{% endblock %}
```

### blog/templates/blog/base.html

```html
<div class="page-header">
    {% if user.is_authenticated %}
        <!-- 変更なし。省略 -->
    {% else %}
        <a href="{% url 'login' %}" class="top-menu"><span class="glyphicon glyphicon-lock"></span></a>
        <!-- 以下の1行を追加 -->
        <a href="{% url 'register' %}" class="top-menu"><span class="glyphicon glyphicon-user"></span></a>
    {% endif %}
    <h1><a href="/">Django Girls Blog</a></h1>
</div>
```

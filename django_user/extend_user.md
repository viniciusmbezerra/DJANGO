## Extendendo com relacionamento de 1:1


* Criando o relacinamento entre os models
```python
# Adicionando no admin
from django.contrib import admin

from authors.models import Profile


@admin.register(Profile)
class ProfileAdmin(admin.ModelAdmin):
    ...
```

```python
# No arquivo de models
from django.contrib.auth import get_user_model
from django.db import models

User = get_user_model()


class Profile(models.Model):
    author = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(default='', blank=True)
```

* Criando signal para criar um profile logo após um user
```python
# No arquivo apps.py
class AuthorsConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'authors'

    def ready(self, *args, **kwargs) -> None:
        import authors.signals  # noqa
        super_ready = super().ready(*args, **kwargs)
        return super_ready
```

```python
# No arquivo signals.py
from django.contrib.auth import get_user_model
from django.db.models.signals import post_save
from django.dispatch import receiver

from authors.models import Profile

User = get_user_model()


@receiver(post_save, sender=User)
def create_profile(sender, instance, created, *args, **kwargs):
    if created:
        profile = Profile.objects.create(author=instance)
        profile.save()
```
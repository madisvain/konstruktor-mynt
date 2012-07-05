---
layout: post.html
title: 'Django - the undocumented authentication and registration'
tags: ['Django', 'python']
---
Django is very well written and extending it is a piece of cake - something that every Django developer should know how to do. So django has authentication and user registration already built in - something that a lot of people don't know how to use and look for 3rd party plugins like:

* [django-registration](https://bitbucket.org/ubernostrum/django-registration) by [James Bennet](http://www.b-list.org/)
* [django-userarena](https://github.com/bread-and-pepper/django-userena) by [Petar Radosevic](http://wunki.org)

Both of these libraries are very good at what they do but you really don't need them for simple & secure accounts. The solution I'm going to introduce is somewhat documented in the [django.contrib.auth documentation](https://docs.djangoproject.com/en/dev/topics/auth/) but there are little tricks I'm sure you are not aware of.


## django.contrib.auth
This is a fantastic module - very well tested as it has tons of users and features. The documentation is clear about what this module does when added to the [INSTALLED_APPS](https://docs.djangoproject.com/en/1.4/ref/settings/#installed-apps) setting but gives little clues how to go about using and customizing it for your special needs.

I will try to save you the trouble of going through the whole process of learning the ins and outs of django.contrib.auth and show you how to set up customized login, logout, registration and a password reset without including any 3rd party applications and doing it with minimal code and overhead.

### accounts application
For this post I'm going to write all of the code into my own 'accounts' application but you could name it anything you want.

~~~ { python }
python manage.py startapp accounts
~~~

This will set up the app directory for you but you'll still need to create a urls.py and forms.py into it as we'll be needing them very soon.

#### django.contrib.auth.urls.py
So if you read the documentation then it's clear that django.contrib.auth provides a couple of very handy views for us to use

~~~ { python }
from django.conf.urls import patterns, url

urlpatterns = patterns('',
    url(r'^login/$', 'django.contrib.auth.views.login', name='login'),
    url(r'^logout/$', 'django.contrib.auth.views.logout', name='logout'),
    url(r'^password_change/$', 'django.contrib.auth.views.password_change', name='password_change'),
    url(r'^password_change/done/$', 'django.contrib.auth.views.password_change_done', name='password_change_done'),
    url(r'^password_reset/$', 'django.contrib.auth.views.password_reset', name='password_reset'),
    url(r'^password_reset/done/$', 'django.contrib.auth.views.password_reset_done', name='password_reset_done'),
    url(r'^reset/(?P<uidb36>[0-9A-Za-z]{1,13})-(?P<token>[0-9A-Za-z]{1,13}-[0-9A-Za-z]{1,20})/$',
        'django.contrib.auth.views.password_reset_confirm',
        name='password_reset_confirm'),
    url(r'^reset/done/$', 'django.contrib.auth.views.password_reset_complete', name='password_reset_complete'),
)
~~~

The documentation currently does not explain that the easiest way to set up login, logout, password changing and password resetting is just to include the django.contrib.auth.urls into your urls and overwrite the required templates to fit your needs.

### yourproject.accounts.urls.py

~~~ { python }
from django.conf.urls import patterns, url, include

# Login, logout, password change, password reset
urlpatterns = patterns('yourproject.accounts.views',
    url(r'^', include('django.contrib.auth.urls')),
)
~~~

### required templates
Create the following file structure into the accounts application to specify the required templates. The names of the templates are pretty self explanitory. If you want to look at some samples then look at the [admin templates for these views](https://github.com/django/django/tree/master/django/contrib/admin/templates/registration) and also read the [documentation](https://docs.djangoproject.com/en/1.4/topics/auth/#module-django.contrib.auth.views) for variables available.

* Accounts
    * templates
        * registration
            * login.html
            * logged_out.html
            * password_change_form.html
            * password_change_done.html
            * password_reset_form.html
            * password_reset_email.html
            * password_reset_subject.txt
            * password_reset_done.html
            * password_reset_confirm.html
            * password_reset_complete.html
    * __init__.py
    * forms.py
    * models.py
    * tests.py
    * urls.py

Thats it! After completing this you will have a fully functional authentication system with password changing and resetting.

<p style="font-size: 10px;">
* Remember to include accounts.urls from the root urls.py.<br/>
** By default the login view redirects to /accounts/profile/ - this can be changed by changing the <a href="https://docs.djangoproject.com/en/dev/ref/settings/#login-redirect-url">LOGIN_REDIRECT_URL</a> and <a href="https://docs.djangoproject.com/en/dev/ref/settings/#login-url">LOGIN_URL</a> in the settings.py.<br/>
*** If you are using the django.admin then make sure your accounts application comes before the django.admin application in <a href="https://docs.djangoproject.com/en/1.4/ref/settings/#installed-apps">INSTALLED_APPS</a> setting otherwise the admin templates are going to show up
</p>

## Registration
So up until now we have not done anything special. Probably many of you already know how to do this. Next I'm going to explain how to have registration without any 3rd party modles and minimal code. We are going to do this by extending the password reset view already provided by django.contrib.auth.

So let's add some urls to the _yourproject.accounts.urls.py_

~~~ { python }
...

urlpatterns += patterns('django.contrib.auth.views',
url(r'^register/$', 'password_reset', {
        'template_name': 'registration/register.html',
        'password_reset_form': RegistrationForm,
        'email_template_name': 'registration/mail/account_created_email.html',
        'subject_template_name': 'registration/mail/account_created_subject.txt',
        'post_reset_redirect': '/accounts/register/done/'
    },
    name = 'register'
),
url(r'^register/done/$', 'password_reset_done', {
        'template_name': 'registration/register_done.html'
    },
    name = 'register_done'
),
url(r'^register/(?P<uidb36>[0-9A-Za-z]{1,13})-(?P<token>[0-9A-Za-z]{1,13}-[0-9A-Za-z]{1,20})/$',
    'password_reset_confirm', {
        'template_name': 'registration/register_confirm.html'
    },
    name='register_confirm'
),
url(r'^register/complete/$', 'password_reset_complete', {
        'template_name': 'registration/register_complete.html'
    },
    name='register_complete'),
)
~~~

So these are the urls we are going to be using for user registration. These pretty much look the same as password reset views but have different tempates and some other minor modifications.

### registration view
So as you can see we are overwriting the default template, form, email_template, subject_template and post_redirect url. There are all used by the [django.contrib.auth password_reset view](https://github.com/django/django/blob/master/django/contrib/auth/views.py#L135).

The form is the most important part of this puzzle as this is where the magic happens. We will create a form that will have fields required for the registration and the form is also going to have a customized save method that will just create an inactive user and send him a account activation email.

~~~ { python }
class RegistrationForm(PasswordResetForm):
    """
    Creates an inactive user.
    Sends the created user an email with a link for activating his account and
    setting a password.
    """
    error_css_class = 'error'
    required_css_class = 'required'

    # Extra fields
    first_name = forms.CharField(label=_('First name'), max_length=30)
    last_name = forms.CharField(label=_('Last name'), max_length=30, required=False)
    terms = forms.BooleanField(label=_("Accept Terms of Service"), required=True)

    def clean_email(self):
        if User.objects.filter(email=self.cleaned_data['email']).exists():
            raise forms.ValidationError(_('A user account with this email already exists!'))
        return self.cleaned_data["email"]

    def clean(self):
        cleaned_data = super(RegistrationForm, self).clean()
        
        # If the user did not exist
        if cleaned_data.get('email'):
            # Creates an inactive user and accepts the terms of service
            user =  User.objects.get_or_create_inactive(
                first_name = cleaned_data['first_name'],
                last_name = cleaned_data['last_name'],
                email = cleaned_data['email']
            )
            profile = user.get_or_create_profile()
            profile.save()
        
            # Cache the user for save method
            self.users_cache = [user]
        return cleaned_data
~~~

This form also makes sure the user accepts the Terms of Service before he/she can register.

### registration confirm



## user in the templates
After having the user logged in - you'd probably like to use the user information in your templates. Thats easily achiveable by including a [template context processor](https://docs.djangoproject.com/en/dev/ref/settings/#std:setting-TEMPLATE_CONTEXT_PROCESSORS) which will include the user object in all of the rendered templates.

By default this template context processor is not included.

~~~ { python }
TEMPLATE_CONTEXT_PROCESSORS = (
    ...
    'django.core.context_processors.request',
)
~~~

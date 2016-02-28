---
layout: post
title:  "О том, как такое сделано"
date:   2016-02-28 22:48:29 +0600
categories: jekyll update
excerpt_separator: <!-- excerpt -->
---
# Как я сделал этот сайт

Создаем репозиторий, как указано на [github-pages][github-pages]

Устанавливаем rvm (Ruby version Manager) [Страница установки][rvm-install]

{% highlight bash %}
brew install gpg
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable --ruby
{% endhighlight %}

<!-- excerpt -->

Устанавливаем ruby версии 2.3.0
{% highlight bash %}
rvm install ruby-2.3.0
{% endhighlight %}

Переходим в корневую директорию сайта
{% highlight bash %}
cd code/sites/qpalzz/
rvm use ruby-2.3.0
{% endhighlight %}

Устанавливаем gem Bundler
{% highlight bash %}
gem install bundler
{% endhighlight %}

Устанавливаем gem Jekyll
{% highlight bash %}
gem install jekyll
jekyll new .
{% endhighlight %}

Создаем файл Gemfile со следующим содержимым
{% highlight yaml %}
source 'https://rubygems.org'

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
{% endhighlight %}

Обновляем Bundler
{% highlight bash %}
bundle update
bundle install
{% endhighlight %}

Запускаем сайт локально
{% highlight bash %}
jekyll serve
{% endhighlight %}

Теперь его можно закоммитить и запушить в github-репозиторий!

Документация:

* [RVM][rvm-start-page]
* [Jekyll docs][jekyll-docs]
* [Jekyll’s GitHub repo][jekyll-gh]

[github-pages]: https://pages.github.com
[rvm-install]: https://rvm.io/rvm/install
[rvm-start-page]: https://rvm.io
[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
---
title: El Capitan. Папки вверху списка в Finder.app
layout: post
---
Для меня очень большой проблемой являлось то, что было сложно привыкнуть к раположению папок в списке содержимого программы Finder.app. В отличие от систем Windows, а так же Gnome, к которым я привык, невозможно отсортировать содержимое так, чтобы директории находились всегда вверху списка. 

![ordered_folders]({{ site.baseurl }}/uploads/ordered_folders.png)

В процессе поиска решения этой проблемы был найден пост [Force Your Mac to Sort Folders on Top of Files (Windows Style)][folder-on-top-original]. В нем предлагалось установить пробел в значении секции Folder файла /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings. Но он не подходил, так как при открытии в текстовом редакторе в файле, описанном в этом способе была каша, так как он находится в бинарном представлении. Затем я наткнулся на способ восстановить текстовое представление, используя программу plutil. Но восстановить файл в представление xml не вышло, так как система имела включенный [SIP][SIP], и даже от имени суперпользователя (root) этого сделать не удавалось. Так что сначала пришлось найти способ  отключения SIP ([How do I disable System Integrity Protection (SIP) AKA “rootless” on OS X 10.11, El Capitan?][disable-sip]). Объединив эти решения была предпринята попытка изменить данный файл.

Перезагрузив компьютер и, при загрузке, нажимая клавиши CMD+R загрузился в Recovery Mode. Затем вызвал терминал и набрал csrutil disable, тем самым отключив действие SIP. Перезагрузка.

Загрузившись и залогинившись под своим пользователем я смог изменять файл /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings. 

Введя команду 
{% highlight bash %}
sudo plutil -convert xml1 /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings 
{% endhighlight %}
я сконвертировал файл в представление xml. Затем, используя редактор nano изменил его так, чтобы в секции с ключем Folder значение string начиналось со знака "&nbsp;" (пробел).

Затем я произвел обратную процедуру над файлом 
{% highlight bash %}
sudo plutil -convert binary1 /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.string
{% endhighlight %}
тем самым закодировав его обратно в бинарное представление. Перелогинившись я не обнаружил изменений в отображении списков файлов. 

Я решил попробовать произвести такую же процедуру над аналогичным файлом, но уже русской локализации /System/Library/CoreServices/Finder.app/Contents/Resources/ru.lproj/InfoPlist.string. После перелогинивания появились изменения и я был очень рад.

Напоследок я перезагрузился в Recovery Mode и включил SIP обратно, набрав в терминале csrutil enable.

Еще раз проверил порядок отображения файлов и директорий и остался доволен.

### Приведу полный список действий для получения такого же эффекта:

- Перед началом всех действий крайне рекомендую произвести backup системы

- Перезагрузка в режим Recovery Mode с нажатыми клавишами CMD+R

- Отключить SIP 
{% highlight bash %}
csrutil disable
{% endhighlight %}

- Перезагрузиться и залогиниться от имени пользователя

- В терминале набрать 
{% highlight bash %}
sudo plutil -convert xml1 /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings 
{% endhighlight %}

- Открыть файл текстовым редактором (например nano) от имени суперпользователя
{% highlight bash %}
sudo nano /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings
{% endhighlight %}

- Изменить текст с
{% highlight xml %}
<key>Folder</key>
<string>Folder</string>
{% endhighlight %}
на 
{% highlight xml %}
<key>Folder</key>
<string> Folder</string>
{% endhighlight %}

- Переконвертировать файл обратно в бинарное представление
{% highlight bash %}
sudo plutil -convert binary1 /System/Library/CoreServices/Finder.app/Contents/Resources/English.lproj/InfoPlist.strings 
{% endhighlight %}

- Те же действия проделать с файлом /System/Library/CoreServices/Finder.app/Contents/Resources/ru.lproj/InfoPlist.strings

- В терминале набрать 
{% highlight bash %}
sudo plutil -convert xml1 /System/Library/CoreServices/Finder.app/Contents/Resources/ru.lproj/InfoPlist.strings 
{% endhighlight %}

- Открыть файл текстовым редактором (например nano) от имени суперпользователя
{% highlight bash %}
sudo nano /System/Library/CoreServices/Finder.app/Contents/Resources/ru.lproj/InfoPlist.strings
{% endhighlight %}

- Изменить текст с
{% highlight xml %}
<key>Folder</key>
<string>Папка</string>
{% endhighlight %}
на 
{% highlight xml %}
<key>Folder</key>
<string> Папка</string>
{% endhighlight %}

- Переконвертировать файл обратно в бинарное представление
{% highlight bash %}
sudo plutil -convert binary1 /System/Library/CoreServices/Finder.app/Contents/Resources/ru.lproj/InfoPlist.strings 
{% endhighlight %}

- Удостовериться, что директории появляются вверху списка содержимого

- Перезагрузиться в Recovery Mode зажав CMD+R

- Включить SIP
 {% highlight bash %}
csrutil enable
{% endhighlight %}

Если в некоторых директориях поддиректории не показываются вверху списка, необходимо в настройках параметров вида установить Упорядочивание: не выбрано, а параметр Сортировка: по типу.

![views_parameters]({{ site.baseurl }}/uploads/views_parameters.png)

К сожалению, на отоброжение директорий в списке файлов в окнах открытия/сохранения в различных программах это не повлияло.  

[SIP]: https://support.apple.com/en-us/HT204899
[folder-on-top-original]: http://www.howtogeek.com/67100/force-your-mac-to-put-folders-on-top-of-files-windows-style/
[disable-sip]: http://apple.stackexchange.com/questions/208478/how-do-i-disable-system-integrity-protection-sip-aka-rootless-on-os-x-10-11
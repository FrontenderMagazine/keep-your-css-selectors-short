#Используйте короткие селекторы

Есть одно *очень* важное проверенное правило: в то время, как сайты растут,
селекторы сокращаются.

Если вы хотите создавать расширяемые и масштабируемые, гибкие и предсказуемые
сайты, вы должны всерьез заботиться о том, чтобы CSS-селекторы были как можно
более удобными для разработчиков, т.е. короткими.

Уменьшение длины селекторов помогает во многом:

* Увеличивает эффективность селекторов
* Снижает зависимость от контекста
* Улучшает переносимость
* Снижает риск ошибки в селекторе
* Снижает их вес
* Делает код более отказоустойчивым

Я рассмотрю каждый из пунктов, что бы не оставить неясностей. Вы увидите, что
между ними много общего (например, снижение зависимости от контекста означает
так же улучшение переносимости селекторов), но каждый из них имеет право на
самостоятельное существование.


##Увеличение эффективности селекторов

Я уже писал об эффективности CSS-селекторов. В этой статье я обойду
вниманием многие аспекты, поэтому для полного понимания я рекомендую вам
сначала прочесть статью «[Составление эффективных CSS-селекторов][1]».

Если мы не рассматривает исключения (`*{}` - обычно самый медленный селектор,
в зависимости от того, как он используется; селектор по `id` является самым
быстрым, за ними следуют селекторы по классам; контекстные селекторы, а за ними
селекторы по псевдоклассам), мы можем с уверенностью сказать, что короткие
селекторы работают быстрее.

Это станет понятно, если сравнить 2 селектора:

    html body .header .nav{}
    .nav{}

Очевидно, что в первом примере браузер должен учитывать выполнение 4
условий: принадлежность к классу `.nav`, затем к классу `.header`, затем к
элементу `body` и затем, наконец, к элементу `html` (браузеры читают селекторы
справа налево).

Во втором примере браузеру нужно проверить выполнение только одного условия:
соответствия классу `.nav`. Браузеру нужно сделать *в четыре раза* меньше
работы, чтобы проверить соответствие селектору. Каждый раз, когда вы составляете
селектор, попробуйте сократить его, насколько это возможно без потерь. Вместо
`ul.nav{}` (2 проверки) используйте `.nav{}` (1 проверка). Вместо `.nav li a{}`
(3 проверки) используйте `.nav a{}` (2 проверки).

[Производительность селекторов выросла, и это уже не то, о чем *действительно* стоит волноваться][2],
но это не означает, что мы должны действовать нерационально. Я уверен, что
никто из нас не будет переживать о потере £5, но мы ведь не станем засовывать
банкноты в шредер... Понятие эффективности селекторов *существует*, и вы
можете увеличить их эффективность там, где сделать это **очень просто**.


##Снижение зависимости от контекста

Сокращая длину селекторов, вы снижаете величину каскада контекстных селекторов
(например, `.sidebar .promo{}`) и селекторов по дочернему элементу
(например, `.sidebar > .promo{}`). Избегая контекстных селекторов, вы избавляете
элемент от необходимости находиться внутри другого элемента. Давайте еще раз
рассмотрим пример с `.sidebar .promo{}`...

Используя селектор `.sidebar .promo{}` мы говорим, что хотим применить стили к
любому рекламному блоку внутри элемента `.sidebar`. Это означает, что мы
должны всегда размещать `.promo` внутри `.sidebar`, если хотим, что бы к нему
применились стили; мы зависим от контекста.

Заменив `.sidebar .promo{}` на что-нибудь вроде `.secondary-promo{}`, мы
получаем возможность расположить элемент практически *где угодно*, где бы нам
этого ни хотелось. Теперь мы можем расположить его не только в боковой панели,
как раньше, но и в подвале или в шапке страницы, или даже после статьи.

Снижая уровень вложенности, мы можем существенно снизить зависимость от
расположения элементов и повысить переносимость кода...


##Улучшение переносимости кода

Итак, теперь мы не привязаны к селекторам, зависящим от контекста, и мы
понимаем, что наши компоненты обладают большей переносимостью. Мы можем легко
перемещать элементы, потому что работа нашего CSS-кода зависит не от того, где
они расположены, а только от наличия. Замечательно!

Еще один способ улучшить переносимость - не уточнять их больше, чем необходимо,
как, например, селекторы вида `ul.nav{}` или `a.button{}` или `div.content{}`.

Использование слишком точных селекторов плохо, потому, что они снижают
эффективность (больше проверок, чем нам на самом деле нужно), а также (и это
гораздо важнее) потому, что они привязывают нас к конкретным элементам. Мы
больше не можем, например, применить класс `.button` для элемента `<input>`
или `<button>`. Мы не можем [применить класс `.nav` к `<ol>`, чтобы получить "хлебные крошки"][3].

**Селекторы должны быть элементо-независимыми**. Вашему CSS-коду должно быть все
равно, к какому элементу вы хотите применить ваши стили.

Еще один способ улучшить переносимость селекторов - это *полностью* отказаться
от использования селекторов по элементу. Вот пример:

    /* Основные стили для виджета */
    .widget{}

    /* Стилизация заголовков виджета */
    .widget > h2{}

Здесь у нас есть проблемный селектор; что, если этот `<h2>` необходимо будет
заменить на `<h3>`? Что, если нам необходимо будет добавить другой `<h2>`, не
являющийся заголовком, в качестве дочернего элемента для `.widget`? Мы составили
очень негибкий и непереносимый селектор. Вместо этого нам следует сделать так:

    /* Основные стили для виджета */
    .widget{}

    /* Стилизация заголовков виджета */
    .widget-title{}

Теперь мы можем применить `.widget-title` к *любому* элементу - например, к
`<h4>` — и можем использовать в виджете сколько угодно `<h4>` без классов и без
применения к ним стилей заголовков. Чудесно!


##Снижение риска ошибки в селекторе

Чем длиннее селектор, тем в выполнении большего количества условий необходимо
убедиться браузеру, прежде чем он сможет применить его. Чем больше проверок,
тем, естественно, больше шанс, что что-то пойдет не так.

Избыточный селектор вида `body > div:nth-of-type(2) > article:first-child >
p:first-child{}` — взятый из моего доклада [Ломаем хорошие привычки][4]
 - содержит *10* проверок; десять условий, которые должны выполниться, чтобы
 браузер смог выбрать элемент.

Достаточно, чтобы расположение `div:nth-of-type(2)` изменилось, или чтобы
`p:first-child` заменили на `blockquote`, или чтобы `article:first-child`
перестал быть дочерним элементом `div:nth-of-type(2)`, или произошла *любая
другая* ошибка - и селектор перестанет работать. Простая замена этого селектора
на класс `.intro{}` означает, что ошибка может случиться только в одном месте,
а вероятность этого близка к нулю (нужно специально удалить этот класс из кода,
чтобы браузер не нашел совпадений).

Короткие селекторы имеют по статистике меньший шанс ошибки.


##Снижение веса

Это очень важно! Вот, что на самом деле имеет значение!

Более длинные селекторы имеют больший вес. Вес селекторов - это ночной кошмар
и **вам следует снижать его, насколько это возможно**. Мы уже знаем, что
[не стоит использовать селекторы по `id` в CSS][5], но длинный каскад селекторов
так же плох.

Селекторы вида `.widget > h2{}` имеют больший вес (наряду с другими
указанными проблемами), чем селекторы вида `.widget-title{}`.

`.nav li a{}` имеет больший вес, чем `.nav a` (а также менее эффективен).
Снижение длины селектора снижает его вес, и это **очень важно**. Большой вес
ведет к непрерывной конкурентной борьбе между селекторами, которая может быть
выиграна только при использовании дополнительных селекторов с *большим весом*
(или использованием `!important`, но в таком случае - позор вам). Это ужасно.
Самый простой способ снизить вес (после *полного отказа от использования
селекторов по `id` в CSS*) это отказ от использования длинных селекторов.


##Повышение отказоустойчивости

Это очень специфический, но и очень хороший пример того, как короткие селекторы
могут повысить отказоустойчивость. Тем не менее, должен предупредить, что вы
можете рассмотреть то, о чем я собираюсь вам рассказать, с разных точек зрения;
вы можете утверждать, что код становится более гибким и может изящно обрабатывать
ошибки **или** вы можете утверждать, что код слишком толерантен к ошибкам.
Тем не менее, вот вам реальная история...

Работая над достаточно крупным проектом в Sky, я придерживался своих правил,
когда реализовывал (вертикальную) навигационную панель:

    .nav{ /* Стили для навигации */ }

    /* Заметьте, что я не использовал стили для .nav li, так как это была
    вертикальная панель. */

    .nav a{ display:block; /* Дополнительные стили */ }

В CMS была ошибка, которая привела к тому, что генерируемая разметка выглядела
вот так:

    <ul class=nav>
        <a href=#></a>
        <a href=#></a>
        <a href=#></a>
        <a href=#></a>
    </ul>

Заметили ошибку? Нет `<li>`! Это совсем не круто, но, так как я использовал
`.nav a{}` вместо `.nav li a{}`, ничего не сломалось. Мой код был более
устойчив к ошибкам, чем если бы я добавил дополнительную проверку.

Ну, это не делает разметку правильной, и *позволяет* писать худший код,
чем при использовании более точного селектора, но вы сами видите,
насколько CSS может быть толерантным к ошибкам разметки.

Как я и говорил, вы можете занять любую сторону. Более точный селектор
означал бы, что мы бы сразу же заметили ошибку в CMS, потому что для `<a>` не
применились бы стили. Но! В то же время наш CSS был достаточно гибким,
чтобы не обращать на это внимание. Выводы делайте сами, а я займу нейтральную
позицию, будучи немного разочарован тем, что ошибка в CMS не была замечена. Это
очень специфический пример того, как короткие селекторы могут сделать CSS более
отказоустойчивым.


##В заключение

Я говорил, что это правило я применял к большим сайтам, но, честно говоря, вам
стоит применять его везде. Правило, о котором мы говорим, становится все более
важным (а неприменение его сказывается очень болезненно) на больших проектах,
но оно *определенно* будет полезно на проектах любых размеров, больших и маленьких.

Итак, при использовании большего количества классов и меньшего числа потомков,
используя короткие, переносимые и элементо-независимые селекторы, и, в целом,
принимая во внимание необходимость поддержки и возможность изменений в коде при
написании CSS, мы можем улучшать качество нашего кода до бесконечности. Мы
можем сделать наш код более эффективным и отказоустойчивым, более гибким
и удобными для повторного использования просто пересмотрев один из наиболее
простых и фундаментальных аспектов CSS - наши селекторы.

[1]: http://csswizardry.com/2011/09/writing-efficient-css-selectors/
[2]: http://calendar.perfplanet.com/2011/css-selector-performance-has-changed-for-the-better/
[3]: http://csswizardry.com/2011/09/the-nav-abstraction/
[4]: https://speakerdeck.com/u/csswizardry/p/breaking-good-habits?slide=15
[5]: http://csswizardry.com/2011/09/when-using-ids-can-be-a-pain-in-the-class/
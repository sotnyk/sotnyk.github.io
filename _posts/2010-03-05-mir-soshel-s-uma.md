---
id: 94
title: 'ɐwʎ ɔ vǝmоɔ dиw'
date: '2010-03-05T09:52:15+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=94'
permalink: /2010/03/05/mir-soshel-s-uma/
---

Обнаружил, что UNICODE-шрифтах есть перевернутые буквы. Еще не разбирался, для каких алфавитов это действует, на каких системах и т.п. Но выглядит необычно:

.ʁɔvʎнdǝʚǝdǝu dиw – ɐwʎ ɔ vǝmоɔ dиw  
  
К сожалению, XP данную фичу не поддерживает (по крайней мере в скайпе). Возможно, там просто шрифты не полные. Еще посмотрю. Мне эту фишку показали Илья и Катя.

Update 2011-03-05:  
Более подробно как это сделать, можно прочитать здесь: <https://sotnyk.github.io/?p=507>  
А сделать здесь: <https://sotnyk.github.io/crazy-world/>

Update 2023-01-08:
При переезде на Github Pages пока отключил данную страницу.

Если вам интересно повторить у себя, то там был такой скрипт, из которого можете вытащить строки преобразования:

```html
 <script type="text/javascript">
          \<!--
        var original = "abcdefghigklmnopqrstuvwxyz"+
        ".,;!?'"+
        "абвгдеёжзийклмнопрстуфхцчшщьъэя";
        var transformed = "ɐqɔpǝɟƃɥıɾʞlɯuodbɹsʇnʌʍxʎz" +
        "˙'؛¡¿," +
        "ɐgʚL6ǝǝжɛииʞvwноudɔшʎфхпhmmqqєʁ"
        function MakeUpDown() {
            var lovercased = document.getElementsByName("SourceText")[0].value.toString().toLowerCase()
            var res = "";
            for (i = lovercased.length - 1; i >= 0; --i) {
                var pos = original.indexOf(lovercased.substring(i, i + 1), 0);
                if (pos < 0)
                    res += lovercased.substring(i, i + 1);
                else
                    res += transformed.substring(pos, pos + 1);
            }
            res = res.replace("ю", "oı").replace("ы", "ıq");
            document.getElementsByName("ResultText")[0].value = res;
        }
\-->
      </script>

<form action="" name="InputForm">Вводите здесь:

 <input name="SourceText" onchange="MakeUpDown()" onkeyup="MakeUpDown()" style="width: 100%" type="text" value="мир сошел с ума опять"></input>

Забирайте отсюда:

 <input name="ResultText" readonly="readonly" style="width: 100%; font-family: Tahoma;" type="text" value=""></input>

</form> <script type="text/javascript">
          \<!--
          MakeUpDown();
\-->
      </script>
```
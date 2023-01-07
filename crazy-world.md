---
id: 503
title: 'plɹoʍ ʎzɐɹɔ'
date: '2011-03-05T14:54:56+00:00'
author: serge
layout: page
guid: 'http://sotnyk.com/'
---

Переворачиваем слова. Поддерживаются латинский и кириллица (в объеме английского и русского алфавитов). Не все символы переворачиваются хорошо. [Подробности здесь](http://localhost/?p=507).

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
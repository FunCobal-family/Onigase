# 鬼瀬 (鬼雲改) 正規表現 Version 0.0.1

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [基本要素](#%E5%9F%BA%E6%9C%AC%E8%A6%81%E7%B4%A0)
- [文字](#%E6%96%87%E5%AD%97)
- [文字種](#%E6%96%87%E5%AD%97%E7%A8%AE)
- [量指定子](#%E9%87%8F%E6%8C%87%E5%AE%9A%E5%AD%90)
- [錨](#%E9%8C%A8)
- [文字集合](#%E6%96%87%E5%AD%97%E9%9B%86%E5%90%88)
- [拡張式集合](#%E6%8B%A1%E5%BC%B5%E5%BC%8F%E9%9B%86%E5%90%88)
- [後方参照](#%E5%BE%8C%E6%96%B9%E5%8F%82%E7%85%A7)
- [部分式呼出し ("田中哲スペシャル")](#%E9%83%A8%E5%88%86%E5%BC%8F%E5%91%BC%E5%87%BA%E3%81%97-%E7%94%B0%E4%B8%AD%E5%93%B2%E3%82%B9%E3%83%9A%E3%82%B7%E3%83%A3%E3%83%AB)
- [捕獲式集合](#%E6%8D%95%E7%8D%B2%E5%BC%8F%E9%9B%86%E5%90%88)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

使用文法: ONIG_SYNTAX_RUBY (既定値)

## 基本要素

\ 退避修飾 (エスケープ) 正規表現記号の有効/無効の制御
| 選択子
(...) 式集合 (グループ)
[...] 文字集合 (文字クラス)

## 文字

\0 空白 (0x00,NUL)

\t 水平タブ (0x09)

\v 垂直タブ (0x0B)

\ タブ一括(\t,\v のいずれにもマッチ)

\n 改行 (0x0A,LF...unix)

\r 復帰 (0x0D,CR...mac)

\r\n 復帰+改行(0x0D 0x0A,CRLF...windows,dos)

\b 後退空白 (0x08)

\f 改頁 (0x0C)

\a 鐘 (0x07)

\e 退避修飾 (0x1B)

\nnn 八進数表現 符号化バイト値(の一部)

\xHH 十六進数表現 符号化バイト値(の一部)

\x{7HHHHHHH} 拡張十六進数表現 コードポイント値

\uHHHH 拡張十六進数表現 コードポイント値

\cx 制御文字表現 コードポイント値

\C-x 制御文字表現 コードポイント値

\M-x 超 (x|0x80) コードポイント値

\M-\C-x 超 + 制御文字表現 コードポイント値

※ \b は、文字集合内でのみ有効

※ ONIG_SYNTAX_PERL では \o{nnn} (八進数表現)も使用できる。

## 文字種

. 任意文字 (改行を除く)

\w 単語構成文字

    Unicode以外の場合:
        英数字 および "_"。

    Unicodeの場合:
        General_Category
        -- (Letter|Mark|Number|Connector_Punctuation)

        ASCII外の文字を含むかどうかは ONIG_OPTION_ASCII_RANGE オプションに依存する。

\W 非単語構成文字

\s 空白文字

    Unicode以外の場合:
        \0,\t, \n, \v, \f, \r, \x20

    Unicodeの場合:
        0009, 000A, 000B, 000C, 000D, 0085(NEL),
        General_Category
        -- Line_Separator
        -- Paragraph_Separator
        -- Space_Separator

        ASCII外の文字を含むかどうかは ONIG_OPTION_ASCII_RANGE オプションに依存する。

\S 非空白文字

\d 10 進数字

    Unicodeの場合: General_Category -- Decimal_Number

    ASCII外の文字を含むかどうかは ONIG_OPTION_ASCII_RANGE オプションに依存する。

\D 非 10 進数字

\h 16 進数字 [0-9a-fA-F]

\H 非 16 進数字

Character Property

    * \p{property-name}
    * \p{^property-name}    (negative)
    * \P{property-name}     (negative)

    property-name:

     + 全てのエンコーディングで有効
       Alnum, Alpha, Blank, Cntrl, Digit, Graph, Lower,
       Print, Punct, Space, Upper, XDigit, Word, ASCII,

     + EUC-JP, Shift_JIS, CP932で有効
       Hiragana, Katakana, Han, Latin, Greek, Cyrillic

     + UTF-8, UTF-16, UTF-32で有効
       UnicodeProps.txt 参照

    \p{Punct} は、Unicodeの場合とそれ以外で少し異なった動作をする。Unicode
    以外の場合は、"$+<=>^`|~" の9個の文字にマッチするが(これは [[:punct:]] と
    同じである)、Unicodeの場合はマッチしない。
    Unicodeの場合、\p{XPosixPunct} はこの9個の文字にマッチする。

\R 改行文字 (Linebreak)

    Unicodeの場合:
        (?>\x0D\x0A|[\x0A-\x0D\x{85}\x{2028}\x{2029}])

    Unicode以外の場合:
        (?>\x0D\x0A|[\x0A-\x0D])

\X 拡張書記素クラスタ (Extended Grapheme cluster)

    Unicodeの場合:

参照: [Unicode Standard Annex #29 UNICODE TEXT SEGMENTATION](http://unicode.org/reports/tr29/)

    Unicode以外の場合:
        (?>\x0D\x0A|(?m:.))

## 量指定子

欲張り

    ?       一回または零回
    *       零回以上
    +       一回以上
    {n,m}   n回以上m回以下
    {n,}    n回以上
    {,n}    零回以上n回以下 ({0,n})
    {n}     n回

無欲

    ??      一回または零回
    *?      零回以上
    +?      一回以上
    {n,m}?  n回以上m回以下
    {n,}?   n回以上
    {,n}?   零回以上n回以下 (== {0,n}?)

強欲 (欲張りで、繰り返しに成功した後は回数を減らすような後退再試行をしない)

    ?+      一回または零回
    *+      零回以上
    ++      一回以上

    ({n,m}+, {n,}+, {n}+ は、ONIG_SYNTAX_JAVAとONIG_SYNTAX_PERLでのみ強欲な
    指定子)

    例. /a*+/ === /(?>a*)/

## 錨

^ 行頭

$ 行末

\b 単語境界

\B 非単語境界

\A 文字列先頭

\Z 文字列末尾、または文字列末尾の改行の直前

\z 文字列末尾

\G 照合開始位置

## 文字集合

^... 否定 (最低優先度演算子)
x-y 範囲 (x から y まで)
[...] 集合 (文字集合内文字集合)
..&&.. 積演算 (^の次に優先度が低い演算子)

     例. [a-w&&[^c-g]z] ==> ([a-w] and ([^c-g] or z)) ==> [abh-w]

※ '[', '-', ']'を、文字集合内で通常文字の意味で使用したい場合には、これらの文字を'\\'で退避修飾しなければならない。

POSIX ブラケット ([:xxxxx:], 否定 [:^xxxxx:])

    Unicode以外の場合:

      alnum    英数字
      alpha    英字
      ascii    0 - 127
      blank    \t, \x20
      cntrl
      digit    0-9
      graph    \x21-\x7E および 多バイト文字全部を含む
      lower
      print    \x20-\x7E および 多バイト文字全部を含む
      punct
      space    \t, \n, \v, \f, \r, \x20
      upper
      xdigit   0-9, a-f, A-F
      word     英数字, "_" および 多バイト文字

    Unicodeの場合:

      alnum    Letter | Mark | Decimal_Number
      alpha    Letter | Mark
      ascii    0000 - 007F
      blank    Space_Separator | 0009
      cntrl    Control | Format | Unassigned | Private_Use | Surrogate
      digit    Decimal_Number
      graph    [[:^space:]] && ^Control && ^Unassigned && ^Surrogate
      lower    Lowercase_Letter
      print    [[:graph:]] | Space_Separator
      punct    Connector_Punctuation | Dash_Punctuation | Close_Punctuation |
               Final_Punctuation | Initial_Punctuation | Other_Punctuation |
               Open_Punctuation | 0024 | 002B | 003C | 003D | 003E | 005E |
               0060 | 007C | 007E
      space    Space_Separator | Line_Separator | Paragraph_Separator |
               0009 | 000A | 000B | 000C | 000D | 0085
      upper    Uppercase_Letter
      xdigit   0030 - 0039 | 0041 - 0046 | 0061 - 0066
               (0-9, a-f, A-F)
      word     Letter | Mark | Decimal_Number | Connector_Punctuation


    POSIXブラケットがASCII外の文字にマッチするかどうかは
    ONIG_OPTION_ASCII_RANGEオプションとONIG_OPTION_POSIX_BRACKET_ALL_RANGE
    オプションに依存する。

## 拡張式集合

(?#...) 注釈

(?imxdau-imx) 孤立オプション

i: 大文字小文字照合

m: 複数行

x: 拡張形式

    文字集合オプション (文字範囲オプション)
        d: デフォルト (Ruby 1.9.3 互換)
            \w, \d, \s は、非ASCII文字にマッチしない。
            \b, \B, POSIXブラケットは、各エンコーディングのルールに従う。
        a: ASCII
            ONIG_OPTION_ASCII_RANGEオプションがオンになる。
            \w, \d, \s, POSIXブラケットは、非ASCII文字にマッチしない。
            \b, \B は、ASCIIのルールに従う。
        u: Unicode
            ONIG_OPTION_ASCII_RANGEオプションがオフになる。
            \w (\W), \d (\D), \s (\S), \b (\B), POSIXブラケットは、各エンコーディングのルールに従う。

(?imxdau-imx:式) 式オプション

(式) 捕獲式集合

(?:式) 非捕獲式集合

(?=式) 先読み

(?!式) 否定先読み

(?<=式) 戻り読み

(?<!式) 否定戻り読み

    戻り読みの式は固定文字長でなければならない。
    しかし、最上位の選択子だけは異なった文字長が許される。
    例. (?<=a|bc) は許可. (?<=aaa(?:b|cd)) は不許可

    否定戻り読みでは、捕獲式集合は許されないが、
    非捕獲式集合は許される。

\K 保持
戻り読みの別表記。\K の左側を保持し、検索結果に含まない。

(?>式) 原子的式集合
式全体を通過したとき、式の中での後退再試行を行なわない

(?\<name>式), (?'name'式)
名前付き捕獲式集合
式集合に名前を割り当てる(定義する)。
(名前は単語構成文字でなければならない。)

    名前だけでなく、捕獲式集合と同様に番号も割り当てられる。
    番号指定が禁止されていない状態 (10. 捕獲式集合 を参照)のときは、名前を使わないで番号でも参照できる。

    複数の式集合に同じ名前を与えることは許されている。

(?(条件)真の式), (?(条件)真の式|偽の式)　条件式

(条件)が真であれば真の式がマッチし、偽であれば偽の式がマッチする。
(条件)には以下のものが使用できる。

    (n)  (n >= 1)
    番号指定の後方参照が何かにマッチしていれば真、マッチしていなければ偽

    (<name>), ('name')
    名前指定の後方参照が何かにマッチしていれば真、マッチしていなければ偽

    バグ: 指定されていた名前が多重定義されていた場合、最も左のグループを参照するが、これは本来 \k<name>と同じ動作になるべきである。

(?~式) 非包含オペレータ (実験的)

式にマッチする文字列を含まない任意の文字列にマッチする。

より正確には、(?~式) は、.\*式.\* がマッチする集合の補集合にマッチする。これは形式言語理論の意味で正規である。

(?:(?!式).)\* と似ているが簡単に書ける。

例:

    (?~abc) は以下にマッチする: "", "ab", "aab", "ccdd" 等
    以下にはマッチしない: "abc", "aabc", "ccabcdd" 等

    \/\*(?~\*\/)\*\/ は C スタイルのコメントにマッチする:
    "/**/", "/* foobar */" 等

    \A\/\*(?~\*\/)\*\/\z は "/**/ */" にはマッチしない。
    これは、無欲な量指定子(.*?)を使った \A\/\*.*?\*\/\z とは異なる。

    (?:(?!abc).)*c とは違い、(?~abc)c は "abc" にマッチする。
    なぜなら (?~abc)は"ab" にマッチするからである。

    (?~) は何にもマッチしない。

理論的背景は田中哲氏の論文とスライドで議論されている

(どちらも日本語):

\* [Absent Operator for Regular Expression](https://staff.aist.go.jp/tanaka-akira/pub/prosym49-akr-paper.pdf)

\* [正規表現における非包含オペレータの提案](https://staff.aist.go.jp/tanaka-akira/pub/prosym49-akr-presen.pdf)

## 後方参照

\n \k\<n> \k'n' (n >= 1) 番号指定参照
\k<-n> \k'-n' (n >= 1) 相対番号指定参照
\k\<name> \k'name' 名前指定参照

名前指定参照で、その名前が複数の式集合で多重定義されている場合には、
番号の大きい式集合から優先的に参照される。
(マッチしないときには番号の小さい式集合が参照される)

※ 番号指定参照は、名前付き捕獲式集合が定義され、
かつ ONIG_OPTION_CAPTURE_GROUP が指定されていない場合には、
禁止される。(10. 捕獲式集合 を参照)

※ ONIG_SYNTAX_PERL では、\g{n}, \g{-n}, \g{name} も使用可能。
Perl 文法で名前が多重定義されていた場合、最も左のグループのみが
参照される。

再帰レベル付き後方参照

    (n >= 1, level >= 0)

    \k<n+level>  \k'n+level'
    \k<n-level>  \k'n-level'
    \k<-n+level> \k'-n+level'
    \k<-n-level> \k'-n-level'

    \k<name+level> \k'name+level'
    \k<name-level> \k'name-level'

    後方参照の位置から相対的な部分式呼出し再帰レベルを指定して、そのレベルでの
    捕獲値を参照する。

    例-1.

      /\A(?<a>|.|(?:(?<b>.)\g<a>\k<b+0>))\z/.match("reer")

    例-2.

      r = Regexp.compile(<<'__REGEXP__'.strip, Regexp::EXTENDED)
      (?<element> \g<stag> \g<content>* \g<etag> ){0}
      (?<stag> < \g<name> \s* > ){0}
      (?<name> [a-zA-Z_:]+ ){0}
      (?<content> [^<&]+ (\g<element> | [^<&]+)* ){0}
      (?<etag> </ \k<name+1> >){0}
      \g<element>
      __REGEXP__

      p r.match('<foo>f<bar>bbb</bar>f</foo>').captures

## 部分式呼出し ("田中哲スペシャル")

\g<0> \g'0' パターン全体の再帰呼び出し

\g\<n> \g'n' (n >= 1) 番号指定呼出し

\g<-n> \g'-n' (n >= 1) 後方への相対番号指定呼出し

\g<+n> \g'+n' (n >= 1) 前方への相対番号指定呼出し

\g\<name> \g'name' 名前指定呼出し

※ ONIG_SYNTAX_RUBY では多重定義された名前による部分式呼び出しはできない。

※ 最左位置での再帰呼出しは禁止される。

例. (?\<name>a|\g\<name>b) => error
(?\<name>a|b\g\<name>c) => OK

※ 番号指定呼出しは、名前付き捕獲式集合が定義され、
かつ ONIG_OPTION_CAPTURE_GROUP が指定されていない場合には、
禁止される。 (10. 捕獲式集合 を参照)

※ 呼び出された式集合のオプション状態が呼出し側のオプション状態と異なっている
とき、呼び出された側のオプション状態が有効である。

     例. (?-i:\g<name>)(?i:(?<name>a)){0} は "A" に照合成功する。

※ ONIG_SYNTAX_PERL では \g<> の代わりに (?&name), (?n), (?-n), (?+n), (?R),
(?0) を使用する。
多重定義された名前による部分式呼び出しは可能である。その際、最も左の
部分式が利用される。

## 捕獲式集合

捕獲式集合(...)は、以下の条件に応じて振舞が変化する。
(名前付き捕獲式集合は変化しない)

case 1. /.../ (名前付き捕獲式集合は不使用、オプションなし)

     (...) は、捕獲式集合として扱われる。

case 2. /.../g (名前付き捕獲式集合は不使用、オプション 'g'を指定)

     (...) は、非捕獲式集合として扱われる。

case 3. /..(?\<name>..)../ (名前付き捕獲式集合は使用、オプションなし)

     (...) は、非捕獲式集合として扱われる。
     番号指定参照/呼び出しは不許可。

case 4. /..(?\<name>..)../G (名前付き捕獲式集合は使用、オプション 'G'を指定)

     (...) は、捕獲式集合として扱われる。
     番号指定参照/呼び出しは許可。

但し
g: ONIG_OPTION_DONT_CAPTURE_GROUP

G: ONIG_OPTION_CAPTURE_GROUP

('g'と'G'オプションは、ruby-dev ML で議論された。)

これらの振舞の意味は、
名前付き捕獲と名前無し捕獲を同時に使用する必然性のある場面は少ないであろう
という理由から考えられたものである。

---

補記 1. 文法依存オプション

- ONIG_SYNTAX_RUBY
  (?m): 終止符記号(.)は改行と照合成功

- ONIG_SYNTAX_PERL、ONIG_SYNTAX_JAVA、ONIG_SYNTAX_PYTHON
  (?s): 終止符記号(.)は改行と照合成功
  (?m): ^ は改行の直後に照合する、$ は改行の直前に照合する

- ONIG_SYNTAX_PERL
  (?d), (?l): (?u)と同じ

補記 2. 独自拡張機能

- 16 進数数字、非 16 進数字 \h, \H
- 名前付き捕獲式集合 (?\<name>...), (?'name'...)
- 名前指定後方参照 \k\<name>
- 部分式呼出し \g\<name>, \g\<group-num>

補記 3. Perl 5.18.0 と比較して存在しない機能

- \N{name}, \N{U+xxxx}, \N
- \l,\u,\L,\U, \C
- \v, \V, \h, \H
- (?{code})
- (??{code})
- (?|...)
- (?[])
- (\*VERB:ARG)

\* \Q...\E
但し ONIG_SYNTAX_PERL と ONIG_SYNTAX_JAVA では有効

補記 4. Ruby 1.8 の日本語化 GNU regex(version 0.12)との違い

- 文字 Property 機能追加 (\p{property}, \P{Property})
- 16 進数字タイプ追加 (\h, \H)
- 戻り読み機能を追加
- 強欲な繰り返し指定子を追加 (?+, \*+, ++)
- 文字集合の中の演算子を追加 ([...], &&)
  ('[' は、文字集合の中で通常の文字として使用するときには
  退避修飾しなければならない)
- 名前付き捕獲式集合と、部分式呼出し機能追加
- 多バイト文字コードが指定されているとき、
  文字集合の中で八進数または十六進数表現の連続は、多バイト符号で表現された
  一個の文字と解釈される
  (例. [\xa1\xa2], [\xa1\xa7-\xa4\xa1])
- 文字集合の中で、一バイト文字と多バイト文字の範囲指定は許される。
  ex. /[a-あ]/
- 孤立オプションの有効範囲は、その孤立オプションを含んでいる式集合の
  終わりまでである
  例. (?:(?i)a|b) は (?:(?i:a|b)) と解釈される、(?:(?i:a)|b)ではない
- 孤立オプションはその前の式に対して透過的ではない
  例. /a(?i)\*/ は文法エラーとなる
- 不完全な繰り返し範囲指定子は通常の文字列として許可される
  例. /{/, /({)/, /a{2,3/
- 否定的 POSIX ブラケット [:^xxxx:] を追加
- POSIX ブラケット [:ascii:] を追加
- 先読みの繰り返しは不許可
  例. /(?=a)\*/, /(?!b){5}/
- 数値で指定された文字に対しても、大文字小文字照合オプションは有効
  例. /\x61/i =~ "A"
- 繰り返し回数指定で、最低回数の省略(0 回)ができる
  /a{,n}/ == /a{0,n}/
  最低回数と最大回数の同時省略は許されない。(/a{,}/)
- /a{n}?/は無欲な演算子ではない。
  /a{n}?/ == /(?:a{n})?/
- 無効な後方参照をチェックしてエラーにする。
  /\1/, /(a)\2/
- 無限繰り返しの中で、長さ零での照合成功は繰り返しを中断させるが、
  このとき、中断すべきかどうかの判定として、捕獲式集合の捕獲状態の
  変化まで考慮している
  /(?:()|())_\1\2/ =~ ""
  /(?:\1a|())_/ =~ "a"

補記 5. 実装されているが、既定値では有効にしていない機能

- 捕獲履歴参照

  (?@...) と (?@\<name>...)

  例. /(?@a)\*/.match("aaa") ==> [<0-1>, <1-2>, <2-3>]

  有効にしていない理由は、どの程度役に立つかはっきりしないため。

補記 6. 問題点

- エンコーディングバイト値が適正な価かどうかのチェックは行なっていない。

  例: UTF-8

  - 先頭バイトとして不正なバイトを一文字とみなす
    /./u =~ "\xa3"

  - 不完全なバイトシーケンスのチェックをしない
    /\w+/u =~ "a\xf3\x8ec"

  これを調べることは可能ではあるが、遅くなるので行なわない。

  文字列として、そのようなバイト列を指定した場合の動作は保証しない。

終り

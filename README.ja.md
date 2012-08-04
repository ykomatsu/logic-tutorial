とてもやさしい関係・関数プログラミング入門
====

このチュートリアルは魔法のようで面白い関係プログラミング（論理プログラミングとしても知られています）と関数プログラミングへとあなたを案内します。
このチュートリアルはあなたがLisp、Clojure、Javaの知識、さらには関数プログラミングの知識さえも持っていることを前提としていません。
このチュートリアルが前提としていることはあなたがコマンドラインを使うことを嫌がらないことと、今までの人生で少なくとも1つのプログラミング言語を使ったことがあることだけです。

作業中
----

このチュートリアルはまだまだ作業中です。
その前編を読み終えて何かを学ぶことはできますが、次の2、3週間のうちにかなりの量が追加されるであろうことは覚悟しておいてください。

なぜ論理プログラミングなのか
----

関係パラダイムでプログラムを書くことの目的は何でしょうか。
第一は、あの美学です。

論理プログラムは関数プログラミング言語の有する宝石にさえも勝る宣言的な性質を持っていることが多く、単純に美しいです。
論理プログラムは検索を用いるので、アルゴリズムの詳細によってごちゃごちゃしていないことが多いです。
もしあなたがこれまでPrologに挑戦したことがないのであれば、関係プログラミングはときどきほとんど魔法のように見えるでしょう。

しかしながら、関係パラダイムを学ぶ最も重要な理由はそれが「楽しい」からだ、ということは認めます。

最初のステップ
----

はい、始める準備はできました。
<code>lein repl</code>または<code>cake repl</code>と入力すれば、Clojureのプロンプトに入ることができるでしょう。
最初にすべてがうまく動いていることを再確認しましょう。
Clojure REPLで次のコードを入力してください。

```clj
user=> (require 'clojure.core.logic)
```

REPLはnilを出力して制御をあなたに返すはずです。
もしそれがこのチュートリアルについての問題を記録していなければ私はそれを調査します。
もしすべてがうまく動いていれば次のコードを実行してください。

```clj
user=> (load "logic_tutorial/tut1")
```

あなたは無害な警告をいくつか見るでしょう。それから次のコードを実行してください。

```clj
user=> (in-ns 'logic-tutorial.tut1)
```

プロンプトは変わり、あなたが今作業をしているのは関係プログラミングの魔法を使うことのできる場所です。
REPLのプロンプトは<code>logic-tutorial.tut1</code>を示していると思いますが、私達は簡略化のために<code>tut1</code>と示すことにします。

質問と解答
----

多くのプログラミングシステムと異なり、関係プログラミングでは私達はコンピューターに本当に質問をすることができます。
しかしコンピューターに質問をする前に、私達はいくつかの事実を定義しなければなりません。
私達が最初にコンピューターに知ってほしいことは、世の中には男性というものが存在するということです。

```clj
tut1=> (defrel man x)
#'tut1/man
```

そして次に私達は何人かの男性を定義しなければなりません。

```clj
tut1=> (fact man 'Bob)
nil
tut1=> (fact man 'John)
nil
```

これで私達は誰が男性なのかを尋ねることができます。
質問にはいつも<code>run</code>または<code>run\*</code>を使います。
慣習として私達は論理変数<code>q</code>を宣言してコンピューターに<code>q</code>の取り得る値を教えてくれるよう求めます。
次に例を示します。

```clj
tut1=>  (run 1 [q] (man q))
(John)
```

私達はコンピューターに対して「誰が男性なのか」という質問への解答を1つだけ教えてくれるよう求めています。
私達は2つ以上の解答を求めることもできます。

```clj
tut1=> (run 2 [q] (man q))
(John Bob)
```

さてこれは非常にクールです。
私達がもっと多くの解答を求めた場合にはどうなるのでしょうか。

```clj
tut1=> (run 3 [q] (man q))
(John Bob)
```

同じ結果です。
なぜなら私達はコンピューターに世の中には2人の男性がいるとしか教えなかったからです。
コンピューターは知らないことについて解答することはできません。
他の種類の関係と事実を定義してみましょう。

```clj
tut1=> (defrel fun x)
#'tut1/fun
tut1=> (fact fun 'Bob)
nil
```

新しい種類の質問をしてみましょう。

```clj
tut1=> (run* [q] (man q) (fun q))
(Bob)
```

ここではいくつかの新しいことが起きています。
私達は<code>run\*</code>を使いました。
これは私達がコンピューターの見付けることのできるすべての解答を求めているということを意味します。
質問それ自体は前とは違う形で定式化されています。なぜなら私達は男性であり *かつ* 面白い人は誰なのかを尋ねているからです。
次のコードを入力してください。

```clj
tut1=> (defrel woman x)
#'tut1/woman
tut1=> (fact woman 'Lucy)
nil
tut1=> (fact woman 'Mary)
nil
tut1=> (defrel likes x y)
#'tut1/likes
```

関係は1つの存在についてのものであってはいけません。
私達が定義できるのは存在と存在との間の関係です。

```clj
tut1=> (fact likes 'Bob 'Mary)
nil
tut1=> (fact likes 'John 'Lucy)
nil
tut1=> (run* [q] (likes 'Bob q))
(Mary)
```

これで私達は誰が誰を好きなのかを尋ねることができます。
次のコードを試してみましょう。

```clj
tut1=> (run* [q] (likes 'Mary q))
()
```

うーん、これは動きません。
これは私達が *Maryが好意を抱いている人* についてはまったく言及しておらず、言及しているのはBobがMaryを好きだということだけだからです。
次のコードを試してみましょう。

```clj
tut1=> (fact likes 'Mary 'Bob)
nil
tut1=> (run* [q] (fresh [x y] (likes x y) (== q [x y])))
([Mary Bob] [Bob Mary] [John Lucy])
```

おお、たくさんの新しい情報が出てきました。
fresh式はこれまでに見たことがありません。
なぜこれが必要なのでしょうか。
慣習により<code>run</code>は質問への解答となる1つの値を<code>q</code>として返します。
今回私達が知りたいのは誰が誰を好きなのかということです。
これは私達がそれらの複数の値を記憶しておくための論理変数を作らなければならないということを意味しています。
それから私達はそれらの値の両方をClojureのベクター（他のプログラミング言語における配列のようなもの）に入れて<code>q</code>に代入します。

次のコードを試してみましょう。

```clj
tut1=> (run* [q] (fresh [x y] (likes x y) (likes y x) (== q [x y])))
([Mary Bob] [Bob Mary])
```

ここでは私達はお互い好き同士である人のリストだけを求めています。
私達がどのような順番で質問をしたかはまったく問題ではないことに注意してください。

```clj
tut1=> (run* [q] (fresh [x y] (likes x y) (== q [x y]) (likes y x)))
([Mary Bob] [Bob Mary])
```

いくつかの家系
----

私達は実際にいくつかの面白い関係を<code>tut1</code>ファイルの中で事前に定義しています。詳しく見る前にまずは試してみましょう。

```clj
tut1=> (fact parent 'John 'Bobby)
nil
tut1=> (fact male 'Bobby)
nil
```

これで私達はこの問い合わせを実行することができます。

```clj
tut1=> (run* [q] (son q 'John))
(Bobby)
```

事実をもう一つ追加してみましょう。

```clj
tut1=> (fact parent 'George 'John) 
nil
tut1=> (run* [q] (grandparent q 'Bobby))
(George)
```

できました。
私達は既存の関係を組み合わせて新しい関係を定義することができます。
合成のおかげです。
しかしこれは厳密にはどのように動いているのでしょうか。

ファイルに何が書いてあるのか、ちょっと見てみましょう。
ファイルの先頭の名前空間の宣言の後にいくつかの関係が定義されていることが分かるでしょう。

```clj
(defrel parent x y)
(defrel male x)
(defrel female x)
```

その後にはいくつかの関数があります。

```clj
(defn child [x y]
  (parent y x))

(defn son [x y]
  (all
    (child x y)
    (male x)))

(defn daughter [x y]
  (all
    (child x y)
    (female x)))
```

私達は関係を関数として定義することができます。
いくつかの新しい事実を定義してそれらの事実についての質問をするためにそれらの関係を使って遊んでみましょう。
もしあなたがもっと冒険をしてみたいと思うのであれば、新しい関係を書いてそれを使ってみてください。

プリミティブ
----

ちょっと後戻りしてみましょう。
<code>core.logic</code>は少数のプリミティブの上に成り立っています。それらは<code>run</code>、<code>fresh</code>、<code>==</code>、<code>conde</code>です。
私達は既に<code>run</code>、<code>fresh</code>、<code>==</code>についてはかなり知っています。
<code>run</code>は単純で、それは論理プログラムを実行（<code>run</code>）します。
<code>fresh</code>も非常に単純で、それは新しい論理変数を定義します。
<code>==</code>は少し不可解で、<code>conde</code>についてはこれまでに見たことがありません。

単一化
----

前に<code>==</code>演算子を使ったときに私は代入について嘘を言いました。
<code>==</code>演算子は2つの要素を単一化しなければならないということを意味します。
これは私達がコンピューターに2つのものを選んでそれらを等しくなるよう試みてもらうということを意味します。
もしどちらかの要素に論理変数が現れた場合、コンピューターはその論理変数を他の要素に合うすべての要素に束縛します。
もしコンピューターが2つの要素を等しくできなければ、単一化は失敗します。これが、私達がときどき1つも結果を得られなかった理由です。

次のコードを検討してください。

```clj
tut1=> (run* [q] (== 5 5))
(_.0)
```

おっと、これはどういう意味でしょうか。
これは私達の質問は正しいのですが、<code>q</code>を実際に何らかの値と単一化することができないということを意味します。<code>\_.0</code>は単に私達が具体的な値に束縛することができない論理変数を持っているということを意味します。

```clj
tut1=> (run* [q] (== 5 4))
()
```

5と4とを等しくすることは不可能であり、コンピューターは私達のした質問に対する正しい解答は存在しないということを知らせてくれます。

```clj
tut1=> (run* [q] (== q 5) (== q 5))
(5)
tut1=> (run* [q] (== q 5) (== q 4))
()
```

私達は一度論理変数を具体的な値に単一化した後にその値でもう一度単一化することができますが、今束縛されている値と等しくない具体的な値で単一化することはできません。

これは私達が複合的な要素を単一化することができることを示す例です。

```clj
tut1=> (run* [q] (fresh [x y] (== [x 2] [1 y]) (== q [x y])))
([1 2])
```

これは2つの要素、<code>[x 2]</code>と<code>[1 y]</code>とを単一化するためには、論理変数<code>x</code>を1に束縛し論理変数<code>y</code>を2に束縛しなければならないということを示しています。

注意。2つの変数を互いに単一化することは問題なく可能です。

```clj
tut1=> (run* [q] (fresh [x y] (== x y) (== q [x y])))
([_.0 _.0])
tut1=> (run* [q] (fresh [x y] (== x y) (== y 1) (== q [x y])))
([1 1])
```

複数の世界
----

もう私達は結合、つまり、論理 **積** については良く知っています。

```clj
(run* [q] (fun q) (likes q 'Mary))
```

私達は今これを<code>q</code>が面白くて **かつ** <code>q</code>がMaryを好きであるような<code>q</code>を見付けよと読むことを知っています。

しかし論理 **和** はどのように表現するのでしょうか。

```clj
(run* [q]
  (conde
    ((fun q))
    ((likes q 'Mary))))
```

上のコードはまさに<code>q</code>が面白いか **または** <code>q</code>がMaryを好きであるような<code>q</code>を見付けよということになります。
これは私達がどのようにして<code>core.logic</code>から複数の解答を得るのかということの本質です。

手品
----

もう私達は家系には飽きてしまいました。
親しみやすいコンピューター科学の世界に戻りましょう。
人々がコンピューター科学で最初の頃に紹介するのは配列および、またはリストです。
2つのリストを取ってそれらを結合することは便利なことが多いです。
Clojureにおいてこの機能は<code>concat</code>として存在しています。
しかしながら私達は<code>appendo</code>と呼ばれるその関数の関係プログラミング版を見てみようと思います。
<code>appendo</code>は確かに<code>concat</code>より遅いのですが<code>concat</code>の持っていない魔法の力を持っています。

まず私達は次のチュートリアルを読み込んでその名前空間に切り替えなければなりません。

注意。core.logic 0.6.3以降では、<code>appendo</code>はcore.logic自体に取り込まれています。

```clj
tut1=> (in-ns 'user)
nil
user=> (load "logic_tutorial/tut2")
nil
user=> (in-ns 'logic-tutorial.tut2)
nil
```

関係プログラミングの関数はその関数プログラミング版とはまったく違う形で書かれています。
値を返す代わりに、私達は通常最終的な引数を単一化の結果である出力値とします。
これは関係プログラムが一般的に関数プログラムとまったく異なって見えるということをも意味しています。

<code>src/logic\_tutorial/tut2.clj</code>を開いてください。
<code>appendo</code>の定義が見付かるでしょう。

```clj
(defn appendo [l1 l2 o]
  (conde
    ((== l1 ()) (== l2 o))
    ((fresh [a d r]
       (conso a d l1)
       (conso a r o)
       (appendo d l2 r)))))
```

私達は3つの引数のどれにでも論理変数を渡すことができます。

では次のコードを試してください。

```clj
tut2=> (run* [q] (appendo [1 2] [3 4] q))
([1 2 3 4])
```

合っているように見えます。
ではこれを試してください。

```clj
tut2=> (run* [q] (appendo [1 2] q [1 2 3 4]))
([3 4])
```

<code>appendo</code>はその入力も推測できるということに注意してください。

私達はこのように書くこともできますが、本当はappendoには簡略化した書き方があります。
それはパターンマッチングです。これにより私達が他の多くのプログラムで書かなければならない定型的な部分を書かずに済むのです。

ゼブラ
----

ゼブラパズルまたはアインシュタインのパズルという名前でときどき言及される古典的なパズルがあります。
その制約を解決するためのアルゴリズムを書くことは少し退屈です。
関係プログラミングを使うと単に制約を記述するだけでプログラムが正しい解答を生成してくれます。

パズルは次の方法で記述されます。

<code>src/logic\_tutorial/tut3.clj</code>を見れば、次のコードが見付かるでしょう。

```clj
(defne righto [x y l]
  ([_ _ [x y . ?r]])
  ([_ _ [_ . ?r]] (righto x y ?r)))

(defn nexto [x y l]
  (conde
    ((righto x y l))
    ((righto y x l))))

(defn zebrao [hs]
  (macro/symbol-macrolet [_ (lvar)]
    (all
     (== [_ _ [_ _ 'milk _ _] _ _] hs)                         
     (firsto hs ['norwegian _ _ _ _])                         
     (nexto ['norwegian _ _ _ _] [_ _ _ _ 'blue] hs)       
     (righto [_ _ _ _ 'ivory] [_ _ _ _ 'green] hs)         
     (membero ['englishman _ _ _ 'red] hs)                    
     (membero [_ 'kools _ _ 'yellow] hs)                      
     (membero ['spaniard _ _ 'dog _] hs)                      
     (membero [_ _ 'coffee _ 'green] hs)                      
     (membero ['ukrainian _ 'tea _ _] hs)                     
     (membero [_ 'lucky-strikes 'oj _ _] hs)                  
     (membero ['japanese 'parliaments _ _ _] hs)              
     (membero [_ 'oldgolds _ 'snails _] hs)                   
     (nexto [_ _ _ 'horse _] [_ 'kools _ _ _] hs)          
     (nexto [_ _ _ 'fox _] [_ 'chesterfields _ _ _] hs))))
```

これがプログラムのすべてです。
これを実行してみましょう。

```clj
tut3=> (run 1 [q] (zebrao q))
([[norwegian kools _.0 fox yellow] [ukrainian chesterfields tea horse blue] [englishman oldgolds milk snails red] [spaniard lucky-strikes oj dog ivory] [japanese parliaments coffee _.1 green]])
```

しかしこれはどれ位の速さで実行されるのでしょうか。

```clj
tut3=> (dotimes [_ 100] (time (doall (run 1 [q] (zebrao q)))))
```

私のマシンでは、JVMがウォームアップをするための時間を取った後、このパズルがわずか3ミリ秒で解かれることが分かりました。
ゼブラパズルはそれ自身とても面白いというものではありません。
しかしながらそのような複雑な制約を記述してこれほど速く解決できるのであれば、<code>core.logic</code>はおそらく型推論に用いることができるほど速いことになるでしょう。
時間だけが教えてくれるでしょうが、私は人々がそのような使い方を研究することを奨励します。

次のステップ
----

きっとこの短いチュートリアルは関係プログラミングの美しさを少しは明らかにしたことでしょう。
確かに、ここで私が示した関係プログラミングは制限されたものです。
ですが、人々は私がこの文書を書いているときよりももっと多くの方法でこれらの制限を乗り越えることに積極的に取り組んでいます。

あなたは関係プログラミングを使うことなくプログラマーとして非常にうまくやっていくことができますが、私達が今日使っているツールの多くの側面は関係プログラミングがどのように動くのかについての基本的な理解がなければ不可解に見えるでしょう。
関係プログラミングを使えば、そうでなければ実装することが困難であるような機能を言語に追加することができます。
例えばStandard MLとHaskellの有するすばらしい型システム（と型推論）を、 **core.logic** を使って作ることは魅力的でしょう。
私はまたCLOSの総称的なメソッドの自由な本質とともにMLにパターンマッチングの性能を与えた効率的な述語ディスパッチシステムも **core.logic** を使えば簡単に手に入れることができるとも考えています。

リソース
---

もしあなたがこのチュートリアルを面白いと感じてもっと学びたいと思うのであれば、関係パラダイムをさらに理解するために次の本を私は勧めます。

* [The Reasoned Schemer](http://mitpress.mit.edu/catalog/item/default.asp?ttype=2&tid=10663)
* [Paradigms of Artificial Intelligence Programming](http://norvig.com/paip.html)
* [Prolog Programming For Artificial Intelligence](http://www.amazon.com/Prolog-Programming-Artificial-Intelligence-Bratko/dp/0201403757)
* [Concepts, Techniques, and Models of Computer Programming](http://www.info.ucl.ac.be/~pvr/book.html)


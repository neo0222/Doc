<h1>
<span id="9try-finallyよりもtry-with-resourcesを使うべし" class="fragment"></span><a href="#9try-finally%E3%82%88%E3%82%8A%E3%82%82try-with-resources%E3%82%92%E4%BD%BF%E3%81%86%E3%81%B9%E3%81%97"><i class="fa fa-link"></i></a>9.try-finallyよりもtry-with-resourcesを使うべし</h1>

<ul>
<li>try-finallyでtry句とfinally句で同時に例外発生した場合、try句のほうがかき消されてしまう。</li>
</ul>

<h1>
<span id="17可変性を最小限にせよ" class="fragment"></span><a href="#17%E5%8F%AF%E5%A4%89%E6%80%A7%E3%82%92%E6%9C%80%E5%B0%8F%E9%99%90%E3%81%AB%E3%81%9B%E3%82%88"><i class="fa fa-link"></i></a>17.可変性を最小限にせよ</h1>

<ul>
<li><p>不変クラスは設計、使用、実装が可変クラスよりもしやすい。</p></li>
<li>
<p>不変クラスを作るにあたって、以下の5つをルールを順守するべし。</p>

<ul>
<li>オブジェクトの状態を変更させるようなメソッドを提供しない</li>
<li>継承が不可能であるように作る</li>
<li>全てのフィールドをfinalにする</li>
<li>全てのフィールドをprivateにする</li>
<li>可変クラスへのアクセスをさせないようにする</li>
</ul>
</li>
<li><p>不変クラスは本質的にスレッドセーフであり、同期が必要ない。</p></li>
<li><p>不変クラスのデメリットは、異なる値に対して別々のオブジェクトを作成することとなり、コストが高くつく点にある。これの解決策として、コンパニオンクラスなるものがある。具体例としては、不変クラスであるStringに対応する可変クラスのStringBuilderである。</p></li>
<li><p>不変クラスを継承させない方法として、クラスにfinal修飾子をつける以外の方法として、コンストラクタをprivateとして、ファクトリメソッドをpublicで作成する方法がある。</p></li>
<li><p>BigInteger,BigDecimalが実装された当時は、不変クラスは継承させないほうが良い、ということが分かっていなかったため、継承が可能。</p></li>
<li><p>可能な限り不変クラスに近づけるため、原則として、フィールドはprivate final にするべき。（CountDownLatchは好例）</p></li>
</ul>

<h1>
<span id="18継承よりコンポジションを選ぶべし" class="fragment"></span><a href="#18%E7%B6%99%E6%89%BF%E3%82%88%E3%82%8A%E3%82%B3%E3%83%B3%E3%83%9D%E3%82%B8%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E9%81%B8%E3%81%B6%E3%81%B9%E3%81%97"><i class="fa fa-link"></i></a>18.継承よりコンポジションを選ぶべし</h1>

<ul>
<li>
<p>具象クラスをパッケージをまたいで継承するのは危険。起こりうる問題は以下のように2つある。</p>

<ul>
<li>スーパークラスの実装はリリース毎に変わりうる。サブクラスはそれに合わせて変更を加えなければならないかもしれない。</li>
<li>スーパークラスに新しいメソッドが追加された場合にセキュリティホールとなる場合があるらしい。（<strong>全然ピンと来ない</strong>）</li>
</ul>
</li>
<li><p>継承を回避するにあたっては、もともとスーパークラスになるはずだったクラスを private finalなフィールドとして取り込み（composition）、各メソッドでその取り込んだフィールドのメソッドを参照する（forwarding）ことによって実現できる。</p></li>
<li><p>↑の作りの欠点は、callback framework　には向いていないということ。ラップされたオブジェクトはどれがラップしたオブジェクトであるかを認識しないため、自身への参照でサブクラス呼び出しをすることができない。</p></li>
<li><p>B is a A の関係が本当に成り立たない限り、B extends A はやめたほうが良い。これはJavaプラットフォームライブラリでも守れていなくて、Stack はVectorではないので、StackがVectorを継承するべきではないし、Properties と HashListの関係も同様である。</p></li>
</ul>

<h1>
<span id="23タグ付きクラスよりクラス階層を選ぶ" class="fragment"></span><a href="#23%E3%82%BF%E3%82%B0%E4%BB%98%E3%81%8D%E3%82%AF%E3%83%A9%E3%82%B9%E3%82%88%E3%82%8A%E3%82%AF%E3%83%A9%E3%82%B9%E9%9A%8E%E5%B1%A4%E3%82%92%E9%81%B8%E3%81%B6"><i class="fa fa-link"></i></a>23.タグ付きクラスよりクラス階層を選ぶ</h1>

<h2>
<span id="タグ付きクラス" class="fragment"></span><a href="#%E3%82%BF%E3%82%B0%E4%BB%98%E3%81%8D%E3%82%AF%E3%83%A9%E3%82%B9"><i class="fa fa-link"></i></a>タグ付きクラス</h2>

2種類以上の特性を示し、その特性をフィールドに有しているタグで切り替えるクラスをタグ付きクラスと呼ぶ。
以下のクラスはその実例で、円と長方形を表現することが可能である。

```Java
package tryAny.effectiveJava;

class Figure {
    enum Shape {
        RECTANGLE, CIRCLE
    }

    // 図形タイプを保持する
    final Shape shape;

    // shape が RECTANGLE の場合だけ利用する
    double length;
    double width;

    // shape が CIRCLE の場合だけ利用する
    double radius;

    // 円のためのコンストラクタ
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    // 長方形のためのコンストラクタ
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch (shape) {
        case RECTANGLE:
            return length * width;
        case CIRCLE:
            return Math.PI * (radius * radius);
        default:
            throw new AssertionError();
        }
    }
}
```


<p>このようなタグ付きクラスは欠点がたくさんある。<br>
以下、欠点一覧。</p>

<ul>
<li>タグ付きクラスの定型文には、enum、タグフィールド、switchさせる文があり、たくさんの実装が1つのクラスに混ざるので、可読性が落ちる</li>
<li>利用している特性に関係ないフィールドも保持せねばならず、メモリ使用量が大きくなる</li>
<li>コンストラクタが無関係なフィールドも初期化しない限りは、フィールドをfinalとして生成することができない</li>
<li>コンストラクタでタグフィールドを初期化し、コンパイラの補助なしで正しくフィールドを初期化する必要がある。できないとプログラムは実行時に落ちる</li>
<li>ソースをいじらない限り、タグクラスに新しい特性を追加することはできない。また、追加したときに、switch文にケースを追加しないと実行時に失敗する</li>
<li>インスタンスのデータ型がその特性に関して手掛かりを与えてくれない</li>
</ul>

<h2>
<span id="タグ付きクラスの代替法クラス階層" class="fragment"></span><a href="#%E3%82%BF%E3%82%B0%E4%BB%98%E3%81%8D%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%AE%E4%BB%A3%E6%9B%BF%E6%B3%95%E3%82%AF%E3%83%A9%E3%82%B9%E9%9A%8E%E5%B1%A4"><i class="fa fa-link"></i></a>タグ付きクラスの代替法：クラス階層</h2>

<p>オブジェクト指向言語であれば、クラス階層を用いることで改良できる。</p>

<p>タグ付きクラスからクラス階層に修正する手順としては、</p>

<ol>
<li>abstract なクラスを作成し、その中にタグの値で動作を切り替えていたメソッド(上の例だとareaメソッド)を、abstract メソッドとして定義する</li>
<li>タグの値に依存しないメソッド、フィールド値は abstract クラスに入れる（上の例だとそのようなものは無い）</li>
<li>タグ付きクラスの特性に当たる部分について、サブクラスを作成する。（上記例だとcircle,rectangle）</li>
<li>特性独自のフィールドを各サブクラスに入れる。（circleならradius、rectangleならlength,width）</li>
</ol>

<p>上記の修正結果は以下のようになる。</p>

```Java
package tryAny.effectiveJava;

abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
    final double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * (radius * radius);
    }
}

class Rectangle extends Figure {
    final double length;
    final double width;

    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    double area() {
        return length * width;
    }
}
```

<p>こうすることによって上記であげたタグ付きクラスの欠点は解消されている。</p>

<h3>
<span id="その他クラス階層の良い点" class="fragment"></span><a href="#%E3%81%9D%E3%81%AE%E4%BB%96%E3%82%AF%E3%83%A9%E3%82%B9%E9%9A%8E%E5%B1%A4%E3%81%AE%E8%89%AF%E3%81%84%E7%82%B9"><i class="fa fa-link"></i></a>その他クラス階層の良い点</h3>

<p>クラス階層は、型の間の本来の階層関係について反映できるため、柔軟性を改良したり（？）、コンパイル時のエラーチェックをより良いものにすることができる。</p>

<p>上記例で正方形の型について追加することになったときは、クラス階層の特性を使って以下のように記述することができる。</p>

```Java
class Square extends Rectangle {

    Square(double side) {
        super(side, side);
    }
}
```

<h1>
<span id="40一貫してoverrideアノテーションを使うべし" class="fragment"></span><a href="#40%E4%B8%80%E8%B2%AB%E3%81%97%E3%81%A6override%E3%82%A2%E3%83%8E%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E4%BD%BF%E3%81%86%E3%81%B9%E3%81%97"><i class="fa fa-link"></i></a>40.一貫してOverrideアノテーションを使うべし</h1>

<p>Javaライブラリはいくつかのアノテーションを提供してるが、多くのプログラマにとって、<code>@Override</code> が一番重要だろう。<br>
一貫して<code>@Override</code> を使用していれば、バグが起こりにくくなる。<br>
以下の<a href="https://www.weblio.jp/content/bigram" rel="nofollow noopener" target="_blank">bigram</a>を模したクラスを例に見ていく（バグあり）。</p>


```Java
package tryAny.effectiveJava;

import java.util.HashSet;
import java.util.Set;

//Can you spot the bug?
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first = first;
        this.second = second;
    }

    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++)
            for (char ch = 'a'; ch <= 'z'; ch++)
                s.add(new Bigram(ch, ch));
        System.out.println(s.size());
    }
}
```

<p>このプログラムは26を出力すると思いきや、260を出力する。<br>
原因はequalsをOverrideするつもりがOverloadしてしまっていることにある。<code>Object.equals</code>の引数はObject型であり、シグニチャが違う。<br>
こういった誤りを防ぐために、Overrideするメソッドには<code>@Override</code>をつける。そうすると以下のコードはコンパイルエラーとなる。</p>

```Java
@Override public boolean equals(Bigram b) {
    return b.first == first && b.second == second;
}
```

<p>正しくは以下のようになる。</p>

```Java
@Override
public boolean equals(Object o) {
    if (!(o instanceof Bigram))
        return false;
    Bigram b = (Bigram) o;
    return b.first == first && b.second == second;
}
```

<p>このようなことから、<strong>親クラスのメソッドをOverrideするときは<a href="/Override" class="user-mention js-hovercard" title="Override" data-hovercard-target-type="user" data-hovercard-target-name="Override">@Override</a>をつけるべきである</strong>。<br>
これには一つ例外があって、具象クラスにおいて、親の抽象メソッドをOverrideするときは、そもそもOverrideしないとコンパイルエラーが出るので、<code>@Override</code>の記述の必要はない（記述による害もない）。</p>


<h1>
<span id="51メソッドのシグニチャは注意深く設計せよ" class="fragment"></span><a href="#51%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%AE%E3%82%B7%E3%82%B0%E3%83%8B%E3%83%81%E3%83%A3%E3%81%AF%E6%B3%A8%E6%84%8F%E6%B7%B1%E3%81%8F%E8%A8%AD%E8%A8%88%E3%81%9B%E3%82%88"><i class="fa fa-link"></i></a>51.メソッドのシグニチャは注意深く設計せよ</h1>

<ul>
<li>メソッドの名前は注意深く決めるべき。理解しやすく、同パッケージ内の他の名前と矛盾しないようにする。また、広く一般的に合意を得ている名前を付ける。迷ったらガイダンスとして、JavaライブラリAPIを見てみる（良くないのもあるが、良いのがたくさんある）。</li>
<li>便利なメソッドを過度に提供すべきでない。あまりに多いメソッドがあると、学習、利用、ドキュメント作成、テスト、保守が大変になる。頻繁に使われる場合にのみ、記述の省略化ができるようなメソッドの提供を考慮すべき。迷ったら作らないほうが良い。</li>
<li>引数の数を多くとるべきでない。引数の数は4つ以下に抑えるべきである。利用者は多くの引数を覚えることができないので、リファレンスをみながら使用しなければならない。特に、同じ型で多くの引数がある場合は避けたい。なぜなら、引数の順番を間違えたとしても、コンパイルエラーとならず、意図したものと違う処理がなされる可能性があるから。引数の数を減らすテクニックは以下の3つ。

<ul>
<li>1つのメソッドを複数のメソッドに分割する。（<strong>Listのsublist、indexOf、lastIndexOfの例をあげていたがいまいちわからず</strong>）</li>
<li>ヘルパークラスを作る。例えば、トランプの柄と数を引数に取るものがあるならば、柄と数をまとめたEntityを作成し、それを引数に取るようにする。</li>
<li>ビルダーパターンを使う。（Item2）</li>
</ul>
</li>
<li>引数の型は具象クラスよりもインターフェースに優先すべき。具象クラスにした場合には、メソッドの使用者に特定の実装を強いることになり、時にはコストの高いコピーを強いるときがある。</li>
<li>boolean 引数よりも、2値のenumとすべき。なぜなら、enumの方は後に拡張することが容易であるから（要素を2つから3つにする）。また、enumはその中にメソッドを持つこともできる。（Item34）</li>
</ul>


<h1>
<span id="54nullではなく空のコレクション配列を返すべし" class="fragment"></span><a href="#54null%E3%81%A7%E3%81%AF%E3%81%AA%E3%81%8F%E7%A9%BA%E3%81%AE%E3%82%B3%E3%83%AC%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E9%85%8D%E5%88%97%E3%82%92%E8%BF%94%E3%81%99%E3%81%B9%E3%81%97"><i class="fa fa-link"></i></a>54.nullではなく、空のコレクション、配列を返すべし</h1>

<ul>
<li>nullを返すとなると、呼び出し側でnullをチェックするためのコードが必要となり、煩わしい。空のコレクション、配列を返すべきである。</li>
</ul>

```java
//The right way to return a possibly empty collection
public List<Cheese> getCheeses() {
    return new ArrayList<>(cheesesInStock);
}
```

```java
//The right way to return a possibly empty array
public Cheese[] getCheeses() {
    return cheesesInStock.toArray(new Cheese[0]);
}
```

<ul>
<li>空のコレクション、配列を返すよりnullを返すほうが、メモリ割り当てがない分性能が良くなるという意見がある。これは以下2点で誤っている。

<ul>
<li>このレベルで性能が良くなるかは疑わしい。証明できている場合を除いたら、心配する必要はない。</li>
<li>メモリ割り当てをすることなく空のコレクション、配列を返すことは可能である。</li>
</ul>
</li>


```Java
// Optimization - avoids allocating empty collections
public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? Collections.emptyList()
        : new ArrayList<>(cheesesInStock);
}
```

```Java
// Optimization - avoids allocating empty arrays
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];
public Cheese[] getCheeses() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```

<h1>
<span id="57ローカル変数のスコープは最小にせよ" class="fragment"></span><a href="#57%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E5%A4%89%E6%95%B0%E3%81%AE%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%97%E3%81%AF%E6%9C%80%E5%B0%8F%E3%81%AB%E3%81%9B%E3%82%88"><i class="fa fa-link"></i></a>57.ローカル変数のスコープは最小にせよ</h1>

<ul>
<li>ローカル変数のスコープを最小にする最も効果的なテクニックは、その変数を最初に用いる直前に宣言することである。</li>
<li>ローカル変数は基本的に宣言時に初期化しておくべきである。</li>
<li>ループ文においては、while文よりfor文を選択すべき。

<ul>
<li>なぜなら、for文は繰り返しに用いるその場限りの変数を定義できるのに対して、while文ではそれができないから。</li>
<li>for文のほうがコピペによるミスが起きにくい。（構文が割と定まっている）</li>
</ul>
</li>
<li>ローカル変数のスコープを最小にするためにメソッドを単位を小さくすべき。</li>
</ul>


<h1>
<span id="58従来のforループよりfor-eachのループを選択すべき" class="fragment"></span><a href="#58%E5%BE%93%E6%9D%A5%E3%81%AEfor%E3%83%AB%E3%83%BC%E3%83%97%E3%82%88%E3%82%8Afor-each%E3%81%AE%E3%83%AB%E3%83%BC%E3%83%97%E3%82%92%E9%81%B8%E6%8A%9E%E3%81%99%E3%81%B9%E3%81%8D"><i class="fa fa-link"></i></a>58.従来のforループより、for-eachのループを選択すべき</h1>

<ul>
<li>従来のfor文のほうが記述すべきことが多くて、ミスが発生する確率が上がる。</li>
<li>入れ子のイテレーションがあるときもfor-eachが使える。</li>
</ul>

```java
package tryAny.effectiveJava;

import java.util.Arrays;
import java.util.Collection;

public class NestedFor {
    enum Suit {
        CLUB, DIAMOND, HEART, SPADE
    };

    enum Rank {
        ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE, TEN, JACK, QUEEN, KING
    };

    public static void main(String[] args) {
        Collection<Suit> suits = Arrays.asList(Suit.values());
        Collection<Rank> ranks = Arrays.asList(Rank.values());

        for (Suit suit : suits) {
            for (Rank rank : ranks) {
                System.out.println("柄" + suit + "：数" + rank);
            }
        }
    }
}
```


<ul>
<li>
<p>一般的にfor-eachが使えない場面が3つある。</p>

<ul>
<li>フィルタリングして特定の要素を削除する場合</li>
<li>要素の値を変換する場合</li>
<li>複数の要素集合を、並列で走査する場合</li>
</ul>
</li>
<li><p>Iterableをimplementsしているオブジェクトに対してfor-eachで要素を取り出すことができる。</p></li>
</ul>

<h1>
<span id="59ライブラリを知り利用する" class="fragment"></span><a href="#59%E3%83%A9%E3%82%A4%E3%83%96%E3%83%A9%E3%83%AA%E3%82%92%E7%9F%A5%E3%82%8A%E5%88%A9%E7%94%A8%E3%81%99%E3%82%8B"><i class="fa fa-link"></i></a>59.ライブラリを知り、利用する</h1>

<ul>
<li>ランダムな値を作るにおいて、下記の1番目のような実装では値の平均値がおかしくなる。ライブラリを使用することで正しくランダム値生成ができる。</li>
</ul>

```Java
package tryAny.effectiveJava;

import java.util.Random;
import java.util.concurrent.ThreadLocalRandom;

import org.apache.commons.lang3.time.StopWatch;

public class RandomTest {
    public static void main(String[] args) {
        int n = 2 * (Integer.MAX_VALUE / 3);
        int low1 = 0;
        StopWatch sw1 = new StopWatch();
        sw1.start();
        for (int i = 0; i < 1000000; i++) {
            if (random(n) < n / 2) {
                low1++;
            }
        }
        sw1.stop();

        System.out.println(low1); // 500000位になると思いきやならない。666666位になる。
        System.out.println(sw1.getTime());

        int low2 = 0;
        StopWatch sw2 = new StopWatch();
        sw2.start();
        for (int i = 0; i < 1000000; i++) {
            if (tlr.nextInt(n) < n / 2) {
                low2++;
            }
        }
        sw2.stop();
        System.out.println(low2);// 500000位になる。
        System.out.println(sw2.getTime());// 速度はあまり変わらない
    }

    static Random rnd = new Random();

    static int random(int n) {
        return Math.abs(rnd.nextInt()) % n;
    }

    static ThreadLocalRandom tlr = ThreadLocalRandom.current();

}
```

<ul>
<li>
<p>自分で処理を書くのではなく、ライブラリを使用することで得られる利点は以下の5つ。</p>

<ul>
<li>エキスパートが練りに練った実装を使える。</li>
<li>時間を節約できる。</li>
<li>ライブラリ自体が進化していくので、恩恵にあずかれる。</li>
<li>ライブラリの機能がリリース事に増えるので、その恩恵にあずかれる。</li>
<li>標準的なライブラリを使うことで、自身のコードが主流に則ったものとなり、読みやすく、つかいやすいものとなる。</li>
</ul>
</li>
<li><p>すべてのプログラマが、java.lang,java.util,java.ioとそれらのサブパッケージには親しくなっておくべき。</p></li>
</ul>

<h1>
<span id="60正確な値を求める場合はfloatとdoubleの使用を控えるべし" class="fragment"></span><a href="#60%E6%AD%A3%E7%A2%BA%E3%81%AA%E5%80%A4%E3%82%92%E6%B1%82%E3%82%81%E3%82%8B%E5%A0%B4%E5%90%88%E3%81%AFfloat%E3%81%A8double%E3%81%AE%E4%BD%BF%E7%94%A8%E3%82%92%E6%8E%A7%E3%81%88%E3%82%8B%E3%81%B9%E3%81%97"><i class="fa fa-link"></i></a>60.正確な値を求める場合はfloatとdoubleの使用を控えるべし</h1>

<ul>
<li><p>特に金の計算には使わないほうが良い。floatとdoubleでは0.1を表現できない。代わりにBigDecimal、int、longを用いる。</p></li>
<li><p>BigDecimalのデメリットは、使いづらく、遅いところ。</p></li>
<li><p>9桁以下の計算にはintを、18桁以下ならlongを、19桁以上ならBigDecimalを使うべき。</p></li>
</ul>

<h1>
<span id="64インターフェースを参照するようにせよ" class="fragment"></span><a href="#64%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9%E3%82%92%E5%8F%82%E7%85%A7%E3%81%99%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%9B%E3%82%88"><i class="fa fa-link"></i></a>64.インターフェースを参照するようにせよ</h1>

<h2>
<span id="インターフェースで型を宣言しといた方が柔軟な設計になる" class="fragment"></span><a href="#%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9%E3%81%A7%E5%9E%8B%E3%82%92%E5%AE%A3%E8%A8%80%E3%81%97%E3%81%A8%E3%81%84%E3%81%9F%E6%96%B9%E3%81%8C%E6%9F%94%E8%BB%9F%E3%81%AA%E8%A8%AD%E8%A8%88%E3%81%AB%E3%81%AA%E3%82%8B"><i class="fa fa-link"></i></a>インターフェースで型を宣言しといた方が柔軟な設計になる</h2>

<p><strong>適切なインターフェース型あるならば、引数、戻り値、変数、フィールド全てがインターフェース型で宣言されるべきである。</strong><br>
オブジェクトクラスを参照するのはコンストラクタにおいてのみである。</p>

<p>以下、Setインターフェースの実装クラスである、LinkedHashSetを例に見ていく。</p>

```Java
// Good - uses interface as type
Set<Son> sonSet = new LinkedHashSet<>();
```

<p>上記はいい例。</p>

```Java
// Bad - uses class as type!
LinkedHashSet<Son> sonSet = new LinkedHashSet<>();
```


<p>上記は悪い例。</p>

<p>インターフェースで宣言した例だと、以下のように変えるだけで、実装クラスが基本的にエラーなく変えられる（インターフェースで宣言しているメソッドのみ使用しているため）。</p>


```Java
Set<Son> sonSet = new HashSet<>();
```

<p>ただし、気を付けなくてはいけないのは、インターフェースでは規定されていない機能に依存したコードである場合には、実装クラスを変える影響があり得るということだ。<br>
例えば、LinkedHashSetの並び変えのポリシーに依存していた場合には、HashSetに実装を変えた時に影響が出るだろう。</p>

<h2>
<span id="適切なインターフェースがない時" class="fragment"></span><a href="#%E9%81%A9%E5%88%87%E3%81%AA%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9%E3%81%8C%E3%81%AA%E3%81%84%E6%99%82"><i class="fa fa-link"></i></a>適切なインターフェースがない時</h2>

<p>適切なインターフェースがない時は、クラスの型で宣言を行うのが正しい。</p>


<h3>
<span id="value-class" class="fragment"></span><a href="#value-class"><i class="fa fa-link"></i></a>value class</h3>

<p>例えば、StringやBigIntegerなどの値を表すクラス（value class）を考えてみる。<br>
value classは複数の実装があることがまずない。また、おおむねfinalなクラスであり、対応するインターフェースがない。<br>
value classは引数、変数、フィールド、戻り値の型として適している。</p>

<h3>
<span id="クラスベースのフレームワークを使っているとき" class="fragment"></span><a href="#%E3%82%AF%E3%83%A9%E3%82%B9%E3%83%99%E3%83%BC%E3%82%B9%E3%81%AE%E3%83%95%E3%83%AC%E3%83%BC%E3%83%A0%E3%83%AF%E3%83%BC%E3%82%AF%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E3%81%84%E3%82%8B%E3%81%A8%E3%81%8D"><i class="fa fa-link"></i></a>クラスベースのフレームワークを使っているとき</h3>

<p>クラスベースのフレームワークを使用しており、適切な基盤の型がインターフェースでなくて、クラスであるときがある。<br>
そういった場合も適したクラス（だいたいabstract）を継承するのが良い。<br>
java.ioのOutputStreamなどはこれに当たる。</p>

<h3>
<span id="適したインターフェースはないが実装クラスに適したメソッドが実装されているケース" class="fragment"></span><a href="#%E9%81%A9%E3%81%97%E3%81%9F%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9%E3%81%AF%E3%81%AA%E3%81%84%E3%81%8C%E5%AE%9F%E8%A3%85%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%AB%E9%81%A9%E3%81%97%E3%81%9F%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%8C%E5%AE%9F%E8%A3%85%E3%81%95%E3%82%8C%E3%81%A6%E3%81%84%E3%82%8B%E3%82%B1%E3%83%BC%E3%82%B9"><i class="fa fa-link"></i></a>適したインターフェースはないが、実装クラスに適したメソッドが実装されているケース</h3>

<p>例えば、PriorityQueueにはcomparatorメソッドがあるが、Queueインターフェースにはない。もしcomparatorメソッドに依存するプログラムを書くのであれば、PriorityQueueを参照する。ただし、このようなケースはレアである。</p>

<p>上記3つのケースは網羅的なものではなく、クラスで参照するなんとなくの場面について伝えたものである。</p>

<p><strong>もし適したインターフェースがない場合は、目的の機能を持っている一番抽象度の高いクラスを参照すべき。</strong></p>

<h1>
<span id="77例外を無視してはならない" class="fragment"></span><a href="#77%E4%BE%8B%E5%A4%96%E3%82%92%E7%84%A1%E8%A6%96%E3%81%97%E3%81%A6%E3%81%AF%E3%81%AA%E3%82%89%E3%81%AA%E3%81%84"><i class="fa fa-link"></i></a>77.例外を無視してはならない</h1>

<ul>
<li>エラーを無視するのが適切な場合もありうるが、その場合はcatchブロックの中でなぜエラーを無視するかのコメントを入れ、catchするエラーの変数名を<strong>ignored</strong>にするべきである。</li>
<li>検査例外も非検査例外も等しくこのItem77を守るべき。</li>
</ul>

Ruby 2.4 のための RuboCop
=========

<sub>
2016/12/26 [五反田.rb#17(ruby2.4について) - connpass](https://gotanda-rb.connpass.com/event/47098/)
</sub>



---

だれこれ
-------

---


<img style="width: 30%; border-radius: 50%" alt="ほにゃー" src="pocke.svg">

- Pocke ( Masataka Kuwabara )
- Engineer at Actcat Inc.
- [ハイパーおふとんクリエイター](https://twitter.com/p_ck_/status/809910187708403712)

---


Ruby 2.4 の新機能
------

---

省略
---

- キャッチアップしてたら説明するだけ時間の無駄だし…
- [サンプルコードでわかる！Ruby 2.4の新機能と変更点 - Qiita](http://qiita.com/jnchito/items/9f9d45581816f121af07)
  - jnchitoさんのわかりやすい記事に感謝


---


RuboCop とは
-------


---

- Ruby の静的解析ツール
- コードを解析して、「このメソッド使ってるのよくないよ!」とかできる
- [RuboCopをRailsオプションやLintオプションで使ってみよう - SideCI Blog](http://blog-ja.sideci.com/entry/2015/03/12/160441)


([SideCI](https://sideci.com)でもRuboCopをサポートしています(宣伝))

---


今日話したいこと
---

---


Ruby 2.4 のための RuboCop
-----


---

Ruby 2.4 を導入する上で、有効に働くRuboCopのルール(Cop)をご紹介します。

<sub>
尚、2016年12月26日現在、未リリースの機能も含むことをご了承下さい。
(と言うかマージすらされてないかも…)
</sub>


---


`Lint/UnifiedInteger`
--------

[Document](http://rubocop.readthedocs.io/en/latest/cops_lint/#lintunifiedinteger)

<sub>
version 0.43.0 以上が必要です
</sub>

---

### Ruby 2.4 での変更点

- 分かれていた`Fixnum`と`Bignum` classが`Integer`に統合された
- Cレベルの実装の差異がRubyのクラスの違いとして現れていた
- これにより、`json` gemのバージョンアップが必要になったり
- [参考](http://qiita.com/jnchito/items/9f9d45581816f121af07#fixnum%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%A8bignum%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%8Cinteger%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%AB%E7%B5%B1%E5%90%88%E3%81%95%E3%82%8C%E3%81%9F)

---

### 例

Ruby 2.4 では、`Fixnum` / `Bignum` クラスは両方共 `Integer` への参照となります。


```ruby
Fixnum == Integer # => true
Bignum == Integer # => true
```


---

### RuboCop の対応

- `Fixnum` / `Bignum` を使用しているコードを検知
- 自動で`Integer`に置換可能
- また、Ruby 2.4未満でも動作
  - Ruby 2.4への移行がスムーズになる
  - 意図して`Fixnum`を参照していることは少ないと考えられる


---

### UnifiedIntegerの為のみにRuboCopを使う

```sh
$ rubocop --only Lint/UnifiedInteger

# 自動修正したい場合
$ rubocop --only Lint/UnifiedInteger -a
```

---

`Lint/Debugger`
------


[Document](http://rubocop.readthedocs.io/en/latest/cops_lint/#lintdebugger)

<sub>
version 0.46.0 以上が必要です
</sub>

---

### Ruby 2.4 での変更点

- `binding.irb` とすることで、そのスコープでirbを開くことが出来るようになった
- `binding.pry`の`irb`版
  - 別途gemを入れる必要がなくて便利
- [参考](http://qiita.com/jnchito/items/9f9d45581816f121af07#%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0%E3%81%AE%E5%AE%9F%E8%A1%8C%E4%B8%AD%E3%81%ABirb%E3%81%8C%E9%96%8B%E3%81%91%E3%82%8Bbindingirb%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%AE%E8%BF%BD%E5%8A%A0)


---

### RuboCop の対応

- RuboCopには元々、`binding.pry`等を検出するCopが存在する
  - 本番にデバッグコードを入れないため
- version 0.46.0からはRuby 2.4 を使用している場合、`binding.irb`の使用も検出するように
  - Ruby 2.4でも安心してデバッグできます


---

### 使い方

設定ファイルで使用するRubyのバージョンを指定する必要がある。

```yaml
# .rubocop.yml
AllCops:
  TargetRubyVersion: 2.4
```

---

`Performance/RegexpMatch`
------

[Document](http://rubocop.readthedocs.io/en/latest/cops_performance/#performanceregexpmatch)

<sub>
未リリース(もっと言うと、未マージ…)
</sub>

---

### Ruby 2.4 での変更点

- `String#match?`, `Regexp#match?`メソッドが追加された
  - `Regexp#match` メソッドなどは元からある
  - それらとの違いは、`MatchData`を生成しないため高速なこと
- [参考](http://qiita.com/jnchito/items/9f9d45581816f121af07#%E6%AD%A3%E8%A6%8F%E8%A1%A8%E7%8F%BE%E3%81%AB%E6%96%87%E5%AD%97%E5%88%97%E3%81%8C%E3%83%9E%E3%83%83%E3%83%81%E3%81%97%E3%81%9F%E3%81%8B%E3%81%A9%E3%81%86%E3%81%8B%E3%81%A0%E3%81%91%E3%82%92%E8%BF%94%E3%81%99regexpmatchstringmatch)

---

### 例

```ruby
# MatchData を使用していない為、`match?`で充分
if /^(ab)+$/.match(str)
  something
end

# MatchData を使用している為、`match?`は適用できない
if /^(ab)+$/.match(str)
  something Regexp.last_match
end

# MatchData を使用している為、`match?`は適用できない
if match = /^(ab)+$/.match(str)
  something match[1]
end
```


---


### RuboCop の対応


- 不必要に`match`メソッドを使用している箇所を検出
  - `match`が必要なところは検出しない
- `match?`を使用するように自動的に修正
- 安全に既存コードを速く出来る
- ([昨日頑張って書いた](https://github.com/bbatsov/rubocop/pull/3824))

---

### 使い方

設定ファイルで使用するRubyのバージョンを指定する必要がある。

```yaml
# .rubocop.yml
AllCops:
  TargetRubyVersion: 2.4
```


```sh
$ rubocop --only Performance/RegexpMatch

# 自動修正したい場合
$ rubocop --only Performance/RegexpMatch -a
```


---


まとめ
----

- RuboCopを活用すると、Ruby 2.4をより便利に、より安全に活用できるかも?
- 「Ruby 2.4 移行のためにこういうCopほしい」とかありましたら教えて下さい、実装します
- (実は今日紹介した機能は全部私が追加した)

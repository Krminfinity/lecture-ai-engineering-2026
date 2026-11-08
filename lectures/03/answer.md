# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
	- 同じ「宿題の3レベルとは何か」という質問を実行した。
	- 第1回の app.ask は、講座固有の定義を知らず、「基礎・応用・発展」などの一般的な分類を推測して回答した。
	- 第3回の app.agent は、正しく「基礎・実装・発展」の3レベルを挙げ、どれか1つに取り組めば完了すると回答した。
	- 外部の用語集を参照できる仕組みによって、講座固有の質問に正確に答えられることが分かった。

- `--trace` で見えたツール呼び出し（ツール名と引数）：
	- LoRAとQLoRAの比較：
		- search_glossary({'query': 'LoRA'})
		- search_glossary({'query': 'QLoRA'})
	- 第2回の確認問題：
		- make_quiz({'lecture': 2})
		- 「Hugging Face Hub」の意味を問う問題が生成された。

- 1つの質問での LLM 呼び出し回数（`requests`）：
	- LoRAとQLoRAの比較：requests=2、tool_calls=2
	- 確認問題の生成：requests=2、tool_calls=1
	- ツールを呼び出した後、その結果をもとに回答を生成するため、1つの質問でLLMを複数回呼び出している。

- 追加したツールと、docstring に書いた説明：
	- make_quiz(lecture: int) -> str を追加した。
	- 指定された講義回の用語集からランダムに1件を選び、確認問題と答えをJSON形式で返す。
	- docstringには、「指定した講義回の用語集から確認問題を1問作り、問題と答えを返す」「講義内容の復習問題を求められたときに使う」と記載した。
	- app/agent.py に登録し、--list-toolsで表示されることを確認した。
	- 実際にmake_quizが呼ばれ、確認問題を生成した。
	- pytestでは15件のテストがすべて成功した。


## 宿題

- 取り組んだレベル：基礎

- やったこと：
  - app/agent.py の INSTRUCTIONS から、search_glossary による検索を必須とする1行を削除した。
  - 同じLoRA・QLoRAの比較質問を --trace 付きで実行した。
  - 指示を復元した後にも再実行し、pytestで動作を確認した。

- 結果：
  - 変更前：search_glossary 2回、requests=2、tool_calls=2。
  - 変更後：2回実行し、どちらも検索2回、requests=2、tool_calls=2。
  - 復元後：検索2回、requests=2、tool_calls=2。
  - いずれもLoRAとQLoRAの違いを正しく説明した。
  - 最終テストは15件すべて合格した。

- 気づき・疑問：
  - 検索必須の指示を削除しても検索回数は変わらなかった。
  - ツールのdocstringに残った「答える前に使うこと」という説明が影響している可能性がある。
  - 因果関係を確かめるには、docstringのみを変更する追加実験や複数回の比較が必要である。
   

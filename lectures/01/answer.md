# 第1回 解答

## 演習

- 講座に固有の質問（宿題の3レベル）へのモデルの回答（要約でよい）：AIE2026（AIエンジニア育成プログラムや関連するAI講座など）の宿題における**「3レベル」**という表現は、一般的に**「課題の難易度（初級・中級・上級）」**または**「実装・理解の段階（基礎・応用・発展）」**を指して使われます。
- 用語集の正しい意味と比べて気づいたこと：妥当そうなことを返してくるが、一般論としてそれとなく自信を持って回答してくる。
- 表示できたトークン数（入力 / 出力）：[tokens] input=23, output=635

## 宿題

- 取り組んだレベル：基礎
- やったこと：モデルやTEMPERATUREを切り替えて出力を確認
- 結果：

- 【モデル別】
- 実行コマンド：AIE_MODEL=gemini-3.8-flash uv run python -m app.ask "LoRAとは何ですか？"
⇒背景、仕組み、メリット、使われ方と詳細に説明してくれている。また具体例を挙げての理解しやすくしてくれている。

- 実行コマンド：AIE_MODEL=gemini-3.5-flash-lite uv run python -m app.ask "LoRAとは何ですか？"
⇒要点をコンパクトに説明してくれてるが、詳細の説明については割愛されている。

- 【AIE_TEMPERATURE別(※モデルはgemini-3.5-flash-liteで固定)】
- 実行コマンド：AIE_MODEL=gemini-3.5-flash-lite AIE_TEMPERATURE=0.0 uv run python -m app.ask "LoRAとは何ですか？"
- 実行コマンド：AIE_MODEL=gemini-3.5-flash-lite AIE_TEMPERATURE=1.0 uv run python -m app.ask "LoRAとは何ですか？"
- 実行コマンド：AIE_MODEL=gemini-3.5-flash-lite AIE_TEMPERATURE=2.0 uv run python -m app.ask "LoRAとは何ですか？"

⇒内容自体はどれも正しそうになっている。値が大きいからといって、創造的な回答にはなっていなそう。
　temperatureがコマンドとして、受け入れてはいることは確認できるが、出力に効いているいるかは別の質問で検証する必要あり。

- 気づき・疑問：
gemini-3.8-flashの方が、gemini-3.5-flash-liteと比べ、出力に時間がかかった。
gemini-3.8-flashについて、何回か「モデルが混み合っています（503）。少し待ってから再実行してください。」と返ってきた。
またトークン数を見ると、詳細に返答しているためか、gemini-3.8-flashの方が多めには返ってきた。

上述からプロジェクト等で運用する際は、APIを叩いた後に安定的に出力されるかという点、
コストも併せて考えると少しバージョンの古いモデルを使った方が良いのかと想像。
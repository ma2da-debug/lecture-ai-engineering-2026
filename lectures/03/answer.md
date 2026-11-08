# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
- `--trace` で見えたツール呼び出し（ツール名と引数）：
- 1つの質問での LLM 呼び出し回数（`requests`）：
- 追加したツールと、docstring に書いた説明：

## 宿題

- 取り組んだレベル：実装
- やったこと：app/tools.py に save_learning_log（learning_log/ に Markdown で保存）を追加し、app/agent.py で requires_approval=True を付けて登録した。output_type に DeferredToolRequests を加え、CLI で承認（y/N）を聞き、DeferredToolResults と message_history を渡して続きを実行するようにした。
- 結果：質問「LoRAとQLoRAの違いを調べて、学習の記録として保存して」
  - y の場合：search_glossary（LoRA, QLoRA）→ save_learning_log が承認待ちで停止 → 承認すると learning_log/20261108-140108_LoRAとQLoRAの違い.md に保存された
  - N の場合：search_glossary（LoRA のみ）→ save_learning_log を拒否 → モデルが同じ内容で再度 save_learning_log を呼び、再び承認待ちになった → 2回目も拒否すると、保存せずに回答だけを返した
- 気づき・疑問：
  - 拒否しても、モデルには「The tool call was denied.」としか伝わらないため、同じ呼び出しを繰り返すことがあった。拒否の理由を ToolDenied で伝えれば防げるのではないか（未検証）
  - 同じ質問でも、検索の回数（LoRA と QLoRA で2回／LoRA だけで1回）が実行ごとに変わった
  - --trace の usage は、承認後に再開した実行の分（requests=1）だけで、最初の実行の分は含まれていなかった
  - GitHubのCodespacesはあまり使ったことがなく、こんなことできるんだと、改めて発見です

1. arguments 是字符串，不是对象。 注意它是 "{\"city\":\"北京\"}" 这种转义过的字符串。这是 OpenAI 格式的规矩，llama.cpp 照抄了。你需要 parse 两次：先把整个响应反序列化成 struct，再把 arguments 这个 String 单独 serde_json::from_str 一次。这是 2.1 节最容易卡住的地方，提前知道能省你一晚上。

2. reasoning_content 是独立字段。 它和 content 分开返回了，这点很好。你做上下文管理（3.3 节）时就有了选择权：把这段思考塞回历史里（preserve_thinking 的效果），还是丢掉省上下文。建议默认丢掉，到 3.3 再对比。

3. content 是空字符串而不是 null。 你的 struct 里这个字段可以直接用 String，不用 Option<String>。但保险起见还是 Option<String> + #[serde(default)]，因为普通对话时字段形态可能不一样。

4. n_ctx 是 65536。 你启动时给的上下文窗口是 64K，不是模型上限的 262K。够用，但第 3 章测"读 5000 行文件"时心里有个数。

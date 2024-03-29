<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/vllm-project/vllm/main/docs/source/assets/logos/vllm-logo-text-dark.png">
    <img alt="vLLM" src="https://raw.githubusercontent.com/vllm-project/vllm/main/docs/source/assets/logos/vllm-logo-text-light.png" width=55%>
  </picture>
</p>

<h3 align="center">
Easy, fast, and cheap LLM serving for everyone
</h3>


---

**针对本地部署的模型，一般单机只部署单个模型，为了统一下游负载均衡的调用，更新了调用时模型名的校验机制；同时将<|im_end|>,<|im_start|>作为默认的stop token**

本地启动的模型一般以路径为模型名，调用时需要对齐
比如:
```
python -m vllm.entrypoints.openai.api_server --model /path/some-merged-gpt --trust-remote-code
```
那么请求
```
requests.post('http://serv-url.com/v1/chat/completions',json={
            "model": "/path/some-merged-gpt",
            "messages": [{"role": "user", "content": "hello"}]
        },headers={"Content_Type": "application/json"})
```
中model的参数就必须暴露启动时的路径
因此在只有一个模型serving的场景下，bypass了模型名校验，可以使用任意名称作为model参数调用
modifications are in ```vllm/entrypoints/openai/serving_engine.py```

add <|im_end|>,<|im_start|> as default stop tokens in:
```/vllm/entrypoints/openai/protocol.py```

<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=26&duration=3000&pause=800&color=00D9FF&center=true&vCenter=true&width=900&lines=Hey%2C+I%27m+Tej;I+write+NKI+kernels+and+ship+the+apps+that+call+them;Silicon+%E2%86%92+compiler+%E2%86%92+runtime+%E2%86%92+product;Fungible+engineer%3A+pick+a+layer&v=3" alt="Animated introduction" />

**Founding engineer who works the whole stack,** from systolic arrays and interconnect cost models<br/>
up to voice apps people talk to while they wait in a Seattle ramen line.

[![Site](https://img.shields.io/badge/varuntej.dev-21262d?style=flat-square&logo=googlechrome&logoColor=white)](https://varuntej.dev)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-21262d?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/varun-tej07/)
[![Medium](https://img.shields.io/badge/Medium-21262d?style=flat-square&logo=medium&logoColor=white)](https://medium.com/@varuntej07)
![Seattle](https://img.shields.io/badge/Seattle-21262d?style=flat-square&logo=googlemaps&logoColor=white)

<br/>

<img src="assets/stack.svg" width="100%" alt="One voice request traced from product, through devices, cloud runtime, inference, compiler and fabric, down to silicon" />

</div>

## The stack, with receipts

Most engineers pick a layer. I follow the bug wherever it goes.

<table>
<tr><th align="left">Layer</th><th align="left">What I built or broke open</th><th align="left">Proof</th></tr>

<tr><td><b>L5 · Product</b></td><td>
Founding engineer on <b>Aura</b>, a voice-first AI companion that remembers you, reaches out first, and runs your calendar and email.
Also shipped <b>Depth-wise</b> (questions become knowledge trees), <b>Pocket-Panel</b> (two Nova Sonic voice agents arguing live over websockets) and <b>MedTriageAI</b> (call a phone number, get triaged).
</td><td><a href="https://auravoiceapp.com">auravoiceapp.com</a><br/><a href="https://depthwise.app">depthwise.app</a><br/><a href="https://pocketpanel-production.up.railway.app">Pocket-Panel</a></td></tr>

<tr><td><b>L4 · Devices</b></td><td>
<b>Aura Desktop</b> (Tauri): fully on-device hold-to-talk dictation, OS-level hotkeys, voice control of apps and media keys, macOS at-rest encryption.
A custom <b>Android keyboard</b> that puts the assistant inside every app.
<b>Ooink AI</b>: an animated voice pig on a tablet kiosk outside Ooink Ramen, Seattle, answering menu questions from people in line.
</td><td><a href="https://github.com/AuraVoice/Aura-Desktop/pulls?q=is%3Apr+author%3Avaruntej07+is%3Amerged">25 merged PRs</a><br/><a href="https://github.com/varuntej07/ooink-ai">ooink-ai</a></td></tr>

<tr><td><b>L3 · Cloud runtime</b></td><td>
Aura's backend: FastAPI on Cloud Run, a separately deployed LiveKit voice worker so a slow call never blocks the request path, Cloud Tasks with idempotent claims,
and a failover contract across Anthropic, Gemini and OpenAI, with per-stage STT, LLM and TTS fallbacks.
</td><td><a href="https://github.com/varuntej07/Aura">Aura</a></td></tr>

<tr><td><b>L2 · Inference</b></td><td>
Compiled CLIP to a static NEFF on <b>Inferentia2</b> and read the hardware trace: <b>17.9x to 66x</b> faster than CPU at ~1e-5 logit drift.
Profiled Qwen2.5-VL-7B on a T4 and showed that quantizing the vision encoder is free; the 12.4 GB decoder is the only constraint that matters.
</td><td><a href="https://github.com/varuntej07/clip-neuron-profiling">clip-neuron-profiling</a><br/><a href="https://github.com/varuntej07/vlm-inference-profiler">vlm-inference-profiler</a></td></tr>

<tr><td><b>L1 · Compiler + fabric</b></td><td>
Root-caused a silent <code>torch_neuronx.trace</code> miscompile: a <code>[T, C]</code> buffer read as if laid out <code>[C, T]</code>, the exact pattern in every Whisper-family encoder.
The fix took the Voxtral encoder from cosine <b>0.372 to 0.99873</b>.
Built an alpha-beta cost model for Trainium collectives: a hierarchical ring cuts <b>254</b> EFA-gated steps to <b>14</b> at 128 ranks.
</td><td><a href="https://github.com/aws-neuron/aws-neuron-sdk/issues/1403">aws-neuron-sdk#1403</a><br/><a href="https://github.com/varuntej07/trainium-collectives">trainium-collectives</a></td></tr>

<tr><td><b>L0 · Silicon</b></td><td>
Wrote flash-decoding attention kernels in <b>NKI</b> (GQA, online softmax carried across KV tiles), merged into AWS's official samples.
Then found all six community kernels broken on SDK 2.32, four of them <b>silently returning zeros</b>, and ported mine to NKI 0.6.0 and validated it on Inf2.
</td><td><a href="https://github.com/aws-neuron/nki-samples/pull/129">nki-samples#129</a> merged<br/><a href="https://github.com/aws-neuron/nki-samples/issues/134">#134</a> · <a href="https://github.com/aws-neuron/nki-samples/pull/133">#133</a></td></tr>
</table>

## On the bench right now

**[voxtral-neuron-router](https://github.com/varuntej07/voxtral-neuron-router)**: fine-tune Voxtral Mini 3B on Trainium, serve it on Inferentia2, and turn raw speech straight into one of Aura's 34 tool calls.
The dataset is done (5,568 clips, 6.6 hours). The encoder compiles and runs at 82 ms on Inf2, but I don't quote that number until its output matches the CPU reference. That hunt is what turned up #1403.

## Bugs I chased into other people's code

- **AWS Neuron:** a silent compiler miscompile ([#1403](https://github.com/aws-neuron/aws-neuron-sdk/issues/1403)) and six broken sample kernels ([#134](https://github.com/aws-neuron/nki-samples/issues/134))
- **Graphify:** Windows hook paths stripped by Git Bash ([#1987](https://github.com/Graphify-Labs/graphify/issues/1987)), a search guard that never fired on Grep ([#1986](https://github.com/Graphify-Labs/graphify/issues/1986)), and a CLI crash on closed pipes ([#1807](https://github.com/Graphify-Labs/graphify/issues/1807)), two of them sent with a fix PR
- **Claude Code:** IDE detection reporting VS Code inside Android Studio ([#42900](https://github.com/anthropics/claude-code/issues/42900))

## Writing

[Triton Is Not CUDA in Python: It's a Tiling DSL](https://medium.com/@varuntej07/triton-is-not-cuda-in-python-its-a-tiling-dsl-c65c15ce3c46) · [Why PyTorch Wastes Your GPU Memory on Purpose](https://medium.com/@varuntej07/why-pytorch-wastes-your-gpu-memory-on-purpose-and-why-thats-brilliant-0a76899797fb)

---

## stats

<div align="center">

<img height="175em" src="https://github-profile-summary-cards.vercel.app/api/cards/stats?username=varuntej07&theme=tokyonight" alt="GitHub stats" />
<img height="175em" src="https://github-profile-summary-cards.vercel.app/api/cards/repos-per-language?username=varuntej07&theme=tokyonight" alt="Top languages by repository" />

</div>

<div align="center">

<img src="https://streak-stats.demolab.com?user=varuntej07&theme=tokyonight&hide_border=true&date_format=M%20j%5B%2C%20Y%5D" />

</div>

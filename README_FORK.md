# llama-cpp-turboquant-gemma4

Fork of [TheTom/llama-cpp-turboquant](https://github.com/TheTom/llama-cpp-turboquant) synced with upstream [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) master as of April 13, 2026 (b8766).

## What's new vs. upstream turboquant

- Merged upstream llama.cpp master, bringing support for:
  - **Gemma 4** (E2B, E4B, 26B-A4B, 31B) — text, vision, and audios
  - Upstream bugfixes through build b8766
- Minor merge fix to keep TurboQuant KV cache working with upstream's graph refactor

## Tested working with turbo3 KV cache

- Gemma 4 E4B (text + vision via mmproj)
- Qwen3 4B Thinking 2507
- Llama-3.1-8B

- **Do not mix KV cache types.** Using different types for `-ctk` and `-ctv` (e.g. `-ctk f16 -ctv turbo3`) falls back to a slow generic attention path and destroys performance. Always use matching types.
- On Blackwell (RTX 50-series) with CUDA Toolkit < 12.8, build with `-DCMAKE_CUDA_ARCHITECTURES=89` to get working PTX-JIT'd kernels; native `sm_120` requires CUDA 12.8+.

## Quick build (CUDA)

```bash
cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_CUDA_ARCHITECTURES=<your_arch>
cmake --build build -j4
```

CUDA architecture values:
- `89` — RTX 40-series (Ada)
- `90` — H100 (Hopper)
- `120` — RTX 50-series (Blackwell), requires CUDA Toolkit 12.8+

## Run with turbo KV cache

Flash attention is required.

```bash
./build/bin/llama-server -m model.gguf -ngl 99 -fa on \
  -ctk turbo3 -ctv turbo3 -c 32768
```

Available KV cache types: `turbo2` (2-bit), `turbo3` (3-bit), `turbo4` (4-bit).

## Credits

- TurboQuant algorithm: [Zandieh et al., ICLR 2026](https://research.google/blog/turboquant-redefining-ai-efficiency-with-extreme-compression/)
- llama.cpp integration: [TheTom](https://github.com/TheTom/llama-cpp-turboquant)
- Upstream llama.cpp: [ggml-org](https://github.com/ggml-org/llama.cpp)

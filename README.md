# Scene Weaver Pro

Clone this complete project and its already working perfectly without any issues. So run this completed project on our server.

https://github.com/rns420/swift-scene-creator.git

https://www.pixazo.ai/models/

note:- pixazo api key is with 0 credits balance so make sure use only free ai model FLUX.1 Schnell.

https://paraloncloud.com/console api key :-

note:- paraloncloud.com api key is with 0 credits balance so make sure use only free ai models Qwen 3.8 27B Is Live: a Free, OpenAI-Compatible Vision (keep thinking mode disabled)

Changes you have to Complete project is working perfectly without any issues but Genrate one completed video taking too much time. Here's what I found before pausing — the bottleneck isn't the key pool, it's the batch structure in the generation loop:

- Prompts are batched 3 scenes per call (`BATCH = 3`), and drawing for a batch only starts after that batch's prompt call returns (~19s). So each batch is serialized: prompt → then its 3 images.

- Only 4 batches run at once (`KEY_SLOTS = 4`), meaning at most 4 images are in flight at any moment — but each Pixazo key can handle its own concurrent request, and image calls take ~18s each. With a 12-scene script you get roughly: 19s (bible) + 19s (prompts) + 3×18s (images per batch) ≈ 90s+, barely better than serial.

- Retries also back off 1–5s per attempt, and `pool` waits for the slowest batch before starting the next.

What remains to fix:

1. Split the pipeline into two independent stages — fire all prompt batches concurrently first (4 parallel calls covering the whole script in one ~19s wave), then feed every prompt into a global image queue.

2. Raise image concurrency above 4 (e.g. 8 — two in flight per Pixazo key) instead of tying it to `KEY_SLOTS`, and rotate the key by a global counter rather than `index % 4`, so no key idles while another is busy.

3. Reduce prompt latency by using a larger `BATCH` (fewer chat calls, since a single chat call costs ~19s regardless of size).

Expected result: total time ≈ bible (19s) + one prompt wave (~20s) + ceil(scenes/8) × 18s, instead of scaling linearly with batch count.

Pixazo api key 1

03178ba869a446eba82bce98a79fefc3

Pixazo api key 2

048e52aee2094e24bad1b46a0fb15753

Pixazo api key 3

d004a01679f843e7ba090fa1d88c926d

Pixazo api key 4

9379183b074f4655adc0fa351dd4fa29

Paraloncloud api key 1

prlc_667ae9e467f065c6202fc7e12f07f575a8111b7ad906dd73

Paraloncloud api key 2

prlc_99b14331acd49b119237bef2ecc2e1078ecdd0f3be8a83d7

Paraloncloud api key 3

prlc_a16ea589738ffd489a8c2bb8550facce032e2263922de645

Paraloncloud api key 4

prlc_9dec184306d8d0dbb7d12c98d6dc22ce35d5ac3feaf2ccb9

This project was built with [Lovable](https://lovable.dev).

## Build with Lovable

Continue developing this project in the [Lovable editor](https://lovable.dev/projects/ab0bcfc4-6fba-421f-bdca-89f18627483f).

- **Ship faster**: describe what you want to build and Lovable handles the code.
- **Stay in sync**: every change made in Lovable is committed straight to this repository.
- **Full ownership**: this code is yours. Push to `main` on GitHub and your changes sync back into Lovable, ready for your next prompt.

## Development

Prefer working locally? You need Node.js and npm — [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating).

```sh
git clone <this-repository-url>
cd <repository-name>
npm i
npm run dev
```

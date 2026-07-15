<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/hero-dark.svg">
  <img alt="Abhishek Rajput — Backend · Robotics · AI Systems" src="assets/hero-light.svg" width="100%">
</picture>

<table align="center">
<tr>
<td width="230" align="center" valign="middle">

</td>
<td valign="middle">
I build backends that talk to robots, LLMs, and 60,000 sensors — and stay up.<br>
<sub><b>open to:</b> backend / systems-integration roles in robotics, AI, high-growth startups &nbsp;·&nbsp; <a href="https://linkedin.com/in/abhishek-rajput-4ba60221a">linkedin</a> · <a href="mailto:abhishek.rajput7202@gmail.com">email</a> · <a href="https://systemsabhishekrajput.hashnode.dev">blog</a></sub>
</td>
</tr>
</table>

<br>

<img alt="SYS.01 — Autonomous Robot Control · 150ms" src="assets/card-robot.svg" width="100%">

```text
┌─────────┐  yolov8 · 3d lidar · uwb → ros2 sensor fusion
│ SENSORS ├──────────────────┐
└─────────┘                  ▼
┌─────────┐  ws ~60ms  ┌─────────────────────────┐  modbus tcp ~40ms  ┌────────┐
│   HMI   │◄──────────►│  django orchestrator    │◄──────────────────►│ plc /  │
│ (react) │            │  + redis state reconcile│                    │ drives │
└─────────┘            └─────────────────────────┘                    └────────┘
                       total control loop: ~150ms   (legacy plc path: 500ms)
```

<details>
<summary>&nbsp;the HMI is classified, so here it is in ascii ↓</summary>

```text
┌─ teleop console ────────────────────────────────────────── ● LIVE ─┐
│                                                                    │
│  ┌──────────────────────┐   vel 0.8 m/s      heading 214°          │
│  │                      │   batt ▓▓▓▓▓▓▓░░ 87%                     │
│  │    [ camera feed ]   │   mode: AUTO       e-stop: ARMED         │
│  │                      │                                          │
│  └──────────────────────┘   obstacle: CLEAR  (lidar ∅ 4.2m)        │
│                                                                    │
│  [ HOLD ]  [ RESUME ]  [ DOCK ]              uwb fix: ±10cm        │
└─ zero safety incidents · 1.5y in production ───────────────────────┘
```

</details>

<sub>[architecture](https://excalidraw.com/#json=fcp0jF_oETfRzEMimgbsv,fW-qgNg7sEZ2U1_GQrdSBg) · [how we went from PLC-heavy control to a sub-150ms web HMI →](https://systemsabhishekrajput.hashnode.dev/modernizing-defense-robotics-from-plc-heavy-control-to-a-sub-150ms-web-orchestrated-hmi)</sub>

<br><br>

<img alt="SYS.02 — Production RAG Pipeline · 1.5–2s" src="assets/card-rag.svg" width="100%">

```text
 ingest:  docs ──► ocr ──► semantic chunks ──► tei embeddings ──► qdrant
                                                                     │
 query:   user ──► embed ─────────────────────► top-k retrieval ◄────┘
                                                       │
                                          cross-encoder rerank
                                                       │
                                       llama 3 ──► answer   ~1.5–2s e2e
```

<details>
<summary>&nbsp;▶ trace one query through the pipeline ↓</summary>

```text
┌─ retrieval trace ───────────── q: "reset torque on axis 3" · 1.6s ─┐
│  embed 384d ► qdrant top-8 ► cross-encoder rerank ► llama-3        │
│                                                                    │
│  #  source                     cosine       rerank  keep           │
│  1  servo_manual.pdf  p.42     ▓▓▓▓▓▓▓▓ .91  #8 ► #1  ●            │
│  2  faults_axis3.md   §3.1     ▓▓▓▓▓▓▓░ .87  #3 ► #2  ●            │
│  3  torque_calib.pdf  p.7      ▓▓▓▓▓▓░░ .81  #1 ► #3  ●            │
│  4  changelog_fw.txt  L204     ▓▓▓▓░░░░ .64  #2 ► —   ○ dropped    │
│     +4 more candidates retrieved · reranked · top-3 kept           │
│                                                                    │
│  answer  ████████████████░░ streaming · 47 tok · grounded          │
└─ breaker: closed · cache hit 63% · retries 0 · p95 1.9s ───────────┘
```

</details>

<img alt="RAG demo — live retrieval and generation" src="assets/rag-demo.gif" width="100%">

<sub>[architecture](https://excalidraw.com/#json=WxdU0V5sXS3nq6P_Y6B64,JiHbZ1uTvnB1_zVRGzv5hA) · [full demo video](https://github.com/user-attachments/assets/8a085ae0-b3b3-487c-a681-ca29f5237222)</sub>

<br><br>

<img alt="SYS.03 — Multi-Tenant IIoT Platform · 60k+ tags" src="assets/card-iiot.svg" width="100%">

```text
 20+ facilities ──► gateways ──► celery ingest ──► redis (hot) ──► postgres
     60k tags                        │
                                     └──► ws fan-out ──► dashboards (500+ users)
```

<details>
<summary>&nbsp;the client dashboards are restricted — here's the ops console ↓</summary>

```text
┌─ ops console ──────────────────────────── 20 sites · ● 500 online ─┐
│  site        tags    ingest/s   lag    state                       │
│  PLANT-01    8,204   ▓▓▓▓▓▓▓░ 1.2k   14ms   ● nominal              │
│  PLANT-07    9,880   ▓▓▓▓▓▓▓▓ 1.5k   19ms   ● nominal              │
│  DEPOT-03    6,140   ▓▓▓▓▓░░░ 0.8k   52ms   ▲ backpressure         │
│  RIG-11      4,905   ▓▓▓▓▓▓░░ 1.0k   22ms   ● nominal              │
│     … 16 more tenants · isolated · RBAC-scoped                     │
│                                                                    │
│  redis hot ██████████████░░ 61k keys   celery ▓▓▓▓▓▓▓░ 88%         │
│  ws fan-out ► 500 dashboards   drops 0   reconnect <200ms          │
└─ postgres p99 18ms · alerts 0 · uptime 99.98% · 90d ───────────────┘
```

</details>

<br>

<img alt="Stack — Backend / AI·RAG / Robotics" src="assets/stack.svg" width="100%">

<br>

<img alt="Contribution activity" src="https://github-readme-activity-graph.vercel.app/graph?username=Abhishekvoid&bg_color=0d1117&color=8b949e&line=ffa657&point=79c0ff&area=true&area_color=1c1408&hide_border=false&border_color=30363d" width="100%">

<p align="center">
  <sub>⌘ built by hand — every svg and ascii diagram in this readme is hand-written, like the systems above</sub>
</p>

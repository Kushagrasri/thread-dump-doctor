# Thread Dump Doctor

**Free, browser-based Java thread dump analyzer.** Paste or upload a `jstack` / `kill -3` output and get an instant, severity-ranked diagnosis — no account, no upload, no data ever leaves your machine.

**[Open Thread Dump Doctor →](https://kushagrasri.github.io/thread-dump-doctor)**

---

## What it detects

| Finding | Severity |
|---|---|
| JVM-reported deadlocks | Critical |
| Classic A→B→A lock-cycle deadlocks | Critical |
| CompletableFuture self-deadlock (pool self-starvation) | Critical |
| Socket-read I/O starvation (Triangle C) | Critical |
| Callback-thread hijacking (OkHttp / Netty) | Critical |
| In-callback `.join()` / `.get()` blocking | Critical |
| Thread pool saturation (zero idle workers) | Warning |
| `ForkJoinPool.commonPool()` overload | Warning |
| Unbounded `WAITING` in user/app code | Warning |
| Four-bucket triage summary | Info |

## How to use

**Single dump analysis:**
1. Run `jstack <pid> > dump.txt` or `kill -3 <pid>`
2. Open [Thread Dump Doctor](https://kushagrasri.github.io/thread-dump-doctor)
3. Drag & drop `dump.txt` → click **Analyze**

**Two-dump diff (confirm a live stall):**
1. Take two dumps ~30 seconds apart
2. Drop both files → click **Run two-dump diff**
3. Threads with unchanged stacks are flagged as stuck

## Supported formats

- OpenJDK / Oracle JDK `jstack`
- HotSpot `kill -3` signal output
- VisualVM thread dump export
- GraalVM native image (partial)

## Privacy

All processing runs in JavaScript in your browser. No thread dump data is sent to any server. The page works fully offline after the first load.

## Run locally (Python server)

```bash
git clone https://github.com/kushagrasri/thread-dump-doctor
cd thread-dump-doctor
python3 doctor.py --serve
# open http://localhost:8765
```

### CLI usage

```bash
# Pretty text report
python3 doctor.py /path/to/dump.txt

# JSON output (CI-friendly, exits with code 1 on critical)
python3 doctor.py --json /path/to/dump.txt | jq .verdict

# Two-dump diff
python3 doctor.py --diff dump_a.txt dump_b.txt
```

### HTTP API (when running `--serve`)

| Method | Path | Body | Response |
|--------|------|------|----------|
| `GET` | `/` | — | `index.html` |
| `POST` | `/analyze` | `{"path": "/abs/path"}` | Full JSON report |
| `POST` | `/verdict` | `{"path": "/abs/path"}` | Short verdict |
| `POST` | `/diff` | `{"path_a": "...", "path_b": "..."}` | Diff JSON |

## Requirements

- Python 3.9+ (for the local server / CLI)
- Any modern browser (for the standalone HTML)
- No third-party dependencies

## Files

```
thread_dump_doctor/
├── standalone.html   # self-contained browser tool (no server needed)
├── doctor.py         # CLI entry point + HTTP server
├── parser.py         # HotSpot thread-dump parser
├── detectors.py      # all detector logic
├── report.py         # human-readable text formatter
├── robots.txt        # search engine / AI crawler permissions
├── sitemap.xml       # sitemap for indexing
├── llms.txt          # AI-readable tool description
└── site.webmanifest  # PWA manifest
```

## Related tools / alternatives

Looking for alternatives? Thread Dump Doctor is similar to:
- **fastthread.io** — online thread dump analyzer (uploads data to server)
- **Spotify thread dump analyzer** — older open-source tool
- **VisualVM** — full JVM profiler with thread dump support
- **JVM thread dump analyzers** built into IntelliJ IDEA and Eclipse

Thread Dump Doctor differs by running 100% client-side, detecting CompletableFuture-specific patterns, and being usable with production dumps that cannot be uploaded externally.

---

**Keywords:** java thread dump analyzer, jstack analyzer, deadlock detector, thread pool saturation, CompletableFuture deadlock, ForkJoinPool analyzer, JVM concurrency debugger, java blocked threads, lock contention analyzer

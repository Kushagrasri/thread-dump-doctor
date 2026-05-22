# Thread Dump Doctor

**Free, browser-based Java thread dump analyzer.** Upload a `jstack` / `kill -3` / `jcmd` output and get an instant, severity-ranked diagnosis — no account, no upload, no data ever leaves your machine.

**[Open Thread Dump Doctor →](https://kushagrasri.github.io/thread-dump-doctor)**

> Diagnoses Java application hangs, freezes, deadlocks, thread pool saturation, high latency, I/O starvation, and more — instantly in your browser.

---

## What it detects

| Finding | Severity | Common symptom |
|---|---|---|
| JVM-reported deadlocks | Critical | App completely frozen |
| Classic A→B→A lock-cycle deadlocks | Critical | App frozen, no JVM deadlock message |
| CompletableFuture self-deadlock (pool self-starvation) | Critical | Async app hangs under load |
| Socket-read I/O starvation | Critical | All threads stuck on DB/HTTP |
| Callback-thread hijacking (OkHttp / Netty) | Critical | Network requests stall |
| In-callback `.join()` / `.get()` blocking | Critical | Async chain deadlocked |
| Thread pool saturation (zero idle workers) | Warning | High latency, request queuing |
| `ForkJoinPool.commonPool()` overload | Warning | parallelStream() stalled |
| Unbounded `WAITING` in user/app code | Warning | Silent hang, no error |
| Four-bucket triage summary | Info | At-a-glance thread health |

---

## Common symptoms this tool helps with

- **Java app is hanging / frozen** — take a thread dump, upload here, see which threads are deadlocked
- **Spring Boot stopped responding** — find saturated Tomcat/Jetty executor or HikariPool exhaustion
- **High CPU in Java process** — identify which RUNNABLE threads are spinning
- **Slow response times / latency spikes** — detect lock contention or pool saturation during slowdowns
- **Thread count growing (thread leak)** — four-bucket triage shows runaway thread creation
- **Intermittent microservice failures** — use diff mode to compare healthy vs unhealthy snapshots
- **JDBC connection pool exhausted** — detect HikariCP, c3p0, or DBCP saturation
- **CompletableFuture chain hung** — detect pool self-starvation (Triangle B pattern)
- **OkHttp / Netty threads blocked** — detect callback-thread hijacking

---

## How to use

### Single dump analysis
1. Generate a thread dump:
   ```bash
   jstack <pid> > dump.txt          # standard
   jcmd <pid> Thread.print > dump.txt  # modern alternative
   kill -3 <pid>                    # then check stdout/stderr
   ```
   In Docker: `docker exec <container> jstack 1 > dump.txt`
   In Kubernetes: `kubectl exec <pod> -- jstack 1 > dump.txt`

2. Open [Thread Dump Doctor](https://kushagrasri.github.io/thread-dump-doctor)
3. Drag & drop `dump.txt` → click **Analyze**

### Two-dump diff (confirm a live stall)
1. Take two dumps ~30 seconds apart
2. Drop both files → click **Run two-dump diff**
3. Threads with unchanged stacks between snapshots are definitively stuck

---

## Supported formats

- OpenJDK / Oracle JDK `jstack` (Java 8, 11, 17, 21)
- HotSpot `kill -3` signal output
- `jcmd Thread.print` output
- VisualVM thread dump export
- GraalVM native image (partial)

## Framework compatibility

Recognizes thread pools from: **Apache Tomcat**, **Jetty**, **Undertow**, **Spring Boot**, **OkHttp**, **Netty**, **HikariCP**, **ForkJoinPool**, **ExecutorService**, **ScheduledExecutorService**

---

## Privacy

All processing runs in JavaScript in your browser. No thread dump data is sent to any server. Works fully offline after the first page load. Safe for production thread dumps containing sensitive class and package names.

---

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

# JSON output (CI-friendly — exits with code 1 on critical)
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

---

## Requirements

- Any modern browser (for the standalone page — no install needed)
- Python 3.9+ (for the local server / CLI only)
- No third-party dependencies

---

## Files

```
thread_dump_doctor/
├── standalone.html   # self-contained browser tool (no server needed)
├── doctor.py         # CLI entry point + HTTP server
├── parser.py         # HotSpot thread-dump parser
├── detectors.py      # all detector logic
├── report.py         # human-readable text formatter
├── robots.txt        # search engine / AI crawler permissions
├── sitemap.xml       # sitemap for Google/Bing indexing
├── llms.txt          # AI-readable tool description (ChatGPT, Perplexity, Claude)
└── site.webmanifest  # PWA manifest
```

---

## Alternatives / related tools

| Tool | Requires upload | CompletableFuture detection | Browser-based |
|---|---|---|---|
| **Thread Dump Doctor** (this) | No | Yes | Yes |
| fastthread.io | Yes | No | Yes |
| IBM Thread Analyzer | No | No | No (desktop) |
| Spotify thread dump analyzer | No | No | No (desktop) |
| VisualVM | No | No | No (desktop) |
| IntelliJ IDEA | No | No | No (IDE) |

---

## Author

Built by [Kushagra Srivastava](https://www.linkedin.com/in/kushagrasri)

---

**Keywords:** java thread dump analyzer, jstack analyzer, java deadlock detector, thread pool saturation, CompletableFuture deadlock, ForkJoinPool analyzer, JVM concurrency debugger, java blocked threads, lock contention analyzer, spring boot thread dump, java application hang, java app slow, jdbc connection pool exhausted, jvm thread leak, java high cpu, tomcat thread dump, netty thread blocked, okhttp deadlock, java 17 thread dump, java 21 virtual threads

---
layout: default
title:  Roadmap
tab:    roadmap
---

## XRootD Development Roadmap

XRootD's program of work is tracked in detail in the [XRootD Evolution](https://github.com/orgs/xrootd/projects/1) project on GitHub.

XRootD's development plans have also been discussed in the recent [WLCG OTF #9](https://indico.cern.ch/event/1655633/) at CERN in March 2026, and [WLCG OTF #13](https://indico.cern.ch/event/1639200/contributions/7264076/) session at the latest [XRootD and FTS Workshop](https://indico.cern.ch/event/1639200/timetable/?view=indico_weeks_view) in September 2026 at CNRS, Lyon, France.

This document lists the items under discussion to prepare XRootD for the future, that is, for HL-LHC and beyond. It is based on the [XRootD Evolution](https://github.com/orgs/xrootd/projects/1) project on GitHub, the [community needs](https://indico.cern.ch/event/1655633/contributions/6977952/attachments/3244220/5787780/xrootd-community-needs.pdf#view=Fit) discussed in [WLCG OTF #9](https://indico.cern.ch/event/1655633/), and the [XRootD and FTS Workshop](https://indico.cern.ch/event/1639200/timetable/?view=indico_weeks_view). Every item appears twice: as a row in the milestone tables, and with its details in the [list further down](#items-in-detail).

### Milestones for 2026

All five items target XRootD 6.3, due on 18 December 2026.

| Item | Description | Impact | Risk | Decision Point | Target Date |
|---|---|---|---|---|---|
| XRootD Monitoring | Complete monitoring solution for metrics, logs and traces, maintained within the XRootD project and speaking OTLP. | Reliable, plug & play, cloud-native monitoring of remote data access, integrated with Grafana and the WLCG monitoring pipeline. | Sites stay on the UDP stream and external collectors; MONIT's open items for XRootD stay open; no source-level view of data access for the 2027 Data Challenge and HL-LHC planning. | End of November 2026 (code freeze): what is not merged moves to 6.4. | XRootD 6.3, Dec 2026 |
| Server-side Tape REST API | Complete server-side WLCG Tape REST API support, including durable `prepare` requests that survive a restart. | XRootD can front tape backends such as CTA, HPSS, etc. with the WLCG standard API. | Sites need separate tape gateways; staging requests are lost on restart; WLCG tape workflows cannot standardize on REST. | End of November 2026 (code freeze): ship durable prepare in 6.3, or the handler alone and durable prepare in 6.4. | XRootD 6.3, Dec 2026 |
| SSH Certificate Authentication (experimental) | New `XrdSecssh` plugin ([#3002](https://github.com/xrootd/xrootd/pull/3002)): authenticate with SSH keys or OpenSSH user certificates, over TLS only. | Lightweight alternative to X.509 for small deployments and interactive users. | Small sites and users keep needing X.509 certificates, the main barrier to entry outside the grid. | End of November 2026: review complete, or it moves to 6.4. End of 2027: drop the "experimental" label after a year of use. | XRootD 6.3 (experimental), Dec 2026 |
| OAuth2 / OpenID Connect Authentication (experimental) | New `XrdSecjwt` and `XrdAccjwt` plugins ([#3013](https://github.com/xrootd/xrootd/pull/3013)): bearer-token (JWT) authentication and authorization based on [RFC6749](https://www.rfc-editor.org/info/rfc6749/)/[RFC6750](https://www.rfc-editor.org/info/rfc6750/) and OpenID Connect, for both `roots://` and HTTPS. | Enables use of CERN SSO tokens with XRootD/EOS (e.g. in SWAN); one token validator for xroot and HTTP, without `libSciTokens`. | CERN services keep workarounds for SSO tokens; users keep needing certificates; XRootD falls behind HTTP storage that accepts OIDC. | End of November 2026: review complete, or it moves to 6.4. Together with SSH, that is about 18,500 new lines to review before the code freeze. | XRootD 6.3 (experimental), Dec 2026 |
| Security advisory process | Decide how we assign CVEs and publish advisories, and how long after a fix ships in all maintained branches. | Sites learn about fixed vulnerabilities and upgrade; XRootD meets the expectations of OpenSSF and CVE processes. | Sites cannot tell which releases fix which vulnerabilities, so they upgrade later than they should; CVE assignment is left to reporters. | December 2026: agree the policy. | Policy Dec 2026; applied from 2027/Q1 |
{: .roadmap}

### Milestones for 2027

| Item | Description | Impact | Risk | Decision Point | Target Date |
|---|---|---|---|---|---|
| Token authentication in production | Bring `XrdSecjwt`/`XrdAccjwt` out of experimental: pilot with CERN SSO in EOS and SWAN, and decide whether they replace `ztn` and `XrdSciTokens` for WLCG tokens. | One token stack for CERN SSO and WLCG tokens, for xroot and HTTP, with one external dependency fewer (`libSciTokens`). | Two token stacks to maintain and secure; CERN SSO support stays experimental; sites do not know which one to deploy. | 2027/Q1: agree the pilot with CERN SSO, EOS and SWAN. 2027/Q4: replace `ztn` and `XrdSciTokens` with jwt, or keep both. The protocol id changed, so a `ztn`-only client cannot authenticate to a `jwt`-only server. | Pilot H1 2027; production in 6.7, Dec 2027; any removal of `ztn` in 7.0 (2029) |
| Async I/O with Linux's [io_uring](https://en.wikipedia.org/wiki/Io_uring) | Enables each server thread to hold many I/O operations in flight. | Reduced CPU usage and latency from syscalls: more throughput from the same hardware, important for the HL-LHC era. | More servers and CPU needed for HL-LHC rates; the blocking I/O path stays the limit on fast NVMe and 400 Gb/s NICs. | June 2027: go/no-go on prototype results, measured with the fio benchmarks against the current I/O path. | Prototype in 6.5, Jun 2027; opt-in in 6.7, Dec 2027 |
| HTTP/2 and S3 server (prototype) | New `XrdHttp2` plugin ([RFC9113](https://datatracker.ietf.org/doc/html/rfc9113), nghttp2) to replace `XrdHttp`, with server-side support for the [Amazon S3 API](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html) on top. | XRootD as a lightweight S3 storage backend, an alternative to MinIO; HTTP performance improvements for multi-stream and async operations. | Others fill the gap left by MinIO; `XrdHttp` stays on HTTP/1.1; XRootD misses users outside HEP. | December 2027: on the prototype results, decide scope and effort for 7.0. | Prototype Dec 2027; experimental 2028; default in 7.0 (2029) |
| Complete large file support | Reliable transfers of large files (>100GB): resume incomplete transfers, and streaming transfers (like `GET`/`PUT` in HTTP) in the XRootD protocol. | Large transfers no longer restart from zero after a failure, saving bandwidth and time. | Failed large transfers restart from scratch, and this gets worse as file sizes grow for HL-LHC; users move to other tools. | 2027/Q1: decide which parts work without a protocol change (6.x) and which wait for 7.0. | Resume in 6.6, Sep 2027; streaming in 7.0 (2029) |
| Standard log format | Apache-style access logs and a structured format for client and server log messages. | Logs readable by people and by structured log parsers (Loki, OpenSearch) without custom rules. | Operators keep writing fragile parsers; the logs from the 6.3 monitoring work stay harder to use. | 2027/Q1: agree the format with sites and MONIT. | XRootD 6.4, Mar 2027 |
| Cloud-native deployment | Official container image (not yet released), Helm chart, operators, and Kubernetes deployment guides. | Sites deploy XRootD on Kubernetes the same way as the rest of their stack. | Each site builds its own images and charts; harder adoption by new and cloud users. | 2027/Q1: agree on the image registry and the chart's scope (server, proxy, cache). | Image in 6.4, Mar 2027; Helm chart Jun 2027; guides Dec 2027 |
| Website and documentation overhaul | Move documentation into version control as text, build all formats from one source, and renew the website. | Lower barrier to entry for users, admins and operators, with step-by-step guides for common deployments. | Users keep finding scattered, outdated documentation; support load stays on developers; new users go elsewhere. | 2027/Q1: choose the toolchain and structure. | Reference docs and new website, Dec 2027 |
| XCache evolution | In-flight checksums for vector reads (pgreadv), block-size and range tuning, less read amplification, client-side caching, test coverage. | Caches that serve analysis workloads efficiently and safely. | Analysis through caches stays slow and amplifies WAN reads; corruption in vector reads goes undetected. | 2027/Q2: set priorities with the sites that run XCache. | Through 2027, in 6.5–6.7 |
| Enhanced Metalink ([RFC5854](https://www.rfc-editor.org/info/rfc5854/)) | Complete support for RFC5854: more resilience, local and in-memory metalink files. | ALICE can use metalinks fully, closing a long-time request. | A long-standing request from a major experiment stays open, and ALICE keeps its workarounds. | 2027/Q1: agree the scope with ALICE. | XRootD 6.5, Jun 2027 |
| Baseline: security, stability, performance | Test suite split into unit, integration and end-to-end tests; fio benchmarks in CI; static analysis; threat model; AI policy. | Fewer regressions and vulnerabilities reach releases; performance is tracked from release to release. | Users and security reporters find our regressions and vulnerabilities before we do; reports keep arriving faster than they can be fixed. | Reviewed at each release; threat model by June 2027. | Continuous; a milestone in each 2027 release |
| OpenSSF best practices | Scorecard checks, SBOM in CI, REUSE compliance, governance and community channels. | Meets the supply-chain expectations of CERN and other adopters; visible project health. | Adopters' security reviews flag XRootD; harder to be chosen as infrastructure outside HEP. | 2027/Q2: governance proposal. | Scorecard and SBOM Jun 2027; REUSE Dec 2027 |
| XRootD 7.0 scope | Decide what goes into the next major release: protocol changes, HTTP/2 and S3, UTF-8, multi-user, clustering. | Protocol changes land together, with time for sites to deploy them before Run 4. | Without a scope decision, protocol changes slip past the start of Run 4 and land when sites can no longer upgrade. | December 2027: freeze the scope. | Scope Dec 2027; release 2029 |
{: .roadmap}

### Milestones for 2028 and later (rough estimates)

| Item | Description | Impact | Risk | Decision Point | Target Date |
|---|---|---|---|---|---|
| Evolution of client Python bindings | Pythonic interfaces, asynchronous operations, [fsspec](https://filesystem-spec.readthedocs.io/en/latest/#), and interfaces to replace gfal in DIRAC/Rucio. | Python users and data management systems use XRootD directly. | gfal stays a dependency; Python analysis users prefer other protocols. | 2027/Q4: confirm demand, and help, from Rucio and DIRAC. | 2028 |
| XRootD clustering evolution | Documentation and a mini-roadmap for clustering, then features with community interest (see below). | Smarter redirection for federations such as CMS AAA and the CERNBox redirectors. | A heavily used, poorly documented feature stays with few experts; federations keep sending clients to failing sites. | December 2027: which protocol changes go into 7.0. | Docs and mini-roadmap 2028; protocol changes in 7.0 (2029) |
| UTF-8 support | Support UTF-8 encoding in URL paths for both XRootD and HTTP. | Special and escaped characters in file and directory names work everywhere. | EOS keeps its workarounds for HTTP; users with non-ASCII names hit errors. | December 2027 (7.0 scope). | XRootD 7.0, 2029 |
| Multi-user support | Native `uid`/`gid` tracking of files and directories. | Correct ownership on POSIX backends; required for NFS support. | No path to NFS; sites depend on an out-of-tree plugin. | December 2027 (7.0 scope). | XRootD 7.0, 2029 |
| SASL/PAM authentication | PAM/LDAP and SASL support, and a common set of authentication methods for XRootD and HTTP. | XRootD fits the identity systems sites already run; needed for NFS. | Each protocol keeps its own authentication; harder integration at new sites. | December 2027, together with multi-user. | 2028–2029 |
| XRootD as storage solution | A data-integrity solution similar to EOS, reference backends (Ceph, ZFS, distributed filesystems), and full deployment guides. | XRootD easy to deploy as proxy, cache or object store; more users. | XRootD stays a protocol layer that others build storage around, with fewer users and contributors. | 2028: choose the reference backend, after the S3 decision. | 2028–2029 |
| XRootD as API gateway | A high-performance, extensible server-side API building on `XrdSsi`, beyond `XrdHttpExtHandler`. | Server-side services not backed by storage, e.g. ServiceX. | Such services are built elsewhere; `XrdSsi` stays underused. | 2028. | 2029 or later |
| pNFS ([RFC8434](https://www.rfc-editor.org/info/rfc8434/)) | Server-side support for the parallel Network Filesystem protocol ([plan](https://codimd.web.cern.ch/Ph2Giot8STGOwh-WEfgOlg) in CodiMD). | Replace FUSE mounts with NFS mounts and the native Linux kernel client. | FUSE mounts stay, with their performance and stability limits. | After multi-user support ships in 7.0. | 2030 or later |
| RDMA ([RFC5040](https://www.rfc-editor.org/info/rfc5040/)) | Remote Direct Memory Access as a separate data channel. LAN only. | XRootD viable in HPC; efficient EOS ↔ batch movement in the Tier-0; NICs do most of the data movement. | XRootD unsuitable for HPC; CPU keeps being spent on data movement. | 2028: only with an HPC partner and dedicated effort. | 2030 or later |
| Other items and ideas | Smaller features and outreach (see below). | Candidates for future milestones. | Good ideas are lost, or done without a plan. | Reviewed every year at the XRootD and FTS Workshop. | Unscheduled |
{: .roadmap}

### Items in detail

#### 2026

1. **XRootD Monitoring**
   * Metrics, logs and traces from the source, speaking OTLP, integrated with Grafana
   * Closes the open items for XRootD in MONIT's list from CHEP 2026
   * Announced at [WLCG OTF #9](https://indico.cern.ch/event/1655633/contributions/6977951/attachments/3244206/5787749/xrootd-status-and-plans.pdf#view=Fit&page=37) in March, prototype at the workshop in September, release in December
   * Includes errors and failures on the wire ([#2951](https://github.com/xrootd/xrootd/issues/2951), suggested at the workshop)
   * *Target:* XRootD 6.3, December 2026

1. **Server-side Tape REST API**
   * Persistent Tape REST API handler in `XrdHttp`, on master since September 2026
   * Routes Tape REST through the native `prepare` request, with end-to-end tests
   * Durable `prepare` requests that survive a server restart still under review
   * *Target:* XRootD 6.3, December 2026

1. **SSH Certificate Authentication (experimental)**
   * New `XrdSecssh` plugin, [#3002](https://github.com/xrootd/xrootd/pull/3002) on GitHub
   * Raw `ssh-ed25519`/`ssh-rsa` keys mapped through an `authorized_keys`-style file, or OpenSSH user certificates checked against a CA
   * Only over TLS: the plugin proves possession of a key on the connection
   * [SSH Authentication](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Certificate-based_Authentication) is a good alternative to X509 for small deployments
   * *Target:* XRootD 6.3 (experimental), December 2026

1. **OAuth2 / OpenID Connect Authentication (experimental)**
   * New `XrdSecjwt` (`sec.protocol jwt`) and `XrdAccjwt` plugins, [#3013](https://github.com/xrootd/xrootd/pull/3013) on GitHub
   * Bearer-token authentication based on [RFC6749](https://www.rfc-editor.org/info/rfc6749/)/[RFC6750](https://www.rfc-editor.org/info/rfc6750/), with keys from OpenID discovery or a JWKS URL
   * Same validator for `roots://` and for HTTPS (`Authorization: Bearer`)
   * Enables use of CERN SSO tokens with XRootD/EOS, e.g. in SWAN; the `xrdjwt` tool obtains tokens from CERN SSO, WLCG IAM, Google or GitHub
   * Reads an existing SciTokens configuration, but does not link `libSciTokens`
   * *Target:* XRootD 6.3 (experimental), December 2026; otherwise 6.4, March 2027

1. **Security advisory process**
   * Decide on *how to handle CVE assignment and publishing*
   * Publish advisories once the fix has shipped in all maintained branches for an agreed time
   * *Target:* policy in December 2026, applied to advisories from 2027/Q1

#### 2027

1. **Token Authentication in Production**
   * Pilot of `XrdSecjwt`/`XrdAccjwt` with CERN SSO in EOS and SWAN, agreed in 2027/Q1
   * Decide whether `jwt` and `XrdAccjwt` replace `ztn` and `XrdSciTokens` for WLCG tokens
   * Plan the migration: the protocol id changed, so a `ztn`-only client cannot authenticate to a `jwt`-only server
   * Before production, require an audience for every issuer (today `aud` is only compared when one is configured)
   * *Target:* pilot in H1 2027, production in 6.7 (December 2027), any removal of `ztn` in XRootD 7.0 (2029)

1. **Async I/O with Linux's [io_uring](https://en.wikipedia.org/wiki/Io_uring)**
   * Each server thread holds many I/O operations in flight
   * Reduced CPU usage and latency due to syscalls, important for the HL-LHC era
   * Measure first: prototype compared with the current I/O path using the fio benchmarks
   * *Target:* prototype in 6.5 (June 2027), opt-in in 6.7 (December 2027)

1. **Server-side support for S3 protocol (XrdHttp2 + XrdS3)**
   * This can be added together with HTTP/2.0 support ([RFC9113](https://datatracker.ietf.org/doc/html/rfc9113))
   * Implement new `XrdHttp2` plugin using [nghttp2](https://nghttp2.org/) to replace `XrdHttp`
   * MinIO officially [archived its open-source repository on GitHub](https://github.com/minio/minio) in February 2026
   * See also the [MinIO EOL announcement](https://www.docker.com/blog/minio-end-of-life-how-to-stay-patched-and-audit-ready-with-docker-els/) on DockerHub
   * Discussions about alternatives now happen regularly on [HackerNews](https://news.ycombinator.com/item?id=49709381)
   * This is a strategic area with a visible gap left by MinIO that XRootD could fill
   * *Target:* prototype in December 2027, experimental in 2028, default in XRootD 7.0 (2029)

1. **Complete support for large file transfers**
   * Features to make it easier and more reliable to transfer files >100GB
   * Ability to resume incomplete file transfers (6.x)
   * Streaming data transfers in XRootD protocol, like `GET`/`PUT` in HTTP (protocol change, 7.0)
   * *Target:* resume in 6.6 (September 2027), streaming in XRootD 7.0 (2029)

1. **Standardize Log Message Format (client & server)**
   * Use Apache-style access log formatting
   * Current log format is difficult to understand
   * Formatting is “resistant” to being picked up by structured log parsers
   * See corresponding [item](https://github.com/orgs/xrootd/projects/1/views/1?pane=issue&itemId=95187132) on XRootD Evolution
   * *Target:* XRootD 6.4, March 2027

1. **Cloud-Native XRootD**
   * First presented at the [WLCG OTF #9](https://indico.cern.ch/event/1655633/contributions/6977951/attachments/3244206/5787749/xrootd-status-and-plans.pdf#view=Fit&page=37) in March 2026
   * This is an overarching theme guiding some of the development items
   * Official container image, planned since XRootD 6.2 and not yet released
   * Cloud-native monitoring, integrated with Grafana, coming in XRootD 6.3
   * Better integration with Kubernetes: Helm charts, operators
   * Deployment documentation for Kubernetes
   * *Target:* image in 6.4 (March 2027), Helm chart by June 2027, guides by December 2027

1. **Website and Documentation Overhaul**
   * See documentation [item](https://github.com/orgs/xrootd/projects/1/views/1?pane=issue&itemId=95187520) in XRootD Evolution
   * Documentation is scattered, inconsistent
   * Need to migrate to textual format within version control
   * Build all documentation formats out of same common source
   * *Target:* reference documentation and new website by December 2027

1. **XCache Evolution**
   * XCache needs improvements to support analysis use-cases
   * Need support for in-flight checksums for readv (pgreadv)
   * Tuning of block sizes, read/readv range coalescing
   * Reduce read amplification through proxy/cache
   * Client-side caching ([JournalCache](https://indico.cern.ch/event/1639200/contributions/7247620/) and [uCache](https://indico.cern.ch/event/1639200/contributions/7255818/))
   * Test coverage for proxy/cache in the test suite ([#2747](https://github.com/xrootd/xrootd/issues/2747) on GitHub)
   * More details in the [presentation](https://indico.cern.ch/event/1639200/contributions/7244504/) by Matevž at the latest workshop
   * *Target:* through 2027, in releases 6.5 to 6.7

1. **Improved support for Metalink files**
   * Long-time request from ALICE, see [issues](https://github.com/xrootd/xrootd/issues?q=is%3Aissue%20state%3Aopen%20metalink) on GitHub on this topic
   * Complete support for [RFC5854](https://www.rfc-editor.org/info/rfc5854/)
   * Main topics: improve resilience, support local files, in-memory files
   * *Target:* XRootD 6.5, June 2027

1. **Baseline: Security, Stability, Performance**
   * Refactor test suite into *unit, integration, and end-to-end* tests, improve coverage
   * Fix security vulnerability reports (publishing is covered by the 2026 advisory process item)
   * Fix issues reported by static analysis tools (valgrind, sanitizers, clang-tidy, coverity)
   * Integrate [fio](https://github.com/axboe/fio) benchmarks into test suite, track performance and detect regressions
   * Finalize the AI policy and add `AGENTS.md` or similar configuration to the repository
   * Create formal [security threat model](https://community.owasp.org/Threat_Modeling) for XRootD (see [item](https://github.com/orgs/xrootd/projects/1/views/1?pane=issue&itemId=195918569) in XRootD Evolution)
   * *Target:* continuous, with a milestone in each 2027 release; threat model by June 2027

1. **OpenSSF Best Practices Evolution**
   * We should continue to evolve our development practices
   * Project management, governance, community engagement channels, etc
   * Use OpenSSF Scorecard GitHub [action](https://github.com/ossf/scorecard-action) for security scanning
   * Make XRootD REUSE compliant (see https://reuse.software/)
   * Automatic Software Bill of Materials (SBOM) for XRootD in CI (use [FOSSA](https://fossa.com/customers/cncf/) GitHub [action](https://github.com/fossas/fossa-action))
   * See https://xrootd.org/2026/05/24/announcement_openssf.html
   * See also https://insights.linuxfoundation.org/project/xrootd/security
   * *Target:* Scorecard and SBOM by June 2027, REUSE by December 2027

1. **XRootD 7.0 Scope**
   * Collect the changes that need a protocol or ABI break: streaming transfers, UTF-8, multi-user, clustering
   * Decide whether HTTP/2 and S3 become the default HTTP implementation
   * Leave sites time to deploy 7.0 before Run 4
   * *Target:* scope frozen in December 2027, release in 2029

#### 2028 and later

1. **Evolution of Client Python Bindings**
   * Modernize interfaces to be more pythonic
   * Support for asynchronous operations and [fsspec](https://filesystem-spec.readthedocs.io/en/latest/#)
   * Add interfaces for replacing gfal in DIRAC/Rucio
   * *Target:* 2028

1. **XRootD Clustering Evolution**
   * XRootD clustering does not receive much attention in general
   * However, it's heavily used in CMS AAA, CERNBox redirectors, etc
   * Need to create proper documentation for the clustering features
   * Need to create a mini-roadmap for evolution of clustering features
   * Some items with community interest:
     * Cluster-level detection of corrupted files
     * GeoIP support for more intelligence in redirection
     * VO-specific site selection algorithm (CMS request)
     * Automatic quarantine for sites based on availability / errors
     * Multi-source support similar to CMS-SW (`XrdAdaptor`)
     * Load-balancing recently worked on for RAL use cases
   * Identify potential for protocol-level enhancements targeting XRootD 7
   * *Target:* documentation and mini-roadmap in 2028, protocol changes in XRootD 7.0 (2029)

1. **UTF-8 Support in URLs**
   * Pain-point in EOS, requires some hacks to work for HTTP
   * Needed to support other locales and special characters in file names
   * *Target:* XRootD 7.0, 2029

1. **Native support for multi-user (uid/gid tracking)**
   * Pre-requisite for NFS4 implementation
   * Out-of-tree plugin by OSG exists, could serve as base
   * *Target:* XRootD 7.0, 2029

1. **SASL/PAM Authentication**
   * Integration with PAM/LDAP important also for NFS support
   * [SASL](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) provides higher-level mechanism for various authentication methods
   * In general, need to find ways to homogenize XRootD/HTTP authentication methods
   * *Target:* 2028–2029, together with multi-user support

1. **XRootD as Storage Solution**
   * This is a strategic item to gain more users
   * Need to provide a "data integrity" solution similar to what EOS provides
   * Possible XRootD solution with Ceph (`XrdCeph`, `CephFS`)
   * Alternatives: XRootD + ZFS, XRootD + distributed FS (NFS, GPFS, HDFS, Lustre)
   * Need to create full deployment guides for various use-cases
   * Make it easy to deploy XRootD as proxy, cache, object store
   * *Target:* 2028–2029

1. **XRootD as API Gateway**
   * XRootD as an API gateway (building on underutilized XrdSsi)
   * Supported by `XrdHttpExtHandler` at the moment
   * Need high-performance alternative with more extensibility
   * Could be used in implementations of things like ServiceX
   * Allow building server-side functionality not backed by storage
   * *Target:* 2029 or later

1. **pNFS (Parallel NFS)**
   * Server-side support for [RFC8434](https://www.rfc-editor.org/info/rfc8434/), see the [plan](https://codimd.web.cern.ch/Ph2Giot8STGOwh-WEfgOlg) in CodiMD
   * Replace FUSE mounts with NFS mounts, use native Linux kernel client
   * Needs multi-user support (uid/gid tracking) first
   * *Target:* 2030 or later

1. **Remote Direct Memory Access (RDMA)**
   * Important feature for making XRootD viable in HPCs
   * Good fit also for efficient data movement within Tier0 (EOS ↔ batch)
   * Main impact is reducing syscalls and CPU usage
   * Offload most data movement work to NICs
   * Can be implemented as separate RDMA data channel
   * See also: https://codimd.web.cern.ch/Z5aMkjbtSsmo_ySwBbSvzw
   * *Target:* 2030 or later, with an HPC partner

1. **Miscellaneous Items/Ideas**
   * Migrate `xrootdfs` to FUSE3, implementation based on file descriptors
   * Command line tool (`xrdsync`) for synchronization of whole directories, like `rsync`
   * Support for Kerberos authentication over HTTP
   * Support for GRPC for server-side API extensibility
   * TLS offload to the kernel or NIC via [kTLS](https://docs.kernel.org/networking/tls-offload.html) (see also [#2238](https://github.com/xrootd/xrootd/issues/2238) on GitHub)
   * Overhaul of support for macaroons (needed to improve security)
   * Dynamically adjusted timeouts for vector reads (`readv`), see [#2333](https://github.com/xrootd/xrootd/issues/2333) on GitHub
   * Decide on dropping or reviving `XrdEc` plugin for erasure coding
   * Integration with systemd for seamless server upgrades/restarts
   * Benchmarking performance on 400Gbps NICs and higher
   * Proactive advertisement of XRootD in other communities
     * XRootD as AI/ML storage backend
     * XRootD in data analytics outside of HEP (DuckDB, pandas, parquet, etc)
     * XRootD as HTTP/S3 object storage replacing MinIO
     * XRootD as replacement for squid/varnish in CVMFS deployments
   * *Target:* unscheduled, reviewed every year at the XRootD and FTS Workshop

### Summary

Overall, our goal is to gradually modernize XRootD's features and implementation to use latest C++ features, improve stability, performance, and extend XRootD to be usable beyond scientific remote data access use cases. We have lowered the entry barrier for development with our recent work on continuous integration and modernization of the build system and packaging. Now it's time to modernize the website and documentation to also lower the barrier to entry for regular users, system admins, and operators of XRootD, by providing step-by-step guides for common deployment use cases, and making the website attractive to potential new users.

In 2027, with the same team, we focus on three developments: `io_uring` for performance, an HTTP/2 and S3 prototype to fill the gap MinIO left, and resuming large transfers. We also bring token authentication for CERN SSO and WLCG tokens into production; it may already ship as experimental in 6.3. The decisions at the end of 2027 fix the scope of XRootD 7.0, which should ship in 2029 so that sites can deploy it before Run 4.

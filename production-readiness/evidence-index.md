# CloudNotes Evidence Index

## Review Metadata

- Assessment date: 2026-10-14
- Repository: cloudnotes-v1
- Assessment branch: assessment/production-readiness
- Reviewed commit: `83da3cd68f6414350ba79f53f8b85ecce41b13de`
- Environment: Local development / Kubernetes; verify per artifact
- Evidence policy: No result is marked PASS until actual evidence is inspected.

## Evidence Register

### E01 — Application and Deployment Configuration
- Candidate source: `app.py`, `k8s/deployment.yaml`
- Type: Source/configuration
- Date and source revision: Tests run 2026-10-14; HEAD `83da3cd68f6414350ba79f53f8b85ecce41b13de`; working tree had uncommitted changes.
- Expected result: Application and deployment configuration match the declared release scope.
- Observed result: `./venv/bin/pytest -q` reported 2 passed and 1 Flask deprecation warning. The tests check two routes with database initialization mocked. `/health` and `/ready` also returned successful JSON responses when called inside a CloudNotes pod.
- Supports: R3, R4, O3; other checks only if directly supported.
- Limitations: Configuration alone does not prove successful rollout, recovery, or application behavior.

### E02 — Container and Local Environment
- Candidate sources: `Dockerfile`, `docker-compose.yml`, `Makefile`
- Type: Build and environment configuration
- Date and source revision: Not verified; record when the artifact is collected and inspected.
- Expected result: The documented local environment can be built and run reproducibly.
- Observed result: Not yet tested.
- Supports: R3, R4 when supported by actual build/run results.
- Limitations: Files alone do not prove a successful build or deployment.

### E03 — Kubernetes Configuration
- Candidate sources:
  - `k8s/configmap.yaml`
  - `k8s/deployment-good.yaml`
  - `k8s/ingress.yaml`
  - `k8s/network-policy.yaml`
  - `k8s/pvc.yaml`
- Type: Infrastructure configuration
- Date and source revision: Inspected 2026-10-14; HEAD `83da3cd68f6414350ba79f53f8b85ecce41b13de`; working tree had uncommitted changes.
- Expected result: Workload, networking, storage, and configuration match the declared scope.
- Observed result: Three CloudNotes pods and one PostgreSQL pod were Ready. The cloudnotes service listed three endpoints on port 5000. A NetworkPolicy selecting PostgreSQL and allowing TCP 5432 from pods labelled `app=cloudnotes` exists.
- Supports: R3, R4, S4, and D1 only where the actual configuration supports the claim.
- Limitations: Manifests do not prove that access restrictions, backups, or recovery work.

### E04 — Monitoring Configuration
- Candidate sources: `prometheus.yml`, `promtail.yml`
- Type: Monitoring and logging configuration
- Date and source revision: Not verified; record when the artifact is collected and inspected.
- Expected result: Relevant metrics and logs can be collected safely.
- Observed result: Not yet tested.
- Supports: O1 and O3 when backed by actual results.
- Limitations: Configuration files do not prove dashboard visibility, alert delivery, or incident response.

### E05 — Local Failover Test
- Candidate source: Previously performed CloudNotes failover exercise; locate the original script/output before linking it.
- Type: Failure and recovery test
- Date and source revision: Not verified; record when the artifact is collected and inspected.
- Expected result: Service recovers after the tested component fails and preserves the tested application behavior.
- Observed result: Previously reported local test showed approximately 5.412 seconds to recovery, stable recovery, preserved baseline records, and successful new-note write/read. Verify the original output and tested revision before using these results.
- Supports: R2, and R4 only if the test also supports capacity/dependency claims.
- Limitations: Local failover does not prove database restore, regional recovery, or production availability.

### E06 — Observability Test Results
- Candidate source: Actual dashboard screenshots, alert test output, logs, or notification records.
- Type: Runtime evidence
- Date and source revision: Not verified; record when the artifact is collected and inspected.
- Expected result: Relevant signals are visible and an alert reaches the responsible responder.
- Observed result: Missing until actual artifacts are located and inspected.
- Supports: O1, O2, O3, O4 as applicable.
- Limitations: A monitoring configuration is not proof of successful alert delivery.

### E07 — Security Review
- Candidate source: Actual access tests, secret-handling review, dependency/image scan, and network-policy validation.
- Type: Security evidence
- Date and source revision: Not verified; record when the artifact is collected and inspected.
- Expected result: Intended access boundaries hold, secrets are protected, and findings are triaged.
- Observed result: Missing until actual test and audit results are inspected.
- Supports: S1, S2, S3, S4 as applicable.
- Limitations: A configured policy or secret reference alone does not prove the control works.

### E08 — Backup and Restore
- Candidate source: Actual backup execution records and isolated restore/integrity test.
- Type: Disaster recovery evidence
- Date and source revision: Not verified; record when the artifact is collected and inspected.
- Expected result: Expected data is restored, integrity is verified, and recovery timing/data loss are measured.
- Observed result: Unverified until actual restore output is found.
- Supports: D1, D2, D3, D4 only where directly demonstrated.
- Limitations: A backup script or local failover test does not prove successful data restoration.

### E09 — Baseline Cost Estimate
- Candidate source: Google Cloud Pricing Calculator Screenshot 2.
- Type: Cost estimate
- Date and assumptions: 2026-10-14; verify screenshot and assumptions.
- Expected result: Baseline estimate uses the documented region, currency, runtime, and service scope.
- Observed result: Previously reported estimate: $561.77/month.
- Supports: C1 and C2.
- Limitations: This is an estimate, not an invoice or measured production spend.

### E10 — Optimized Cost Estimate
- Candidate source: Google Cloud Pricing Calculator Screenshot 4.
- Type: Cost estimate
- Date and assumptions: 2026-10-14; verify screenshot and assumptions.
- Expected result: Optimized estimate uses the same pricing basis and scope as the baseline, apart from the intended sizing changes.
- Observed result: Previously reported estimate: $400.01/month.
- Supports: C1 and C2.
- Limitations: Estimated savings do not prove production performance or realized savings.

### E11 — Cost Comparison
- Candidate source: Google Cloud Pricing Calculator Screenshot 5 or comparison document.
- Type: Cost analysis
- Expected result: Baseline and optimized estimates are compared consistently.
- Observed result: Calculated difference is $161.76/month, approximately 28.79%.
- Supports: C1, C2; C3 and C4 require separate ownership/inventory evidence.
- Limitations: Excluded services, usage variation, and taxes mean this is not a final bill.

### E12 — Local Ingress Forwarding Test
- Candidate source: Terminal output from `minikube service ingress-nginx-controller -n ingress-nginx --url` and subsequent curl tests.
- Type: Runtime connectivity test
- Date and source revision: Tested 2026-10-14; HEAD `83da3cd68f6414350ba79f53f8b85ecce41b13de`; working tree had uncommitted changes.
- Expected result: Requests using host `cloudnotes.local` reach the application through local ingress forwarding.
- Observed result: `curl` to `http://127.0.0.1:33179/health` and `/ready`, with `Host: cloudnotes.local`, returned HTTP 200 and the expected JSON status responses.
- Supports: R3 and R4 only as limited evidence of current routing and endpoint availability.
- Limitations: This used a local Minikube forwarding URL and does not prove production network availability, sustained availability, or failure recovery.

## Missing Evidence and Follow-up

- Locate and verify each candidate artifact before linking it.
- Add the repository-relative path or a stable, accessible URL for every evidence ID used.
- Record actual test dates, source revisions, expected results, observed results, and limitations.
- Mark missing evidence as UNVERIFIED in the checklist.
- Do not include credentials, tokens, raw kubeconfigs, private user records, or database dumps.
- Do not claim database restoration or successful alert delivery without direct evidence.

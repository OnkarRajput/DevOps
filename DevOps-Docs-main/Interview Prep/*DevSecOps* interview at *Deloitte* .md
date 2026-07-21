This video details 27 technical questions asked during a *DevSecOps* interview at *Deloitte* for a 3.5 years of experience role. Here is a summary of the questions and the candidate's responses:

**1. Self-introduction:** 
Focus on technical expertise (3.5 years experience), hands-on work with CI/CD, containerization, security tool integration, and banking/e-governance domain experience (2:48-5:12).

**2. Pipeline stages:** 
Explained as a linear flow: Source code PR -> Build/Unit tests -> *SonarQube* (Quality Gates) -> *Fortify* (SAST) -> *Dependency-Check/Snyk* (SCA) -> Docker build -> *SBOM* generation -> Deployment (*Helm/EKS*) -> *Zap* (DAST) -> Monitoring (*Prometheus/Grafana*) (5:32-10:57).

**3. Security tools integrated:** *
Fortify, SonarQube, Dependency-Check, Snyk, Zap, Trivy, Twistlock* (10:57-11:03).

**4. Identified vulnerabilities:** 
OSS dependency CVEs, secrets in repos, *SQL injection*, *XSS*, and cryptographic failures (11:03-12:27).

**5. Remediation vs. Recommendation:** 
Both are done; includes creating *Jira* tickets, recommending library upgrades, or applying compensating controls like *WAF* rules (12:27-14:56).

**6. Validating False/True Positives:** 
Reproduce locally, cross-verify with other tools, and document justification for suppression in the scanner (14:56-16:53).

**7. Securing Jenkins pipeline:** 
Use pipeline-as-code, credential management via *AWS Secrets Manager*, masking secrets, ephemeral IAM roles, and build isolation (16:53-19:12).

**8. Handling exposed credentials in logs:** 
Immediate rotation of secrets, using secret bindings, and filtering sensitive output patterns (19:12-21:16).

**9. Secrets vs. Environment Variables in Jenkins:** 
Secrets are encrypted at rest for sensitive data; environment variables are plain text for non-sensitive data (21:17-22:47).

**10. Deployment cluster:** Primarily *AWS EKS*; 
uses namespaces for isolation and *Helm* for deployment (22:47-24:49).

**11. Application deployment method:** Uses *Kubernetes* 
manifests (*YAML*) or *Helm* charts; images tagged dynamically in the pipeline (24:49-27:21).

**12. Dockerfile best practices:** 
Minimal base images (*Alpine/Distroless*), non-root users, multi-stage builds, pinning versions, and cleaning caches (27:21-29:33).

**13. Security in Docker phases:** 
Build-time (signing/scanning), Push-time (private registry/RBAC), and Runtime (read-only FS/resource limits) (29:33-32:32).

**14. Vulnerabilities in Docker:** 
Base image vulnerabilities, running as root, unnecessary packages, hard-coded secrets, and missing health checks (32:32-34:20).

**15. Remediation provided to developers:** 
Suggesting *parameterized queries* for *SQLi*, library upgrades for dependencies, and *multi-stage builds* for Docker (34:20-35:24).

**16. What is SBOM?:** 
Software Bill of Materials; an inventory of software components, versions, and licenses (35:24-36:46).

**17. Difference between SCA and SBOM:** 
*SCA* focuses on detection/remediation of vulnerabilities; *SBOM* focuses on inventory and traceability (36:46-38:35).

**18. Difference between DevOps and DevSecOps:** 
*DevOps* prioritizes speed/delivery; *DevSecOps* integrates security as a shared responsibility throughout the SDLC (38:35-39:50).

**19. Duration in DevSecOps role:** 
Discusses 3 years of hands-on experience, including pipeline design, triage, and incident response (39:50-41:40).

**20. DAST tool used and integration:** 
Primarily *Zap* (headless mode) integrated post-deployment to staging endpoints (41:40-44:26).

**21. Vulnerability triage process:** 
Validate findings (reproduction), classify severity, map business impact, and document suppression (44:26-46:27).

**22. Integrating SAST/DAST into the pipeline:** 
Use *Fortify SSC* for *SAST* results and *API-based* triggering for *DAST* (46:27-48:33).

**23. Scenario: Application team reporting scan failure:** 
Verify tool status, scan configurations, and access; escalate to security admin or network team if necessary (48:33-50:45).

**24. SSRF (Server-Side Request Forgery):** 
Identified via *Zap*; mitigated by enforcing allow-lists and URL validation (50:45-52:17).

**25. Troubleshooting scan failures:** 
Verify logs, network connectivity, authentication, and environment accessibility (52:17-54:19).

**26. Common causes of pipeline failure:** 
Tool outages, credential expiration, dependency resolution issues, or storage limits (54:19-55:12).

**27. Candidate questions:** 
Focused on project scope and team structure (55:12-55:47).
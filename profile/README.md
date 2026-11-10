# Petclinic Cloud-Native DevSecOps Lab

Dự án thực hành **Cloud-Native DevSecOps** chuẩn doanh nghiệp trên hạ tầng Local (VMware Workstation + Vagrant + Ansible + Floci + K3s + Cilium + ArgoCD).

## Cấu trúc Repositories

| Repository | Vai trò | Công nghệ chính |
|---|---|---|
| [`petclinic-infra`](https://github.com/petclinic-devsecops-lab/petclinic-infra) | Hạ tầng (IaC), Local Cloud, Quyết định kiến trúc (ADR) & Threat Model | Vagrant, Ansible (`ansible_local`), Floci (`wal` + IAM Enforcement), K3s, Cilium + Hubble, Terraform 1.14, Checkov |
| [`petclinic-app`](https://github.com/petclinic-devsecops-lab/petclinic-app) | Mã nguồn Microservices & Shift-Left CI Supply Chain | Spring Boot 4.0.1, Java 21, Distroless, gitleaks, Semgrep CE, Trivy (SCA/Image/SBOM SPDX), Cosign Keyless (OIDC), Renovate |
| [`petclinic-gitops`](https://github.com/petclinic-devsecops-lab/petclinic-gitops) | GitOps CD, Helm Charts, Chính sách Zero-Trust, DAST & Sao lưu | ArgoCD (App-of-Apps), Traefik (Gateway API), cert-manager, ESO, Kyverno, PSA `restricted`, Cilium NetworkPolicy, OWASP ZAP, `age` Backup |

## Mục tiêu Bảo mật (Security Objectives)

- **SO-01:** Không có secret trong Git (`gitleaks` pass, secret chỉ nằm trong Floci Secrets Manager).
- **SO-02:** Chỉ container image đã được CI ký bằng Cosign keyless (OIDC) mới được phép khởi chạy trên cụm K3s (Kyverno `verifyImages`).
- **SO-03:** Pod chạy với đặc quyền tối thiểu (Pod Security Admission `restricted`, non-root, read-only root filesystem, drop `ALL` capabilities).
- **SO-04:** Mạng Zero-Trust với Cilium NetworkPolicy mặc định chặn cả ingress/egress, mở theo từng cặp kết nối và xác minh qua Hubble.
- **SO-05:** Không có lỗ hổng CVE mức `CRITICAL`/`HIGH` đã có bản vá trên thư viện và image; ngoại lệ phải có VEX và thời hạn.
- **SO-06:** IaC và manifest Kubernetes không có lỗi cấu hình mức `HIGH`/`CRITICAL` (`Checkov`, `kubeconform`, `kyverno test`).
- **SO-07:** Sao lưu MySQL định kỳ mã hóa bất đối xứng bằng `age` đẩy lên Floci S3 và diễn tập khôi phục (Restore Drill) thành công.
- **SO-08:** Đặc quyền tối thiểu trên Cloud với Floci IAM Enforcement (`FLOCI_SERVICES_IAM_ENFORCEMENT_ENABLED=true`).

Xem chi tiết thiết kế tại [`petclinic-infra/docs/baseline.md`](https://github.com/petclinic-devsecops-lab/petclinic-infra/blob/main/docs/baseline.md).

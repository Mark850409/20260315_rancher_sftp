# SFTP GitOps / ArgoCD

## 部署前必要步驟：建立 Secret

**`sftp-key` Secret 不應放在 Git 中**（含 SSH 公鑰等敏感資料），需在 cluster 內另行建立。

ArgoCD 只會同步本目錄的 Deployment、Service、PVC。Secret 請用以下方式之一處理：

1. **手動建立**（一次性）  
   在 `sftp` namespace 建立 Secret，key 為 `authorized_keys`，值為 SSH 公鑰內容（與 SFTP 登入用金鑰對應）。

2. **Sealed Secrets / SOPS**  
   若要用 Git 管理，請用 [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) 或 [SOPS](https://github.com/getsops/sops) 加密後再放入 repo，並由 ArgoCD 搭配對應 controller 還原。

3. **External Secrets Operator**  
   從 Vault、AWS Secrets Manager 等拉取 `authorized_keys` 並寫入 `sftp-key` Secret。

## 敏感資訊提醒

- `sftp.yaml` 中 `args` 的 `zane:zane@1348:1001` 為 SFTP 使用者與密碼（或 uid:gid），若需嚴格避免進版控，可改為從 Secret 或環境變數注入。

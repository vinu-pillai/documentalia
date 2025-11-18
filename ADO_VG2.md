
# 🔐 Azure DevOps Variable Group Access Management (Enterprise Best Practices)

## 🧱 1. Foundational Principles for Access Management

| Principle | Description |
|----------|-------------|
| **Least Privilege** | Give only the minimum required access to perform a task (edit vs. use). |
| **Separation of Duties (SoD)** | Developers can use secrets but should not manage or see secret values, especially in Prod. |
| **Environment Segregation** | Different access levels for Dev, QA, Staging, Prod variable groups. |
| **Central Governance** | Control and consistency of sensitive groups (e.g., production secrets) should be enforced centrally. |
| **Role-Based Access Control (RBAC)** | Use Azure DevOps security groups and Azure AD groups mapped to specific roles. |

---

## 🔐 2. Key Roles and Responsibilities

| Role | Responsibility | Access Level |
|------|----------------|--------------|
| **Platform Admin / DevOps Engineer** | Owns security model, enforces access boundaries, manages prod groups | Admin (global or scoped) |
| **Service Owner / Team Lead** | Manages environment-specific configs (non-prod), reviews variable needs | Contributor / Admin (scoped) |
| **Developer / Engineer** | Can reference (use) variable groups in pipelines but not modify | Reader / User |
| **QA Engineer** | May use variable groups for test environments; no edit access | User |
| **Release Manager** | May need edit rights for release-specific groups | Admin (on select groups) |

---

## 🧩 3. How to Manage Access — Best Practice Structure

### ✅ A. Project-Specific Access for Dev/Test Groups

- Delegate ownership of Dev and QA groups to each project’s DevOps engineer or team lead.
- Use naming like `VG-Dev-App1`, `VG-QA-App1`.
- Grant:
  - **Edit (Admin)** rights to DevOps/Leads
  - **Use** permission to CI/CD pipelines
  - **Read/User** to developers and QA

### ✅ B. Centralized Control for Production Variable Groups

- Manage prod variable groups centrally by DevSecOps or Platform Engineering.
- Use naming like `VG-Prod-App1`, `VG-Prod-Common-Secrets`.
- Only pipelines with manual approvals should use these groups.

### ✅ C. Use Azure AD Groups for Access Management

- Define Azure AD groups like:
  - `AAD-DevOps-Admins`, `AAD-App1-Dev`, `AAD-Prod-Security`
- Map these to Azure DevOps roles.

### ✅ D. Use Folder Structure to Organize Groups (Optional)

- Use path-based naming like:
  - `/App1/Dev/VG-App1-Dev`
  - `/App1/Prod/VG-App1-Prod`

---

## ⚙️ 4. Access Recommendation Matrix

| Variable Group Type | Managed By | Editable By | Usable By | Notes |
|---------------------|------------|-------------|-----------|-------|
| `VG-Dev-*` | Project Team | DevOps Lead / Sr Dev | Developers | Freely editable |
| `VG-QA-*` | Project Team | DevOps / QA Lead | QA, Devs | QA automation |
| `VG-Stg-*` | Central + Team Shared | DevOps Lead | Developers | Approval needed |
| `VG-Prod-*` | Platform/DevSecOps | DevSecOps only | Pipelines only | Most restricted |
| `VG-Common-*` | Platform Team | Admins only | All teams | Shared read-only config |

---

## 🔒 5. Enforcing Governance Controls

- 🔐 Use approvals/checks on production groups
- ✅ Link prod groups to Azure Key Vault
- 📜 Enable audit logs for changes
- 📊 Review access quarterly
- 🧪 Test group usage via pipeline previews

---

## 🛠️ 6. Automation (Optional)

```bash
az pipelines variable-group create   --name "VG-Prod-App1"   --variables ENV=prod API_URL=https://prod.api
```

---

## 🧠 7. Key Takeaways

| Best Practice | Justification |
|---------------|---------------|
| Centralize prod secrets, delegate dev/test configs | Separation of risk and velocity |
| Use AAD groups, not individual users | Scalable and auditable |
| Pipeline-specific use permissions | Avoid unintended access |
| Mask all secrets and use Key Vault | Secure, compliant, and maintainable |
| Audit and review quarterly | Ensures continued alignment and security |

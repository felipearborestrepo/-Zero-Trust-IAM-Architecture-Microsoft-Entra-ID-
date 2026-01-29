# 🛡️ IAM Access Control Architecture (Microsoft Entra ID)
**Focus:** Conditional Access • Authentication Strengths • Zero Trust Sessions • IAM Evidence Pack  
**Platform:** Microsoft Entra ID (Azure AD)  
**Level:** Intermediate IAM / SOC-ready lab

![Image 5](https://github.com/user-attachments/assets/016daa1c-f588-4e37-89fc-e13255536677)

---

## 📌 Project Summary
This project documents a **real-world IAM access control architecture** built in Microsoft Entra ID.  
The goal is to segment identities, enforce **modern phishing-resistant authentication**, apply **enterprise Conditional Access policies**, and produce an **evidence pack** (screenshots + sign-in logs) you can use for interviews.

---

## 🎯 What You’ll Build
✅ Segmented users + security groups (standard, privileged, external)  
✅ Authentication methods enabled (Authenticator, FIDO2, TAP)  
✅ **Authentication Strength** profile: *Phishing-Resistant-Only*  
✅ 4 Conditional Access policies (users, admins, high-risk, guests)  
✅ Session & device trust enforcement (sign-in frequency, persistent browser, compliant device)  
✅ Validation using sign-in logs (CA applied, auth method enforced, session controls triggered)  
✅ Final **IAM Evidence Pack** (screenshots that prove everything)

---

# 🧱 PHASE 0 — Access Architecture Design (Foundation)
**Purpose:** Segment identities *before* protecting them (real enterprise approach).

## ✅ Step 0.1 — Create Users
**Path:** Entra admin center → **Identity** → **Users** → **All users** → **New user**

Create:
- `Felipe-User` (standard user)
- `Felipe-Admin` (admin user)
- `Felipe-Guest` (guest/external user)

📸 **Screenshot(s) to capture**

![1](https://github.com/user-attachments/assets/3a250716-411e-401a-97ed-f015a943f4a1)

---

## ✅ Step 0.2 — Create Groups
**Path:** Entra admin center → **Identity** → **Groups** → **New group**

Create Security groups:
- `IAM-Standard-Users`
- `IAM-Privileged-Admins`
- `IAM-External-Users`

📸 **Screenshot(s) to capture**

![Image 1-29-26 at 14 47](https://github.com/user-attachments/assets/3835a64f-e62e-4867-8ade-bc78c0bf547e)

---

## ✅ Step 0.3 — Assign Users to Groups
**Path:** Entra admin center → **Groups** → select group → **Members** → **Add members**

Assign:
- `Felipe-User` → `IAM-Standard-Users`
- `Felipe-Admin` → `IAM-Privileged-Admins`
- `Felipe-Guest` → `IAM-External-Users`

---

# 🔐 PHASE 1 — Authentication Strengths (Modern Auth Core)
**Purpose:** Define “acceptable MFA” (phishing-resistant) and enforce it via Conditional Access.

## ✅ Step 1.1 — Enable Authentication Methods
**Path:** Entra admin center → **Protection** → **Authentication methods**

Enable (at minimum):
- **Microsoft Authenticator**
- **FIDO2 Security Key**
- **Temporary Access Pass**

> Tip: If you can’t enable for “All users”, target your test groups first.

📸 **Screenshot(s) to capture**

![2](https://github.com/user-attachments/assets/da3c1cd9-e486-48fd-826f-43dd3325ae68)

---

## ✅ Step 1.2 — Create Authentication Strength: Phishing-Resistant-Only
**Path:** Entra admin center → **Protection** → **Authentication methods** → **Authentication strengths** → **New authentication strength**

Create:
- **Name:** `Phishing-Resistant-Only`
- **Include:**
  - ✔ FIDO2 security keys
  - ✔ Passkeys (if available in your tenant/device)
  - ✔ Certificate-based authentication (optional)

📸 **Screenshot(s) to capture**

![4](https://github.com/user-attachments/assets/28abbd84-e92f-4de0-b488-4299f4666bd9)

---

# 🧠 PHASE 2 — Conditional Access Architecture (4 Enterprise Policies)
**Purpose:** Control access by role, risk, and user type.

**Path (for all CA policies):**  
Entra admin center → **Protection** → **Conditional Access** → **Policies** → **New policy**

---

## ✅ CA #1 — Baseline User Protection (Require MFA)
**Name:** `CA – Users – Require MFA`  
**Users:** `IAM-Standard-Users`  
**Cloud apps:** **All cloud apps**  
**Grant:** ✔ Require multifactor authentication  
**Enable policy:** On (or Report-only if you’re testing safely)

📸 Screenshot:

![3](https://github.com/user-attachments/assets/6bd3c969-11a3-4ebc-9b2e-9462ee3bd403)

---

## ✅ CA #2 — Phishing-Resistant for Admins (Authentication Strength)
**Name:** `CA – Admins – Phishing Resistant Only`  
**Users:** `IAM-Privileged-Admins`  
**Cloud apps:** **All cloud apps**  
**Grant:** ✔ Require authentication strength → `Phishing-Resistant-Only`

📸 Screenshot:

![4](https://github.com/user-attachments/assets/738e3d1f-4e09-4bc3-97cc-71f53d42d00b)

---

## ✅ CA #3 — High Risk Lockdown
**Name:** `CA – High Risk – Block or Secure`  
**Users:** **All users**  
**Conditions:** ✔ Sign-in risk = **High**  
**Grant:**  
- Option A (simple & strict): ✔ **Block access**  
- Option B (if available): ✔ Require password change + MFA

📸 Screenshot:

![9](https://github.com/user-attachments/assets/6dcefe2f-86a7-4917-8ed4-8f3a40962906)

![10](https://github.com/user-attachments/assets/3d7e3799-7dd6-4b2b-8cf3-1990f03b8065)

> Note: Sign-in risk requires Entra ID P2 / Identity Protection features.

---

## ✅ CA #4 — External User Control (Guests)
**Name:** `CA – Guests – Limited Access`  
**Users:** **Guest or external users** (or your group `IAM-External-Users`)  
**Cloud apps:** Select apps **or** All cloud apps  
**Grant:** ✔ Require MFA  
**Session (recommended):**
- ✔ Sign-in frequency
- ✔ Persistent browser session = Never

📸 Screenshot:

![12](https://github.com/user-attachments/assets/d098a118-0b95-45b1-8ffe-51d2447bb0dd)

![13](https://github.com/user-attachments/assets/2c7b44f5-112c-4cee-9713-121b654f614f)

![14](https://github.com/user-attachments/assets/8f352644-7d2a-444c-9e3a-43adc29d21fc)

![15](https://github.com/user-attachments/assets/15c12fb0-5f3b-422c-b143-2a001667cb67)

---

# 🧱 PHASE 3 — Session & Device Trust (Zero Trust Enforcement)
**Purpose:** Enforce short sessions, prevent persistent login, and require trusted devices (optional).

## ✅ Step 3.1 — Configure Session Controls in ONE CA Policy
Pick one policy (commonly the Admin policy or Users policy).

**Session controls:**
- ✔ **Sign-in frequency:** 1 hour
- ✔ **Persistent browser session:** Never
- ✔ **App enforced restrictions:** (if available)

📸 Screenshot(s) to capture:

![16](https://github.com/user-attachments/assets/99c65047-90eb-4a32-b034-02def52334df)

---

# 🔎 PHASE 4 — Validation & Attack Simulation (Proof via Logs)
**Purpose:** Prove policies actually apply (this is what recruiters want).

## ✅ Step 4.1 — Test Logins
Run these tests (incognito recommended):

1) **Normal user login**
- Sign in as `Felipe-User` to a cloud app (e.g., `portal.azure.com` or `myapps.microsoft.com`)

![17](https://github.com/user-attachments/assets/1264f548-52a7-4b75-8ea4-1b77c8c65f6e)

![18](https://github.com/user-attachments/assets/a41c3fc1-6b52-42cc-8463-addbe354f9e5)

2) **Admin login**
- Sign in as `Felipe-Admin`
- Confirm phishing-resistant enforcement (FIDO2/passkey if configured)

![19](https://github.com/user-attachments/assets/a26b770a-cfd4-47e3-bd54-6823368dc72a)

![20](https://github.com/user-attachments/assets/272fa6f2-00d7-44cc-ae51-17d9c9fd1176)

![21](https://github.com/user-attachments/assets/bca9d041-576b-4c27-b43f-513180aef54f)

3) **Guest login**
- Sign in as `Felipe-Guest` to allowed apps
- Confirm session controls trigger

![22](https://github.com/user-attachments/assets/8d1dd746-9db2-4ff1-bbb5-be742f9fdf2b)

---

## ✅ Step 4.2 — Collect Sign-in Logs
**Path:** Entra admin center → **Monitoring** → **Sign-in logs**

For each test sign-in, open the event and capture:
- **Conditional Access** tab → Which policy applied
- **Authentication details** → Which method used
- **Status** → Success/Failure + reason

📸 Screenshot(s) to capture:

![23](https://github.com/user-attachments/assets/e016b22e-90b9-4a0a-ad68-96a87648fe51)

![24](https://github.com/user-attachments/assets/121f7a88-9d7e-485f-aa11-0e991a5e5408)

![25](https://github.com/user-attachments/assets/7133c98e-a53b-489a-9838-d810c7a48d6d)

![26](https://github.com/user-attachments/assets/bb780c1e-583c-45cd-818e-658bfea5a934)

![27](https://github.com/user-attachments/assets/bf59807f-1da9-4680-9271-2295392a3404)

![28](https://github.com/user-attachments/assets/f43d5f71-61ff-4cec-ba7b-85aeb598c6f2)

![29](https://github.com/user-attachments/assets/6b40370d-6ef7-431d-85c3-9f211478ded6)

![30](https://github.com/user-attachments/assets/d59c8621-4e80-41b4-8186-15c463d214bf)

![31](https://github.com/user-attachments/assets/bc246183-dc37-43b3-89a8-a6934edac60d)



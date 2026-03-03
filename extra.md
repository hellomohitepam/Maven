
| File             | Responsibility                        |
| ---------------- | ------------------------------------- |
| `pom.xml`        | What this project needs                     |
| `settings.xml`   | How Maven behaves on THIS machine (Maven Configuration File)   |
| `.m2/repository` | Where dependencies are stored locally |

# You basically have two possible Maven installations:
## 1. Standalone Maven (that you downloaded)

### 🔹 Used When:
- You run mvn clean install in terminal / command prompt
- CI/CD pipelines (Jenkins, GitHub Actions, etc.)
- Other IDEs (Eclipse, VS Code)
- System-wide builds

### 🔹 Advantages:
- You control the exact Maven version
- Works everywhere on your machine
- Required for build servers

## 2. Bundled Maven inside IntelliJ IDEA
### 🔹 Used When:
- You click Build Project
- You reload Maven project
- You run Maven goals from IntelliJ Maven panel

### 🔹 Advantages:
- No setup required
- Works immediately
- Managed by IntelliJ

> In IntelliJ, you can change to your installed Maven:

### In real projects:
- Company will specify a Maven version.
- You should configure IntelliJ to use the same Maven version as your system.
- Even better: Modern projects often use Maven Wrapper (mvnw) — which ensures everyone uses the exact same Maven version automatically.

### Always make sure:
- IntelliJ Maven version
- Terminal Maven version (mvn -v)
- CI/CD Maven version
> are aligned.

## There are two possible locations for settings.xml.
- 1️⃣ User-Level settings.xml `C:\Users\YourUsername\.m2\settings.xml`
  > 👉 This file overrides global settings its Personal config.
- 2️⃣ Global settings.xml `C:\Program Files\Apache\Maven\conf\settings.xml`
  > 👉 Applies to all users on the machine

> Maven does NOT create settings.xml by default. You manually create it or Your company gave you one or for configuring private repositories & configuring proxy/mirrors















  

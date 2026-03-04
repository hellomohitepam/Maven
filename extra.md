


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

| File             | Responsibility                        |
| ---------------- | ------------------------------------- |
| `pom.xml`        | What this project needs               |
| `settings.xml`   | How Maven behaves on THIS machine (Maven Configuration File)   |
| `.m2/repository` | Where dependencies are stored locally |

# 🔧 What “Behavior” Actually Means in setting.xml
> Behavior = things like:
- 🌐 Where dependencies are downloaded from
- 🔐 How authentication works
- 🌍 Whether a proxy is used
- 📦 Where artifacts are stored locally
- 🎛 Which profiles are auto-activated
- 🔁 How remote repositories are mirrored
> These apply to all projects built on that machine.

## There are two possible locations for settings.xml.
- 1️⃣ User-Level settings.xml `C:\Users\YourUsername\.m2\settings.xml`
  > 👉 This file overrides global settings its Personal config.
- 2️⃣ Global settings.xml `C:\Program Files\Apache\Maven\conf\settings.xml`
  > 👉 Applies to all users on the machine

> 📌 Maven does NOT create settings.xml by default. You manually create it or Your company gave you one or for configuring private repositories & configuring proxy/mirrors
> 📌 settings.xml has higher control power than pom.xml Because: settings.xml is machine-level and pom.xml is project-level

## Normal flow of pom.xml & setting.xml
- pom.xml defines repo
- settings.xml provides authentication

### ⚠ What Happens If Same Profile ID Exists in Both? -> Maven merges them.

## Use pom.xml profile when:
> 📦 Shared with the project (committed to Git)
- It changes project behavior
- Team needs same behavior
- It should be version-controlled


## Use settings.xml profile when:
> 🔐 Not committed to project
- It contains secrets
- It configures repositories
- It is environment-specific
- It is machine-specific


- An artifact is any file that Maven consumes, manages, or produces — most commonly a JAR, WAR, or POM file. It is the output of a build or a dependency pulled from a repository.

- GroupId identifies the organization or project family — it's like a namespace. It's broad and shared across multiple modules.
- ArtifactId identifies the specific module or component within that group — it's the exact "thing" being built.

```
Compile phase  →  "Does this class EXIST?"
                   javac checks JAR → yes/no
                   just VERIFICATION

Testing phase  →  "Give me the actual CLASS to EXECUTE"
                   JVM finds JAR → loads .class file
                   actual LOADING & EXECUTION
```

# 📦 Maven Dependency Scopes vs Build Lifecycle

| Scope        | Compile Phase | Test Phase | Runtime Phase | Packaged in Artifact | Description                                               |
| ------------ | ------------- | ---------- | ------------- | -------------------- | --------------------------------------------------------- |
| **compile**  | ✅ Yes         | ✅ Yes      | ✅ Yes         | ✅ Yes                | Default scope. Available everywhere.                      |
| **provided** | ✅ Yes         | ✅ Yes      | ❌ No          | ❌ No                 | Provided by the runtime container (e.g., server).         |
| **runtime**  | ❌ No          | ✅ Yes      | ✅ Yes         | ✅ Yes                | Needed only when the program runs.                        |
| **test**     | ❌ No          | ✅ Yes      | ❌ No          | ❌ No                 | Used only for testing (JUnit, Mockito).                   |
| **system**   | ✅ Yes         | ✅ Yes      | ❌ No          | ❌ No                 | Like `provided` but dependency is from local system path. |

| Dependency   | Typical Scope |
| ------------ | ------------- |
| Spring Core  | `compile`     |
| Servlet API  | `provided`    |
| MySQL Driver | `runtime`     |
| JUnit        | `test`        |
| Local JAR    | `system`      |

# 🧠 Simple Way to Remember
| Scope    | Meaning                  |
| -------- | ------------------------ |
| compile  | Needed to build and run  |
| provided | Server will provide it   |
| runtime  | Needed only when running |
| test     | Needed only for testing  |
| system   | Local jar manually added |

# Maven default build lifecycle
```
Your .java files
        ↓
Task 1: VALIDATE
        → Is pom.xml correct?
        → Are all required fields present?
        → Is project structure correct?
        ↓
Task 2: COMPILE
        → Run javac on your .java files
        → Convert to .class files
        → Check for syntax errors
        ↓
Task 3: TEST
        → Run all your JUnit test classes
        → Check if all tests pass
        → Fail build if any test fails
        ↓
Task 4: PACKAGE
        → Take all .class files
        → Bundle into JAR or WAR file
        → Put in target/ folder
        ↓
Task 5: VERIFY
        → Run integration tests
        → Check package is valid
        ↓
Task 6: INSTALL
        → Copy JAR to your local
          ~/.m2/repository
        → Other local projects can use it
        ↓
Task 7: DEPLOY
        → Upload JAR to remote repository
        → Other teams can download it

Final Output: my-app-1.0.0.jar ✅

```


## Why Maven Does not run next phases if maven test phase fail?
```
If your code is FAILING tests
→ your code has BUGS
→ why create a broken JAR?
→ why deploy broken code to server?
→ better to STOP and fix bugs first!
```

## Maven generate the report
> It is generated by Surefire Plugin which Maven uses internally:
```
your-project/
└── target/
    └── surefire-reports/        ← Maven creates this folder
        ├── MyServiceTest.txt    ← human readable report
        └── MyServiceTest.xml    ← machine readable report
```

# Clean lifecycle 
- ❌ Does NOT delete from ~/.m2 local repository
- ❌ Does NOT delete from remote repository
- ✅ ONLY deletes target/ folder

## Why Only target/ Folder?
target/ folder contains:
→ compiled .class files
→ generated JAR/WAR
→ test reports
→ temporary build files

## These are GENERATED files
→ can be regenerated anytime by running mvn install
→ safe to delete and rebuild fresh

# pre-process & post-process
> The word "processes" here simply means any custom task/script you want to run before or after cleaning.

## pre-process
```
Examples:
→ Take backup of test reports before they get deleted
→ Stop a running server that is using files inside target/
→ Send notification to team "cleanup is starting"
→ Save logs before deletion
```

## post-process
```
Examples:
→ Notify CI/CD pipeline "cleanup done, start fresh build"
→ Restart server that was stopped
→ Create empty folders needed for next build
→ Log cleanup completion time
```

# site lifecycle
## Maven can automatically generate a website for your project containing:
```
generated-site/
├── index.html              ← project home page
├── dependencies.html       ← all dependencies listed
├── team.html               ← developer team info
├── license.html            ← license info
├── surefire-report.html    ← test results
└── checkstyle.html         ← code quality report
```

# Which Processes Before and After?

### Pre-site — Before generating site
```
Examples:
→ Collect test reports first before generating site
→ Generate code coverage data Run code quality checks so results are ready for site
→ Prepare templates and themes
```

### Post-site — After generating site
```
Examples:
→ Compress generated HTML files
→ Add custom pages to generated site
→ Validate generated HTML is correct
→ Prepare site for deployment
→ Zip site files for transfer
```
### Site-deploy — Deploy to web server
```
Generated HTML files are UPLOADED to:
→ Apache web server
→ GitHub Pages
→ Company internal server
→ Any web hosting
```







  

# email-jenkins-project3

Excellent! Let’s now create the full **Project 3: Jenkins Email Notification** pipeline, where Jenkins sends an email on **success or failure** of the build.

---

# ✅ **Project 3: Jenkins Email Notification with Jenkinsfile**

### 🎯 Goal:

Send an automatic email when the Jenkins build **succeeds** or **fails**, using `emailext` in a declarative `Jenkinsfile`.

---

## ✅ Requirements

### 🔧 1. Jenkins Plugins

* Email Extension Plugin

Install via:
**Manage Jenkins → Plugin Manager → Available →** `Email Extension Plugin`

---

### 🔧 2. Configure SMTP in Jenkins

Go to:
**Manage Jenkins → Configure System → Extended E-mail Notification**

Set:

* **SMTP Server**: `smtp.gmail.com`
* **Use SMTP Authentication**: ✅ Yes
* **Username**: `your-email@gmail.com`
* **Password**: *(use an App Password)*
* **SMTP Port**: `465`
* **Use SSL**: ✅ Yes
* **Default Content Type**: `text/html`
* **Default Recipients**: `devteam@example.com` *(or your test email)*
* **Default Subject**: `$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!`

Test with "Test configuration" → Make sure it says **Success**.

---

## 📁 Project Folder Structure

```
email-notify-sample/
├── Jenkinsfile
└── (Optional build files if integrating with Maven/Docker/etc)
```

---

## 📜 `Jenkinsfile` (Declarative Pipeline with Email)

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven3' // Optional if you have a build step
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-user>/email-notify-sample.git'
            }
        }

        stage('Dummy Build') {
            steps {
                echo 'Simulating build...'
                // sh 'exit 1' // Uncomment this to test failure email
            }
        }
    }

    post {
        success {
            emailext (
                subject: "✅ SUCCESS: Job ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p><b>Job:</b> ${env.JOB_NAME}</p>
                    <p><b>Build:</b> #${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> SUCCESS ✅</p>
                    <p>Check console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                """,
                to: 'recipient@example.com'
            )
        }

        failure {
            emailext (
                subject: "❌ FAILURE: Job ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p><b>Job:</b> ${env.JOB_NAME}</p>
                    <p><b>Build:</b> #${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> FAILED ❌</p>
                    <p>Check console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                """,
                to: 'recipient@example.com'
            )
        }
    }
}
```

---

### 💡 You Can Test by:

* Forcing a failure (`sh 'exit 1'`) and checking your email
* Then removing it to confirm success emails

---

## 🔐 Gmail App Password (if using Gmail SMTP)

1. Enable 2FA on your Google Account
2. Go to [https://myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
3. Generate a new App Password for “Mail”
4. Use that in Jenkins' SMTP password field

---

                                                     


# 🌟 Java Sample CI/CD Project 🌟

A showcase of a CI/CD pipeline for a Java application using **Jenkins**, **Docker**, and **SonarQube**. This project walks through a streamlined process to build, test, and deploy a Java application using modern DevOps practices.

---

## 📂 Project Structure

- **Dockerfile** - Contains instructions for building the application image.
- **How to Install Sonarqube in Ubuntu Linux.md** - Guide to set up SonarQube on Ubuntu.
- **Jenkinsfile** - CI/CD pipeline configuration for Jenkins.
- **pom.xml** - Maven configuration file.
- **server/** - Contains backend service code.
- **webapp/** - Contains frontend web application code.

---

## 📋 Prerequisites

Before running the pipeline, make sure the following tools are installed:

- **Jenkins** - To automate CI/CD tasks.
- **Docker** - Required for building and pushing Docker images.
- **SonarQube** - For analyzing code quality.
- **Maven** - Used for building the Java project.

---

## 🏁 Getting Started

### 1. Clone the Repository

Clone the repository to your local machine using:

```bash
git clone https://github.com/SaraIravani/Java-sample-ci.git
cd Java-sample-ci
```

### 2. Configure Jenkins

- **Plugins**: Install essential Jenkins plugins, including _Pipeline_, _Docker_, and _SonarQube_.
- **Credentials**:
  - **GitHub**: Create a credential for accessing your GitHub repository.
  - **SonarQube**: Add a token for SonarQube analysis.
  - **DockerHub**: Set up Docker credentials for pushing images.

### 3. Update the Jenkinsfile

The Jenkinsfile defines the stages for building, testing, and deploying the application. Key pipeline stages include:

- **SCM Checkout** - Clones the repository.
- **Build** - Compiles the application using Maven.
- **Test** - Runs unit tests.
- **SonarQube Analysis** - Performs code quality analysis.
- **Docker Image Build & Push** - Builds and pushes the Docker image to DockerHub.
- **CD Pipeline Trigger** - Initiates the continuous deployment pipeline.

### 4. Build & Deploy

1. Create a new pipeline job in Jenkins and point it to your **Jenkinsfile**.
2. Run the job to execute the complete CI/CD pipeline.

---

## 📚 Additional Resources

Here are some useful links and resources:

- [SonarQube Setup on Ubuntu](How%20to%20Install%20Sonarqube%20in%20Ubuntu%20Linux.md) - Step-by-step setup guide.
- [Docker Documentation](https://docs.docker.com/) - Official Docker documentation.
- [Jenkins Documentation](https://www.jenkins.io/doc/) - Official Jenkins documentation.
- [SonarQube Documentation](https://docs.sonarqube.org/latest/) - Official SonarQube documentation.

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

⭐ If you find this project helpful, please consider starring it to support further development!

---

*Happy Building! 🎉*

Java-app
Here is a FULL setup (separate for a, b, c, d, e) with Maven + GitHub + Jenkins + Docker 👇
________________________________________
✅ 1. Maven Structure (Same for ALL programs)
Create 5 separate projects:
addition-app/
evenodd-app/
multiplication-app/
factorial-app/
prime-app/
Inside each:
project-name/
 ├── pom.xml
 └── src/
     └── main/
         └── java/
             └── App.java
________________________________________
✅ 2. Code for EACH APPLICATION
🔹 a) Addition App
App.java
public class App {
    public static void main(String[] args) {
        int a = 5, b = 3;
        System.out.println("Addition: " + (a + b));
    }
}
pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.add</groupId>
  <artifactId>addition-app</artifactId>
  <version>1.0</version>
</project>
________________________________________
🔹 b) EvenOdd App
App.java
public class App {
    public static void main(String[] args) {
        int n = 4;
        if(n % 2 == 0)
            System.out.println("Even");
        else
            System.out.println("Odd");
    }
}
pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.even</groupId>
  <artifactId>evenodd-app</artifactId>
  <version>1.0</version>
</project>
________________________________________
🔹 c) Multiplication App
App.java
public class App {
    public static void main(String[] args) {
        int a = 5, b = 3;
        System.out.println("Multiplication: " + (a * b));
    }
}
pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mul</groupId>
  <artifactId>multiplication-app</artifactId>
  <version>1.0</version>
</project>
________________________________________
🔹 d) Factorial App
App.java
public class App {
    public static void main(String[] args) {
        int n = 5, fact = 1;
        for(int i=1;i<=n;i++)
            fact *= i;
        System.out.println("Factorial: " + fact);
    }
}
pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.fact</groupId>
  <artifactId>factorial-app</artifactId>
  <version>1.0</version>
</project>
________________________________________
🔹 e) Prime App
App.java
public class App {
    public static void main(String[] args) {
        int n = 7, count = 0;
        for(int i=1;i<=n;i++){
            if(n % i == 0)
                count++;
        }
        if(count == 2)
            System.out.println("Prime");
        else
            System.out.println("Not Prime");
    }
}
pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.prime</groupId>
  <artifactId>prime-app</artifactId>
  <version>1.0</version>
</project>
________________________________________
✅ 3. GitHub Steps (Repeat for EACH project)
git init
git add .
git commit -m "Initial commit"
git remote add origin <repo-url>
git push -u origin main
Update:
git add .
git commit -m "Modified code"
git push
________________________________________
✅ 4. Dockerfile (Same for ALL)
FROM eclipse-temurin:11-jdk
COPY target/*.jar app.jar
CMD ["java", "-jar", "app.jar"]

Pipeline:
pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        DOCKER_IMAGE = "iambhara1h/java-app:latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/bhara1h30/exam.git'
            }
        }

        stage('Maven Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    bat '''
                    echo %PASS% | docker login -u %USER% --password-stdin
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }

        // ✅ Replaced Kubernetes with Local Deployment
        stage('Run Container') {
            steps {
                bat '''
                docker stop java-app || exit 0
                docker rm java-app || exit 0
                docker run -d --name java-app -p 8080:8080 %DOCKER_IMAGE%
                '''
            }
        }
    }

    post {
        success {
            echo 'Java CI/CD Pipeline Successful'
        }
        failure {
            echo 'Pipeline Failed'
        }
    }
}
Only docker

For Kubernetes
pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        DOCKER_IMAGE = "iambhara1h/java-app:latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/bhara1h30/exam.git'
            }
        }

        stage('Maven Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'iambhar1h',
                    passwordVariable: 'bharath@23'
                )]) {
                    bat '''
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                docker stop java-app || exit 0
                docker rm java-app || exit 0
                docker run -d -p 8080:8080 --name java-app %DOCKER_IMAGE%
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs.'
        }
    }
}
________________________________________
✅ 6. Final Jenkins Stage View
For EACH app you will see:
✔ Clone
✔ Build
✔ Docker Build
✔ Run
________________________________________
✅ 7. What You Achieved (Write in Record)
Maven project created
Code written in VS Code
Pushed to GitHub
Modified & committed
Jenkins pipeline configured
Docker container created
CI stages executed successfully
________________________________________
If you want next (very useful for exam):
👉 One-click combined project (all 5 in one repo)
👉 Viva questions + answers
👉 Diagram for CI/CD pipeline


Deployment code for Kubernetes
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: java-app
  template:
    metadata:
      labels:
        app: java-app
    spec:
      containers:
      - name: java-app
        image: iambhara1h/java-app:latest
        ports:
        - containerPort: 8080

For html :
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "iambhara1h/html-app:latest"
        KUBECONFIG = "C:\\ProgramData\\Jenkins\\.kube\\config"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/bhara1h30/assesment4.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    bat '''
                    docker login -u %USER% -p %PASS%
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                kubectl apply -f deployment.yaml --kubeconfig=%KUBECONFIG% --validate=false
                '''
            }
        }

        stage('Check Deployment') {
            steps {
                bat 'kubectl get pods --kubeconfig=%KUBECONFIG%'
                bat 'kubectl get services --kubeconfig=%KUBECONFIG%'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}

Here is your FULL CI/CD setup for Static Web Page (ALL 5 tasks) ✅
👉 Simple, clean, exam-ready
________________________________________
✅ 1. HTML Code (Create 5 Pages)
🔹 a) Student Results (results.html)
<!DOCTYPE html>
<html>
<body>
<h2>Student Results</h2>
<table border="1">
<tr><th>Name</th><th>Marks</th></tr>
<tr><td>Bharath</td><td>85</td></tr>
<tr><td>Naveen</td><td>90</td></tr>
</table>
</body>
</html>
________________________________________
🔹 b) Rivera Events (events.html)
<!DOCTYPE html>
<html>
<body>
<h2>Rivera Events</h2>
<ul>
<li>Dance</li>
<li>Music</li>
<li>Drama</li>
<li>Coding</li>
<li>Quiz</li>
</ul>
</body>
</html>
________________________________________
🔹 c) Conference Info (conference.html)
<!DOCTYPE html>
<html>
<body>
<h2>Conference Details</h2>
<p>Topic: AI & Future</p>
<p>Date: 20 April 2026</p>
<p>Venue: Auditorium</p>
</body>
</html>
________________________________________
🔹 d) Price List (price.html)
<!DOCTYPE html>
<html>
<body>
<h2>Price List</h2>
<ul>
<li>Pen - ₹10</li>
<li>Book - ₹50</li>
<li>Bag - ₹500</li>
</ul>
</body>
</html>
________________________________________
🔹 e) Timetable (timetable.html)
<!DOCTYPE html>
<html>
<body>
<h2>Student Timetable</h2>
<table border="1">
<tr><th>Day</th><th>Subject</th></tr>
<tr><td>Monday</td><td>Math</td></tr>
<tr><td>Tuesday</td><td>Science</td></tr>
</table>
</body>
</html>
________________________________________
✅ 2. Dockerfile (for HTML)
FROM nginx:latest
COPY . /usr/share/nginx/html
________________________________________
✅ 3. GitHub Steps
git init
git add .
git commit -m "Initial HTML pages"
git remote add origin <repo-url>
git push -u origin main
Modify → commit again:
git add .
git commit -m "Updated pages"
git push
________________________________________
✅ 4. Jenkins Pipeline (CI/CD)
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "iambhara1h/html-app:latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    bat '''
                    docker login -u %USER% -p %PASS%
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f deployment.yaml --validate=false'
            }
        }
    }
}
________________________________________
✅ 5. Kubernetes Deployment
🔹 deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: html-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: html-app
  template:
    metadata:
      labels:
        app: html-app
    spec:
      containers:
      - name: html-app
        image: iambhara1h/html-app:latest
        ports:
        - containerPort: 80
________________________________________
🔹 service.yaml
apiVersion: v1
kind: Service
metadata:
  name: html-service
spec:
  type: NodePort
  selector:
    app: html-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
________________________________________
🔹 Apply
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
________________________________________
✅ 6. Access Output
http://localhost:30007/results.html
http://localhost:30007/events.html
http://localhost:30007/conference.html
http://localhost:30007/price.html
http://localhost:30007/timetable.html
________________________________________
✅ FINAL STAGES (Jenkins)
✔ Clone Code
✔ Build Docker Image
✔ Push Image
✔ Deploy to Kubernetes
________________________________________
🎯 2-Line Exam Answer
👉 Static web pages are containerized using Docker and deployed to Kubernetes using Jenkins CI/CD pipeline.
________________________________________
If you want:
👉 Folder structure screenshot
👉 Viva Q&A (🔥 very important)
👉 One-page record writing format

pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "iambhara1h/html-app:latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/bhara1h30/html.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    bat '''
                    echo %PASS% | docker login -u %USER% --password-stdin
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }


    }
}
DEPLOYMENT:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: html-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: html-app
  template:
    metadata:
      labels:
        app: html-app
    spec:
      containers:
      - name: html-app
        image: iambhara1h/html-app:latest
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: html-service
spec:
  type: NodePort
  selector:
    app: html-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007

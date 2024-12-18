# what is jenkin CI?
CI involves automatically building , intergating and testing code.
create jenkins
configure build process maven
configure testing using JUnit
set up notification

# Trobleshooting
jenkins error
128 -> no cred configured 
401, 403 -> wrong cred
137 -> memory  not enough
 xms -> initial memory for the pipeline 
 xmx -> max memory

Sonarqube
internal server error
restart the server


if you getting error while pushing large size file in git
you need to update post buffer size





# explain jenkins pipeline?
automation techanology end to end process of building ,testing,and deploying software

# what can you do for creating backup and coping file in jenkins?
ThinBackup Plugin: This plugin automates the backup process. 
After installing it, you can configure backup settings, such as location, backup intervals, 
and the retention policy for old backups.

Manual Backup: To manually back up your Jenkins data, you should archive the following directories and files:
$JENKINS_HOME directory
which contains job configurations, build artifacts, and system settings.
plugins directory 
which contains all installed plugin files and their configurations.
secrets directory
which holds encrypted secrets like API tokens and credentials.
config.xml, jenkins.model.JenkinsLocationConfiguration.xml and hudson.tasks.Mailer.xml which store Jenkins system settings.

# what is process of jenkins transfer onto another server?

create backups of old server
install jenkins on new server
transfer the backup to new server


# what is an agent directive in jenkins ?

agent directive specifies where and how a Pipeline or a specific stage within a Pipeline should be executed

1) any
indicate pipeline/stage can run on any agent
pipeline {
    agent any
    // ...
}

2) none
 pipeline/stage  can run on master node

3) node
 specify a label for the agent to use. The Pipeline or stage will run on any agent with that label.

pipeline {
    agent {
        node {
            label 'linux-node'
        }
    }

4) docker
directive allows you to execute the Pipeline or stage within a Docker container.

pipeline {
    agent {
        docker {
            image 'maven:latest'
        }
    }
    // ...
}


Github integartion

1)install github plaugin
Manage Jenkins > Manage Plugins.

2) configure github authentication
manage jenkins -> configure system

Configure authentication using either:
Username and Password: For basic authentication.
OAuth API Tokens: For more secure access.
SSH Keys: For SSH-based authentication.

goto github setting search "webhook"

generate new tocken
select repos


2) Integrating Maven 
Manage Jenkins > Manage Plugins
search for "maven integration" plugin

create new freestyle project
In the Build Triggers section, you can configure triggers like:

Poll SCM: 
Periodically check for changes in your source code repository.

build section
Goals & Options: 
Specify the Maven goals to execute, e.g., clean install.

Root POM: 
Point to the root POM file of your Maven project.

Additional Build Arguments: 
Add any additional Maven command-line arguments.

Maven Installations: 
Select the Maven installation to use. You can configure multiple Maven installations in Jenkins.

Configure Post-Build Actions (Optional):

Publish JUnit test result report: 
If your project generates JUnit test reports, configure Jenkins to publish them.

Archive the artifacts: 
Specify the artifacts to archive, such as JAR files or WAR files.

Email Notification: 
Configure email notifications to be sent on build success, failure, or unstable builds.

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}

3) integrating Jfrog plaugin
Search for the "JFrog Artifactory" and install it

Configure JFrog Artifactory:
In Manage Jenkins > Configure System, find the "JFrog Artifactory" section.

Configure the following:

Server URL: 
The URL of your Artifactory server.

Credentials: 
Set up credentials to authenticate with Artifactory.

Default Depot: 
The default depot to use for artifact deployment.

In the build step, add a "Deploy to Artifactory" step.

Configure the following:

Server ID: 
The ID of the Artifactory server you configured.

Target Path: 
The path in Artifactory where you want to deploy the artifacts.

Includes: 
Specify the artifacts to deploy, e.g., target/*.jar.

Excludes: 
Specify any artifacts to exclude.

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Deploy to Artifactory') {
            steps {
                withArtifactory {
                    publishArtifacts(
                        resolver: 'my-resolver',
                        artifacts: 'target/*.jar',
                        deployPath: 'libs-release-local'
                    )
                }
            }
        }
    }
}


4) docker integration
Search for the "Docker Pipeline" "kubernetes plugin" in case of kubernetes
plugin and install it
. Configure Docker:

Docker Installation: 
Ensure Docker is installed on your Jenkins server or agents.

Docker Credentials: 
Configure Docker credentials in Jenkins to access private registries.

 Configure Kubernetes:

Kubernetes Cluster: 
Set up a Kubernetes cluster on EKS or any other provider.

Kubeconfig: 
Configure the kubeconfig file for your cluster in Jenkins. This can be done by adding the kubeconfig to the Jenkins server or by using Kubernetes credentials.

docker example

pipeline {
    agent {
        docker {
            image 'maven:latest'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t my-image .'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'docker push my-image'
            }
        }
    }
}

Kubernetes
goto manage jenkins -> configure system


pipeline {
    agent {
        kubernetes {
            defaultContainer 'kubectl'
            yamlFile 'kubernetes/deployment.yaml'
        }
    }
    stages {
        stage('Deploy to EKS') {
            steps {
                sh 'kubectl apply -f kubernetes/deployment.yaml'
            }
        }
    }
}

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh 'docker build -t my-image .'
                sh 'docker push my-image:latest'
            }
        }
        stage('Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}

5) integrating Sonarqube
install plugin "SonarQube Scanner"

Configure the following:

Name: 
A unique name for the server.

Server URL: 
The URL of your SonarQube server.

Server authentication: 
Choose the authentication method (e.g., token-based).

In the build step, add a Execute SonarQube Scanner step.
Configure the following:
Server: 
Select the SonarQube server you configured.

Analysis properties: 
Add any additional SonarQube analysis properties, such as specific analysis tasks or quality gates.






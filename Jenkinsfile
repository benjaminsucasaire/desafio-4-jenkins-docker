pipeline {
	agent any
	    tools {
            maven 'M3'
        }
	environment {
		BUILD_RELEASE_VERSION = readMavenPom().getVersion().replace("-SNAPSHOT", "")
		IMAGE = readMavenPom().getArtifactId()
		DOCKER_REGISTRY = "benjaminsucasaire"
        DOCKER_HUB_LOGIN = credentials('Dokcerhub-Applying-Sintad-bash')
	}
	stages {
		stage('checkout github') {
			steps {
				echo 'Descargando codigo de SCM'
				sh 'rm -rf *'
				checkout scm
			}
		}
		stage('check tools') {
			steps {
				sh "mvn -version"
			}
		}
		stage('Compilando Java') {
			steps {
				echo 'Compilando Java'
				sh 'mvn clean install -Dmaven.test.skip=true'
			}
		}
	    stage('Initialize'){
	    steps {
            def dockerHome = tool 'myDocker'
            env.PATH = "${dockerHome}/bin:${env.PATH}"
            }
         }

		stage('Build docker') {
			steps {
				echo("Hago un build de docker imagen ${IMAGE}:v${BUILD_RELEASE_VERSION}")
				sh("docker build -t ${IMAGE}:v${BUILD_RELEASE_VERSION} .")
				sh("docker tag ${IMAGE}:v${BUILD_RELEASE_VERSION} ${DOCKER_REGISTRY}/${IMAGE}:v${BUILD_RELEASE_VERSION}")
			}
		}

        stage('docker deploy') {
			steps {
                echo("Hago Docker Login")
                sh 'docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW'
				sh("docker push ${DOCKER_REGISTRY}/${IMAGE}")
				echo("Borro la imagen ${IMAGE}:v${BUILD_RELEASE_VERSION}")
				sh("docker rmi ${IMAGE}:v${BUILD_RELEASE_VERSION}")
			}
		} 
	}
}
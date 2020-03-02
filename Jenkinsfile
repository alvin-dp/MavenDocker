def appversion="1.0-SNAPSHOT"
def appname="my-app"
def regestryname = "alvindpdevops/mavenappdocker"

node('dockerslaveTF'){
    tool name: 'maven', type: 'maven'
    stage('test install maven') {
        withEnv(["PATH=${env.PATH}:${tool 'maven'}/bin"]){
            sh 'env | grep PATH'
            sh 'mvn -v'
    }
    }
    stage('Get app from Repo') {
            git credentialsId: 'git_devops', url: 'git@github.com:alvin-dp/simple-java-maven-app.git'
    }
    stage('list WorkDir') {
            sh 'ls -la'
    }                         
    stage('Build') {
        withEnv(["PATH=${env.PATH}:${tool 'maven'}/bin"]){
        sh 'mvn -B -DskipTests clean package'
        }
    }
  //stage('PostBuild') {
  //      withEnv(["PATH=${env.PATH}:${tool 'maven'}/bin"]){
  //      sh 'mvn jar:jar install:install help:evaluate -Dexpression=project.name'
        //appname=sh 'mvn help:evaluate -Dexpression=project.name | grep "^[^\[]"'
        //appname=sh label: 't1', returnStdout: true, script: 'NAME="mvn help:evaluate -Dexpression=project.name"'
        //appversion=sh 'mvn help:evaluate -Dexpression=project.version | grep "^[^\[]"'
        //appversion=sh label: 't2', returnStdout: true, script: 'VERSION="mvn help:evaluate -Dexpression=project.version"'
        //sh 'echo $NAME'
        //sh 'echo $VERSION'
   //     }
  //}
  stage('Test') {
      withEnv(["PATH=${env.PATH}:${tool 'maven'}/bin"]){
      sh 'mvn test'
      sh 'ls -la'
      stash includes: 'target/*.jar', name: 'artiApp'
      }
  }
}
node('dockerslave1'){
    tool name: 'tool_docker'
    stage('test install docker') {
        withEnv(["PATH=${env.PATH}:${tool 'tool_docker'}/bin"]){
        sh 'docker -v'
        }
    } 
    stage('test connect to Docker host') {    
        withEnv(["PATH=${env.PATH}:${tool 'tool_docker'}/bin"]){
            sh 'docker ps -a'
        } 
    }
    stage('Get Dockerfile from Repo') {
            git credentialsId: 'git_devops', url: 'git@github.com:alvin-dp/MavenDocker.git'
    }
    stage('unstash for deploy') {
        unstash 'artiApp'
        sh 'ls -la'
        sh 'ls -la target/'        
        sh 'cat Dockerfile'
    } 
    stage('Buiuld Docker'){
        withEnv(["PATH=${env.PATH}:${tool name: 'tool_docker'}/bin"]){        
            sh "docker build --no-cache --build-arg APP_NAME=${appname} --build-arg APP_VERSION=${appversion} -t ${regestryname} ."
        }

    }
    stage('Upload to Docker hub'){
        withEnv(["PATH=${env.PATH}:${tool name: 'tool_docker'}/bin"]){   
            withDockerRegistry(credentialsId: 'docker_hub_creds', toolName: 'tool_docker') {
                        sh "docker push ${regestryname}"
                    }
        }
    }
    
}

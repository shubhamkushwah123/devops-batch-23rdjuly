node{
    
    def mavenHome
    def mavenCMD
    def docker
    def dockerCMD
    def tagName
    
    stage('Jenkins Environment Preparation'){
        echo 'initializing environment...'
        mavenHome = tool name: 'maven' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        docker = tool name: 'docker' , type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        dockerCMD = "${docker}/bin/docker"
        tagName = 9.0
    }
    
    stage('code checkout'){
        echo 'checking out the code from git repository...'
        git 'https://github.com/shubhamkushwah123/devops-batch-23rdjuly.git'
    }
    
    stage('clean package'){
    try{
        echo 'cleaning... compiling ... testing ...  packaging...'
        sh "${mavenCMD} clean package"
    }
    catch(Exception e){
        echo "exception occured in ${JOB_NAME} .. ${BUILD_NUMBER}"
        currentBuild.result= "FAILURE"
        emailext body: 'The Build has been failed.. Please investigate.', subject: '${JOB_NAME} - FAILED', to: 'shubham@gmail.com'
    }
    }
        
    stage('build docker images'){
        echo 'building docker image...'
        sh "${dockerCMD} build -t shubhamkushwah123/addressbook:${tagName} ."
    }
        
        
    stage('docker login and push'){
        withCredentials([string(credentialsId: 'dockerPwd', variable: 'dockerhubPwd')]) {
            sh "${dockerCMD} login -u shubhamkushwah123 -p ${dockerhubPwd}"
            sh "${dockerCMD} push shubhamkushwah123/addressbook:${tagName}"
        }
    }
    
    stage('ansible configure & deploy'){
        echo "configuring test environment & deploy"
      // ansiblePlaybook become: true, credentialsId: 'ansible-private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'hosts', playbook: 'playbook-test.yml'
    
        sh 'ansible-playbook playbook-test.yml -i /home/ubuntu/hosts'
        
    }
    
    
}

node {
  def root = tool name: 'Go 1.10', type: 'go'

  def jobnameparts = JOB_NAME.tokenize('/') as String[]
  def jobconsolename = jobnameparts[0]

  ws("${JENKINS_HOME}/jobs/${jobconsolename}/builds/${BUILD_ID}/") {
    withEnv([
      "GOPATH=${JENKINS_HOME}/jobs/${jobconsolename}/builds",
      "GOROOT=${root}",
      "PATH+GO=${root}/bin"
      ]) {
      env.PATH="${GOPATH}/bin:$PATH"
      
      stage('Checkout') {
        echo 'Checking out SCM'
        checkout scm
      }
      
      stage('Pre Test') {
        echo 'Pulling Dependencies'

        sh "rm -rf ../src/${jobconsolename} && mkdir -p ../src/${jobconsolename} && cp -vr * ../src/${jobconsolename}"
    
        sh 'go version'
        sh "cd ../src/${jobconsolename} && go get -v"
      }
  
      stage('Build') {
        echo 'Building Executable'
      
        sh "go build -o ${jobconsolename}"
      }

      def tag = sh(returnStdout: true, script: "git tag --contains | head -1").trim()

      if (tag) {
        stage('Deploy') {
          if (env.BRANCH_NAME == 'master') {
            archiveArtifacts artifacts: "${jobconsolename}"
          }
        }
      }
    }
  }
}

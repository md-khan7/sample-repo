# sample-repo
node {

    def server

    def buildInfo

    def rtMaven

    

    stage ('Clone') {

        git url: 'https://github.com/jfrog/project-examples.git'

    }

 

    stage ('Artifactory configuration') {

        // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:

        server = Artifactory.server 'artifactory'




        rtMaven = Artifactory.newMavenBuild()

        rtMaven.tool = 'MAVEN_HOME' // Tool name from Jenkins configuration

        rtMaven.deployer releaseRepo: 'test-local', snapshotRepo: 'test-local', server: server

        rtMaven.resolver releaseRepo: 'test-local', snapshotRepo: 'test-local', server: server




        buildInfo = Artifactory.newBuildInfo()

    }

 

    stage ('Test') {

        rtMaven.run pom: 'maven-examples/maven-example/pom.xml', goals: 'clean test'

    }

        

    stage ('Install') {

        rtMaven.run pom: 'maven-examples/maven-example/pom.xml', goals: 'install', buildInfo: buildInfo

    }

        

    stage ('Publish build info') {

        server.publishBuildInfo buildInfo

    }

}

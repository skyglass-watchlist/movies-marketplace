def imageName = 'skyglass/movies-marketplace'
def registry = 'https://registry.hub.docker.com'

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")

    // stage('Quality Tests'){
    //     imageTest.inside {
    //         sh "npm run lint"
    //     }
    // }

    // stage('Unit Tests'){
    //     sh "docker run --rm -v $PWD/coverage:/app/coverage ${imageName}-test npm run test"
    //     publishHTML (target: [
    //         allowMissing: false,
    //         alwaysLinkToLastBuild: false,
    //         keepAll: true,
    //         reportDir: "$PWD/coverage/marketplace",
    //         reportFiles: "index.html",
    //         reportName: "Coverage Report"
    //     ])
    // }

    stage('Build'){
        docker.build(imageName, '--build-arg ENVIRONMENT=sandbox .')
    }

    stage('Push'){
        docker.withRegistry(registry, 'dockerHubCredentials') {
            docker.image(imageName).push(commitID())

            if (env.BRANCH_NAME == 'develop') {
                docker.image(imageName).push('develop')
            }
        }
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}
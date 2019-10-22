node {
    stage('CI'){
        checkout scm
        //git branch: 'jenkins2-course', 
        //    url: 'https://github.com/g0t4/solitaire-systemjs-course'

        // pull dependencies from npm
        bat 'npm install'
        // on linux use: sh 'npm install'
    
        // stash code & dependencies to expedite subsequent testing
        // and ensure same code & dependencies are used throughout the pipeline
        // stash is a temporary archive
        stash name: 'everything', 
              excludes: 'test-results/**', 
              includes: '**'
        
        // test with PhantomJS for "fast" "generic" results
        bat 'npm run test-single-run -- --browsers PhantomJS'
        // on linus use: sh 'npm run test-single-run -- --browsers PhantomJS'
        
        // archive karma test results (karma is configured to export junit xml files)
        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
        
    }
}

stage('Brwoser Testing') {
    parallel chrome: {
        runTests('Chrome')
    }, firefox: {
        runTests('Firefox')
    }
}

def runTests(browser) {
    node {
        bat 'del /s /q *.*'
        unstash "everything"
        bat "npm run test-single-run -- --browsers ${browser}"
        // archive karma test results (karma is configured to export junit xml files)
        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
    }
}

def notify(status){
    emailext (
      to: "chinmaysah@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}
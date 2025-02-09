node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        junit 'test-reports/results.xml'
    }
    
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
    }

    stage('Deploy') {
        env.VOLUME = "${pwd()}/sources:/src"
        env.IMAGE = 'cdrx/pyinstaller-linux:python2'
        dir(env.BUILD_ID) {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'pyinstaller -F add2vals.py'"
        }
        archiveArtifacts "sources/dist/add2vals"
        sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'rm -rf build dist'"

        echo 'Jeda eksekusi pipeline selama 1 menit'
        sleep(time: 60, unit: 'SECONDS')
        echo 'Pipeline selesai.'
    }
}

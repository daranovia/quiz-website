node {

    env.PROD_HOST = "10.10.178.7"
    env.PROD_USER = "dara"

    checkout scm

    stage("Build") {
        docker.image('composer:latest').inside('-u root') {
            sh '''
            rm -f composer.lock
            composer install --no-interaction --prefer-dist
            '''
        }
    }

    stage("Test") {
        docker.image('ubuntu:latest').inside('-u root') {
            sh 'echo "Ini adalah test stage"'
        }
    }

    stage("Deploy") {
        docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
            sshagent(credentials: ['ssh-prod']) {

                sh '''
                mkdir -p ~/.ssh
                ssh-keyscan -H $PROD_HOST >> ~/.ssh/known_hosts

                rsync -rav --delete ./ \
                $PROD_USER@$PROD_HOST:/home/$PROD_USER/prod.kelasdevops.xyz/ \
                --exclude=.env \
                --exclude=storage \
                --exclude=.git
                '''
            }
        }
    }

}

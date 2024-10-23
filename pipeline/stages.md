```groovy
    stage('Verificar conexión SSH') {
        withCredentials([sshUserPrivateKey(credentialsId: 'EC2_SSH_CREDENTIAL', keyFileVariable: 'identity')]) {
            // Probar la conexión SSH antes de proceder con el despliegue
            def sshTestCommand = "ssh -o StrictHostKeyChecking=no -i $identity ${env.EC2_USER}@${env.EC2_IP} 'echo Conexión exitosa'"
            echo "Probando conexión SSH a ${env.EC2_IP}..."
            sh sshTestCommand
        }
    }

    stage('Desplegar en EC2') {
        def ec2IP = env.EC2_IP
        def ec2User = env.EC2_USER
        def jarName = env.JAR_NAME

        withCredentials([sshUserPrivateKey(credentialsId: 'EC2_SSH_CREDENTIAL', keyFileVariable: 'identity')]) {
            echo "Transfiriendo JAR a la instancia EC2..."
            def scpCommand = "scp -o StrictHostKeyChecking=no -i $identity target/${jarName} ${ec2User}@${ec2IP}:/home/${ec2User}/"
            sh scpCommand

            echo "Verificando la presencia del archivo JAR en la instancia EC2..."
            def sshCheckFileCommand = "ssh -o StrictHostKeyChecking=no -i $identity ${ec2User}@${ec2IP} 'ls /home/${ec2User}/${jarName} || echo \"Archivo no encontrado\"'"
            sh sshCheckFileCommand

            echo "Deteniendo la instancia previa de la aplicación (si existe)..."
            def sshStopCommand = """
                ssh -o StrictHostKeyChecking=no -i $identity ${ec2User}@${ec2IP} \
                'pgrep -f ${jarName} && pkill -f ${jarName} || echo "No corriendo"'
            """
            sh(script: sshStopCommand, returnStatus: true)

            echo "Iniciando la aplicación en segundo plano..."
            def sshStartCommand = """
                ssh -o StrictHostKeyChecking=no -i $identity ${ec2User}@${ec2IP} \
                'nohup java -jar /home/${ec2User}/${jarName} > /home/${ec2User}/app.log 2>&1 &'
            """
            sh sshStartCommand

            echo "Verificando el estado de la aplicación..."
            def maxRetries = 5
            def delay = 5 // segundos entre reintentos
            def retries = 0
            def isAppRunning = false

            while (retries < maxRetries && !isAppRunning) {
                def result = sh(script: "ssh -o StrictHostKeyChecking=no -i $identity ${ec2User}@${ec2IP} 'pgrep -f ${jarName}'", returnStatus: true)
                if (result == 0) {
                    isAppRunning = true
                    echo "La aplicación está en ejecución."
                } else {
                    echo "La aplicación no está en ejecución, reintentando en ${delay} segundos..."
                    sleep(delay)
                    retries++
                }
            }

            if (!isAppRunning) {
                error("La aplicación no se pudo iniciar después de ${maxRetries} reintentos.")
            }

            echo "Mostrando las últimas líneas del log de la aplicación..."
            def sshShowLogsCommand = "ssh -o StrictHostKeyChecking=no -i $identity ${ec2User}@${ec2IP} 'tail -n 20 /home/${ec2User}/app.log || echo \"No hay logs disponibles\"'"
            sh sshShowLogsCommand
        }
    }
```
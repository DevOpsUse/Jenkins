**Examples of environment section in declarative pipleine**

  
    sh 'printenv'
    
    pipeline{
        agent any
        stages{
            stage("Env example-1"){
                steps{
                    sh 'printenv'
                }
            }
        }
    }
    
    -------------------------------------------------------------------
    
    pipeline {
      agent any
      environment { 
          CC = 'clang'
      }
      stages {
          stage('Example1') {
              environment { 
                  DEBUG_FLAGS = '-g'
              }
              steps {
                  //sh 'printenv'

                  sh 'echo CC value in stage-1: ${CC}'
                  sh 'echo DEBUG_FLAGS value in stage-1: ${DEBUG_FLAGS}'
              }
          }
          stage('Example2') {
              steps {
                  sh 'echo CC value in stage-2: ${CC}'
                  sh 'echo DEBUG_FLAGS value in stage-2: ${DEBUG_FLAGS}'
              }
          }
      }
  }

-------------------------------------------

    pipeline {
        agent any
        environment{
            myVar = "V2DevOpsOnline"
        }
        stages {
            stage('Example') {
                steps {
                    echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL} - ${myVar} - ${env.myVar}"


                    sh'echo Single quote Single line: Running ${BUILD_ID} on ${JENKINS_URL} - ${myVar} '

                    sh"echo Double quote Single line:Running ${BUILD_ID} on ${JENKINS_URL} - ${myVar} - ${env.myVar} "

                    sh'''
                        echo Single quote Multi line: Running ${BUILD_ID} on ${JENKINS_URL} - ${myVar}
                    '''

                    sh"""
                        echo Double quote Multi line: Running ${BUILD_ID} on ${JENKINS_URL} - ${myVar} - ${env.myVar} 
                    """
                }
            }
        }
    }
----------------------------

    pipeline {
        agent any 
        environment {
            // Using returnStdout
            CC = """${sh(
                    returnStdout: true,
                    script: 'echo "clang"'
                )}""" 
            // Using returnStatus
            EXIT_STATUS = """${sh(
                    returnStatus: true,
                    script: 'exit 1'
                )}"""
        }
        stages {
            stage('Example') {
                environment {
                    DEBUG_FLAGS = '-g'
                }
                steps {
                    sh 'printenv'
                }
            }
        }
    }
--------------------------------

    pipeline {
        agent any 
        environment {
            MY_CREDS = credentials("v2devopsonline")
        }
        stages {
            stage('Example') {
                steps {
                    sh 'echo username: ${MY_CREDS_USR} and pwd: ${MY_CREDS_PSW}'
                    
                    //sh 'curl -u ${MY_CREDS_USR}:${MY_CREDS_PSW} http://www.something.com'
                }
            }
        }
    }
----------------------------------

    pipeline {
        agent {
            // Define agent details here
        }
        environment {
            // The MY_KUBECONFIG environment variable will be assigned
            // the value of a temporary file.  For example:
            //   /home/user/.jenkins/workspace/cred_test@tmp/secretFiles/546a5cf3-9b56-4165-a0fd-19e2afe6b31f/kubeconfig.txt
            MY_KUBECONFIG = credentials('my-kubeconfig')
        }
        stages {
            stage('Example stage 1') {
                steps {
                    sh("kubectl --kubeconfig $MY_KUBECONFIG get pods")
                }
            }
        }
    }
 -----------------------------
 
     pipeline {
        agent any
        environment {
            EXAMPLE_CREDS = credentials('example-credentials-id')
        }
        stages {
            stage('Example') {
                steps {
                    /* CORRECT */
                    sh('curl -u $EXAMPLE_CREDS_USR:$EXAMPLE_CREDS_PSW https://example.com/')
                }
            }
        }
    }
----------------------

    pipeline {
        agent any
        stages {
            stage('Example Username/Password') {
                environment {
                    SERVICE_CREDS = credentials('my-predefined-username-password')
                }
                steps {
                    sh 'echo "Service user is $SERVICE_CREDS_USR"'
                    sh 'echo "Service password is $SERVICE_CREDS_PSW"'
                    sh 'curl -u $SERVICE_CREDS https://myservice.example.com'
                }
            }
            stage('Example SSH Username with private key') {
                environment {
                    SSH_CREDS = credentials('my-predefined-ssh-creds')
                }
                steps {
                    sh 'echo "SSH private key is located at $SSH_CREDS"'
                    sh 'echo "SSH user is $SSH_CREDS_USR"'
                    sh 'echo "SSH passphrase is $SSH_CREDS_PSW"'
                }
            }
        }
    }
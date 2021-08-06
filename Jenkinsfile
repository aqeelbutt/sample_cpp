pipeline {
    agent any

    environment {
        NEXUS_P = credentials('nexus-admin-password')
        SONAR_P = credentials('ncwdg-sample_c2-sonar-token')
        APP_NAME = 'sample_c'
    }

    stages {
      stage('Run CPP Check') {
          steps {
              sh '''cppcheck --xml --xml-version=2 . 2> cppcheck.xml'''
          }
        }
      stage('Publish CppCheck') {
          steps {
              publishCppcheck pattern:'cppcheck.xml'
              }
            }
      stage('Run SonarQube Scan') {
          steps {
              sh ''' /opt/sonar-scanner/bin/sonar-scanner  -Dproject.settings=sonar-project.properties -Dsonar.login=${SONAR_P} '''
          }
      }
      stage('CMAKE Build') {
          steps {
              cmakeBuild(
                installation: 'InSearchPath'
           )
          }
        }
      stage('Convert .cpp to .exe') {
          steps {
              sh '''g++ test.cpp -o ${APP_NAME}.exe
                    chmod a+x ${APP_NAME}.exe'''
            }
        }
      stage('Upload .exe to Nexus') {
          steps {
                sh '''curl -k -v -u admin:${NEXUS_P} --upload-file ${APP_NAME}.exe https://192.168.122.206/repository/ncwdg-repo/${APP_NAME}/${JOB_NAME}/1.0.${BUILD_NUMBER}/${APP_NAME}-${BUILD_NUMBER}.exe'''
            }
          }
         }
       }

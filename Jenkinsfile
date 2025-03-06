pipeline {
    agent any 

    parameters {
        choice(name: 'product', choices: ['a', 'b', 'c'], description: 'Select product')
        choice(name: 'environment', choices: ['dev', 'sit', 'uat'], description: 'Select environment')
        choice(name: 'environment', choices: ['sample','sample2','sample3'], description: 'Select environment')        
    }

    environment {
        MYSQL_ROOT_PASSWORD = credentials('mysql-root-password') 
    }

    stages {
        stage('Prepare Database Mapping') {
            steps {
                script {
                    def databaseMappingFile = "${WORKSPACE}/database-mapping.json"

                    if (!fileExists(databaseMappingFile)) {
                        echo "Database mapping file not found. Copying from /root/mount_try/new/"
                        sh "cp /root/mount_try/new/jenkins_Mysql_sample/database-mapping.json ${databaseMappingFile}"
                    }

                    if (!fileExists(databaseMappingFile)) {
                        error "Database mapping file still not found after copying. Please check the path."
                    }
                }
            }
        }

        stage('Validate Database Selection') {
            steps { 
                script {
                    def databaseList = databaseChoices(params.product, params.environment)

                    if (!databaseList.contains(params.database) || params.database.trim() == '') {
                        error "Selected database '${params.database}' is not valid for product '${params.product}' and environment '${params.environment}'. Available choices: ${databaseList}"
                    } else {
                        echo "Database '${params.database}' is valid for '${params.product}-${params.environment}'."
                    }
                }
            }
        }

        stage('Generate Credentials') {
            steps {
                script {
                    def credentials = generateCredentials(params.database)

                    env.DB_USERNAME = credentials.username
                    env.DB_PASSWORD = credentials.password

                    echo "Generated database credentials successfully."
                }
            }
        }

        stage('Send Email') {
            steps {
                emailext body: """ 
                Generated credentials for ${params.product} - ${params.environment} - ${params.database}: 
                Username: ${env.DB_USERNAME} 
                Password: ${env.DB_PASSWORD} 
                """, 
                subject: "Database Credentials", 
                to: 'santhosh.h@convergentechnologies.com'
            }
        }
    }
}

def databaseChoices(product, environment) {
    def dbMap = readJSON file: "${WORKSPACE}/database-mapping.json"
    
    def dbList = dbMap["${product}-${environment}"]
    if (dbList instanceof List) {
        return dbList.collect { it.toString() }  
    }
    return []
}

def generateCredentials(database) {
    def username = "user_${database}_${UUID.randomUUID().toString().take(8)}"
    def password = UUID.randomUUID().toString()

    sh """
    mysql -u root -p${MYSQL_ROOT_PASSWORD} -e "SHOW DATABASES;"
    mysql -u root -p${MYSQL_ROOT_PASSWORD} -e "CREATE USER '${username}'@'%' IDENTIFIED BY '${password}';"
    mysql -u root -p${MYSQL_ROOT_PASSWORD} -e "GRANT INSERT, DELETE, UPDATE ON ${database}.* TO '${username}'@'%';" 
    mysql -u root -p${MYSQL_ROOT_PASSWORD} -e "FLUSH PRIVILEGES;"
    """

    return [username: username, password: password]
}

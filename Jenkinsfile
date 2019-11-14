bucketName = "pv-jenkins-backup20191113114559737500000001"
node('master') {
    try {
        // Backup
        todayDate = sh(script: "date +%Y-%m-%d", returnStdout: true).trim()
        sh "cd $JENKINS_HOME; tar --exclude '${JENKINS_HOME}/workspace' -zcf /tmp/${todayDate}.tar.gz ${JENKINS_HOME} || [ \$? -eq 1 ]"
        s3Upload bucket: bucketName, file: "/tmp/${todayDate}.tar.gz"
        // Cleanup
        Date date = new Date().minus(6)
        staleDate = date.format("yyyy-MM-dd")
        l = s3FindFiles bucket: bucketName, onlyFiles: true
        l.each {
            if("${it}".compareToIgnoreCase(staleDate) < 0) {
                s3Delete bucket: bucketName, path: "${it}"
            }
        }   
    }
    finally {
        cleanWs()
        sh "rm -f /tmp/${todayDate}.tar.gz"
    }
}
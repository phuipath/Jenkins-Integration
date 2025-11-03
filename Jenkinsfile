pipeline {
  agent any
  environment {
    MAJOR = '1'
    MINOR = '0'
  }
  stages {
    stage ('PostBuild') {
      steps {
        UiPathTest (
          testTarget: [$class: 'TestSetEntry', testSet: "Jenkins USPS Informed Delivery TC"],
          orchestratorAddress: "https://staging.uipath.com/uibank/",
          orchestratorTenant: "AutomationCoE",
          folderName: "SandBox/Phil Harris",
          timeout: "10000",
          traceLoggingLevel: 'None',
          testResultsOutputPath: "result.xml",
          credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: "credentialsId"]
        )
      }
    }
  }
}
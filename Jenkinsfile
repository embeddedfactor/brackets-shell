#!groovy

def project = "brackets-shell"
def platforms = [
  [platform: 'linux', host: 'trustydev', python: 'python2', bear: 'bear'],
  [platform: 'win32', host: 'windows7dev'],
  [platform: 'darwin', host: 'macossierradev']
]

def builds = [:]

for (int i = 0; i < platforms.size(); i++) {
  def platform = platforms[i].get('platform')
  def host = platforms[i].get('host')
  def python = platforms[i].get('python', 'python')
  def bear = platforms[i].get('bear', '')
  builds[platform] = {
    node(host) {
      echo 'Cleanup Workspace'
      deleteDir()
      env.PLATFORM = platform

      echo 'Checkout SCM'
      checkout scm
      //Patch first
      if (platform == 'win32') {
        sh 'npm install --msvs_version=2013'
        sh 'npm install grunt-cli --msvs_version=2013'
      } else {
        sh 'npm install'
        sh 'npm install grunt-cli'
      }
      sh 'node_modules/.bin/grunt'
      //sh 'cp ../package.json www_package.json'
      sh 'node_modules/.bin/grunt stage'
      tar -czf ../shell-pack-${PLATFORM}.tar.gz staging
      archiveArtifacts artifacts: "*.tar.gz", fingerprint: true
    }
  }
}

stage('Build') {
  parallel builds
}


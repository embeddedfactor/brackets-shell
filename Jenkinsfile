#!groovy
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
        env.NPM_EXTRA = '--msvs_version=2013'
      } else {
        env.NPM_EXTRA = ''
      }
      sh '''
        export PATH="/c/nodejs/x64/v7.9.0/bin:/opt/nodejs/x64/v7.9.0/bin:$PATH"
        npm install ${NPM_EXTRA}
        npm install grunt-cli ${NPM_EXTRA}

        node_modules/.bin/grunt
        node_modules/.bin/grunt stage
        tar -czf ../shell-pack-${PLATFORM}.tar.gz staging
      '''
      archiveArtifacts artifacts: '*.tar.gz', fingerprint: true
    }
  }
}

stage('Build') {
  parallel builds
}

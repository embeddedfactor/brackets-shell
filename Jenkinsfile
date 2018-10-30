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

      echo 'Checkout SCM'
      checkout scm
      //Patch first
      env.PLATFORM = platform
      if (platform == 'win32') {
        env.NPM_EXTRA = '--msvs_version=2013'
      } else {
        env.NPM_EXTRA = ''
      }
      def staging = ''
      if (platform == "darwin") {
        staging = "installer/mac/staging/*.app"
      } else if (platform == "win32") {
        staging = "installer/win/staging"
      } else {
        staging = "installer/linux/debian/package-root/opt/*"
      }
      env.STAGING = staging
      sh '''
        export PATH="/c/nodejs/x64/v6.9.4/bin:/opt/nodejs/x64/v6.9.4/bin:$PATH"
        npm install ${NPM_EXTRA}
        npm install grunt-cli ${NPM_EXTRA}

        node_modules/.bin/grunt --force
        node_modules/.bin/grunt stage
        tar -C `dirname ${STAGING}` -czf shell-pack-${PLATFORM}.tar.gz `basename ${STAGING}`
      '''
      archiveArtifacts artifacts: '*.tar.gz', fingerprint: true
    }
  }
}

stage('Build') {
  parallel builds
}

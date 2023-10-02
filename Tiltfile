allow_k8s_contexts('kubernetesOCI')
sha1=str(local("cat Dockerfile | openssl dgst -sha1 | tr -d '\n'"))
Namespace='sandbox-odoo-dev'
ModuleName='my_module'
ModulePath='./'+ModuleName
Registry='ttl.sh/sanbox-odoo-dev-'+sha1
default_registry(Registry)

load('ext://helm_resource', 'helm_resource', 'helm_repo')
load('ext://namespace', 'namespace_create')
os.putenv ( 'NAMESPACE' , Namespace )
os.putenv ( 'MODULENAME', ModuleName )
os.putenv ('MODULEPATH', ModulePath)
os.putenv ( 'DOCKER_REGISTRY' , Registry ) 
namespace_create(Namespace)
helm_repo('highcanfly', 'https://helm-repo.highcanfly.club/')

custom_build('odoo_bitnami_custom_tilted', './kaniko-build.sh', [
    ModuleName
], skips_local_docker = True,
    live_update = [
        sync(ModulePath, '/dev-addons/'+ModuleName)
    ])

helm_resource('odoo-dev',
    'highcanfly/odoo',
    namespace = Namespace,
    flags = ['--values=./_values.yaml', '--set', 'odoo.image.registry=ttl.sh'],
    image_deps = ['odoo_bitnami_custom_tilted'],
    image_keys=[('odoo.image.registry','odoo.image.repository', 'odoo.image.tag')],
)
load('ext://uibutton', 'cmd_button', 'location')

cmd_button(name='push module',
           resource='odoo-dev',
           argv=['find', ModuleName , '-exec', 'touch', '{}','+'])

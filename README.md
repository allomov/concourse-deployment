# concourse-deployment

- Requires new [BOSH CLI v0.0.120+](https://github.com/cloudfoundry/bosh-cli)

# Description

This repo is used to deploy Councourse CI by new bosh-cli.

# Files

* `concourse.yml` - standard deployment manifeset from [Councourse docs](https://concourse.ci/deploying-concourse.html).
* `concourse-single-vm.yml` - single vm deployment test

# How to test

```
bosh interpolate ~/workspace/concourse-deployment/concourse-single-vm.yml \
    --vars-store creds.yml \
    -v external_url=ci.altoros.com \
    -v external_ip=eip
```

# How to use

```
bosh -e bosh cc > cloud-config.yml

cat <<EOT >> add-concourse-nodes-modification.yml
---
- type: append
  path: /vm_types
  value: 
  - cloud_properties:
      ephemeral_disk:
        size: 50000
      instance_type: m3.xlarge
    name: concourse_large
EOT

mntr apply cloud-config.yml -m add-concourse-nodes-modification.yml > cloud-config.yml
bosh -e bosh update-cloud-config cloud-config.yml
bosh -e bosh upload-stemcell https://bosh.io/d/stemcells/bosh-aws-xen-hvm-ubuntu-trusty-go_agent?v=3312.5

bosh -e bosh -d concourse -n ~/workspace/concourse-deployment/concourse-single-vm.yml \
                          -v external_url=https://ci.domain.com
                          -v external_ip=<elastic-ip-address>
```

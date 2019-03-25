# suse-openstack-stemcell
Build Openstack KVM openSUSE stemcell

## Starting the environment

Start the builder container using

```bash
$ cd ~/workspace
$ git clone https://github.com/abhilash07/suse-openstack-stemcell.git
$ cd suse-openstack-stemcell
$ cd ~/workspace/suse-openstack-stemcell/ci/docker/suse-os-image-stemcell-builder
$ ./build
$ cd ~/workspace/suse-openstack-stemcell/ci/docker/
$ chmod 755 run 
$ ./run suse-os-image-stemcell-builder
```

```bash
Sample Output:
Successfully built b3c17e17fb6a
Successfully tagged bosh/suse-os-image-stemcell-builder:latest
```

and install the required rubygems

```
bundle install --local
```

## Building the image Run

```bash
# Usually the script expects the building user to have user id 1000. The SUSE based container also supports
# other ids, though. This behaviour can be enabled by setting the `SKIP_UID_CHECK` environment variable.
export SKIP_UID_CHECK=1
mkdir -p /opt/bosh/tmp
bundle exec rake stemcell:build_os_image[opensuse,leap,/opt/bosh/tmp/os_leap_base_image.tgz]
```
```bash
Sample Output:
  enabled services
    systemctl is-enabled chronyd.service
      keeps the system clock up to date (stig: V-38620 V-38621)

Finished in 23.32 seconds (files took 0.42752 seconds to load)
303 examples, 0 failures
```

At the end of the process you should see all image tests pass.

## Building the openSUSE openstack stemcell

Start the generation of the stemcell by running the following command:

```
export BOSH_MICRO_ENABLED=no
bundle exec rake stemcell:build_with_local_os_image[openstack,kvm,opensuse,leap,/opt/bosh/tmp/os_leap_base_image.tgz]
```
```bash
CIS test case verification
  For all infrastructure except Azure
    confirms that all CIS test cases ran

Stig test case verification
  confirms all stig test cases ran
del devmap : loop0p1

Finished in 13.41 seconds (files took 0.52329 seconds to load)
414 examples, 0 failures

user@69be1c1fcf30:/opt/bosh> cd tmp/
user@69be1c1fcf30:/opt/bosh/tmp> ls 
bosh-stemcell-0000-openstack-kvm-opensuse-leap-go_agent-raw.tgz  bosh-stemcell-0000-openstack-kvm-opensuse-leap-go_agent.tgz  os_leap_base_image.tgz
```

At the end of the process the stemcell builder will run some tests. If they all pass a stemcell should exist in the `tmp` folder under your current directory.

## Copy builded Openstack Stemcells from docker container to your local host

```
docker cp your-contaier-id:/opt/bosh/tmp/bosh-stemcell-0000-openstack-kvm-opensuse-leap-go_agent.tgz foo.txt your-local-hostpath/bosh-stemcell-0000-openstack-kvm-opensuse-leap-go_agent.tgz
```
```
Note: Copy Openstack Stemcell Raw format, only if your Openstack use Ceph Storage
docker cp your-contaier-id:/opt/bosh/tmp/bosh-stemcell-0000-openstack-kvm-opensuse-leap-go_agent.tgz foo.txt your-local-hostpath/bosh-stemcell-0000-openstack-kvm-opensuse-leap-go_agent.tgz
```


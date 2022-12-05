THIS ROLE is a wrapper around the great role of `geerlingguy`'s Docker role so i have the same base and variable needed for my other Ansible roles that install apps in docker containers

It only set some defaults and include the role `geerlingguy/docker`

After installing docker the two variables for the docker user are saved and can be user in other roles to run containers as the docker user

`docker_uid` User ID of docker user
`docker_gid` group ID of docker

yumrepo_server Cookbook
========================
This cookbook configures a Yum repository to be served from your system. It will automatically install and configure the Apache web server (as it's the only server that's available without requiring the EPEL yum repo).  It also install the createrepo package for the utilities needed to setup a directory as a yum source.

Requirements
------------

#### cookbooks
- `apache2`
- `logrotate`

#### packages
- `httpd` - needed to serve the packages in the repository
- `createrepo` - needed to setup the repository
- `logrotate` - needed by the httpd package to configure log file management

#### platforms
Any system that uses Yum and has the required pakcages

Attributes
----------

#### yumrepo_server::default

*  **['yum']['repo\_base\_dir']**
    _Type:_ String
    _Description:_ The base directory where the repositories will be configured.  Will be used as Apache's DocumentRoot
    _Default:_ /var/lib/yum-repo

*  **['apache']['include\_logrotate']**
    _Type:_ Boolean
    _Description:_ Flag to determine if logrotate will be configured for Apache logs
    _Default:_ true

Usage
-----
### Recipes

#### yumrepo_server::default

Just include `yum_repo_server` in your node's `run_list`:

```json
{
  "name":"my_node",
  "run_list": [
    "recipe[yum_repo_server]"
  ]
}
```

### LWRPs

#### yumrepo_server

##### :create action

Create a yum repository inside of the relative path given (will be rooted at node['yum']['repo_base_dir'])

_With implied directory_

```ruby
yumrepo_server 'relative/yum/repo/path' do
  action :create
  options "-v --quiet"  # An optional string of options to pass to the createrepo command
  remote_source "http://upstream.com/path" # An optional URL string used as a base to retrieve packages from
  packages %w(pkg1.rpm pkg2.rpm pkg3.rpm)  # An optional array of package names to be configured in the repo (default is all packages). Required with the :remote_source attribute to specify which packages to retrieve.
end
```

_With explicit directory_

```ruby
yumrepo_server 'creates my yum repo' do
  action :create
  dir 'relative/yum/repo/path' # Required relative path to create repo at (this value will be appended to the value of node['yum']['repo_base_dir'])
  options "-v --quiet"  # An optional string of options to pass to the createrepo command
  remote_source "http://upstream.com/path" # An optional URL string used as a base to retrieve packages from
  packages %w(pkg1.rpm pkg2.rpm pkg3.rpm)  # An optional array of package names to be configured in the repo (default is all packages). Required with the :remote_source attribute to specify which packages to retrieve.
end
```

##### :update action

Update the the yum repository at the path given

```ruby
yumrepo_server 'relative/yum/repo/path' do
  action :update
  options "-v --quiet"  # An optional string of options to pass to the createrepo command
end
```

##### :delete action

Deletes the repository at the given path (basically delete the directory)

```ruby
yumrepo_server 'relative/yum/repo/path' do
  action :delete
end
```

License and Authors
-------------------

Authors: Michael Morris
License: 3-clause BSD